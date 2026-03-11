# CLAUDE.md — Estudio de Impacto Técnico (TFG)

## Objetivo del proyecto

TFG de análisis del impacto del **diseño táctico de tareas de entrenamiento** sobre métricas técnico-físicas de futbolistas de la Real Sociedad.

- **Pregunta central**: ¿Cuánto explican las 4 variables de diseño táctico (Espacio, Agrupación, Polaridad, Equilibrio) la varianza en el rendimiento técnico-físico individual?
- **Datos**: 4,446 observaciones de jugadores con sensor PlayerMaker, procesadas en `Datos/Matriz_V3.xlsx`
- **Pipeline analítico**: Limpieza → EDA → ANOVA factorial → GLMM bayesianos (PyMC)
- **Estado**: **COMPLETADO**. Todos los modelos están ajustados e interpretados.

---

## Stack y herramientas

| Herramienta | Uso |
|---|---|
| Python 3 + Jupyter | Entorno de análisis |
| pandas, numpy | Manipulación de datos |
| scipy, statsmodels | Tests estadísticos, ANOVA, EMMs |
| matplotlib, seaborn | Visualización |
| PyMC | Modelos GLMM bayesianos |
| ArviZ | Diagnósticos bayesianos, LOO-CV, plots |
| openpyxl | Lectura/escritura de archivos Excel |
| NetCDF4 / ArviZ | Guardado/carga de objetos InferenceData (.nc) |

No hay `requirements.txt` explícito. El entorno asume conda/mamba con PyMC stack instalado.

---

## Estructura de carpetas

```
ESTUDIO_IMPACTO_TECNICO_VERSION_FINAL/
│
├── 01_LIMPIEZA/          # Pipeline de datos raw → Matriz_V3
│   ├── 01_limpieza_previa.ipynb          # PlayerMaker_Matrix → Matriz_V1
│   ├── 02_seleccion_vd.ipynb             # Selección de 4 VD de 17 candidatas
│   ├── 03_creacion_metricas.ipynb        # Normalización /min, GrupoEdad → Matriz_V2
│   ├── 04_limpieza_metricas.ipynb        # Eliminación no-participación → Matriz_V3
│   ├── 05_diccionario_metricas.ipynb     # Documentación de las 19 variables finales
│   └── 06_estructura_diseno.ipynb        # Análisis del diseño factorial (desbalance, anidamientos)
│
├── 02_EDA/               # Exploración antes del modelado
│   ├── 01_descriptivo_univariante.ipynb  # Distribuciones de las 4 VD
│   ├── 02_variabilidad_intra_inter.ipynb # ICC por jugador
│   ├── 03_efecto_base_vi.ipynb           # Efectos principales de cada VI
│   ├── 04_interacciones_2_way_VI.ipynb   # Interacciones entre pares de VI
│   ├── 05_grupoedad_moderador.ipynb      # GrupoEdad como moderador
│   ├── 06_perfil_factorial_completo.ipynb # 16 combinaciones 2⁴
│   └── 07_correlaciones_vd.ipynb         # Correlaciones entre VD (trade-off técnico-físico)
│
├── 03_ANOVA/
│   └── 01_anova_factorial.ipynb          # ANOVA factorial Tipo III previo a GLMM
│
├── 04_GLMM/              # Modelos bayesianos principales
│   ├── 01_glmm_toques.ipynb              # Gamma, M0-M5, selección M1
│   ├── 01b_glmm_toques_fichas_categoria.ipynb
│   ├── 02_glmm_golpeos.ipynb             # Hurdle Gamma, selección M1
│   ├── 02b_glmm_golpeos_fichas_categoria.ipynb
│   ├── 03_glmm_distancia.ipynb           # Gamma, selección M2 (con GrupoEdad)
│   ├── 03b_glmm_distancia_fichas_categoria.ipynb
│   ├── 04_glmm_HID.ipynb                 # Hurdle Gamma, selección M2
│   └── 04b_glmm_HID_fichas_categoria.ipynb
│
├── 04_GLMM_INTERPRETACIONES/  # FUENTE DE VERDAD — no duplicar aquí
│   ├── 01_TOQUES.MD
│   ├── 02_GOLPEOS.MD
│   ├── 03_DISTANCIA.MD
│   ├── 04_HID.MD
│   └── 05_SINTESIS_GENERAL.MD
│
└── Datos/
    ├── PlayerMaker_Matrix.xlsx   # Raw data (NO modificar)
    ├── Matriz_V1.xlsx            # 4,621 × 28 (post-limpieza inicial)
    ├── Matriz_V2.xlsx            # 4,621 × 19 (post-métricas derivadas)
    ├── Matriz_V3.xlsx            # 4,446 × 19 (DATASET FINAL — NO modificar)
    ├── glmm_toques_final.nc      # InferenceData modelo Toques (PyMC/ArviZ)
    ├── glmm_golpeos_final.nc     # InferenceData modelo Golpeos
    ├── glmm_distancia_final.nc   # InferenceData modelo Distancia
    ├── glmm_hid_final.nc         # InferenceData modelo HID
    └── idata_HID_final.nc        # InferenceData HID (variante)
```

Los archivos `_fichas_categoria` son notebooks de interpretación por GrupoEdad individual, no modelos nuevos.

---

## Decisiones clave ya tomadas

**No reabrir estas decisiones sin justificación explícita.**

### Variables dependientes (4 seleccionadas de 17 candidatas)
| VD normalizada | Tipo distribucional | % Ceros | Modelo GLMM elegido |
|---|---|---|---|
| `Total Touches / min` | Gamma (log link) | 0% | M1 (efectos principales) |
| `Golpeos +15 m/s / min` | Hurdle Gamma | 29.5% | M1 (efectos principales) |
| `Distance Covered / min` | Gamma (log link) | 0% | M2 (+ GrupoEdad) |
| `HID / min` | Hurdle Gamma | 30.1% | M2 (+ GrupoEdad) |

- **HID threshold**: 5.5 m/s (≈ 20 km/h). El umbral original de 4 m/s fue descartado por redundancia alta con Distancia (r ≈ 0.81).
- **Golpeos**: suma de Release Velocity Zones 4+5+6 (velocidades > 15 m/s).
- **Outliers superiores**: detectados pero NO eliminados (representan rendimiento real extremo, no errores).
- **Criterio de exclusión de filas**: AND simultáneo de `Total Touches (#) < 3.0` Y `Total Touches / min < 0.30` (no-participación). Solo OR habría sido demasiado agresivo.

### GrupoEdad (5 categorías)
| GrupoEdad | Equipos | N |
|---|---|---|
| Infantil | Infantil Txiki, Handi | 625 |
| Cadete | Cadete Txiki, Vasca | 716 |
| Juvenil | EASO, Juvenil Div. Honor | 1,006 |
| Senior M. | Real Sociedad, Real Sociedad C, SANSE | 1,015 |
| Neskak | Neskak A, B, C | 1,084 |

- `NombreCorrecto` (12 valores) está **anidado 1:1 dentro de GrupoEdad** → nunca cruzar ambos en el mismo modelo. Usar siempre `GrupoEdad`.

### Jerarquía de modelos GLMM (M0 → M5)
- M0: solo intercepto aleatorio por `Player Id`
- M1: efectos principales de 4 VI de tarea
- M2: M1 + GrupoEdad como efecto fijo
- M3: M2 + interacciones 2-way entre VI
- M4: M3 + interacciones VI × GrupoEdad
- M5: máxima complejidad (raramente convergente)

Comparación con **LOO-CV** (ArviZ). Selección por peso de stacking.

### Tipo de SS
SS **Tipo III** en todo el proyecto. El diseño factorial es severamente desbalanceado (CV = 137%, ratio máx/mín de celda = 91x). Tipo I daría resultados sesgados.

---

## Nombres de variables clave en el código

```python
# Dataset final
df = pd.read_excel("Datos/Matriz_V3.xlsx")

# Variables independientes (VI) de diseño táctico
VI = ["Espacio", "Agrupación", "Polaridad", "Equilibrio"]

# Variables dependientes normalizadas (VD /min) — nombres exactos en el Excel
VD_norm = [
    "Total Touches / min",
    "Golpeos +15 m/s / min",
    "Distance Covered / min",
    "HID / min"
]

# Variables dependientes absolutas
VD_abs = [
    "Total Touches (#)",
    "Golpeos +15 m/s (#)",
    "Distance Covered (m)",
    "HID (m)"
]

# Variables de contexto/identificación
ID_vars = ["Phase Id", "Player Id", "NombreCorrecto", "GrupoEdad", "Phase Duration (min)"]

# Niveles de cada VI (contraste usado en GLMM: segundo vs. primero)
# Espacio: "amplio" (ref) vs. "reducido"
# Agrupación: "grande" (ref) vs. "pequeño"
# Polaridad: "Polarizado" (ref) vs. "No polarizado"
# Equilibrio: "Equilibrio" (ref) vs. "Desequilibrio"
```

---

## Convenciones de código y naming

- **Notebooks**: numerados con dos dígitos (`01_`, `02_`...). El prefijo indica orden de ejecución dentro de su carpeta.
- **Versiones de datos**: `Matriz_V1.xlsx` → `V2` → `V3`. No crear `V4` sin nueva fase documentada.
- **Modelos guardados**: `glmm_[vd]_final.nc` donde `[vd]` = `toques`, `golpeos`, `distancia`, `hid`.
- **InferenceData**: siempre guardado y cargado con `az.from_netcdf()` / `idata.to_netcdf()`.
- **Sufijo `_fichas_categoria`**: notebooks de resumen por GrupoEdad individual, NO contienen nuevos modelos, solo predicciones condicionadas del modelo ya ajustado.

---

## Comandos útiles

```python
import pandas as pd
import arviz as az
import pymc as pm

# Cargar dataset final
df = pd.read_excel("Datos/Matriz_V3.xlsx")

# Cargar modelo GLMM ya ajustado (no re-ajustar sin motivo)
idata_toques = az.from_netcdf("Datos/glmm_toques_final.nc")
idata_golpeos = az.from_netcdf("Datos/glmm_golpeos_final.nc")
idata_distancia = az.from_netcdf("Datos/glmm_distancia_final.nc")
idata_hid = az.from_netcdf("Datos/glmm_hid_final.nc")

# Diagnósticos rápidos
az.summary(idata_toques, var_names=["beta_espacio", "beta_agrupacion",
                                     "beta_polaridad", "beta_equilibrio"])

# Comparación de modelos
comp = az.compare({"M1": idata_m1, "M2": idata_m2}, ic="loo", scale="log")

# R² bayesiano
az.r2_score(y_true, y_pred_samples)
```

---

## Estado actual y próximos pasos

### Completado
- [x] Limpieza y pipeline de datos (`01_LIMPIEZA/`)
- [x] EDA completo con 7 notebooks (`02_EDA/`)
- [x] ANOVA factorial (`03_ANOVA/`)
- [x] 4 modelos GLMM bayesianos ajustados y guardados (`04_GLMM/`)
- [x] Interpretaciones validadas en formato .md (`04_GLMM_INTERPRETACIONES/`)
- [x] Síntesis transversal (`04_GLMM_INTERPRETACIONES/05_SINTESIS_GENERAL.MD`)

### Las interpretaciones finales están en (leer antes de cualquier análisis adicional)
- [04_GLMM_INTERPRETACIONES/01_TOQUES.MD](04_GLMM_INTERPRETACIONES/01_TOQUES.MD)
- [04_GLMM_INTERPRETACIONES/02_GOLPEOS.MD](04_GLMM_INTERPRETACIONES/02_GOLPEOS.MD)
- [04_GLMM_INTERPRETACIONES/03_DISTANCIA.MD](04_GLMM_INTERPRETACIONES/03_DISTANCIA.MD)
- [04_GLMM_INTERPRETACIONES/04_HID.MD](04_GLMM_INTERPRETACIONES/04_HID.MD)
- [04_GLMM_INTERPRETACIONES/05_SINTESIS_GENERAL.MD](04_GLMM_INTERPRETACIONES/05_SINTESIS_GENERAL.MD)

### Posibles próximos pasos
- Redacción formal del TFG (capítulos metodología, resultados, discusión)
- Visualizaciones para presentación (forest plots, predicciones factoriales, descomposición de varianza)
- Cualquier análisis adicional debe documentarse en un nuevo notebook numerado, nunca modificando los existentes

---

## Cosas a evitar

| Qué | Por qué |
|---|---|
| Modificar `Datos/Matriz_V3.xlsx` | Es el dataset final validado. Cualquier cambio rompe la reproducibilidad. |
| Eliminar outliers superiores | Representan rendimiento real extremo, no errores de sensor. Decisión documentada en NB04. |
| Usar SS Tipo I en ANOVA/GLMM | El diseño está severamente desbalanceado (CV=137%). Solo Tipo III es correcto. |
| Incluir `NombreCorrecto` y `GrupoEdad` en el mismo modelo | `NombreCorrecto` está anidado en `GrupoEdad` (12 → 5, relación 1:1). Colindanidad perfecta. |
| Usar HID con umbral 4 m/s (≈14.4 km/h) | Alta redundancia con `Distance Covered` (r ≈ 0.81). Usar siempre umbral 5.5 m/s. |
| Re-ajustar los GLMMs desde cero | Los `.nc` ya están guardados. Cargar con `az.from_netcdf()` salvo que haya un motivo concreto. |
| Duplicar contenido de los .md de interpretaciones | Son fuente de verdad. Referenciarlos, no copiarlos. |
| Añadir interacciones VI × GrupoEdad al modelo final | LOO-CV descartó estos modelos (M4/M5). El modelo es aditivo en escala log. |

---

## Hallazgo central (para orientar cualquier trabajo futuro)

El diseño táctico explica entre el 3% (Golpeos) y el 21% (Toques) de la varianza en las métricas técnico-físicas. Existe un **trade-off técnico-físico estructural**: las condiciones que maximizan Toques (espacio reducido, no polarizado) son opuestas a las que maximizan HID (espacio amplio). Ver síntesis completa en [05_SINTESIS_GENERAL.MD](04_GLMM_INTERPRETACIONES/05_SINTESIS_GENERAL.MD).
