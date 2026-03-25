# Interpretación de Coeficientes — Modelo Hurdle Gamma HID

## Contexto del Modelo

Este modelo Hurdle Gamma tiene **dos partes**:

1. **PARTE MU (μ)**: Intensidad condicional — ¿Cuánta HID/min se genera **dado que se produjo carrera de alta intensidad**?
   - Familia: Gamma con enlace log
   - Interpretación: exp(β) = ratio multiplicativo sobre la intensidad condicional

2. **PARTE PSI (ψ)**: Probabilidad binaria — ¿Se produce o no carrera de alta intensidad?
   - Familia: Bernoulli con enlace logit
   - Interpretación: exp(β) = odds ratio (OR)

**Condición de referencia** (intercepto):
- Formato del Juego: **LSG** (espacio amplio)
- Polaridad: **Polarizado**
- Equilibrio: **Equilibrio**
- GrupoEdad: **Cadete** (primera alfabéticamente)

---

## PARTE MU — Intensidad Condicional E[HID | HID > 0]

### Intercepto (Condición de Referencia)

**β = +0.994, exp(β) = 2.71 m/min, HDI [2.23, 3.24]**

**Interpretación**: En la condición de referencia (LSG, Polarizado, Equilibrio, Cadete), la intensidad condicional esperada de HID es de **2.71 m/min** (dado que se produjo HID). Este es el valor base sobre el cual actúan todos los demás efectos multiplicativamente.

---

### 1. EFECTOS PRINCIPALES — Variables Independientes de Diseño

#### Formato del Juego: SSG vs LSG
**β = -1.449, exp(β) = 0.237, HDI [0.179, 0.309] ✓ CREÍBLE**

**Interpretación**: El espacio reducido (SSG) **reduce la intensidad condicional de HID a un 23.7% de la referencia** (LSG). Es decir, cuando se logra producir HID en espacio reducido, la intensidad es **76.3% menor** que en espacio amplio.

**Implicación práctica**: El espacio reducido no solo disminuye la probabilidad de que se produzca HID (efecto PSI, ver abajo), sino que también **limita severamente la intensidad** de la carrera de alta intensidad cuando esta ocurre. Es el efecto más fuerte del modelo.

---

#### Polaridad: No Polarizado vs Polarizado
**β = -0.533, exp(β) = 0.597, HDI [0.414, 0.832] ✓ CREÍBLE**

**Interpretación**: El juego no polarizado **reduce la intensidad condicional de HID a un 59.7% de la referencia** (polarizado). Equivalente a una reducción del **40.3%**.

**Implicación práctica**: La polarización del juego (jugadores especializados por zona) favorece carreras de mayor intensidad cuando estas se producen. El juego no polarizado, al requerir mayor versatilidad posicional, puede limitar las aceleraciones máximas sostenidas.

---

#### Equilibrio: Desequilibrio vs Equilibrio
**β = +0.119, exp(β) = 1.142, HDI [0.815, 1.539] ✗ NO CREÍBLE**

**Interpretación**: El desequilibrio numérico aumenta la intensidad condicional en un 14.2%, pero el HDI incluye el cero → **efecto no creíble**.

**Implicación práctica**: El equilibrio numérico no tiene un efecto consistente sobre la intensidad de HID condicional. Esto sugiere que la ventaja/desventaja numérica no altera significativamente la magnitud de las carreras de alta intensidad una vez que se producen.

---

### 2. EFECTOS PRINCIPALES — GrupoEdad

#### Infantil vs Cadete
**β = -1.777, exp(β) = 0.182, HDI [0.081, 0.369] ✓ CREÍBLE**

**Interpretación**: Los jugadores infantiles generan **solo el 18.2% de la intensidad condicional de HID** de los cadetes. Reducción del **81.8%**.

**Implicación práctica**: Los jugadores más jóvenes tienen una capacidad aeróbica y velocidad máxima muy inferior. Incluso cuando logran actividades de alta intensidad, estas son de mucha menor magnitud que en categorías superiores.

---

#### Juvenil vs Cadete
**β = +1.551, exp(β) = 4.962, HDI [2.602, 8.936] ✓ CREÍBLE**

**Interpretación**: Los juveniles generan **4.96 veces más intensidad condicional de HID** que los cadetes. Aumento del **396%**.

**Implicación práctica**: Los juveniles tienen capacidad física desarrollada que les permite alcanzar intensidades de carrera mucho mayores. Este es el grupo con mayor capacidad de sprint sostenido.

---

#### Neskak vs Cadete
**β = -0.587, exp(β) = 0.584, HDI [0.302, 1.009] ✗ NO CREÍBLE**

**Interpretación**: El equipo femenino genera un 58.4% de la intensidad condicional de los cadetes (reducción del 41.6%), pero el HDI incluye el 1.0 → **efecto no creíble**.

**Implicación práctica**: No hay evidencia concluyente de diferencia en la intensidad condicional de HID entre Neskak y Cadete. Las diferencias observadas pueden deberse a variabilidad muestral.

---

#### Senior Masculino vs Cadete
**β = +1.465, exp(β) = 4.547, HDI [2.363, 7.938] ✓ CREÍBLE**

**Interpretación**: Los seniors masculinos generan **4.55 veces más intensidad condicional de HID** que los cadetes. Aumento del **355%**.

**Implicación práctica**: Los jugadores seniors, con madurez física completa, tienen capacidad de generar carreras de muy alta intensidad. Similar a los juveniles pero con mayor experiencia táctica.

---

### 3. INTERACCIONES — Formato del Juego × GrupoEdad

Estas interacciones evalúan si el efecto del espacio reducido (SSG) varía según la categoría de edad.

#### SSG × Infantil
**β = +0.137, exp(β) = 1.178, HDI [0.752, 1.827] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia de que el efecto negativo del SSG sea diferente en infantiles.

---

#### SSG × Juvenil
**β = -0.296, exp(β) = 0.753, HDI [0.542, 1.029] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia concluyente de moderación.

---

#### SSG × Neskak
**β = +1.284, exp(β) = 3.678, HDI [2.515, 5.259] ✓ CREÍBLE**

**Interpretación**: En Neskak, el efecto negativo del SSG **se atenúa**. El ratio es:
- Efecto base SSG: 0.237
- Con interacción: 0.237 × 3.678 = **0.872**

**Implicación práctica**: El equipo femenino **mantiene mejor la intensidad de HID en espacio reducido** que los demás grupos. Esto puede deberse a diferencias en estilo de juego, menor dependencia de carreras largas, o mayor adaptabilidad táctica al espacio reducido.

---

#### SSG × Senior Masculino
**β = +0.034, exp(β) = 1.050, HDI [0.734, 1.434] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia de moderación.

---

### 4. INTERACCIONES — Polaridad × GrupoEdad

Estas interacciones evalúan si el efecto del juego no polarizado varía según la categoría.

#### Polarizado × Infantil
**β = +0.418, exp(β) = 1.579, HDI [0.881, 2.574] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia concluyente de moderación.

---

#### Polarizado × Juvenil
**β = +0.590, exp(β) = 1.846, HDI [1.207, 2.748] ✓ CREÍBLE**

**Interpretación**: En juveniles, el efecto negativo del juego no polarizado **se atenúa**. El ratio combinado es:
- Efecto base No Polarizado: 0.597
- Con interacción: 0.597 × 1.846 = **1.102**

**Implicación práctica**: Los juveniles **no pierden intensidad de HID en juego no polarizado**; de hecho, incluso pueden aumentarla ligeramente. Esto sugiere que su versatilidad física les permite mantener intensidades altas independientemente de la especialización táctica.

---

#### Polarizado × Neskak
**β = +0.326, exp(β) = 1.422, HDI [0.867, 2.130] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia concluyente.

---

#### Polarizado × Senior Masculino
**β = +0.713, exp(β) = 2.090, HDI [1.337, 3.165] ✓ CREÍBLE**

**Interpretación**: En seniors, el efecto negativo del juego no polarizado **se atenúa significativamente**:
- Efecto base No Polarizado: 0.597
- Con interacción: 0.597 × 2.090 = **1.248**

**Implicación práctica**: Los seniors masculinos **aumentan la intensidad de HID en juego no polarizado** (24.8% más que polarizado). Esto puede reflejar mayor capacidad táctica para explotar espacios en transición cuando no hay especialización posicional.

---

### 5. INTERACCIONES — Equilibrio × GrupoEdad

#### Equilibrio × Infantil
**β = -1.748, exp(β) = 0.184, HDI [0.096, 0.337] ✓ CREÍBLE**

**Interpretación**: En infantiles, el efecto del desequilibrio es **muy negativo**:
- Efecto base Desequilibrio: 1.142
- Con interacción: 1.142 × 0.184 = **0.210**

**Implicación práctica**: Los jugadores infantiles **pierden el 79% de la intensidad de HID en situaciones de desequilibrio**. Esto sugiere que la ventaja/desventaja numérica afecta muy negativamente su capacidad de generar carreras intensas, posiblemente por menor capacidad de adaptación táctica.

---

#### Equilibrio × Juvenil
**β = +0.348, exp(β) = 1.446, HDI [0.960, 2.142] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia concluyente.

---

#### Equilibrio × Neskak
**β = -0.221, exp(β) = 0.820, HDI [0.534, 1.214] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia concluyente.

---

#### Equilibrio × Senior Masculino
**β = +0.286, exp(β) = 1.359, HDI [0.884, 1.941] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia concluyente.

---

---

## PARTE PSI — Probabilidad P(HID > 0)

### Intercepto (Condición de Referencia)

**logit = +2.342, P(Y>0) = 0.912, HDI [0.857, 0.945]**

**Interpretación**: En la condición de referencia (LSG, Polarizado, Equilibrio, Cadete), la **probabilidad de que se produzca HID es del 91.2%**. Esto indica que en espacio amplio con las demás condiciones favorables, casi todas las fases incluyen alguna actividad de alta intensidad.

---

### 1. EFECTOS PRINCIPALES — VI de Diseño

#### Formato del Juego: SSG vs LSG
**β = -1.787, OR = 0.172, HDI [0.104, 0.265] ✓ CREÍBLE**

**Interpretación**: El espacio reducido (SSG) **reduce los odds de que se produzca HID a un 17.2%** de los odds en LSG. Es decir, la probabilidad de HID cae dramáticamente.

**Cálculo aproximado de probabilidad**:
- LSG: P ≈ 0.912
- SSG: P ≈ 0.912 / (0.912 + (1-0.912)/0.172) ≈ **0.64** (reducción del 27%)

**Implicación práctica**: El espacio reducido actúa como un **"interruptor"** que apaga la probabilidad de carrera de alta intensidad. Este es el efecto dominante en PSI. Muchas fases en SSG no incluyen ninguna HID.

---

#### Polaridad: No Polarizado vs Polarizado
**β = -0.762, OR = 0.481, HDI [0.290, 0.741] ✓ CREÍBLE**

**Interpretación**: El juego no polarizado **reduce los odds de HID a un 48.1%** de los odds en polarizado.

**Implicación práctica**: La polarización del juego no solo aumenta la intensidad cuando ocurre HID (efecto MU), sino que también **aumenta la probabilidad de que se produzca** en primer lugar.

---

#### Equilibrio: Desequilibrio vs Equilibrio
**β = -0.837, OR = 0.445, HDI [0.273, 0.691] ✓ CREÍBLE**

**Interpretación**: El desequilibrio numérico **reduce los odds de HID a un 44.5%** de los odds en equilibrio.

**Implicación práctica**: A diferencia de la intensidad condicional (donde no había efecto), el equilibrio numérico **sí afecta la probabilidad de que se produzca HID**. Las situaciones de ventaja/desventaja disminuyen la ocurrencia de carreras de alta intensidad.

---

### 2. EFECTOS PRINCIPALES — GrupoEdad

#### Infantil vs Cadete
**β = -0.990, OR = 0.419, HDI [0.149, 0.994] ✓ CREÍBLE**

**Interpretación**: Los infantiles tienen **odds de HID del 41.9%** respecto a cadetes. La probabilidad de que se produzca HID es mucho menor.

**Implicación práctica**: En jugadores infantiles, muchas fases **no incluyen ninguna actividad de alta intensidad** (probabilidad ≈ 70% vs 91% en cadetes). Esto refuerza que su capacidad física es muy limitada.

---

#### Juvenil vs Cadete
**β = +2.317, OR = 11.32, HDI [4.20, 26.20] ✓ CREÍBLE**

**Interpretación**: Los juveniles tienen **odds de HID 11.32 veces mayores** que los cadetes.

**Cálculo de probabilidad**:
- Juveniles: P ≈ **0.99** (casi seguro que hay HID)

**Implicación práctica**: En juveniles, prácticamente todas las fases incluyen alguna actividad de alta intensidad. Su capacidad física está completamente desarrollada.

---

#### Neskak vs Cadete
**β = -0.075, OR = 1.009, HDI [0.415, 2.044] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia de diferencia en la probabilidad de HID entre Neskak y Cadete.

---

#### Senior Masculino vs Cadete
**β = +1.885, OR = 7.39, HDI [2.60, 17.12] ✓ CREÍBLE**

**Interpretación**: Los seniors tienen **odds de HID 7.39 veces mayores** que los cadetes.

**Cálculo de probabilidad**:
- Seniors: P ≈ **0.98**

**Implicación práctica**: Los seniors, al igual que los juveniles, tienen altísima probabilidad de generar HID en cualquier fase.

---

### 3. INTERACCIONES — Formato × GrupoEdad (PSI)

#### SSG × Infantil
**β = -0.889, OR = 0.441, HDI [0.201, 0.872] ✓ CREÍBLE**

**Interpretación**: En infantiles, el efecto negativo del SSG **se agrava**:
- Efecto base SSG: OR = 0.172
- Con interacción: 0.172 × 0.441 = **0.076** (odds muy bajos)

**Implicación práctica**: Los jugadores infantiles en espacio reducido tienen **probabilidad extremadamente baja de producir HID** (≈ 30-40%). El espacio reducido prácticamente elimina la carrera de alta intensidad en esta categoría.

---

#### SSG × Juvenil
**β = -0.028, OR = 1.031, HDI [0.480, 1.859] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia de moderación.

---

#### SSG × Neskak
**β = -0.666, OR = 0.542, HDI [0.262, 0.934] ✓ CREÍBLE**

**Interpretación**: En Neskak, el efecto negativo del SSG **se agrava ligeramente**:
- Efecto base SSG: OR = 0.172
- Con interacción: 0.172 × 0.542 = **0.093**

**Implicación práctica**: Aunque Neskak mantiene mejor la intensidad en SSG (MU), la **probabilidad de que se produzca HID sigue siendo muy baja** en espacio reducido.

---

#### SSG × Senior Masculino
**β = -0.050, OR = 1.023, HDI [0.466, 2.113] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia de moderación.

---

### 4. INTERACCIONES — Polaridad × GrupoEdad (PSI)

#### Polarizado × Infantil
**β = +1.173, OR = 3.510, HDI [1.459, 7.168] ✓ CREÍBLE**

**Interpretación**: En infantiles, el efecto negativo del juego no polarizado **se atenúa**:
- Efecto base No Polarizado: OR = 0.481
- Con interacción: 0.481 × 3.510 = **1.689**

**Implicación práctica**: Los infantiles **tienen MAYOR probabilidad de HID en juego no polarizado** que en polarizado. Esto puede deberse a que la versatilidad posicional les obliga a moverse más, compensando su menor capacidad física individual.

---

#### Polarizado × Juvenil
**β = +0.594, OR = 1.944, HDI [0.880, 3.817] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia concluyente.

---

#### Polarizado × Neskak
**β = +0.204, OR = 1.307, HDI [0.598, 2.423] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia concluyente.

---

#### Polarizado × Senior Masculino
**β = -1.176, OR = 0.332, HDI [0.144, 0.644] ✓ CREÍBLE**

**Interpretación**: En seniors, el efecto negativo del juego no polarizado **se agrava**:
- Efecto base No Polarizado: OR = 0.481
- Con interacción: 0.481 × 0.332 = **0.160**

**Implicación práctica**: Los seniors masculinos **tienen mucha menor probabilidad de HID en juego no polarizado** (aunque cuando ocurre, la intensidad es mayor — ver MU). Esto puede reflejar un estilo de juego más táctico que depende de la especialización.

---

### 5. INTERACCIONES — Equilibrio × GrupoEdad (PSI)

#### Equilibrio × Infantil
**β = -1.180, OR = 0.338, HDI [0.127, 0.701] ✓ CREÍBLE**

**Interpretación**: En infantiles, el efecto negativo del desequilibrio **se agrava**:
- Efecto base Desequilibrio: OR = 0.445
- Con interacción: 0.445 × 0.338 = **0.150**

**Implicación práctica**: Los infantiles en situaciones de desequilibrio tienen **muy baja probabilidad de producir HID**. Esto complementa el efecto MU (menor intensidad) y confirma que el desequilibrio es muy perjudicial para esta categoría.

---

#### Equilibrio × Juvenil
**β = +0.764, OR = 2.320, HDI [0.973, 4.562] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia concluyente (HDI roza el 1.0).

---

#### Equilibrio × Neskak
**β = +0.415, OR = 1.615, HDI [0.780, 3.082] ✗ NO CREÍBLE**

**Interpretación**: No hay evidencia concluyente.

---

#### Equilibrio × Senior Masculino
**β = +1.826, OR = 6.697, HDI [2.829, 13.166] ✓ CREÍBLE**

**Interpretación**: En seniors, el efecto negativo del desequilibrio **se invierte completamente**:
- Efecto base Desequilibrio: OR = 0.445
- Con interacción: 0.445 × 6.697 = **2.980**

**Implicación práctica**: Los seniors masculinos **tienen MAYOR probabilidad de HID en situaciones de desequilibrio** (casi 3 veces más odds). Esto sugiere que explotan tácticamente las ventajas/desventajas numéricas para generar oportunidades de carrera de alta intensidad.

---

---

## RESUMEN EJECUTIVO

### Variables con Efectos Creíbles más Fuertes

#### PARTE MU (Intensidad Condicional):
1. **Formato del Juego**: SSG reduce intensidad a 23.7% → **efecto dominante**
2. **GrupoEdad Juvenil**: 4.96× más intensidad que Cadete
3. **GrupoEdad Senior**: 4.55× más intensidad que Cadete
4. **GrupoEdad Infantil**: Solo 18.2% de intensidad vs Cadete
5. **Polaridad No Polarizada**: Reduce intensidad a 59.7%

**Interacciones MU destacadas**:
- SSG × Neskak: Atenúa efecto negativo del SSG (3.68×)
- Polarizado × Senior: Invierte efecto de no polarizado (2.09×)
- Equilibrio × Infantil: Agrava efecto negativo del desequilibrio (0.18×)

#### PARTE PSI (Probabilidad de Ocurrencia):
1. **Formato del Juego**: SSG reduce odds a 17.2% → **efecto interruptor**
2. **GrupoEdad Juvenil**: 11.32× más odds de HID que Cadete
3. **GrupoEdad Senior**: 7.39× más odds de HID
4. **Equilibrio**: Desequilibrio reduce odds a 44.5%
5. **Polaridad**: No Polarizado reduce odds a 48.1%

**Interacciones PSI destacadas**:
- Equilibrio × Senior: Invierte efecto negativo del desequilibrio (6.70×)
- Polarizado × Infantil: Invierte efecto de no polarizado (3.51×)
- SSG × Infantil: Agrava efecto negativo del SSG (0.44×)
- Polarizado × Senior: Agrava efecto negativo del no polarizado (0.33×)

---

### Mensajes Clave

1. **El espacio es el factor dominante**: SSG reduce tanto la probabilidad (83% menos odds) como la intensidad (76% menos) de HID. Es el "interruptor maestro".

2. **La edad modula TODO**: Las interacciones con GrupoEdad son las más numerosas y fuertes. Los efectos del diseño táctico varían drásticamente según la categoría.

3. **Infantiles son muy vulnerables**: Sufren los efectos negativos más severos de SSG, desequilibrio y polarización. El diseño táctico debe ser especialmente cuidadoso en esta categoría.

4. **Juveniles y Seniors explotan mejor las condiciones**: Mantienen alta intensidad y probabilidad de HID incluso en condiciones subóptimas (no polarizado, desequilibrio).

5. **Neskak tiene perfil único**: Mantiene mejor la intensidad en SSG que otros grupos, pero la probabilidad de ocurrencia sigue siendo muy baja.

6. **El equilibrio numérico afecta de forma opuesta según edad**: Perjudica a infantiles pero beneficia a seniors (especialmente en PSI).
