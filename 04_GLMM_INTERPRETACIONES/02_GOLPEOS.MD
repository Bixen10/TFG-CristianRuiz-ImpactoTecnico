# Resultados: Golpeos +15 m/s / min

---

## 1. Elección del modelo: el Hurdle Gamma como necesidad estadística y sustantiva

La variable *Golpeos +15 m/s / min* presenta el 29,5% de valores exactamente iguales a cero: en casi un tercio de las fases registradas, el jugador no ejecutó ningún golpeo a velocidad igual o superior a 15 m/s. Esta proporción de ceros hace técnicamente inviable la familia Gamma pura, cuyo soporte es estrictamente positivo (0, ∞). Un modelo de Poisson tampoco resulta apropiado, dado que la variable es continua —una tasa por minuto— y presenta una asimetría positiva pronunciada incompatible con la distribución de conteos enteros.

La solución adoptada fue el **modelo Hurdle Gamma**, un modelo de dos componentes que separa el proceso generador de la variable en dos etapas estadística y sustantivamente distintas:

- **Componente ψ (parte binaria)**: modelo logístico que estima *P(Y > 0)*, es decir, la probabilidad de que el jugador registre al menos un golpeo potente en la fase. Equivale a preguntar: *¿se activa el evento?*
- **Componente μ (parte continua)**: modelo Gamma con enlace logarítmico que estima *E[Y | Y > 0]*, es decir, la tasa esperada de golpeos por minuto condicionada a que haya ocurrido al menos uno. Equivale a preguntar: *dado que el evento ocurre, con qué frecuencia se repite?*

La media marginal del modelo, $*E[Y] = P(Y > 0) × E[Y | Y > 0]*$, es el producto de ambas, pero no constituye el foco principal del análisis. La verdadera riqueza interpretativa reside en descomponer qué factores de diseño táctico actúan sobre cada componente, ya que —como se demostrará— algunos factores actúan como *interruptores* (afectan a ψ pero no a μ), otros como *moduladores* (afectan a μ pero no a ψ) y otros operan en ambos planos simultáneamente. Esta separación no sería visible en un modelo convencional y constituye el hallazgo metodológico más relevante del análisis.

La especificación del modelo seleccionado (M1) fue:

$\mu: \quad \texttt{GolpeosMin} \sim \texttt{Espacio} + \texttt{Agrupacion} + \texttt{Polaridad} + \texttt{Equilibrio} + (1 | \texttt{PlayerId})$

$\psi: \quad \text{misma estructura}$

con familia Gamma y enlace logarítmico para la parte μ, y enlace logit para la parte ψ.

---

## 2. Selección del modelo

Se ajustaron cinco modelos GLMM Hurdle Gamma bayesianos con cuatro cadenas de 2000 muestras (1000 de calentamiento). La comparación mediante validación cruzada leave-one-out (PSIS-LOO) arrojó los siguientes resultados:

| Modelo | Descripción | ELPD | ΔELPD vs M1 | dSE |
| --- | --- | --- | --- | --- |
| **M1** | Efectos principales (4 VI) + (1|PlayerId) | **−627.2** | — | — |
| M0 | Solo intercepto + (1|PlayerId) | −719.5 | 92.3 | 25.0 |
| M2 | M1 + GrupoEdad | −819.7 | 192.5 | — |
| M3 | M2 + Espacio×Agrupación | peor | — | — |
| M4 | M2 + todas las VI:GrupoEdad | peor | — | — |

**M1 fue seleccionado como modelo final** por ser el más parsimonioso con la mejor capacidad predictiva según el ELPD. La interpretación del ranking merece tres observaciones:

Primero, la diferencia entre M0 y M1 (ΔELPD = 92,3; dSE = 25,0) confirma que las cuatro variables de diseño táctico aportan información predictiva real sobre la variable respuesta, aunque —como se verá— su peso explicativo sea modesto.

Segundo, el hecho de que M2 —que añade GrupoEdad como efecto fijo— empeore la predicción respecto a M1 (ΔELPD = −192,5) reproduce exactamente el patrón observado en el análisis de *Toques/min*: dado que cada `PlayerId` pertenece a un único `GrupoEdad`, la anidación jerárquica convierte ambas variables en parcialmente redundantes. Cuando GrupoEdad se incluye como efecto fijo, interfiere con el intercepto aleatorio por jugador, generando inestabilidad sin ganancia predictiva real. M2 se utiliza, no obstante, como referencia descriptiva para el análisis por categorías (Sección 8).

Tercero, los **pesos de stacking** resultaron dispersos entre modelos (M0 = 0,30; M1 = 0,24; M2 = 0,34; M4 = 0,12), lo que indica una señal táctica débil sobre esta variable. Este reparto no invalida la selección de M1 —que sigue siendo el mejor según el ranking de ELPD—, sino que anticipa el hallazgo posterior: los golpeos potentes son la variable de diseño más difícil de predecir en el conjunto del TFG, y el sistema táctico explica una fracción menor de su variabilidad que en otras variables dependientes.

---

## 3. Diagnósticos del modelo final

### 3.1. Convergencia y calibración

La convergencia de M1 fue satisfactoria en ambos componentes del hurdle: el R-hat máximo fue de 1,0033 (umbral convencional < 1,01) y el ESS mínimo alcanzó 3169, garantizando muestras efectivas suficientes para la inferencia. Los gráficos de traza mostraron mezcla adecuada en las cuatro cadenas. La comprobación predictiva posterior (PPC) confirmó que el modelo reproduce correctamente la masa de ceros observada (29,5%) así como la asimetría de la parte continua, validando la elección de la familia Hurdle Gamma frente a alternativas más simples.

### 3.2. Descomposición de varianza

La Tabla 1 presenta la descomposición de varianza de M1 en la parte μ (escala log-latente), siguiendo el método de Nakagawa et al. (2017):

**Tabla 1.** Descomposición de varianza de M1 (parte μ).

| Componente | % del total | HDI 95% |
| --- | --- | --- |
| Diseño táctico (4 VI) | 3,0% | [2,0%, 4,0%] |
| Jugador (intercepto aleatorio) | 11,4% | [8,9%, 14,7%] |
| Residual (Gamma) | 85,6% | [82,6%, 88,4%] |

El **R² marginal** (varianza explicada exclusivamente por el diseño táctico) fue de 0,030, y el **R² condicional** (diseño + jugador) de 0,144. El **R² bayesiano global** fue de 0,166 (16,6%).

Estos valores constituyen el resultado más llamativo de la descomposición, y merecen una interpretación cuidadosa en tres niveles:

**Por qué el 3% marginal es el más bajo del TFG.** En el análisis de *Toques/min*, el diseño táctico explicaba el 20,8% de la varianza; aquí, apenas el 3,0%. La diferencia refleja la naturaleza fundamentalmente distinta de ambas variables. Los toques son un resultado del flujo continuo de participación en el ejercicio: el diseño táctico —número de jugadores, dimensiones del espacio— moldea directamente cuántas interacciones técnicas ocurren por minuto. Los golpeos potentes, en cambio, son eventos oportunistas que requieren la confluencia de múltiples condicionantes no controlados por el diseño: la posición en el campo en el momento del golpeo, la recepción previa (balón aéreo o en el suelo, velocidad y dirección de llegada), el ángulo respecto a portería, y la decisión individual de ejecutar. El diseño táctico puede *facilitar o dificultar* el contexto para que ocurran estos eventos —como se verá en las secciones siguientes—, pero no los programa.

**La diferencia radical entre 3% y 14,4%: las diferencias individuales mandan.** Mientras que el diseño táctico contribuye con un 3,0%, el intercepto aleatorio por jugador aporta un 11,4%, lo que implica que las diferencias individuales en potencia de golpeo son aproximadamente cuatro veces más importantes que el sistema táctico como fuente de variación. Este resultado invierte radicalmente el patrón de *Toques/min*, donde el táctico (20,8%) superaba al jugador (10,0%). Tiene plena coherencia sustantiva: la capacidad de ejecutar un golpeo a ≥15 m/s es una habilidad técnico-física fundamentalmente individual, ligada a la técnica de golpeo, la fuerza muscular del tren inferior y la biomecánica de cada jugador —aspectos que el diseño de la tarea no puede sustituir.

**El 85,6% residual no es un fracaso del modelo.** El parámetro de forma Gamma estimado (α = 2,15) corresponde a un coeficiente de variación residual del 68,2%. Este nivel de variabilidad no explicada no indica un modelo mal especificado, sino la naturaleza intrínsecamente estocástica de la variable. Un golpeo potente es una decisión táctica oportunista que emerge del juego en tiempo real; su frecuencia en una fase de 5-10 minutos fluctúa considerablemente incluso para el mismo jugador bajo las mismas condiciones de diseño. Modelar más varianza requeriría incluir covariables de contexto situacional (minuto de fase, marcador parcial, fatiga) que no forman parte del registro PlayerMaker.

La comparación con M2 proporciona información adicional de valor descriptivo: al incluir GrupoEdad, el diseño táctico desciende marginalmente al 2,7%, el intercepto por jugador cae del 11,4% al 8,8%, y GrupoEdad absorbe un 2,5% adicional. Que el efecto de jugador se reduzca al añadir la categoría de edad confirma que parte de la varianza individual se explica por el nivel de desarrollo: los jugadores de mayor edad y los sénior masculinos golpean sistemáticamente con más potencia, y esta diferencia queda parcialmente capturada por GrupoEdad cuando se introduce explícitamente.

---

## 4. Efectos del diseño táctico: coeficientes del modelo final

Las categorías de referencia para la interpretación de los coeficientes son: Espacio = amplio, Agrupación = grande, Polaridad = polarizado, Equilibrio = equilibrio.

### 4.1. Componente μ — Intensidad condicional E[Y | Y > 0]

**Tabla 2.** Coeficientes del componente μ (parte Gamma, escala multiplicativa).

| Efecto | β | exp(β) | HDI 95% de exp(β) | Creíble |
| --- | --- | --- | --- | --- |
| Intercepto (condición referencia) | −1,596 | 0,203 gl/min | [0,193; 0,214] | — |
| Espacio: reducido vs amplio | −0,162 | 0,851 | [0,807; 0,900] | Sí |
| Agrupación: pequeño vs grande | +0,309 | 1,363 | [1,284; 1,443] | Sí |
| Polaridad: no polarizado vs polarizado | −0,029 | 0,972 | [0,896; 1,049] | No |
| Equilibrio: desequilibrio vs equilibrio | +0,055 | 1,057 | [0,978; 1,134] | No |

En la condición de referencia (espacio amplio, agrupación grande, polarizado, equilibrio), la tasa esperada de golpeos potentes —dado que ocurre al menos uno— es de 0,203 golpeos/min, equivalente a aproximadamente 2,0 golpeos en una fase de 10 minutos.

Dos efectos son creíbles en este componente: el espacio y la agrupación, con direcciones opuestas que ya anuncian la disociación que se analizará en profundidad más adelante.

El **espacio reducido** reduce la intensidad condicional en un 15% (exp(β) = 0,851; HDI [0,807; 0,900]). La interpretación es mecánicamente clara: en espacios reducidos, los jugadores disponen de menos metros de carrera libres antes de recibir el balón, lo que limita la capacidad de acumular velocidad segmentaria antes del contacto y genera ángulos de golpeo más cerrados. Incluso cuando se produce un golpeo potente en espacio reducido, este se ejecuta en condiciones biomecánicas menos favorables.

La **agrupación pequeña** incrementa la intensidad condicional en un 36% (exp(β) = 1,363; HDI [1,284; 1,443]), el efecto más grande del componente μ. En grupos con pocos jugadores, cada participante acumula más posesiones individuales por unidad de tiempo, incluyendo más situaciones de finalización. Cuando le llega el balón en posición de golpeo, la ausencia de compañeros con quienes compartir la carga de juego puede traducirse en mayor determinación de ejecución. Este efecto es coherente con el patrón observado en *Toques/min*, donde la agrupación pequeña también aumentaba la participación técnica individual, aunque allí el mecanismo operaba también sobre la probabilidad binaria.

La **polaridad** y el **equilibrio** no muestran efectos creíbles sobre μ (HDI que incluyen holgadamente el valor 1). Una vez que el jugador golpea, ni la orientación de la tarea hacia portería ni la distribución numérica entre equipos modulan la frecuencia de golpeos por minuto. Este resultado, aparentemente contraintuitivo para la Polaridad, se comprende al leer el componente ψ a continuación.

### 4.2. Componente ψ — Probabilidad de golpeo P(Y > 0)

**Tabla 3.** Coeficientes del componente ψ (parte logística, escala de odds ratio).

| Efecto | β | OR = exp(β) | HDI 95% de OR | Creíble |
| --- | --- | --- | --- | --- |
| Intercepto (condición referencia) | +1,565 | — | P(Y>0) = 0,827 [0,805; 0,848] | — |
| Espacio: reducido vs amplio | −0,575 | 0,565 | [0,474; 0,665] | Sí |
| Agrupación: pequeño vs grande | −0,169 | 0,848 | [0,704; 0,996] | Sí (límite) |
| Polaridad: no polarizado vs polarizado | −0,844 | 0,432 | [0,353; 0,517] | Sí |
| Equilibrio: desequilibrio vs equilibrio | −0,129 | 0,883 | [0,722; 1,065] | No |

El intercepto revela que, en la condición de referencia (amplio + grande + polarizado + equilibrio), existe un 82,7% de probabilidad de que un jugador registre al menos un golpeo potente en la fase. Este contexto —espacio amplio, grupo numeroso, tarea con portería, plantilla equilibrada— es el más facilitador posible para la parte binaria del modelo.

### 4.3. La disociación ψ/μ: el hallazgo central del análisis

El resultado más relevante de este análisis no es ningún coeficiente aislado, sino el **patrón de disociación** entre los dos componentes del hurdle. Cada variable de diseño táctico exhibe un perfil cualitativamente distinto según el componente al que afecta:

**Polaridad: interruptor puro.** La polaridad es el factor con mayor efecto sobre el componente ψ: las tareas no polarizadas reducen las odds de que ocurra un golpeo potente a menos de la mitad (OR = 0,432; HDI [0,353; 0,517]), lo que equivale a una reducción del 57% en las odds. El mecanismo es directo: sin portería como objetivo, desaparece el contexto que genera acciones de finalización —tiros a puerta, despejes potentes, pases en largo hacia zona de remate—. La polaridad actúa como un *interruptor*: enciende o apaga el contexto de juego directo que hace posible el golpeo potente. Sin embargo, en el componente μ, el coeficiente de polaridad es prácticamente cero (exp(β) = 0,972; HDI [0,896; 1,049], no creíble). Una vez que el jugador golpea, la presencia o ausencia de portería no cambia con qué frecuencia lo hace dentro de esa misma fase. La polaridad modula *si* se golpea, no *cuánto* se golpea.

**Espacio: actúa en ambos planos.** El espacio reducido reduce tanto la probabilidad de golpeo (OR = 0,565; −43% en las odds; creíble en ψ) como la intensidad condicional (exp(β) = 0,851; −15% en μ). El mecanismo es distinto en cada componente: la reducción de P(Y > 0) refleja que hay menos oportunidades espaciales para ejecutar pases en largo, transiciones de carrera o tiros lejanos; la reducción de E[Y | Y > 0] refleja que los pocos golpeos que ocurren se ejecutan en condiciones físicas menos favorables para la potencia. El espacio es el único factor de diseño que afecta *a la vez al interruptor y al modulador*, operando en ambos planos con la misma dirección.

**Agrupación: modulador puro con dirección opuesta en cada componente.** Este es el resultado más sorprendente del análisis. En el componente ψ, las agrupaciones pequeñas presentan un OR = 0,848 (HDI [0,704; 0,996]), marginalmente creíble: la probabilidad de que se produzca al menos un golpeo potente es ligeramente *menor* en grupos pequeños que en grupos grandes. En el componente μ, la dirección se invierte completamente: exp(β) = 1,363, con un efecto grande y creíble de +36% en la intensidad condicional.

¿Cómo interpretar esta oposición? En grupos grandes, hay más jugadores generando situaciones de juego, más circulaciones de balón y más contextos colectivos que pueden culminar en golpeos potentes por parte de distintos jugadores, lo que eleva P(Y > 0). Sin embargo, la mayor densidad de jugadores también *distribuye* las posesiones y las situaciones de finalización entre más participantes, reduciendo la frecuencia individual de golpeos por jugador dentro de la fase: E[Y | Y > 0] es menor. En grupos pequeños, el mecanismo se invierte: hay menos situaciones colectivas de tiro (P(Y > 0) ligeramente menor), pero cada jugador acumula más posesiones en posición de finalización, lo que eleva la tasa condicional de golpeos. Este *trade-off* entre participación colectiva e intensidad individual es un resultado que solo el modelo hurdle puede desenredar, ya que en un modelo Gamma simple (o incluso en las medias brutas) las dos lógicas opuestas se cancelarían parcialmente.

**Equilibrio: sin efecto en ninguna parte.** El equilibrio numérico entre equipos no muestra efectos creíbles ni en ψ (OR = 0,883; HDI [0,722; 1,065]) ni en μ (exp(β) = 1,057; HDI [0,978; 1,134]). La distribución numérica de jugadores por equipo no afecta ni a la probabilidad de que ocurra un golpeo potente ni a su intensidad cuando ocurre.

---

## 5. Predicciones factoriales: los 16 escenarios de diseño

### 5.1. Cálculo y lógica del Hurdle

Las predicciones para las 16 combinaciones factoriales del diseño táctico se calculan como E[Y] = P(Y > 0) × E[Y | Y > 0], marginalizando el efecto de jugador (intercepto aleatorio centrado en cero). La Tabla 4 presenta el ranking completo ordenado por E[Golpeos/min] descendente.

**Tabla 4.** Predicciones para los 16 escenarios factoriales (ordenadas por E[Y] descendente).

| # | Espacio | Agrupación | Polaridad | Equilibrio | P(Y>0) | E[Y|Y>0] | E[Y] | En 10 min |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Amplio | Pequeño | Polarizado | Desequilibrio | 0,780 | 0,292 | 0,227 | 2,3 |
| 2 | Amplio | Pequeño | Polarizado | Equilibrio | 0,801 | 0,276 | 0,221 | 2,2 |
| 3 | Amplio | Grande | Polarizado | Desequilibrio | 0,808 | 0,214 | 0,173 | 1,7 |
| 4 | Amplio | Pequeño | No polarizado | Desequilibrio | 0,604 | 0,283 | 0,171 | 1,7 |
| 5 | Amplio | Pequeño | No polarizado | Equilibrio | 0,635 | 0,268 | 0,170 | 1,7 |
| 6 | Amplio | Grande | Polarizado | Equilibrio | **0,827** | 0,203 | 0,168 | 1,7 |
| 7 | Reducido | Pequeño | Polarizado | Desequilibrio | 0,666 | 0,248 | 0,165 | 1,7 |
| 8 | Reducido | Pequeño | Polarizado | Equilibrio | 0,694 | 0,235 | 0,163 | 1,6 |
| 9 | Amplio | Grande | No polarizado | Desequilibrio | 0,644 | 0,208 | 0,134 | 1,3 |
| 10 | Amplio | Grande | No polarizado | Equilibrio | 0,673 | 0,197 | 0,132 | 1,3 |
| 11 | Reducido | Grande | Polarizado | Desequilibrio | 0,703 | 0,182 | 0,128 | 1,3 |
| 12 | Reducido | Grande | Polarizado | Equilibrio | 0,729 | 0,172 | 0,126 | 1,3 |
| 13 | Reducido | Pequeño | No polarizado | Equilibrio | 0,494 | 0,228 | 0,113 | 1,1 |
| 14 | Reducido | Pequeño | No polarizado | Desequilibrio | 0,462 | 0,241 | 0,111 | 1,1 |
| 15 | Reducido | Grande | No polarizado | Equilibrio | 0,537 | 0,167 | 0,090 | 0,9 |
| 16 | Reducido | Grande | No polarizado | Desequilibrio | 0,504 | 0,177 | 0,089 | 0,9 |

El rango de predicción va de 0,089 a 0,227 golpeos/min, con un ratio máximo/mínimo de 2,55×. Este ratio es ligeramente inferior al observado para *Toques/min* (2,65×), coherente con la menor varianza explicada por el diseño táctico en esta variable.

### 5.2. La tensión entre componentes: por qué el ranking de E[Y] no es trivial

Una primera lectura de la Tabla 4 podría sugerir que el escenario óptimo simplemente maximiza ambos componentes simultáneamente. Sin embargo, la naturaleza dual del hurdle genera tensiones que hacen el ranking no trivial:

**El escenario #6 tiene la mayor P(Y>0) pero ocupa la posición 6 en E[Y].** La condición de referencia (amplio + grande + polarizado + equilibrio) presenta la mayor probabilidad de golpeo del conjunto (P(Y > 0) = 0,827): en 8 de cada 10 fases, el jugador promedio registra al menos un golpeo potente. Sin embargo, su E[Y] = 0,168 lo sitúa en el sexto puesto, porque su intensidad condicional es la más baja de todos los escenarios polarizados (0,203 gl/min). La agrupación grande genera muchos contextos de golpeo —de ahí la alta P—, pero también distribuye las posesiones entre más jugadores, reduciendo la frecuencia individual cuando se golpea. La alta probabilidad no compensa la baja intensidad condicional.

**El escenario #1 (amplio + pequeño + polarizado + desequilibrio) maximiza E[Y] porque combina el interruptor y el modulador de forma óptima.** Tiene una P(Y > 0) = 0,780 —no la más alta, pero sí elevada— porque el espacio amplio y la polarización activan el componente binario. Y tiene la mayor E[Y | Y > 0] = 0,292 del ranking porque la agrupación pequeña amplifica la intensidad condicional. Es la combinación que mejor equilibra ambos mecanismos.

**Los escenarios #3 y #4 tienen prácticamente el mismo E[Y] por mecanismos opuestos.** El escenario #3 (amplio + grande + polarizado + desequilibrio) alcanza E[Y] = 0,173 a través de una alta P(Y > 0) = 0,808 con intensidad condicional moderada (0,214 gl/min): la polarización y el espacio amplio generan muchas situaciones de golpeo, pero la agrupación grande limita la frecuencia por jugador. El escenario #4 (amplio + pequeño + no polarizado + desequilibrio) alcanza E[Y] = 0,171 a través de una P(Y > 0) = 0,604 con la segunda mayor intensidad condicional del ranking (0,283 gl/min): sin portería, muchas fases no generan ningún golpeo potente, pero cuando lo hacen, la agrupación pequeña concentra la frecuencia individual. Dos escenarios numéricamente equivalentes, mecanísticamente antagónicos. Esta equivalencia numérica que oculta procesos generadores distintos solo es visible gracias al modelo hurdle; un GLMM Gamma simple o una media bruta colapsaría ambos escenarios en el mismo valor sin revelar la diferencia cualitativa.

**La Polaridad domina el ranking de P(Y>0), y la Agrupación domina el ranking de E[Y|Y>0].** Los seis escenarios con mayor P(Y > 0) son todos polarizados. En el ranking de E[Y | Y > 0], los valores más altos corresponden sistemáticamente a agrupaciones pequeñas. Esta segregación de influencias confirma los perfiles identificados en los coeficientes: polaridad como interruptor, agrupación como modulador.

### 5.3. Implicaciones prácticas del ranking

Para el entrenador que diseña tareas en el fútbol formativo, el ranking ofrece orientaciones concretas:

- **Para maximizar golpeos potentes**: la combinación óptima es espacio amplio + grupos pequeños + tarea polarizada (escenario #1). Esta combinación genera un 83% de probabilidad de que ocurra al menos un golpeo, con una tasa condicional de 0,292 golpeos/min — aproximadamente 2,3 golpeos en una fase de 10 minutos.
- **Para priorizar la participación universal en el golpeo** (que todos los jugadores tengan oportunidades): el escenario #6 (amplio + grande + polarizado + equilibrio) maximiza P(Y > 0) = 0,827, aunque con menor intensidad por jugador.
- **Para minimizar golpeos potentes** y enfocar la tarea en otros aspectos técnico-tácticos: la combinación reducido + grande + no polarizado (escenarios #15 y #16) produce la tasa más baja, apenas 0,9 golpeos por cada 10 minutos.

---

## 6. Predicciones vs. medias observadas: por qué el GLMM añade valor

Las métricas de ajuste del modelo sobre las 16 celdas factoriales fueron: MAE = 0,082 golpeos/min, MAPE = 24,6% y correlación de Pearson entre medias observadas y predicciones = 0,41. Estos valores sitúan el ajuste como *moderado*, lo que requiere una interpretación cuidadosa.

Las celdas con mayor error absoluto son aquellas con menor tamaño muestral. El escenario #4 (amplio + pequeño + no polarizado + desequilibrio; N = 23) presenta una media observada de 0,707 golpeos/min frente a una predicción de 0,171 (error relativo: −75,8%). El escenario #5 (N = 23) muestra un error similar (−71,6%). En contraste, el escenario #6 (N = 1.461) presenta un error de apenas +6,0%, y otras celdas con N elevado convergen igualmente hacia errores mínimos.

Este patrón —grandes errores en celdas pequeñas, errores mínimos en celdas grandes— no indica un modelo deficiente, sino todo lo contrario: ilustra precisamente el valor estadístico del GLMM frente a las medias brutas.

Con N = 23 observaciones de una variable con el 29,5% de ceros y un coeficiente de variación residual del 68%, la **media muestral observada** es una estadística altamente inestable. Bajo variabilidad extrema y tamaño muestral reducido, el intervalo de confianza de esa media podría extenderse fácilmente de 0,3 a 1,1 golpeos/min — un rango que abarca la mayor parte del ranking de predicciones. Esa media no representa el parámetro poblacional de la celda; representa la fluctuación aleatoria de 23 fases en una combinación táctica poco frecuente.

La **predicción del modelo** es cualitativamente distinta. El GLMM pondera la información de las 16 celdas simultáneamente, aprovechando la estructura factorial completa. **Para una celda poco representada, el modelo "toma prestada" información de celdas relacionadas (misma polaridad, mismo espacio), regularizando la estimación hacia valores más plausibles según la estructura global. Adicionalmente, incorpora la distribución correcta de la variable (Hurdle Gamma), el efecto de jugador como fuente de variabilidad controlada, y el enlace logarítmico que respeta las restricciones del espacio de parámetros.**

La correlación de 0,41 entre medias observadas y predicciones es completamente coherente con el R² global del modelo (16,6%): cuando la mayor parte de la varianza es residual —intrínsecamente no predecible por el diseño táctico—, no es posible ni deseable esperar correlaciones próximas a 1,0. Lo importante es que allí donde el modelo tiene información suficiente (N grande), acierta con precisión; y allí donde no la tiene (N pequeño), proporciona una estimación regularizada más honesta que la media bruta.

Una última distinción fundamental: la media observada es una estadística descriptiva de los datos disponibles. La predicción del modelo es una estimación del **valor esperado poblacional** para un jugador con capacidad de golpeo promedio en esa combinación táctica. Para cualquier combinación no observada en los datos o para un nuevo contexto de aplicación, solo el modelo puede generar una predicción fundamentada.

---

## 7. Medias marginales estimadas (EMMs)

### 7.1. Lógica de las EMMs en un modelo Hurdle

Las EMMs en un modelo Hurdle Gamma presentan una complejidad adicional respecto a modelos con un único componente: cada factor debe evaluarse simultáneamente en tres planos —P(Y > 0), E[Y | Y > 0] y E[Y]—. El cálculo consiste en marginalizar la predicción de cada nivel del factor de interés sobre todos los niveles de los restantes factores con ponderación uniforme (media sobre 8 combinaciones de los demás factores), manteniendo el intercepto aleatorio en cero (jugador promedio). Los HDI al 95% se obtienen directamente de la distribución posterior de cada EMM.

La utilidad de las EMMs reside en traducir los coeficientes —expresados en escala logit y log— a las dos preguntas clínicas naturales para un técnico deportivo: *¿con qué probabilidad aparece un golpeo potente en esta condición?* y *¿con qué frecuencia ocurre, una vez que aparece?*

### 7.2. EMMs por factor (efectos principales)

**Tabla 5.** EMMs generales por variable de diseño, en los tres planos del modelo Hurdle.

| Factor | Nivel | P(Y>0) | HDI 95% | E[Y|Y>0] | HDI 95% | E[Y] | HDI 95% |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Espacio | Amplio | 0,721 | [0,687; 0,754] | 0,243 | [0,227; 0,257] | 0,175 | [0,161; 0,188] |
| Espacio | Reducido | 0,598 | [0,567; 0,630] | 0,206 | [0,196; 0,219] | 0,123 | [0,114; 0,132] |
| Agrupación | Grande | 0,678 | [0,646; 0,711] | 0,190 | [0,179; 0,201] | 0,130 | [0,121; 0,140] |
| Agrupación | Pequeño | 0,642 | [0,607; 0,674] | 0,259 | [0,244; 0,275] | 0,168 | [0,155; 0,181] |
| Polaridad | No polarizado | 0,569 | [0,527; 0,608] | 0,221 | [0,205; 0,238] | 0,126 | [0,113; 0,140] |
| Polaridad | Polarizado | 0,751 | [0,724; 0,777] | 0,228 | [0,216; 0,240] | 0,171 | [0,160; 0,182] |
| Equilibrio | Desequilibrio | 0,646 | [0,609; 0,681] | 0,231 | [0,215; 0,247] | 0,150 | [0,137; 0,163] |
| Equilibrio | Equilibrio | 0,673 | [0,641; 0,705] | 0,218 | [0,207; 0,232] | 0,148 | [0,137; 0,159] |

Los perfiles de cada factor quedan confirmados y matizan la lectura de los coeficientes:

**Agrupación: el trade-off visible en las EMMs.** La agrupación pequeña tiene P(Y > 0) = 0,642, *inferior* a la grande (0,678). Sin embargo, su E[Y | Y > 0] = 0,259 es sustancialmente mayor que la de la grande (0,190) — una diferencia de +36% que persiste tras la marginalización sobre los demás factores. En consecuencia, la E[Y] favorable a pequeño (0,168 vs 0,130; ΔE[Y] = +0,038) está impulsada *exclusivamente* por la intensidad condicional, no por la probabilidad. Esta distinción es relevante para la planificación del entrenamiento: si el objetivo es que *más jugadores* tengan al menos una situación de golpeo potente en la fase, los grupos grandes son preferibles; si el objetivo es que cada jugador *acumule más golpeos* una vez que tiene la oportunidad, los grupos pequeños son superiores.

**Polaridad: el interruptor más potente.** La diferencia en P(Y > 0) entre polarizado (0,751) y no polarizado (0,569) es de 0,182 puntos —la mayor diferencia entre niveles de cualquier factor en la parte ψ—. Esto confirma que la presencia o ausencia de portería como objetivo directo es el principal modulador de la probabilidad de que se produzca un golpeo potente. En contraste, la diferencia en E[Y | Y > 0] es prácticamente nula (0,228 vs 0,221; diferencia de 0,007 gl/min, con HDIs ampliamente solapados). El efecto total sobre E[Y] (0,171 vs 0,126; Δ = +0,045) es casi enteramente atribuible al componente ψ, una vez más confirmando el perfil de interruptor puro.

**Espacio: efecto simétrico y consistente.** El espacio amplio supera al reducido tanto en P(Y > 0) (0,721 vs 0,598; Δ = 0,123) como en E[Y | Y > 0] (0,243 vs 0,206; ratio = 1,18). Ambas diferencias tienen HDIs que excluyen el valor nulo. El espacio es el único factor cuya influencia se extiende de forma coherente y creíble a los dos componentes del hurdle, con la misma dirección: amplio es superior al reducido en ambas dimensiones.

**Equilibrio: ausencia de efecto confirmada.** La condición de equilibrio muestra una P(Y > 0) ligeramente mayor (0,673 > 0,646) y una E[Y | Y > 0] ligeramente menor (0,218 < 0,231) que el desequilibrio, pero ambas diferencias tienen HDIs que incluyen holgadamente el cero. El E[Y] resultante es prácticamente idéntico (0,148 vs 0,150). La distribución numérica entre equipos no constituye un factor de diseño relevante para el golpeo potente.

### 7.3. Contrastes pairwise

**Tabla 6.** Síntesis de contrastes pairwise en los tres planos.

| Variable | Δψ (prob) | Creíble ψ | Ratio μ | Creíble μ | ΔE[Y] | Creíble E[Y] |
| --- | --- | --- | --- | --- | --- | --- |
| Espacio: reducido − amplio | −0,122 | Sí | 0,85 | Sí | −0,051 | Sí |
| Agrupación: pequeño − grande | −0,036 | No | 1,36 | Sí | +0,038 | Sí |
| Polaridad: Pol − NoPol | +0,182 | Sí | 1,03 | No | +0,045 | Sí |
| Equilibrio: Equil − Deseq | +0,027 | No | 0,95 | No | −0,002 | No |

Los cuatro factores exhiben perfiles completamente distintos en los contrastes pairwise, lo que permite establecer una taxonomía funcional de los efectos de diseño sobre el golpeo potente:

- **Polaridad = interruptor puro**: efecto creíble en ψ, efecto no creíble en μ.
- **Espacio = efecto bifactorial**: efectos creíbles en ψ y en μ, misma dirección.
- **Agrupación = modulador puro**: efecto no creíble en ψ, efecto creíble en μ.
- **Equilibrio = sin efecto**: no creíble en ninguno de los dos componentes.

Esta taxonomía constituye en sí misma un resultado conceptualmente relevante: las variables de diseño táctico no son funcionalmente equivalentes en su influencia sobre el golpeo potente, sino que operan a través de mecanismos distintos. Un análisis que redujera todo a un único índice —como la E[Y] marginal— colapsaría perfiles cualitativamente diferentes en un único número, perdiendo la información más valiosa para la planificación del entrenamiento.

### 7.4. EMMs de interacciones de segundo orden

Las EMMs de las combinaciones de dos factores permiten verificar si los efectos de diseño se combinan de forma consistente y no interactiva, tal como asume el modelo M1.

**Tabla 7.** EMMs Espacio × Polaridad.

| Espacio | Polaridad | P(Y>0) | E[Y|Y>0] | E[Y] |
| --- | --- | --- | --- | --- |
| Amplio | Polarizado | 0,804 | 0,246 | 0,197 |
| Amplio | No polarizado | 0,638 | 0,239 | 0,152 |
| Reducido | Polarizado | 0,698 | 0,209 | 0,145 |
| Reducido | No polarizado | 0,499 | 0,203 | 0,101 |

**Tabla 8.** EMMs Espacio × Agrupación.

| Espacio | Agrupación | P(Y>0) | E[Y|Y>0] | E[Y] |
| --- | --- | --- | --- | --- |
| Amplio | Pequeño | 0,704 | 0,280 | 0,198 |
| Amplio | Grande | 0,737 | 0,206 | 0,152 |
| Reducido | Pequeño | 0,579 | 0,238 | 0,138 |
| Reducido | Grande | 0,618 | 0,175 | 0,108 |

**Tabla 9.** EMMs Agrupación × Polaridad.

| Agrupación | Polaridad | P(Y>0) | E[Y|Y>0] | E[Y] |
| --- | --- | --- | --- | --- |
| Pequeño | Polarizado | 0,735 | 0,263 | 0,194 |
| Grande | Polarizado | 0,766 | 0,193 | 0,149 |
| Pequeño | No polarizado | 0,548 | 0,255 | 0,141 |
| Grande | No polarizado | 0,589 | 0,187 | 0,111 |

El patrón clave en las tres tablas es coherente con la taxonomía establecida: la Polaridad y el Espacio gobiernan el componente P(Y > 0) —las celdas con mayor probabilidad son sistemáticamente aquellas con polaridad y espacio amplio—, mientras que la Agrupación pequeña gobierna el componente E[Y | Y > 0] —los valores más altos de intensidad condicional corresponden sistemáticamente a grupos pequeños, con independencia de qué nivel tengan los otros factores—.

La combinación óptima (amplio + pequeño + polarizado) concentra los tres mecanismos favorables simultáneamente: la probabilidad alta aportada por el espacio y la polarización, y la intensidad condicional alta aportada por la agrupación. Sin embargo, es importante notar que la ganancia no es simplemente aditiva en escala natural: el espacio y la polarización mejoran P(Y > 0) por mecanismos distintos (físico-espacial y de contexto de juego, respectivamente), mientras la agrupación opera sobre E[Y | Y > 0] por un mecanismo de distribución de posesiones. Los tres efectos son independientes entre sí en el modelo M1 —lo que el ajuste LOO confirmó al no encontrar evidencia de interacciones—, pero sus contribuciones se combinan multiplicativamente en E[Y].

---

## 8. Análisis por GrupoEdad

*Nota metodológica: El análisis de esta sección se basa en el modelo M2, que añade GrupoEdad como efecto fijo a M1. Como se indicó en la sección de selección de modelos, M2 no fue elegido como modelo final —M1 tiene mejor capacidad predictiva según LOO-CV— debido a la redundancia estadística entre GrupoEdad y el intercepto aleatorio por jugador. Las estimaciones de M2 tienen valor descriptivo y permiten caracterizar los patrones por categoría, pero no deben interpretarse como superiores a las de M1 en términos inferenciales.*

### 8.1. EMMs por GrupoEdad: patrones globales

**Tabla 10.** EMMs por GrupoEdad en M2, marginalizadas sobre las 4 VI de diseño.

| GrupoEdad | P(Y>0) | HDI 95% | E[Y|Y>0] (gl/min) | HDI 95% |
| --- | --- | --- | --- | --- |
| Sénior Masculino | 0,767 | [0,729; 0,802] | 0,276 | [0,254; 0,300] |
| Juvenil | 0,757 | [0,719; 0,798] | 0,222 | [0,203; 0,241] |
| Cadete | 0,650 | [0,598; 0,702] | 0,194 | [0,174; 0,215] |
| Neskak | 0,560 | [0,516; 0,607] | 0,205 | [0,187; 0,223] |
| Infantil | 0,484 | [0,431; 0,542] | 0,214 | [0,190; 0,238] |

El Sénior Masculino lidera ambos componentes del hurdle: tiene la mayor P(Y > 0) (0,767) y la mayor E[Y | Y > 0] (0,276 gl/min). Este resultado es coherente con el mayor desarrollo técnico-físico de los jugadores adultos: mayor capacidad de crear situaciones de finalización *y* mayor potencia cuando las ejecutan. La cadena de causalidad tiene plena coherencia biomecánica: años de especialización técnica se traducen en mayor eficiencia en la producción de golpeos potentes, tanto en términos de frecuencia de creación de oportunidades como de calidad de ejecución.

El resultado más llamativo, sin embargo, es la **paradoja de Neskak e Infantil**. Estas dos categorías presentan las menores probabilidades de golpeo del conjunto (Neskak: 0,560; Infantil: 0,484), lo que resulta esperable dado su menor desarrollo físico y la naturaleza de las tareas diseñadas para esas edades. Sin embargo, su E[Y | Y > 0] —0,205 y 0,214 gl/min respectivamente— es *mayor* que la de Cadete (0,194). Esto significa que cuando Neskak e Infantil logran ejecutar un golpeo potente, lo hacen con mayor frecuencia por minuto que las categorías cadetes, aunque lleguen a esa situación con menor probabilidad.

¿Cómo interpretar este cruce de rankings? Una posible explicación reside en la naturaleza de las tareas: las sesiones para Infantil y Neskak pueden incluir ejercicios de golpeo específico —práctica de tiro, remates, centros— que concentran varios golpeos potentes en una misma fase cuando estas se activan, generando una mayor tasa condicional por unidad de tiempo. En cadete, los diseños tácticos pueden ser más complejos y orientados al juego colectivo, con golpeos potentes más distribuidos y menos concentrados. Este resultado solo es detectable mediante el modelo hurdle: un análisis de la media bruta de golpeos/min aplastaría la distinción entre P(Y > 0) y E[Y | Y > 0], oscureciendo un patrón de desarrollo por categorías cualitativamente relevante.

Los rankings según cada componente son distintos. Por P(Y > 0): Sénior > Juvenil > Cadete > Neskak > Infantil — una jerarquía perfectamente ordenada por nivel de desarrollo. Por E[Y | Y > 0]: Sénior > Juvenil > Infantil > Neskak > Cadete — donde la categoría Cadete, a pesar de estar en tercer lugar por probabilidad, ocupa el último puesto en intensidad condicional. La coexistencia de rankings diferentes en los dos componentes es un resultado propio del modelo de dos partes, inaccesible para ningún análisis de una sola ecuación.

### 8.2. Los 16 escenarios factoriales por GrupoEdad

**Tabla 11.** P(Y>0) y E[Y|Y>0] para cada combinación factorial y cada GrupoEdad (ordenados por P descendente).

| # | Escenario | ↑ | SrM P | SrM E|>0 | Juv P | Juv E|>0 | Cad P | Cad E|>0 | Nesk P | Nesk E|>0 | Inf P | Inf E|>0 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Pol·Amp·Gra·Equil | 2 | 0,899 | 0,253 | 0,894 | 0,203 | 0,829 | 0,177 | 0,764 | 0,187 | 0,700 | 0,196 |
| 2 | Pol·Amp·Gra·Deseq | 3 | 0,886 | 0,264 | 0,880 | 0,212 | 0,810 | 0,185 | 0,740 | 0,195 | 0,672 | 0,204 |
| 3 | Pol·Amp·Peq·Equil | 3 | 0,869 | 0,340 | 0,862 | 0,273 | 0,783 | 0,239 | 0,707 | 0,252 | 0,635 | 0,264 |
| 4 | Pol·Amp·Peq·Deseq | 4 | 0,853 | 0,355 | 0,846 | 0,285 | 0,761 | 0,249 | 0,680 | 0,263 | 0,605 | 0,275 |
| 5 | Pol·Red·Gra·Equil | 1 | 0,837 | 0,214 | 0,829 | 0,172 | 0,737 | 0,150 | 0,652 | 0,159 | 0,575 | 0,166 |
| 6 | Pol·Red·Gra·Deseq | 2 | 0,818 | 0,223 | 0,810 | 0,180 | 0,711 | 0,157 | 0,622 | 0,166 | 0,544 | 0,173 |
| 7 | Pol·Red·Peq·Equil | 2 | 0,793 | 0,288 | 0,784 | 0,232 | 0,677 | 0,202 | 0,583 | 0,214 | 0,503 | 0,223 |
| 8 | NoPol·Amp·Gra·Equil | 1 | 0,788 | 0,247 | 0,779 | 0,198 | 0,670 | 0,173 | 0,576 | 0,183 | 0,495 | 0,191 |
| 9 | Pol·Red·Peq·Deseq | 3 | 0,772 | 0,301 | 0,762 | 0,242 | 0,649 | 0,211 | 0,552 | 0,223 | 0,471 | 0,233 |
| 10 | NoPol·Amp·Gra·Deseq | 2 | 0,766 | 0,257 | 0,756 | 0,207 | 0,642 | 0,181 | 0,545 | 0,191 | 0,464 | 0,199 |
| 11 | NoPol·Amp·Peq·Equil | 2 | 0,735 | 0,332 | 0,725 | 0,267 | 0,603 | 0,233 | 0,504 | 0,246 | 0,424 | 0,257 |
| 12 | NoPol·Amp·Peq·Deseq | 3 | 0,710 | 0,346 | 0,699 | 0,278 | 0,573 | 0,243 | 0,472 | 0,257 | 0,393 | 0,268 |
| 13 | NoPol·Red·Gra·Equil | 0 | 0,683 | 0,209 | 0,671 | 0,168 | 0,541 | 0,147 | 0,441 | 0,155 | 0,363 | 0,162 |
| 14 | NoPol·Red·Gra·Deseq | 1 | 0,655 | 0,218 | 0,643 | 0,175 | 0,510 | 0,153 | 0,410 | 0,162 | 0,334 | 0,169 |
| 15 | NoPol·Red·Peq·Equil | 1 | 0,617 | 0,282 | 0,604 | 0,226 | 0,469 | 0,198 | 0,371 | 0,209 | 0,299 | 0,218 |
| 16 | NoPol·Red·Peq·Deseq | 2 | 0,587 | 0,293 | 0,574 | 0,236 | 0,437 | 0,206 | 0,342 | 0,217 | 0,273 | 0,227 |

*(Leyenda: Pol = Polarizado, NoPol = No polarizado, Amp = Amplio, Red = Reducido, Peq = Pequeño, Gra = Grande, Deseq = Desequilibrio, Equil = Equilibrio. ↑ = número de factores en nivel "facilitador".)*

### 8.3. Invarianza de los efectos entre categorías

Este apartado presenta el resultado conceptualmente más importante del análisis por GrupoEdad: la demostración de que los efectos del diseño táctico sobre los golpeos potentes son **invariantes entre categorías** en términos relativos, pero de forma diferente según el componente del hurdle.

**Componente μ: invarianza total del porcentaje de cambio.**

Dado que el modelo M2 es aditivo en escala logarítmica (enlace log), el efecto de cualquier VI sobre E[Y | Y > 0] se expresa como un factor multiplicativo —exp(β_VI)— que *no depende* del nivel base de GrupoEdad. Matemáticamente, esto equivale a que el término de GrupoEdad se cancela cuando se calcula el cociente entre dos niveles de la VI:

$\frac{E[Y | Y>0, \text{VI}_A]}{E[Y | Y>0, \text{VI}B]} = \exp(\beta{\text{VI}A} - \beta{\text{VI}_B})$

independientemente de exp(β_GrupoEdad). En consecuencia, el porcentaje de cambio en E[Y | Y > 0] inducido por cada VI es *idéntico para las cinco categorías*:

| VI | % cambio E[Y|Y>0] (igual en todas las categorías) |
| --- | --- |
| Agrupación: pequeño vs grande | +34,6% |
| Espacio: amplio vs reducido | +18,0% |
| Equilibrio: deseq vs equil | +4,3% |
| Polaridad: pol vs no pol | +2,4% |

Sénior Masculino, Juvenil, Cadete, Neskak e Infantil experimentan exactamente el mismo incremento relativo del 34,6% en la tasa condicional de golpeos cuando se pasa de grupos grandes a grupos pequeños. Las diferencias absolutas entre categorías serán mayores para Sénior Masculino —que tiene una base más alta— y menores para Infantil, pero la proporción de cambio es invariante.

**Componente ψ: variabilidad del % de cambio debida a la no-linealidad logística.**

En la parte binaria, el modelo garantiza que los *odds ratios* son constantes entre categorías —el OR de Polaridad es 0,43 para todas—. Sin embargo, la *probabilidad* opera en escala [0,1] a través de la función logística, que es intrínsecamente no-lineal. Un OR constante produce efectos en escala de probabilidad que *varían* según el nivel base de P(Y > 0) de cada categoría:

| VI | Sénior Masc. (base 0,767) | Cadete (base 0,650) | Infantil (base 0,484) |
| --- | --- | --- | --- |
| Polaridad | +21,4% | +34,0% | +54,6% |
| Espacio | +12,9% | +19,8% | +30,5% |
| Agrupación | −6,3% | −9,1% | −13,1% |
| Equilibrio | −2,8% | −4,1% | −5,9% |

La interpretación es directa: la función logística es más pronunciada en torno a probabilidades intermedias que en los extremos. Categorías con probabilidad base más baja (Infantil: 0,484) operan en la región de mayor pendiente de la curva logística, donde un mismo cambio en el predictor lineal produce un mayor cambio en probabilidad. Categorías con probabilidad base alta (Sénior Masculino: 0,767) operan en la región de menor pendiente, donde el cambio en probabilidad se atenúa.

La consecuencia práctica es notable: para las categorías con menor P(Y > 0) base —Neskak e Infantil—, diseñar tareas polarizadas produce una ganancia *relativa* en probabilidad de golpeo considerablemente mayor que para el Sénior Masculino (+54,6% vs +21,4%). Estas son las categorías que más se "benefician", en términos de activación del interruptor, de un diseño táctico orientado a la finalización. Esta amplificación no refleja una interacción estadística entre GrupoEdad y Polaridad —el modelo M2 no contiene ese término—, sino la consecuencia algebraica de la no-linealidad logística sobre los efectos constantes en el predictor lineal.

### 8.4. Rango de variación entre escenarios por categoría

**Comparación entre escenarios extremos (#1 vs #16):**

| GrupoEdad | P #1 | P #16 | % cambio P(Y>0) | % cambio E[Y|Y>0] |
| --- | --- | --- | --- | --- |
| Sénior Masculino | 0,899 | 0,587 | +53,1% | −13,9% |
| Juvenil | 0,894 | 0,574 | +55,7% | −13,9% |
| Cadete | 0,829 | 0,437 | +89,4% | −13,9% |
| Neskak | 0,764 | 0,342 | +123,6% | −13,9% |
| Infantil | 0,700 | 0,273 | +156,5% | −13,9% |

El −13,9% en E[Y | Y > 0] es idéntico para las cinco categorías —consecuencia directa de la invarianza log-link ya descrita—. El contraste entre escenarios extremos tiene el mismo efecto relativo sobre la intensidad condicional de golpeo independientemente de la categoría.

El porcentaje de cambio en P(Y > 0), en cambio, es radicalmente distinto: Infantil muestra un rango de variación del 156,5% entre el mejor y el peor escenario, frente al 53,1% del Sénior Masculino. Para la categoría Infantil, la elección del diseño táctico puede más que duplicar o reducir a menos de un tercio la probabilidad de que un jugador ejecute un golpeo potente en la fase. Esta diferencia subraya que, para las categorías más jóvenes y con menor probabilidad base, las decisiones de diseño táctico tienen un impacto *relativo* sobre la activación del golpeo potente muy superior al que tienen sobre los jugadores más desarrollados.

**Comparación entre los cuatro mejores y los cuatro peores escenarios:**

| GrupoEdad | Top-4 P | Bot-4 P | ΔP | Top-4 E|>0 | Bot-4 E|>0 | ΔE|>0 |
| --- | --- | --- | --- | --- | --- | --- |
| Sénior Masculino | 0,877 | 0,636 | +0,241 | 0,303 | 0,251 | +0,052 |
| Juvenil | 0,871 | 0,623 | +0,248 | 0,243 | 0,201 | +0,042 |
| Cadete | 0,796 | 0,489 | +0,306 | 0,213 | 0,176 | +0,037 |
| Neskak | 0,722 | 0,391 | +0,332 | 0,225 | 0,186 | +0,039 |
| Infantil | 0,653 | 0,317 | +0,336 | 0,235 | 0,194 | +0,041 |

Las diferencias absolutas en P(Y > 0) son mayores para Neskak e Infantil (0,332 y 0,336) que para el Sénior Masculino (0,241), lo que de nuevo refleja la amplificación logística en categorías con menor probabilidad base. Las diferencias absolutas en E[Y | Y > 0] son, inversamente, mayores para el Sénior Masculino (0,052) que para el Cadete (0,037): al tener un nivel base de intensidad condicional más alto, los mismos factores multiplicativos —constantes en porcentaje— producen diferencias absolutas mayores.

Dos escenarios merecen una mención especial para la orientación práctica del entrenador. El escenario #1 (Pol·Amp·Gra·Equil) maximiza P(Y > 0) para todas las categorías —en Sénior Masculino alcanza 0,899, prácticamente garantizando la participación en un golpeo potente—, pero su E[Y | Y > 0] es menor que en los escenarios con agrupación pequeña. El escenario #4 (Pol·Amp·Peq·Deseq) maximiza E[Y | Y > 0] para todas las categorías —en Sénior Masculino alcanza 0,355 gl/min—, a costa de una P(Y > 0) algo menor. Esta dualidad refleja, a escala de GrupoEdad, el mismo trade-off que se observó en los coeficientes globales: el entrenador que prioriza la participación universal en el golpeo potente ("que todos tengan al menos una situación") elegirá el escenario #1; el que prioriza la acumulación de repeticiones de golpeo para los que participan ("que golpeen muchas veces los que golpean") elegirá el escenario #4.

---

## Síntesis del análisis de Golpeos +15 m/s / min

El análisis GLMM Hurdle Gamma bayesiano de los golpeos potentes por minuto produce cuatro conclusiones principales que articulan los hallazgos de este capítulo:

**Primera: el diseño táctico explica poco de la varianza total (R² marginal = 3%), pero opera de forma diferenciada.** El golpeo potente es la variable dependiente de este TFG más resistente al diseño táctico: las diferencias individuales (11,4%) y la variabilidad residual (85,6%) dominan ampliamente sobre el sistema de la tarea. Esto no es un fracaso del modelo ni del diseño, sino una propiedad sustantiva de la variable: los golpeos a ≥15 m/s son decisiones oportunistas emergentes del juego que el entrenamiento puede facilitar pero no programar. En contraste con *Toques/min*, donde el diseño explicaba el 20,8% de la varianza, este resultado subraya que no todas las variables de rendimiento técnico-físico son igualmente sensibles a la manipulación táctica del contexto de práctica.

**Segunda: el modelo hurdle revela una taxonomía funcional de los factores.** La Polaridad actúa como interruptor puro (ψ sí, μ no), el Espacio como factor bifactorial (ψ y μ, misma dirección), la Agrupación como modulador puro con signo cruzado (ψ no, μ sí), y el Equilibrio sin efecto en ningún componente. Esta taxonomía —invisible para cualquier modelo de una sola ecuación— permite caracterizar cada factor de diseño por el mecanismo a través del cual influye sobre el golpeo potente, una información cualitativamente más rica que cualquier índice de tamaño del efecto global.

**Tercera: el rango de predicción entre escenarios extremos (2,55×) refleja una modulación real pero moderada.** La combinación óptima (espacio amplio + agrupación pequeña + tarea polarizada) puede más que duplicar la tasa esperada de golpeos potentes respecto a la combinación mínima (espacio reducido + grupo grande + no polarizado). Este margen de actuación es real y relevante para la planificación del entrenamiento, aunque menor que el observado en *Toques/min*.

**Cuarta: los patrones por GrupoEdad confirman la invarianza relativa de los efectos de diseño, con amplificación logística para las categorías con menor probabilidad base.** Las categorías más jóvenes y la sección femenina, con probabilidades base de golpeo más bajas, son las que más se benefician, en términos relativos, del diseño polarizado. La paradoja de Neskak e Infantil —menor P(Y > 0) pero mayor E[Y | Y > 0] que Cadete— es un hallazgo contraintuitivo que solo el modelo de dos componentes permite detectar y que sugiere diferencias en la estructura de las sesiones de entrenamiento entre categorías que merecería investigación adicional.