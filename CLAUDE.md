# TFG — Estudio del Impacto Técnico-Táctico en SSG/LSG (Real Sociedad)

Trabajo de Fin de Grado en Ciencia de Datos. Analiza el impacto de las variables de diseño táctico (Formato, Polaridad, Equilibrio) sobre métricas de rendimiento físico-técnico de jugadores de fútbol de la cantera de la Real Sociedad, registradas con sensores PlayerMaker.

## Contexto del proyecto

- **Sensor**: PlayerMaker (dispositivo de tobillo)
- **Diseño experimental**: Factorial 2×2×2 no balanceado
- **N total**: ~3.179 observaciones de ~253 jugadores únicos

### Variables independientes (VI)
| Variable | Niveles |
|---|---|
| `Formato_del_Juego` | 2 niveles (p.ej. SSG vs LSG) |
| `Polaridad` | 2 niveles (con/sin dirección) |
| `Equilibrio` | 2 niveles (equilibrado/desequilibrado numéricamente) |

### Variables dependientes (VD)
| Variable en dataset | Etiqueta corta | Nota |
|---|---|---|
| `Total Touches / min` | Toques/min | Continua positiva |
| `Golpeos +15 m/s / min` | Golpeos/min | ~30% ceros (zero-inflated) |
| `Distance Covered (m) / min` | Distancia/min | Continua positiva |
| `High Intensity Distance (20 km/h) / min` | HID/min | ~30% ceros (zero-inflated) |

### Variable moderadora
- `GrupoEdad`: 5 categorías (Infantil, Cadete, Juvenil, Neskak, Senior_Masculino)

---

## Estructura de carpetas

```
ESTUDIO_IMPACTO_TECNICO_SSG_LSG/
├── Datos/                  # Matrices de datos y modelos GLMM guardados
│   ├── PlayerMaker_Matrix.xlsx   # Datos crudos originales
│   ├── Matriz_V1/V2/V3.xlsx      # Versiones limpias sucesivas
│   └── glmm_*.nc                 # Modelos PyMC serializados (excluidos de git)
├── 01_LIMPIEZA/            # Limpieza, selección de VDs y creación de métricas
├── 02_EDA/                 # Análisis exploratorio descriptivo y bivariante
├── 03_ANOVA/               # ANOVA factorial (puente metodológico hacia GLMM)
├── 04_GLMM/                # Modelos GLMM bayesianos con PyMC/Bambi
└── Graficos/               # Gráficos exportados (PNG) y guías para el club
```

### Notebooks por fase (orden de ejecución)

**01_LIMPIEZA**
1. `01_limpieza_previa.ipynb` — Lectura de PlayerMaker_Matrix y limpieza inicial
2. `02_seleccion_vd.ipynb` — Selección y justificación de las 4 VDs
3. `03_creacion_metricas.ipynb` — Cálculo de métricas por minuto (normalización)
4. `04_limpieza_metricas.ipynb` — Filtros de outliers y validación
5. `05_diccionario_metricas.ipynb` — Documentación del diccionario de variables
6. `06_estructura_diseno.ipynb` — Verificación del diseño factorial y balanceo

**02_EDA**
1. `01_descriptivo_univariante.ipynb` — Distribuciones, skewness, kurtosis por VD
2. `02_variabilidad_intra_inter.ipynb` — ICC jugador, variabilidad intra/inter-sujeto
3. `03_efecto_base_vi.ipynb` — Efectos marginales brutos de cada VI sobre VDs
4. `04_interacciones_2_way_VI.ipynb` — Interacciones de 2 vías entre VIs
5. `05_grupoedad_moderador.ipynb` — Rol moderador de GrupoEdad
6. `06_perfil_factorial_completo.ipynb` — Perfil completo del diseño 2×2×2
7. `07_correlaciones_vd.ipynb` — Correlaciones entre VDs

**03_ANOVA**
1. `01_anova_factorial.ipynb` — ANOVA factorial Type III + alternativas robustas

**04_GLMM**
1. `01_glmm_toques.ipynb` — GLMM Gamma/log para Toques/min
2. `01b_glmm_toques_fichas_categoria.ipynb` — Fichas por GrupoEdad (Toques)
3. `02_glmm_golpeos.ipynb` — GLMM Tweedie/hurdle para Golpeos/min
4. `02b_glmm_golpeos_fichas_categoria.ipynb`
5. `03_glmm_distancia.ipynb` — GLMM Gamma/log para Distancia/min
6. `03b_glmm_distancia_fichas_categoria.ipynb`
7. `04_glmm_HID.ipynb` — GLMM Tweedie/hurdle para HID/min
8. `04b_glmm_HID_fichas_categoria.ipynb`
9. `05_graficos.ipynb` — Gráficos de síntesis comparativa entre VDs

---

## Entorno de desarrollo

- **Python**: 3.13.0
- **Entorno virtual**: `.venv/` (activar con `source .venv/bin/activate`)
- **Kernel Jupyter**: `.venv`

### Librerías clave
| Librería | Uso principal |
|---|---|
| `pandas`, `numpy` | Manipulación de datos |
| `scipy`, `statsmodels` | Tests estadísticos clásicos, ANOVA |
| `pingouin` | Welch ANOVA, effect sizes, ICC |
| `pymc`, `bambi` | GLMM bayesianos |
| `arviz` | Diagnósticos y visualización bayesiana |
| `matplotlib`, `seaborn` | Visualización |
| `openpyxl` | Lectura/escritura de Excel |

---

## Datos

- **Fuente activa**: `Datos/Matriz_V3.xlsx` (versión final, N=3.179 filas × 18 columnas)
- **Sin missings** en las columnas VD y VI de interés
- Los archivos `.nc` (modelos PyMC serializados, >500 MB c/u) están excluidos de git via `.gitignore`
- No modificar `Datos/PlayerMaker_Matrix.xlsx` (datos crudos originales)

---

## Decisiones metodológicas clave

### Por qué Type III SS en el ANOVA
El diseño es no balanceado (ratio 26:1 entre celdas). Type III evalúa cada efecto controlando por todos los demás, independientemente del orden de entrada. Se usa Sum/effect coding en statsmodels para garantizar compatibilidad.

### Por qué no se incluye GrupoEdad en el ANOVA factorial completo
Un factorial 2×2×2×5 genera 40 celdas teóricas, de las cuales 8 están vacías y 14 tienen N<50. El ratio de desbalance sube a 37:1. Se optó por **estratificación**: ANOVA global 2×2×2 + 5 ANOVAs separados por GrupoEdad.

### Por qué GLMM es el análisis definitivo (no el ANOVA)
El ANOVA tiene 5 violaciones documentadas que el GLMM resuelve:
1. **Pseudoreplicación** (ICC=0.08-0.23): GLMM incluye `(1|Player Id)` como efecto aleatorio
2. **No normalidad**: GLMM usa familias Gamma/Tweedie; no requiere normalidad de residuos
3. **Heteroscedasticidad**: el link log modela la relación media-varianza automáticamente
4. **Zero-inflation** (~30% en Golpeos y HID): modelos hurdle o Tweedie
5. **Desbalance extremo**: estimación ML/REML robusta al desbalance

### Umbrales de tamaño de efecto (Cohen 1988)
| η²p / ω² | Magnitud |
|---|---|
| < 0.01 | Negligible |
| 0.01 – 0.06 | Pequeño |
| 0.06 – 0.14 | Mediano |
| > 0.14 | Grande |

En ciencias del deporte (fútbol), efectos de ω²=0.01-0.05 son **sustantivamente relevantes** dado el contexto multifactorial (técnica individual, fatiga, motivación, oponente).

---

## Convenciones de código

- **Random seed**: siempre `42` para reproducibilidad
- **Permutaciones**: `N_PERM = 5000` en todos los tests de permutación
- **Alpha**: `0.05` estándar; reportar siempre el p-valor exacto además de las estrellas
- **Estrellas de significación**: `*** p<0.001`, `** p<0.01`, `* p<0.05`, `ns p≥0.05`
- Las funciones auxiliares reutilizables se definen al inicio de cada notebook (no existe módulo central compartido)
- Los gráficos exportados van a `Graficos/` en PNG; los de síntesis del GLMM en `04_GLMM/imagenes_exportadas/`

### Nombres de variables en código
```python
vd_cols   = ['Total Touches / min', 'Golpeos +15 m/s / min',
             'Distance Covered (m) / min', 'High Intensity Distance (20 km/h) / min']
vi_cols   = ['Formato_del_Juego', 'Polaridad', 'Equilibrio']
vd_zeros  = ['Golpeos +15 m/s / min', 'High Intensity Distance (20 km/h) / min']
```

---

## Lo que NO está en este repo (excluidos por .gitignore)

- `Datos/*.nc` — modelos PyMC serializados (>500 MB cada uno)
- `.ipynb_checkpoints/` — checkpoints de Jupyter
- `__pycache__/`, `*.pyc` — caché de Python
- `.DS_Store` — metadatos macOS
