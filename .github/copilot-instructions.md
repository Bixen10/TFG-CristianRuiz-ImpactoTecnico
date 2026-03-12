# Copilot Instructions — Estudio de Impacto Técnico (TFG)

## Objetivo

TFG que analiza el impacto del diseño táctico de tareas de entrenamiento (Espacio, Agrupación, Polaridad, Equilibrio) sobre métricas técnico-físicas de futbolistas de la Real Sociedad. 4,446 observaciones con sensor PlayerMaker. Pipeline: Limpieza → EDA → ANOVA factorial → GLMM bayesianos (PyMC). **Proyecto completado.**

## Stack

- Python 3, Jupyter Notebooks
- pandas, numpy, scipy, statsmodels
- matplotlib, seaborn
- PyMC (GLMM bayesianos), ArviZ (diagnósticos, LOO-CV)
- openpyxl, NetCDF4
- Entorno conda/mamba con PyMC stack

## Estructura de carpetas

```
01_LIMPIEZA/       → Pipeline de datos: raw → Matriz_V3.xlsx (6 notebooks)
02_EDA/            → Exploración univariante, interacciones, ICC, correlaciones (7 notebooks)
03_ANOVA/          → ANOVA factorial Tipo III
04_GLMM/           → 4 modelos bayesianos ajustados + fichas por categoría
04_GLMM_INTERPRETACIONES/ → Markdowns de interpretación (FUENTE DE VERDAD)
Datos/             → Excel de datos (V1→V3) + InferenceData .nc de cada modelo
```

## Variables clave

### Variables independientes (VI) — diseño táctico, todas binarias

- `Espacio`: "amplio" (ref) vs. "reducido"
- `Agrupación`: "grande" (ref) vs. "pequeño"
- `Polaridad`: "Polarizado" (ref) vs. "No polarizado"
- `Equilibrio`: "Equilibrio" (ref) vs. "Desequilibrio"

### Variables dependientes (VD) — normalizadas por minuto

| VD | Distribución | % Ceros | Modelo final |
|---|---|---|---|
| `Total Touches / min` | Gamma (log link) | 0% | M1 (efectos principales) |
| `Golpeos +15 m/s / min` | Hurdle Gamma | 29.5% | M1 (efectos principales) |
| `Distance Covered / min` | Gamma (log link) | 0% | M2 (+ GrupoEdad) |
| `HID / min` | Hurdle Gamma | 30.1% | M2 (+ GrupoEdad) |

### Variables de contexto

- `Player Id` — intercepto aleatorio en todos los GLMM
- `GrupoEdad` — 5 niveles: Infantil, Cadete, Juvenil, Senior M., Neskak
- `NombreCorrecto` — 12 equipos, anidado 1:1 dentro de GrupoEdad (NUNCA usar ambos en un modelo)

## Decisiones clave (no reabrir)

- **HID umbral**: 5.5 m/s (≈20 km/h). El umbral 4 m/s se descartó por redundancia con Distancia (r≈0.81).
- **Golpeos**: suma de Release Velocity Zones 4+5+6 (>15 m/s).
- **Outliers superiores**: NO eliminados (rendimiento real extremo, no errores de sensor).
- **SS Tipo III** en todo el proyecto (diseño desbalanceado: CV=137%, ratio máx/mín=91×).
- **Selección de modelos**: LOO-CV con peso de stacking. M4/M5 (con interacciones VI×GrupoEdad) descartados.
- **Jerarquía de modelos**: M0 (intercepto) → M1 (4 VI) → M2 (+GrupoEdad) → M3 (+interacciones 2-way) → M4 (+VI×GrupoEdad) → M5 (máxima).
- **Hallazgo central**: trade-off técnico-físico — condiciones que maximizan Toques son opuestas a las que maximizan HID.

## Convenciones de naming y estilo

- Responder siempre en **español**.
- Notebooks numerados con dos dígitos: `01_`, `02_`, etc. El prefijo indica orden de ejecución.
- Datos versionados: `Matriz_V1.xlsx` → `V2` → `V3`. No crear `V4` sin nueva fase documentada.
- Modelos guardados: `glmm_[vd]_final.nc` (ej: `glmm_toques_final.nc`).
- InferenceData: cargar con `az.from_netcdf()`, guardar con `idata.to_netcdf()`.
- Sufijo `_fichas_categoria`: notebooks de resumen por GrupoEdad, no modelos nuevos.
- Dataset final: `df = pd.read_excel("Datos/Matriz_V3.xlsx")`.
- Interpretaciones finales: referenciar `04_GLMM_INTERPRETACIONES/*.MD`, no duplicar su contenido.

## Estado actual

**Todo completado**: limpieza (6 NB), EDA (7 NB), ANOVA (1 NB), 4 GLMM bayesianos ajustados y guardados como `.nc`, interpretaciones validadas en `.md`, síntesis transversal en `05_SINTESIS_GENERAL.MD`.

## Gráficos

- **No guardar automáticamente** los gráficos como `.png` (ni `.pdf`, `.svg`, etc.). Solo mostrar con `plt.show()`.
- Si se necesita guardar un gráfico, el usuario lo pedirá explícitamente.

## NO hacer (evitar sugerir)

- Guardar gráficos como archivo (`.png`, `.pdf`, etc.) salvo que el usuario lo pida expresamente.
- Modificar `Datos/Matriz_V3.xlsx` — dataset final validado, rompe reproducibilidad.
- Eliminar outliers superiores — decisión documentada, rendimiento real.
- Usar SS Tipo I — diseño severamente desbalanceado.
- Incluir `NombreCorrecto` y `GrupoEdad` en el mismo modelo — colinealidad perfecta.
- Usar HID con umbral 4 m/s — redundante con Distancia.
- Re-ajustar GLMMs desde cero — cargar los `.nc` existentes salvo motivo justificado.
- Duplicar contenido de los `.md` de interpretaciones — son fuente de verdad.
- Añadir interacciones VI × GrupoEdad — LOO-CV descartó M4/M5.
- Modificar notebooks existentes ya completados — crear nuevos numerados si se necesita análisis adicional.
