## Resultados del GLMM para HID Covered (m) / min

### 1. Justificación del modelo Hurdle Gamma

La variable HID Covered (m) / min —distancia recorrida a alta intensidad (velocidad superior a 20km/h), normalizada por el tiempo de participación del jugador— presenta una estructura distribucional que hace inadecuado el uso de modelos lineales convencionales o de familias de distribuciones simples. El primer rasgo determinante es la presencia de un **30,09 % de ceros estructurales**: en casi un tercio de las observaciones, el jugador no alcanzó en ningún momento el umbral de velocidad de 20 km/h durante toda la tarea de entrenamiento. Estos ceros no representan un exceso anómalo ni un problema de medición, sino un fenómeno físico real y esperado: determinados diseños de tarea —particularmente aquellos con espacio reducido y alta densidad de jugadores— no generan las condiciones cinemáticas necesarias para que se produzcan acciones locomotoras de alta intensidad. Esta distinción es crucial: un modelo de tipo Zero-Inflated asumiría que los ceros provienen de dos mecanismos mezclados (uno siempre produce ceros, otro produce valores positivos y a veces ceros), mientras que el modelo **Hurdle** asume que los ceros y los valores positivos proceden de procesos generadores completamente separados, lo cual se ajusta con mayor fidelidad a la realidad fisiológica del entrenamiento.

El segundo rasgo que justifica esta familia distribucional es la extraordinaria heterogeneidad de los valores positivos. Con un coeficiente de variación del **158,8 %**, HID/min es la más heterogénea de las cuatro variables dependientes del TFG, lo que indica que la distribución tiene una cola derecha muy larga y una varianza fuertemente dependiente de la media —patrón característico de la distribución Gamma. Esta compatibilidad entre la relación media-varianza observada en los valores positivos y la distribución Gamma fue verificada como condición necesaria antes de su adopción en el modelo.

El **GLMM Bayesiano Hurdle Gamma** descompone formalmente la respuesta en dos procesos generadores independientes. La **parte ψ (psi)**, modelada con un enlace logit, estima P(HID > 0): la probabilidad de que el jugador alcance al menos una vez velocidades superiores a 20 km/h durante la fase. La **parte μ (mu)**, modelada con distribución Gamma y enlace logarítmico, estima E[HID/min | HID > 0]: la intensidad condicional media de la carrera de alta intensidad, condicionada a que esta efectivamente se produzca. La esperanza marginal de la variable se recupera como el producto E[HID/min] = P(HID > 0) × E[HID/min | HID > 0]. Esta arquitectura dual permite detectar efectos que actúan únicamente como "interruptores" —modificando la probabilidad de que haya sprint— de aquellos que actúan como "moduladores" —modificando la intensidad condicional cuando el sprint ya se produce—, una distinción de alto valor interpretativo para el análisis del entrenamiento.

---

### 2. Cadena de modelos y selección por LOO-CV

Con el fin de identificar el nivel óptimo de complejidad, se ajustaron cinco modelos con complejidad creciente. El **modelo M0** sirve como línea base nula, con únicamente el intercepto y el efecto aleatorio por jugador en ambas partes del hurdle. El **modelo M1** añade los efectos principales de las cuatro variables de diseño táctico (Espacio, Agrupación, Polaridad y Equilibrio). El **modelo M2** extiende M1 incorporando el grupo de edad (GrupoEdad, con cinco niveles) como efecto fijo en ambas partes del hurdle. El **modelo M3** añade a M2 tres interacciones teóricamente justificadas por análisis previos de varianza: Espacio × GrupoEdad (la de mayor efecto en el dataset, η²p = 0,0455), Agrupación × GrupoEdad, y Espacio × Agrupación (η²p = 0,0145). Finalmente, el **modelo M4** es el modelo maximal, incluyendo todas las interacciones de segundo orden posibles, con el consecuente riesgo de sobreajuste.

La selección entre modelos se realizó mediante validación cruzada dejando una observación fuera (LOO-CV), que estima la densidad predictiva logarítmica esperada fuera de muestra (ELPD). Los resultados son los siguientes:

| Modelo | ELPD LOO | ΔELPD (vs M2) | SE(ΔELPD) | Peso stacking |
| --- | --- | --- | --- | --- |
| **M2** | **-7.503,40** | **0,00** | **—** | **0,915** |
| M3 | -7.694,85 | -191,45 | 19,08 | 0,051 |
| M1 | -7.763,68 | -260,28 | 15,33 | 0,000 |
| M0 | -8.161,99 | -658,59 | 36,51 | 0,000 |
| M4 | -30.445,02 | -22.941,62 | 708,05 | 0,034 |

El **modelo M2 es seleccionado con contundencia**, acaparando un peso de stacking del 91,5 % —lo que significa que la LOO-CV asigna prácticamente toda la masa de capacidad predictiva a este modelo frente al resto. La diferencia entre M2 y M3 (ΔELPD = -191,45, SE = 19,08) es estadísticamente relevante con holgura (razón |ΔELPD|/SE ≈ 10, muy superior al umbral convencional de 2), lo que indica que las tres interacciones incluidas en M3 no solo no mejoran la predicción fuera de muestra, sino que la deterioran de forma significativa. **Ello sugiere que, aunque la interacción Espacio × GrupoEdad es la más fuerte de todo el dataset según el análisis de varianza previo (η²p = 0,0455), añadir este nivel de complejidad al modelo hurdle no produce ganancias predictivas netas, probablemente porque la información disponible en algunas celdas de la parte binaria es insuficiente para estimarla con precisión.** El modelo M4 colapsa completamente, con un ΔELPD de -22.941,62 (SE = 708,05): la proliferación de interacciones en ambas partes del hurdle genera un sobreajuste catastrófico, y su peso de stacking residual del 3,4 % es testimonial.

Un hallazgo relevante en comparación con otras variables dependientes del TFG es que aquí **M2 supera a M1**, mientras que para otras VD el modelo óptimo fue M1. La clave interpretativa es que la capacidad de carrera a alta intensidad presenta un gradiente madurativo extraordinariamente pronunciado entre las categorías de edad (de Infantil a Senior Masculino), hasta el punto de que ignorar el grupo de edad deteriora el ajuste de manera más acusada que en variables más homogéneas entre categorías —como el volumen de toques o la distancia total—. Para HID, la información que aporta GrupoEdad es no redundante con el diseño táctico y contribuye de forma neta a la capacidad predictiva del modelo.

---

### 3. Diagnósticos del modelo final (M2)

El modelo final M2 muestra un ajuste técnico excelente en todos los indicadores de diagnóstico MCMC. El **R-hat máximo fue de 1,0031**, muy por debajo del umbral crítico de 1,01, lo que confirma la convergencia de las cuatro cadenas de muestreo hacia la distribución posterior conjunta. El **tamaño efectivo de muestra mínimo (ESS) fue de 1.804**, ampliamente superior al mínimo recomendado de 400 para inferencia fiable, y la inspección visual de los trace plots confirmó una mezcla adecuada de las cuatro cadenas MCMC sin patrones de autocorrelación problemáticos. En cuanto a la bondad de ajuste, el **R² bayesiano alcanzó 0,4464** (SD = 0,0147), lo que indica que el modelo explica el 44,6 % de la varianza total de HID/min incluyendo tanto los efectos fijos como el efecto aleatorio del jugador. El Posterior Predictive Check (PPC) confirmó que la distribución predictiva posterior reproduce de forma adecuada tanto la masa puntual en cero —que representa el 30 % de los datos— como la cola derecha característica de la distribución Gamma, validando la idoneidad de la familia distribucional elegida.

---

### 4. Descomposición de varianza

La descomposición de varianza en modelos mixtos permite cuantificar qué proporción de la variabilidad total de la variable respuesta es atribuible a cada fuente de variación: los efectos fijos del diseño táctico, el grupo de edad, las diferencias entre jugadores y el residual propio de la distribución. Siguiendo el método de Nakagawa et al. (2017), la descomposición se realiza en la escala log-latente (escala del predictor lineal), lo que garantiza que las componentes sean aditivas y expresables como proporciones del total. Es importante subrayar que **esta descomposición aplica exclusivamente a la parte continua (μ) del hurdle**, es decir, describe la varianza en la intensidad condicional E[HID/min | HID > 0] y no en la probabilidad de ocurrencia de sprint.

Comparar los resultados de M1 y M2 es altamente informativo. En **M1** (cuatro variables de diseño sin GrupoEdad), la varianza se distribuye de la siguiente manera:

| Componente | σ² | % | HDI 95 % |
| --- | --- | --- | --- |
| Diseño táctico (4 VI) | 0,3524 | 16,0 % | [14,2 %, 18,0 %] |
| Jugador (intercepto aleatorio) | 0,4338 | 19,6 % | [15,7 %, 23,7 %] |
| Residual (Gamma) | 1,4182 | 64,4 % | [60,4 %, 68,4 %] |
| **Total** | **2,2044** |  |  |

El R² marginal (solo diseño táctico) es del **16,0 %**, y el R² condicional (diseño táctico + efecto del jugador) alcanza el **35,6 %**. Al incorporar GrupoEdad en **M2**, la descomposición se transforma notablemente:

| Componente | σ² | % | HDI 95 % |
| --- | --- | --- | --- |
| Diseño táctico (4 VI) | 0,3443 | 16,0 % | [14,1 %, 17,7 %] |
| GrupoEdad | 0,2053 | **9,5 %** | [7,1 %, 12,2 %] |
| Jugador (intercepto aleatorio) | 0,1668 | **7,7 %** | [5,2 %, 10,1 %] |
| Residual (Gamma) | 1,4401 | 66,8 % | [63,0 %, 70,4 %] |
| **Total** | **2,1564** |  |  |

Los R² de M2 son: R² marginal solo por diseño táctico **16,0 %**, R² marginal conjunto (tarea + GrupoEdad) **25,5 %**, y R² condicional (todos los fijos + jugador) **33,2 %**.

Tres conclusiones emergen de esta comparación. En primer lugar, el diseño táctico explica exactamente el mismo porcentaje de varianza en M1 y en M2 (16,0 %), lo que confirma que GrupoEdad y el diseño táctico son fuentes de variación ortogonales: la categoría de edad no compite con el diseño táctico sino que captura varianza de una fuente completamente distinta. En segundo lugar, y este es **el gran hallazgo de la descomposición**: al incluir GrupoEdad, el componente de varianza atribuible al jugador cae del 19,6 % (M1) al 7,7 % (M2). Esto significa que gran parte de lo que en M1 se interpretaba como "diferencias entre jugadores" era en realidad "diferencias entre categorías de edad" que el modelo sin GrupoEdad no podía separar y atribuía erróneamente a la heterogeneidad individual. Este fenómeno es el más pronunciado de todo el TFG para HID/min —con 9,5 % de varianza explicada por GrupoEdad—, lo que tiene pleno sentido fisiológico dado el marcado gradiente madurativo en la capacidad de carrera a alta intensidad. En tercer lugar, el **residual Gamma es el componente dominante en ambos modelos** (~64-67 %), reflejo de la altísima heterogeneidad intrínseca de HID (CV = 158,8 %): incluso el mejor modelo deja sin explicar una mayoría de la variabilidad, lo que es esperable en una variable tan sensible al contexto momentáneo de cada acción dentro de cada fase.

---

### 5. Coeficientes del modelo final (M2)

Los coeficientes del modelo hurdle Hurdle M2 se interpretan en las dos partes del modelo de forma separada. La categoría de referencia para todos los contrastes es: espacio amplio, agrupación grande, polaridad Polarizado, equilibrio Equilibrio y grupo de edad Cadete.

**Parte μ — Intensidad condicional.** El intercepto en escala logarítmica (β = +0,846) corresponde a una intensidad condicional esperada de exp(0,846) = **2,338 m/min** (intervalo de mayor densidad al 95 %, en adelante HDI: [1,984; 2,755]) para un jugador Cadete en las condiciones de referencia. Los coeficientes de las variables de diseño táctico y de GrupoEdad se interpretan como factores multiplicativos sobre esta intensidad de referencia:

| Variable | Contraste | β | exp(β) | HDI 95 % | Creíble |
| --- | --- | --- | --- | --- | --- |
| Espacio | Reducido vs. Amplio | -1,232 | **0,292** | [0,267; 0,319] | ✓ |
| Agrupación | Pequeño vs. Grande | +0,202 | **1,225** | [1,131; 1,326] | ✓ |
| Polaridad | No polarizado vs. Polarizado | -0,096 | 0,910 | [0,813; 1,013] | ✗ |
| Equilibrio | Desequilibrio vs. Equilibrio | +0,006 | 1,008 | [0,901; 1,124] | ✗ |
| GrupoEdad | Juvenil vs. Cadete | +0,568 | **1,765** | [1,442; 2,186] | ✓ |

El efecto del **espacio reducido** es el más potente de todo el análisis: con exp(β) = 0,292, la intensidad condicional de HID en espacio reducido es apenas el **29,2 % de la correspondiente al espacio amplio**, lo que equivale a una reducción del 70,8 %. El HDI de este coeficiente [0,267; 0,319] está completamente alejado de 1, confirmando la credibilidad del efecto con alta confianza. La **agrupación pequeña** genera un incremento del 22,5 % sobre la intensidad condicional respecto a la agrupación grande (exp(β) = 1,225, HDI: [1,131; 1,326]): en grupos pequeños, la mayor exigencia individual en términos de participación en la tarea se traduce en acciones de mayor velocidad cuando estas se producen. Por el contrario, ni la **polaridad** ni el **equilibrio** tienen efectos creíbles sobre la intensidad condicional: sus HDI incluyen el valor 1 en escala multiplicativa ([0,813; 1,013] y [0,901; 1,124] respectivamente), por lo que no puede afirmarse que modifiquen la intensidad de la carrera de alta intensidad cuando esta ya se produce. Respecto a GrupoEdad, el contraste Juvenil vs. Cadete (exp(β) = 1,765, HDI: [1,442; 2,186]) indica que los jugadores de categoría Juvenil generan un **76,5 % más de intensidad condicional** que los Cadete bajo idénticas condiciones de diseño, reflejo cuantificado de la mayor madurez locomotora de los jugadores más veteranos.

**Parte ψ — Probabilidad de sprint.** En la parte binaria del modelo, el espacio amplio incrementa de forma creíble la probabilidad de que el jugador supere el umbral de 20km/h, con un odds ratio claramente mayor que 1 cuyo HDI no incluye ese valor. La agrupación muestra un efecto opuesto al observado en la parte μ: **los grupos pequeños reducen la probabilidad de que haya sprint** (Δψ = -0,109, creíble), aunque cuando el sprint se produce, es más intenso. Esta aparente paradoja tiene una explicación funcional: en grupos pequeños, todos los jugadores participan de forma más activa y continua en la tarea, lo que puede reducir el número de situaciones de transición o carrera en profundidad que típicamente generan episodios de alta velocidad. Polaridad y equilibrio, que no eran moduladores creíbles de la intensidad condicional, sí actúan como **interruptores creíbles en la parte ψ**: el juego polarizado aumenta la probabilidad de que se produzca sprint (Δψ = +0,074, creíble), y el equilibrio muestra un efecto creíble similar en dirección (Δψ = +0,073), aunque de mayor ambigüedad en cuanto a su magnitud práctica.

La **disociación interruptor/modulador** es el resultado conceptualmente más rico del análisis. El **espacio** actúa en ambos planos simultáneamente: el espacio reducido reduce tanto la probabilidad de que haya sprint como su intensidad cuando se produce. La **agrupación** también actúa en ambos planos, pero con dirección opuesta: grupos pequeños tienen menor probabilidad de HID pero mayor intensidad condicional. La **polaridad** actúa principalmente como interruptor (modifica la probabilidad) pero no como modulador (no modifica la intensidad condicional cuando ya hay sprint). El **equilibrio** es un interruptor débil —su efecto sobre ψ es marginalmente creíble— y no tiene efecto detectable sobre la intensidad condicional ni sobre E[HID/min].

---

### 6. Predicciones del modelo — 16 combinaciones factoriales

El modelo hurdle permite calcular predicciones de la esperanza marginal de HID/min para cada una de las 16 combinaciones del diseño factorial 2⁴ (dos niveles en cuatro variables de diseño), mediante la fórmula E[HID/min] = P(HID > 0) × E[HID/min | HID > 0]. Estas predicciones son poblacionales —no incluyen el efecto aleatorio del jugador—, por lo que representan la media esperada para un jugador "promedio" de referencia (categoría Cadete) bajo cada combinación de diseño. La siguiente tabla presenta los 16 escenarios ordenados de mayor a menor demanda:

| # | Espacio | Agrupación | Polaridad | Equilibrio | P(HID>0) | HID/min|Y>0 | **E[HID/min]** | HID en 10 min |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Amplio | Pequeño | Polarizado | Equilibrio | 0,837 | 2,852 | **2,386** | 23,9 m |
| 2 | Amplio | Pequeño | Polarizado | Desequilibrio | 0,778 | 2,870 | 2,234 | 22,3 m |
| 3 | Amplio | Grande | Polarizado | Equilibrio | 0,900 | 2,330 | 2,096 | 21,0 m |
| 4 | Amplio | Grande | Polarizado | Desequilibrio | 0,860 | 2,344 | 2,016 | 20,2 m |
| 5 | Amplio | Pequeño | No polarizado | Equilibrio | 0,777 | 2,592 | 2,014 | 20,1 m |
| 6 | Amplio | Pequeño | No polarizado | Desequilibrio | 0,705 | 2,608 | 1,839 | 18,4 m |
| 7 | Amplio | Grande | No polarizado | Equilibrio | 0,860 | 2,117 | 1,819 | 18,2 m |
| 8 | Amplio | Grande | No polarizado | Desequilibrio | 0,807 | 2,130 | 1,720 | 17,2 m |
| 9 | Reducido | Grande | Polarizado | Equilibrio | 0,650 | 0,679 | 0,442 | 4,4 m |
| 10 | Reducido | Pequeño | Polarizado | Equilibrio | 0,515 | 0,832 | 0,428 | 4,3 m |
| 11 | Reducido | Grande | Polarizado | Desequilibrio | 0,560 | 0,684 | 0,383 | 3,8 m |
| 12 | Reducido | Pequeño | Polarizado | Desequilibrio | 0,421 | 0,837 | 0,352 | 3,5 m |
| 13 | Reducido | Grande | No polarizado | Equilibrio | 0,559 | 0,617 | 0,345 | 3,5 m |
| 14 | Reducido | Pequeño | No polarizado | Equilibrio | 0,420 | 0,756 | 0,317 | 3,2 m |
| 15 | Reducido | Grande | No polarizado | Desequilibrio | 0,465 | 0,621 | 0,289 | 2,9 m |
| 16 | Reducido | Pequeño | No polarizado | Desequilibrio | 0,331 | 0,761 | **0,252** | 2,5 m |

La primera y más evidente conclusión de esta tabla es que **el espacio es el factor de separación dominante**. Los ocho escenarios con espacio amplio (escenarios 1-8) producen todos valores de E[HID/min] superiores a 1,70 m/min, mientras que los ocho escenarios con espacio reducido (escenarios 9-16) no superan los 0,44 m/min. La brecha entre ambos grupos no admite comparación con ningún otro factor del diseño: la frontera amplio/reducido establece dos mundos cuantitativamente distintos para la carrera de alta intensidad. El **ratio máximo/mínimo es de 9,47x** (escenario #1 frente al #16), lo que en términos prácticos significa que en una sesión de 10 minutos, el escenario más favorable acumula 23,9 m de HID mientras que el más desfavorable solo genera 2,5 m —una diferencia de más de 21 metros por cada 10 minutos de trabajo.

Dentro del espacio amplio, se observa una disociación interesante entre los escenarios #1 y #3. El escenario #1 (agrupación pequeña, polarizado, equilibrio) tiene una probabilidad de sprint ligeramente menor (P = 0,837) que el #3 (agrupación grande, polarizado, equilibrio; P = 0,900), pero compensa con una intensidad condicional muy superior (2,852 vs. 2,330 m/min), resultando en la mayor E[HID/min] de toda la tabla. Esto ilustra que **los grupos grandes garantizan con más frecuencia que se produzca algún episodio de sprint, pero los grupos pequeños generan sprints más intensos**. Polaridad y equilibrio tienen efectos más modestos dentro de cada mitad de la tabla (diferencias de 0,2 a 0,4 m/min entre escenarios equivalentes), coherente con su estatus de interruptores pero no moduladores de la intensidad condicional.

Las predicciones del modelo GLMM aportan ventajas sustanciales frente al cálculo de medias empíricas por celda del diseño factorial. En primer lugar, muchas celdas del diseño real disponen de escasas observaciones, lo que hace que las medias crudas sean estimadores de alta varianza muestral y baja fiabilidad. El modelo GLMM, al ser un modelo probabilístico global, comparte información entre celdas y produce estimaciones más estables y generalizables. En segundo lugar, las predicciones controlan el efecto del jugador mediante el efecto aleatorio, haciéndolas comparables entre celdas con distintas composiciones de jugadores —algo que las medias crudas no pueden garantizar. La comparación entre predicciones y medias empíricas por celda (MAE = 0,757 m/min, MAPE = 62,5 %, correlación de Pearson r = 0,565) muestra una correspondencia moderada: el modelo captura las tendencias generales del diseño, pero no replica con exactitud las medias crudas porque es un modelo poblacional —sin efectos aleatorios del jugador— mientras que las medias empíricas mezclan jugadores de distintas características y fases con distinta representación en cada celda.

---

### 7. Medias marginales estimadas (EMMs)

Las medias marginales estimadas (EMMs) son estimaciones de la esperanza de la variable respuesta calculadas desde la distribución posterior del modelo, marginalizando sobre los niveles de las demás variables independientes mediante ponderación uniforme. En el contexto de un modelo hurdle, las EMMs se calculan en tres niveles complementarios: la parte ψ (P(HID > 0)), la parte μ (E[HID/min | HID > 0]) y el producto combinado E[HID/min] = P(HID > 0) × E[HID/min | HID > 0]. A diferencia de las medias crudas de los datos, las EMMs **no son medias de observaciones**: son medias esperadas bajo el modelo, controlando el efecto de las demás variables independientes y del jugador. Esta propiedad las hace especialmente valiosas en este estudio, donde el diseño de las sesiones de entrenamiento no es balanceado —distintos jugadores participan en distintas tareas con distinta frecuencia—, lo que invalida las comparaciones directas entre medias crudas.

### 7.1. EMMs principales

La siguiente tabla recoge las EMMs para cada nivel de cada variable de diseño, marginalizando sobre los restantes factores:

| Variable | Nivel | P(HID>0) | HDI 95 % | E[HID/min|Y>0] | HDI 95 % | E[HID/min] | HDI 95 % |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Espacio | Amplio | **0,813** | [0,743; 0,877] | **2,493** | [2,079; 2,936] | **2,019** | [1,627; 2,401] |
| Espacio | Reducido | **0,490** | [0,389; 0,595] | **0,727** | [0,610; 0,858] | **0,353** | [0,253; 0,446] |
| Agrupación | Grande | 0,706 | [0,626; 0,786] | 1,447 | [1,211; 1,708] | 1,141 | [0,933; 1,369] |
| Agrupación | Pequeño | 0,597 | [0,511; 0,690] | 1,772 | [1,465; 2,088] | 1,231 | [0,978; 1,503] |
| Polaridad | No polarizado | 0,614 | [0,517; 0,699] | 1,534 | [1,263; 1,835] | 1,077 | [0,847; 1,329] |
| Polaridad | Polarizado | 0,689 | [0,610; 0,770] | 1,686 | [1,404; 1,971] | 1,295 | [1,059; 1,549] |
| Equilibrio | Desequilibrio | 0,615 | [0,520; 0,699] | 1,616 | [1,333; 1,936] | 1,138 | [0,899; 1,398] |
| Equilibrio | Equilibrio | 0,688 | [0,607; 0,771] | 1,604 | [1,335; 1,880] | 1,233 | [1,005; 1,481] |

La diferencia entre espacio amplio y espacio reducido domina con diferencia todas las comparaciones. En espacio amplio, P(HID > 0) = 0,813 y E[HID/min | Y > 0] = 2,493 m/min; en espacio reducido, ambos valores colapsan a 0,490 y 0,727 respectivamente, resultando en E[HID/min] de apenas 0,353 frente a 2,019. Para agrupación, se aprecia la disociación ya descrita en los coeficientes: la agrupación grande tiene mayor probabilidad (0,706 vs. 0,597) pero menor intensidad condicional (1,447 vs. 1,772), con un efecto neto sobre E[HID/min] prácticamente idéntico (1,141 vs. 1,231) cuyo HDI incluye el 0, confirmando que el efecto neto no es creíble. Las EMMs de polaridad muestran que el juego polarizado genera una E[HID/min] = 1,295, creíblemente superior al no polarizado (1,077), impulsada principalmente por la mayor probabilidad de sprint. En equilibrio, el efecto neto sobre E[HID/min] es muy pequeño y no creíble (1,233 vs. 1,138, HDI que incluye el 0).

### 7.2. Contrastes pairwise — disociación interruptor/modulador

| Variable | Contraste | Δψ | HDI Δψ | Ratio μ | Δμ % | ΔE[Y] | Creíble en E[Y] |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Espacio | Reducido − Amplio | **-0,322** | [-0,376; -0,266] | **0,29** | **-70,8 %** | -1,666 | ✓ |
| Agrupación | Pequeño − Grande | -0,109 | [-0,150; -0,067] | 1,23 | +22,5 % | +0,089 | ✗ |
| Polaridad | Polarizado − No pol. | +0,074 | [+0,031; +0,118] | 1,10 | +10,2 % | +0,217 | ✓ |
| Equilibrio | Equilibrio − Deseq. | +0,073 | [+0,029; +0,117] | 1,00 | -0,5 % | +0,095 | ✗ |

Estos contrastes pairwise confirman y matizan el patrón de disociación interruptor/modulador ya anticipado por los coeficientes. El **espacio** es el único factor que actúa simultáneamente como interruptor potente (Δψ = -0,322, creíble) y como modulador de enorme magnitud (Ratio μ = 0,29, reducción del 70,8 % en intensidad condicional). Su efecto combinado sobre E[HID/min] (ΔE[Y] = -1,666, creíble) no tiene parangón en ningún otro factor. La **agrupación** actúa en ambos planos con dirección opuesta: reduce la probabilidad (Δψ = -0,109, creíble) pero aumenta la intensidad condicional (Ratio μ = 1,23, creíble), y el efecto neto sobre E[HID/min] resulta no creíble (ΔE[Y] = +0,089, HDI que incluye el 0). La **polaridad** actúa fundamentalmente como interruptor: su efecto sobre ψ es creíble (Δψ = +0,074) pero su ratio en μ (1,10) es marginalmente no creíble; sin embargo, el efecto neto sobre E[Y] sí es creíble (ΔE[Y] = +0,217). El **equilibrio** es el interruptor más débil: su Δψ es creíble (+0,073) pero su efecto en μ es prácticamente nulo (ratio = 1,00, -0,5 %), y el efecto neto no es creíble.

### 7.3. EMMs de interacciones de dos vías

Se calcularon las EMMs para las seis combinaciones posibles de pares de variables de diseño (con cuatro combinaciones de niveles por par), marginalizando sobre las dos variables restantes. El par más relevante es **Espacio × Agrupación**, que confirma el escenario de máxima demanda como el de espacio amplio con agrupación pequeña. En el extremo opuesto del espectro de variación factorial se encuentra el par **Polaridad × Equilibrio**: sus cuatro combinaciones producen E[HID/min] entre 1,028 y 1,340 m/min, un rango de apenas 0,312 m/min que contrasta dramáticamente con la variación inducida por cualquier par que incluya la variable espacio.

---

### 8. EMMs condicionadas a GrupoEdad — fichas por categoría

Puesto que M2 es un **modelo aditivo** —sin términos de interacción entre las variables de diseño táctico y GrupoEdad—, los coeficientes β de cada variable de diseño son constantes compartidas por las cinco categorías de edad. Esta característica tiene una implicación fundamental para la interpretación de las EMMs condicionadas: los efectos del diseño táctico sobre cada componente del hurdle son funcionalmente idénticos en todas las categorías en escala relativa, aunque sus manifestaciones en escala absoluta difieren según el nivel base de cada grupo.

### 8.1. Perfil global de HID por GrupoEdad

La tabla siguiente presenta el perfil de HID para cada categoría, marginalizando sobre todas las variables de diseño táctico:

| GrupoEdad | P(HID>0) | HDI 95 % | E[HID/min|HID>0] | HDI 95 % |
| --- | --- | --- | --- | --- |
| Juvenil | **0,880** | [0,840; 0,921] | 2,837 | [2,460; 3,255] |
| Senior Masculino | **0,876** | [0,835; 0,913] | **3,611** | [3,130; 4,077] |
| Cadete | 0,652 | [0,567; 0,734] | 1,610 | [1,341; 1,882] |
| Neskak | 0,487 | [0,409; 0,571] | 1,226 | [1,052; 1,407] |
| Infantil | 0,457 | [0,361; 0,548] | 1,277 | [1,046; 1,517] |

El gradiente madurativo en la capacidad de carrera a alta intensidad queda cuantificado con precisión. La intensidad condicional E[HID/min | HID > 0] varía desde 1,226 m/min (Neskak) hasta 3,611 m/min (Senior Masculino), una diferencia de factor 2,95x. Lo que resulta especialmente revelador es la disociación entre Juvenil y Senior Masculino: ambas categorías presentan probabilidades de sprint prácticamente idénticas (~88 %, con HDI solapados), pero el Senior genera una intensidad condicional un **27,3 % mayor** (3,611 vs. 2,837 m/min). Esto indica que Juvenil y Senior alcanzan el umbral de 20 km/h con la misma frecuencia, pero los Senior recorren más distancia por cada episodio de alta intensidad —reflejo de mayores capacidades de velocidad máxima y potencia locomotora. En el extremo opuesto, las categorías Infantil y Neskak presentan probabilidades de sprint muy bajas (0,457 y 0,487 respectivamente), lo que indica que en la mayoría de las fases de entrenamiento estas categorías no alcanzan el umbral de 20 km/h, especialmente en condiciones de espacio reducido o alta densidad de jugadores.

### 8.2. Invarianza de efectos entre categorías — el hallazgo metodológico central

En M2 (modelo aditivo), cada coeficiente β de las variables de diseño es una constante compartida por todas las categorías de edad. Para la parte μ con enlace logarítmico, esto tiene una consecuencia matemática exacta: el cociente entre los valores de E[HID/min | HID > 0] de dos niveles de una VI es igual a $exp(β_A − β_B)$, que es un valor constante independiente del grupo de edad. Formalmente:

$\frac{E[\text{HID/min}|\text{HID}>0, \text{nivel}_A]}{E[\text{HID/min}|\text{HID}>0, \text{nivel}_B]} = e^{(\beta_A - \beta_B)}$

Los datos confirman esta invarianza de forma prácticamente exacta. El cambio porcentual en E[HID/min | HID > 0] por variable de diseño y grupo de edad es:

| VI | Juvenil | Senior M. | Cadete | Neskak | Infantil |
| --- | --- | --- | --- | --- | --- |
| Espacio (amplio vs. reducido) | **+243,0 %** | **+242,9 %** | **+243,0 %** | **+242,9 %** | **+243,0 %** |
| Agrupación (pequeño vs. grande) | +22,4 % | +22,4 % | +22,5 % | +22,5 % | +22,5 % |
| Polaridad (pol. vs. no pol.) | +10,0 % | +10,0 % | +9,9 % | +10,0 % | +10,0 % |
| Equilibrio (deseq. vs. equil.) | +0,7 % | +0,7 % | +0,7 % | +0,7 % | +0,7 % |

La dispersión entre categorías no supera el 0,1 % en ninguna VI, confirmando una **invarianza perfecta en escala multiplicativa**. Esto no es una coincidencia numérica, sino una consecuencia directa de la aditividad del modelo en la escala del predictor lineal.

La situación es cualitativamente diferente en la parte ψ. Los odds ratios del logit son constantes entre categorías, pero la función de enlace logístico no es lineal, de modo que el mismo odds ratio produce efectos absolutos distintos según el nivel base de probabilidad de cada categoría. El cambio porcentual en P(HID > 0) por variable de diseño y grupo de edad es:

| VI | Juvenil | Senior M. | Cadete | Neskak | Infantil |
| --- | --- | --- | --- | --- | --- |
| Espacio (amplio vs. reducido) | +17,7 % | +18,4 % | +65,8 % | +116,7 % | +127,2 % |
| Agrupación (pequeño vs. grande) | -6,0 % | -6,2 % | -15,4 % | -21,6 % | -22,7 % |
| Polaridad | +4,3 % | +4,5 % | +12,1 % | +18,0 % | +19,1 % |
| Equilibrio | -4,1 % | -4,2 % | -10,6 % | -15,1 % | -15,9 % |

Este patrón revela un resultado de gran relevancia práctica. El efecto del espacio sobre la probabilidad de sprint varía en un rango enorme según la categoría: para Juvenil y Senior —categorías con P(HID > 0) base ya elevadas (~88 %)— pasar de espacio reducido a espacio amplio solo incrementa la probabilidad en un 17-18 %, porque estas categorías generan sprint incluso en condiciones desfavorables. Para Infantil, la misma manipulación implica un incremento del 127 %, duplicando con creces la probabilidad de que ocurra algún episodio de alta velocidad. Esta diferencia es matemáticamente esperable por la no linealidad del logit y biológicamente coherente: **las categorías con menor capacidad locomotora base son las que más se benefician —en términos absolutos— de las decisiones favorables de diseño táctico**. El espacio amplio es el factor más relevante para todas las categorías, pero su impacto es transformador en las categorías más jóvenes.

---

### 9. Ranking de los 16 escenarios por categoría

Una consecuencia directa de la aditividad del modelo M2 es que el **ranking de los 16 escenarios factoriales por P(HID > 0) es absolutamente idéntico en las cinco categorías de edad**, y lo mismo ocurre con el ranking por E[HID/min | HID > 0]. La jerarquía de los diseños de tarea —desde el más exigente hasta el menos exigente en términos de demanda locomotora de alta intensidad— es, por tanto, universal: no depende de la categoría para la que se diseñe la sesión. Esto es un resultado de gran utilidad práctica, pues significa que las recomendaciones de diseño obtenidas del modelo son generalizables.

En cuanto a las comparaciones extremas (escenario #1 vs. #16), la diferencia en E[HID/min | HID > 0] entre el escenario máximo y el mínimo es de **+206 % en todas las categorías** —invarianza perfecta, como era de esperar en la parte μ. Sin embargo, la misma comparación en P(HID > 0) produce resultados muy dispares entre categorías:

| Categoría | P(HID>0) Escenario #1 | P(HID>0) Escenario #16 | Diferencia relativa |
| --- | --- | --- | --- |
| Juvenil | 0,976 | 0,697 | **+40,0 %** |
| Senior M. | 0,975 | 0,688 | **+41,7 %** |
| Cadete | 0,898 | 0,333 | **+169,4 %** |
| Neskak | 0,798 | 0,183 | **+335,9 %** |
| Infantil | 0,773 | 0,163 | **+373,9 %** |

Para Juvenil y Senior Masculino, el diseño de la tarea modifica la probabilidad de sprint en un rango de 28-29 puntos porcentuales (de 0,70 a 0,98 aproximadamente): incluso en el peor escenario posible, estas categorías generan sprint en cerca de 7 de cada 10 fases. Para Infantil y Neskak, el mismo diseño puede determinar que el sprint ocurra en 1 de cada 6 fases (P ≈ 0,16) o en 3 de cada 4 (P ≈ 0,77): la palanca del diseño táctico es de una magnitud cualitativamente diferente en estas categorías.

La comparación entre los cuatro escenarios más favorables (Top-4) y los cuatro más desfavorables (Bottom-4) confirma esta heterogeneidad de impacto:

| Categoría | Top-4 P(HID>0) | Bottom-4 P(HID>0) | Δ(P) | Top-4 E[HID|>0] | Bottom-4 E[HID|>0] | Δ(μ) |
| --- | --- | --- | --- | --- | --- | --- |
| Juvenil | 0,967 | 0,759 | +0,207 | 4,270 | 1,317 | +2,950 |
| Senior M. | 0,965 | 0,752 | +0,213 | 5,432 | 1,676 | +3,756 |
| Cadete | 0,862 | 0,410 | +0,451 | 2,421 | 0,747 | +1,674 |
| Neskak | 0,738 | 0,239 | +0,498 | 1,843 | 0,569 | +1,274 |
| Infantil | 0,709 | 0,215 | +0,494 | 1,920 | 0,593 | +1,327 |

El diferencial Δ(P) entre los mejores y peores escenarios alcanza +0,494-0,498 en Infantil y Neskak, mientras que en Juvenil y Senior se limita a +0,207-0,213. En cuanto a la intensidad condicional, el diferencial Δ(μ) es proporcional al nivel base de cada categoría: un Senior en Top-4 genera 5,432 m/min de HID condicional, frente a 1,676 m/min en Bottom-4 (diferencia de 3,756 m/min), mientras que un Neskak oscila entre 1,843 y 0,569 (diferencia de 1,274 m/min). En ambas dimensiones, las categorías con mayor capacidad locomotora presentan mayores diferencias absolutas entre escenarios extremos, aunque sus diferencias relativas son equivalentes —de nuevo, consecuencia de la invarianza multiplicativa del modelo.

---

### 10. Síntesis e implicaciones

Los resultados del GLMM Bayesiano Hurdle Gamma para HID Covered (m) / min dibujan un panorama en el que **el espacio es, con diferencia, el factor de diseño dominante para la carrera de alta intensidad**, actuando simultáneamente como interruptor y modulador de magnitud sin parangón en el resto de variables del TFG. El espacio reducido reduce en 32,2 puntos porcentuales la probabilidad de que se produzca algún episodio de sprint y, de forma independiente, reduce en un 70,8 % la intensidad condicional de los episodios que sí se producen. El resultado conjunto es que el ratio entre el escenario más y menos favorable es de 9,47 veces —una variación que, en una sesión de entrenamiento de 10 minutos, representa la diferencia entre acumular 23,9 metros de carrera de alta intensidad o apenas 2,5 metros. Ninguna otra variable dependiente del TFG exhibe una sensibilidad tan extrema a las condiciones del espacio de juego.

La **agrupación** presenta un perfil de efectos más complejo y en cierta medida contraintuitivo. Los grupos pequeños se asocian a mayor intensidad condicional de la carrera de alta intensidad (+22,5 %), pero simultáneamente a menor probabilidad de que esta se produzca (-10,9 pp). El efecto neto sobre E[HID/min] resulta no creíble, lo que sugiere que ambos efectos se compensan en la escala de la esperanza marginal. La interpretación funcional es que en grupos pequeños, la participación individual más intensa y continua en la tarea reduce las transiciones y los momentos de carrera en profundidad que típicamente generan los episodios de velocidad máxima, pero cuando estos sí ocurren, son más exigentes. La polaridad actúa únicamente como interruptor: aumenta la probabilidad de que haya sprint (+7,4 pp) sin modificar su intensidad condicional, lo que tiene sentido si se considera que el juego polarizado genera más situaciones de transición y finalización, pero no necesariamente aumenta las distancias recorridas en cada acción individual de alta velocidad.

El **grupo de edad** emerge como una variable de confusión crítica para esta variable dependiente. Con 9,5 % de varianza explicada en la parte continua del hurdle —el valor más alto de todo el TFG para GrupoEdad—, ignorar la categoría de edad equivale a mezclar poblaciones con niveles base de rendimiento locomotor tan distintos que la heterogeneidad artificial se transfiere al componente del jugador (que pasa del 19,6 % en M1 al 7,7 % en M2). La invarianza perfecta de los efectos multiplicativos del diseño táctico sobre E[HID/min | HID > 0] en todas las categorías es el hallazgo metodológico más sólido de esta sección: confirma que el diseño táctico actúa de forma consistente y universalmente predecible en la modulación de la intensidad de la carrera de alta intensidad, independientemente del nivel madurativo del jugador. Sin embargo, los efectos absolutos sobre la probabilidad de sprint son sistemáticamente mayores en las categorías con menor capacidad locomotora base —Infantil y Neskak—, que pueden pasar de probabilidades de sprint inferiores al 20 % a probabilidades cercanas al 77 % en función del diseño elegido.

La implicación práctica central para el entrenamiento es que la elección del espacio de juego no es un detalle organizativo sino la variable de diseño con mayor impacto cuantificable sobre la carrera de alta intensidad. Para todas las categorías, **el espacio amplio combinado con agrupación pequeña es la condición óptima** tanto para maximizar la probabilidad de que se produzcan acciones de alta velocidad como para maximizar su intensidad condicional. Sin embargo, el profesional debe calibrar sus expectativas en función del nivel base de cada grupo: diseñar en espacio amplio para Infantil o Neskak produce un impacto relativo mucho mayor en la probabilidad de sprint que el mismo diseño para Juvenil o Senior. Dicho de otro modo, las decisiones de diseño táctico son especialmente relevantes para las categorías de formación, donde la restricción espacial puede hacer la diferencia entre una sesión con o sin estímulo locomotor de alta intensidad, mientras que para las categorías superiores el diseño modula la cantidad pero raramente elimina el estímulo por completo.

## Por qué el cambio % en μ es idéntico para todos los grupos

La parte μ usa un **enlace logarítmico**:

`log(μ) = β₀ + β_Espacio + β_Agrupacion + ... + β_GrupoEdad`

Despejando:

`μ = exp(β₀) · exp(β_Espacio) · exp(β_Agrupacion) · ... · exp(β_GrupoEdad)`

El modelo es **multiplicativo en escala natural**. Cuando comparas dos niveles de Espacio (amplio vs. reducido), el cociente es:

`μ_Amplio / μ_Reducido = exp(β_Espacio_Amplio - β_Espacio_Reducido) = exp(β_Espacio)`

Observa que `exp(β_GrupoEdad)` **se cancela** en el cociente. Por eso el ratio — y en consecuencia el cambio porcentual— es **el mismo para Juvenil, Senior, Cadete, Neskak e Infantil**.

Numéricamente: si el espacio multiplica μ por 3,43 (es decir, +243 %), lo hace así:

| Categoría | μ en Reducido | μ en Amplio | Ratio |
| --- | --- | --- | --- |
| Senior M. | 1,054 | 3,611 | 3,43× |
| Juvenil | 0,828 | 2,837 | 3,43× |
| Cadete | 0,470 | 1,610 | 3,43× |
| Neskak | 0,358 | 1,226 | 3,43× |
| Infantil | 0,373 | 1,277 | 3,43× |

Los valores absolutos son muy distintos, pero el **factor multiplicativo es idéntico**. Es la propiedad fundamental del log-link: convierte efectos aditivos en el espacio log en efectos multiplicativos en el espacio natural, y esos multiplicadores no dependen del nivel base.

---

## Por qué el cambio % en P(HID > 0) es muy diferente entre grupos

La parte ψ usa un **enlace logit**:

`logit(ψ) = log(ψ / (1-ψ)) = γ₀ + γ_Espacio + γ_Agrupacion + ... + γ_GrupoEdad`

Aquí lo que es constante entre grupos es el **odds ratio**, no el cambio en probabilidad:

`OR = exp(γ_Espacio)   ← idéntico para todas las categorías`

Pero la probabilidad se recupera mediante la función logística, que **no es lineal**:

`ψ = 1 / (1 + exp(-logit))`

Y esta función tiene una forma de S: es **muy empinada cerca de ψ = 0,5** y **muy plana cerca de ψ = 0 o ψ = 1**.

Imagina que el efecto del espacio desplaza el logit en +1,5 unidades. Ese desplazamiento produce cambios en probabilidad muy distintos según dónde estés en la curva:

| Categoría | ψ_base (espacio reducido) | Zona de la curva logística | Δψ al pasar a amplio |
| --- | --- | --- | --- |
| Juvenil / Senior | ~0,82 | **Zona plana** (cerca del techo) | Pequeño (+17-18 %) |
| Cadete | ~0,39 | Zona intermedia | Moderado (+66 %) |
| Neskak / Infantil | ~0,20 | **Zona empinada** (lejos del techo) | Enorme (+117-127 %) |

Visualmente: el mismo desplazamiento horizontal (mismo OR) sobre la curva S produce un desplazamiento vertical (cambio en probabilidad) muy distinto dependiendo de en qué tramo de la curva estés.

---

## La asimetría clave: techo vs. suelo

- **Juvenil y Senior** ya tienen P(HID > 0) ≈ 0,88 de media. Están cerca del **techo** de la curva logística. Aunque el diseño mejore mucho en el logit, la probabilidad no puede subir mucho más de 0,976 (escenario #1). El margen de mejora es pequeño.
- **Infantil y Neskak** tienen P(HID > 0) ≈ 0,46-0,49 de media, pero en espacio reducido pueden caer a ~0,16-0,18. Están en la zona de **máxima sensibilidad** de la curva logística. El mismo cambio en el logit se traduce en un salto de probabilidad enorme.

Por eso la diferencia relativa entre escenario #1 y #16 en P(HID > 0) es:

- **+40 %** para Juvenil (de 0,697 a 0,976 — pequeño margen disponible)
- **+374 %** para Infantil (de 0,163 a 0,773 — enorme margen disponible)

---

## Resumen en una frase

> El log-link genera efectos **multiplicativos constantes** sobre μ (los ratios no dependen del nivel base). El logit-link genera **odds ratios constantes** sobre ψ, pero la no linealidad de la función logística hace que el mismo odds ratio produzca cambios de probabilidad muy distintos según el nivel base de cada categoría.
> 

Es exactamente el mismo fenómeno que hace que en medicina, un fármaco con el mismo odds ratio de éxito tenga mucho más impacto absoluto en pacientes con bajo porcentaje base de recuperación que en los que ya tienen un porcentaje alto.

## Parte μ — distribución Gamma → enlace log

La parte μ modela E[HID/min | HID > 0]: una **intensidad continua estrictamente positiva** (metros por minuto). Por construcción, μ ∈ (0, ∞).

El predictor lineal, en cambio, puede tomar cualquier valor real (−∞, +∞): sumas de coeficientes que pueden ser negativos.

El problema es que si intentas modelar μ directamente como:

`μ = β₀ + β_Espacio + β_Agrupacion + ...`

Nada impide que el modelo produzca predicciones negativas, lo cual carece de sentido físico (no puedes recorrer −0,3 m/min).

La solución: aplica una función que mapee (−∞, +∞) → (0, +∞). Esa función es el **exponencial**:

`μ = exp(predictor lineal)   →   log(μ) = predictor lineal`

El enlace log garantiza que **μ siempre sea positivo**, independientemente de los valores que tomen los coeficientes. Como bonus, convierte efectos multiplicativos (que son naturales para escalas de intensidad) en efectos aditivos en el espacio log, lo que facilita la interpretación como ratios.

Además, la distribución Gamma tiene como propiedad que su varianza crece con el cuadrado de la media: Var = μ²/φ. El enlace log es el **enlace canónico natural** de la Gamma — la elección que hace que la estimación sea matemáticamente más estable y coherente con la estructura de la distribución.

---

## Parte ψ — distribución Bernoulli → enlace logit

La parte ψ modela P(HID > 0): una **probabilidad**. Por definición, ψ ∈ (0, 1).

De nuevo, el predictor lineal vive en (−∞, +∞), así que necesitas una función que mapee ese rango al intervalo (0, 1).

El logit hace exactamente eso:

`ψ = 1 / (1 + exp(−predictor lineal))   →   logit(ψ) = log(ψ / (1−ψ)) = predictor lineal`

Cuando el predictor → −∞, ψ → 0. Cuando el predictor → +∞, ψ → 1. Nunca sale del intervalo (0, 1).

El logit es el **enlace canónico de la distribución Bernoulli** (y de la regresión logística en general). Convierte la probabilidad en log-odds, que sí pueden tomar cualquier valor real, y los efectos se interpretan como odds ratios al exponenciarlos.

---

## En resumen

| Parte | Variable modelada | Rango válido | Función de enlace | Garantía |
| --- | --- | --- | --- | --- |
| μ (Gamma) | Intensidad condicional | (0, ∞) | log | μ nunca es negativo |
| ψ (Bernoulli) | Probabilidad de sprint | (0, 1) | logit | ψ nunca sale de (0,1) |

La elección no es arbitraria ni convencional: es la consecuencia lógica de que cada función de enlace es el **inverso de la función que transforma el espacio natural de cada distribución al espacio lineal**. Usar otro enlace sería técnicamente posible (existen enlace identidad, probit, cloglog...), pero el log y el logit son los canónicos porque minimizan problemas numéricos y tienen la interpretación más limpia para sus respectivas distribuciones.