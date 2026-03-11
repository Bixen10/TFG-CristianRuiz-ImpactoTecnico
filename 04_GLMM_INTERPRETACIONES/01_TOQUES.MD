## 4.1. Toques por minuto (*Total Touches / min*): análisis GLMM bayesiano

### 4.1.1. Selección del modelo

Para identificar la estructura de efectos fijos y aleatorios más adecuada para modelizar la tasa de toques por minuto, se ajustaron seis modelos lineales generalizados mixtos (GLMM) bayesianos con familia Gamma y función de enlace logarítmica, todos ellos con un intercepto aleatorio por jugador. Los modelos se construyeron con complejidad creciente, desde un modelo nulo (M0) hasta un modelo maximal con todas las interacciones de segundo orden (M5):

- **M0 (Nulo)**: únicamente intercepto y efecto aleatorio por jugador. Constituye la línea base sin predictores de diseño táctico.
- **M1 (Efectos principales)**: incorpora las cuatro variables independientes de diseño táctico —Espacio, Agrupación, Polaridad y Equilibrio— como efectos fijos aditivos.
- **M2 (+ GrupoEdad)**: añade la categoría de edad (GrupoEdad) como efecto fijo adicional sobre M1.
- **M3 (Interacción Espacio × Agrupación)**: extiende M2 con la interacción entre las dos variables de dimensión espacial de la tarea.
- **M4 (Moderación VI × GrupoEdad)**: sobre M3, incluye todas las interacciones entre las cuatro VI de diseño y GrupoEdad (16 términos adicionales), evaluando si la categoría de edad modera el efecto de las variables tácticas.
- **M5 (Maximal)**: incorpora todas las interacciones de segundo orden entre los cinco predictores fijos.

La comparación entre modelos se realizó mediante validación cruzada leave-one-out aproximada (LOO-CV) con suavizado de Pareto (PSIS-LOO), que estima la capacidad predictiva fuera de muestra de cada modelo. Los resultados se resumen en la Tabla 4.1.

**Tabla 4.1.** Comparación de modelos mediante LOO-CV (PSIS-LOO) para *Total Touches / min*.

| Modelo | Rango | ELPD | p_loo | ΔELPD | dSE | Peso stacking | Warning |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M1 | 0 | −7 056.0 | 184.0 | 0.0 | — | 0.884 | No |
| M3 | 1 | −7 110.9 | 163.0 | 54.8 | 10.4 | 0.000 | No |
| M2 | 2 | −7 126.1 | 161.7 | 70.1 | 10.2 | 0.000 | No |
| M4 | 3 | −7 314.6 | 226.6 | 258.6 | 26.0 | 0.116 | Sí |
| M0 | 4 | −7 716.4 | 169.7 | 660.4 | 32.0 | 0.000 | Sí |
| M5 | 5 | −9 186.3 | 385.5 | 2 130.3 | 54.8 | 0.000 | Sí |

El modelo M1 (efectos principales) se sitúa en primera posición con un ELPD claramente superior y concentra el 88.4 % del peso de *stacking*, lo que indica que, entre los seis candidatos, es el que mejor predice observaciones nuevas. Que el modelo de efectos principales supere a todos los modelos con interacciones tiene una implicación sustantiva importante: la relación entre las variables de diseño táctico y los toques por minuto es fundamentalmente **aditiva en la escala logarítmica**, lo que equivale a decir que los efectos son **multiplicativos en la escala original**. Dicho de otro modo, el efecto de cada variable de diseño opera de forma independiente, sin que la combinación de dos factores produzca sinergias o antagonismos que vayan más allá de la simple multiplicación de sus efectos individuales.

Resulta ilustrativo analizar por qué los modelos más complejos no mejoran a M1. El modelo M3, que incorpora la interacción Espacio × Agrupación, presenta un coeficiente creíble para dicho término (β = −0.103, con un HDI al 95 % que excluye el cero), lo que sugiere que, en la muestra observada, existe una ligera sinergia entre estas dos variables. Sin embargo, su ΔELPD de 54.8 puntos respecto a M1 indica que esta interacción no mejora la capacidad predictiva fuera de muestra, probablemente porque la ganancia en ajuste no compensa la penalización por complejidad adicional.

El caso de M2 es especialmente revelador. GrupoEdad empeora la predicción (ΔELPD = 70.1) a pesar de ser un predictor aparentemente relevante. Esto se explica por la estructura jerárquica de los datos: cada jugador pertenece a una única categoría de edad, de modo que GrupoEdad está completamente anidado dentro de PlayerId. El intercepto aleatorio por jugador ya captura las diferencias entre categorías de edad como parte de la variabilidad inter-jugador, haciendo redundante la inclusión explícita de GrupoEdad como efecto fijo.

Los modelos M4 y M5 presentan claros signos de sobreajuste: ambos generan advertencias (*warnings*) de Pareto-k elevado, indicando que la aproximación LOO-CV es inestable para algunas observaciones influyentes. Además, sus valores de p_loo (número efectivo de parámetros) están inflados (226.6 y 385.5, respectivamente), confirmando una complejidad excesiva que penaliza la generalización.

En consecuencia, **M1 se selecciona como modelo final** por parsimonia y superior capacidad predictiva. Este modelo captura la estructura esencial de la relación entre el diseño táctico y la actividad técnica del jugador mediante cuatro efectos fijos aditivos y un componente aleatorio por jugador.

---

### 4.1.2. Diagnósticos del modelo final

### 4.1.2.1. Convergencia y calidad del muestreo

El modelo M1 se ajustó con 4 cadenas MCMC de 2 000 iteraciones cada una (tras 1 000 de calentamiento), generando un total de 8 000 muestras posteriores. Los diagnósticos de convergencia son plenamente satisfactorios: el valor máximo de R̂ fue de 1.0035, muy por debajo del umbral recomendado de 1.01, lo que confirma que todas las cadenas convergieron al mismo espacio de la distribución posterior. El tamaño efectivo de muestra (ESS) mínimo fue de 3 015, garantizando una precisión adecuada en la estimación de los percentiles de las distribuciones posteriores. No se registraron divergencias durante el muestreo.

El chequeo predictivo posterior (PPC) confirmó que la familia Gamma con enlace logarítmico reproduce correctamente la asimetría positiva característica de la distribución de toques por minuto. El R² bayesiano del modelo fue de 0.4252 (DE = 0.009), un valor sustancial para datos de rendimiento deportivo, donde la variabilidad intra-individual inherente a la práctica futbolística limita el porcentaje explicable por cualquier modelo.

### 4.1.2.2. Descomposición de la varianza

Para cuantificar la contribución relativa de cada fuente de variabilidad, se realizó una descomposición de la varianza total del modelo en escala logarítmica siguiendo el enfoque de Nakagawa *et al.* (2017), adaptado para la familia Gamma. Esta descomposición permite responder a una pregunta fundamental para la práctica del entrenamiento: *¿qué importa más para la actividad técnica del jugador, el diseño de la tarea o la identidad del jugador?*

La Tabla 4.2 presenta la descomposición para el modelo final M1, que identifica tres fuentes de variabilidad.

**Tabla 4.2.** Descomposición de la varianza del modelo M1 para *Total Touches / min*.

| Componente | σ² | % del total | HDI 95 % |
| --- | --- | --- | --- |
| Diseño táctico (4 VI) | 0.1106 | 20.8 % | [18.8 %, 22.7 %] |
| Jugador (intercepto aleatorio) | 0.0534 | 10.0 % | [7.6 %, 12.2 %] |
| Residual (Gamma) | 0.3670 | 69.1 % | [66.5 %, 71.5 %] |
| **Total** | **0.5310** | **100 %** |  |

De esta descomposición se derivan dos indicadores clave de varianza explicada:

- **R² marginal** (solo efectos fijos de diseño táctico) = 0.2082. Las cuatro variables de diseño de la tarea explican el 20.8 % de la variabilidad total en los toques por minuto.
- **R² condicional** (diseño táctico + jugador) = 0.3087. Al incorporar las diferencias individuales entre jugadores, el modelo explica el 30.9 % de la variabilidad total.

El resultado más destacado es que **el diseño táctico de la tarea constituye la fuente de variabilidad controlable más importante**, explicando más del doble de varianza que las diferencias individuales entre jugadores (20.8 % frente a 10.0 %). Esta asimetría tiene una implicación directa para la práctica del entrenamiento: la manipulación del espacio, la agrupación, la polaridad y el equilibrio de una tarea tiene un impacto mayor sobre la actividad técnica que las diferencias inherentes entre jugadores de distinto nivel, posición o estilo de juego. En otras palabras, un diseño de tarea adecuado puede modificar la conducta técnica de los jugadores de forma más potente que las diferencias individuales que los separan.

El componente residual (69.1 %) representa la variabilidad intra-jugador no explicada por los factores del modelo: fluctuaciones debidas a la fatiga acumulada, el nivel de oposición del rival en cada tarea concreta, el rol táctico asumido en una fase particular, la motivación circunstancial o la propia estocasticidad del rendimiento deportivo. Este porcentaje es habitual y esperable en estudios de rendimiento en el deporte, donde la variabilidad de una misma persona entre sesiones y tareas es, por naturaleza, elevada.

### 4.1.2.3. Descomposición ampliada con GrupoEdad (M2 — análisis descriptivo)

Aunque M2 no fue seleccionado como modelo final (por su inferior capacidad predictiva respecto a M1), su descomposición de varianza ofrece una perspectiva descriptiva complementaria al separar explícitamente la contribución de la categoría de edad. La Tabla 4.3 presenta esta descomposición en cuatro componentes.

**Tabla 4.3.** Descomposición de la varianza del modelo M2 para *Total Touches / min*.

| Componente | σ² | % del total | HDI 95 % |
| --- | --- | --- | --- |
| Diseño táctico (4 VI) | 0.1123 | 21.0 % | [19.3 %, 22.9 %] |
| GrupoEdad | 0.0208 | 3.9 % | [2.5 %, 5.3 %] |
| Jugador (intercepto aleatorio) | 0.0340 | 6.4 % | [4.7 %, 8.1 %] |
| Residual (Gamma) | 0.3666 | 68.7 % | [66.2 %, 71.2 %] |

Los indicadores de varianza explicada de M2 son:

- R² marginal (solo diseño táctico) = 0.2105 (21.0 %).
- R² marginal (diseño táctico + GrupoEdad) = 0.2494 (24.9 %).
- R² condicional (efectos fijos + jugador) = 0.3130 (31.3 %).

La comparación entre ambas descomposiciones revela información valiosa sobre la estructura de las diferencias individuales. En M1, la varianza inter-jugador es de 0.0534 (10.0 % del total). En M2, al extraer explícitamente el efecto de GrupoEdad (σ² = 0.0208, 3.9 %), la varianza inter-jugador residual se reduce a 0.0340 (6.4 %). Esto significa que **aproximadamente el 40 % de la variabilidad atribuida en M1 a las diferencias entre jugadores se debe en realidad a diferencias sistemáticas entre categorías de edad**. Los jugadores de categorías inferiores (Infantil, Alevín) tienden a presentar tasas de toques más elevadas que los de categorías superiores (Juvenil, Senior), y esta diferencia, que M1 absorbe en el intercepto aleatorio, M2 la desglosa explícitamente.

Es importante señalar que la proporción de varianza atribuida al diseño táctico permanece esencialmente estable entre M1 (20.8 %) y M2 (21.0 %), lo que confirma la robustez de esta estimación: independientemente de cómo se modelen las diferencias individuales, las cuatro variables tácticas explican alrededor de un quinto de la variabilidad total. Asimismo, el residual se mantiene prácticamente invariable (~69 % en ambos modelos), lo que indica que la inclusión de GrupoEdad no captura variabilidad intra-jugador adicional, sino que únicamente redistribuye la varianza inter-jugador.

Que M2 no mejore a M1 en capacidad predictiva pese a explicar más varianza total (31.3 % frente a 30.9 %) confirma que GrupoEdad no aporta información nueva más allá de la ya contenida en el intercepto aleatorio. Dado que la pertenencia a un grupo de edad es una propiedad fija de cada jugador, el modelo M1 ya la capta implícitamente al estimar un intercepto específico para cada individuo. Desde un punto de vista práctico, esta estructura de anidamiento (PlayerId ⊂ GrupoEdad) hace que ambos enfoques sean equivalentes en términos predictivos, pero la descomposición de M2 resulta más informativa para comprender de dónde proceden las diferencias entre jugadores.

En síntesis, la descomposición de varianza establece una jerarquía clara de fuentes de variabilidad para los toques por minuto: el diseño de la tarea (~21 %) domina sobre las diferencias entre jugadores (~10 %), de las cuales casi la mitad (~4 %) se atribuye a la categoría de edad. Para el cuerpo técnico, la implicación práctica es directa: **la forma en que se diseña una tarea de entrenamiento importa más que quién participa en ella**.

---

### 4.1.3. Coeficientes del modelo final

El modelo M1 estima los efectos de las cuatro variables de diseño táctico como coeficientes en la escala logarítmica (β), cuyas categorías de referencia son: Espacio = amplio, Agrupación = grande, Polaridad = Polarizado y Equilibrio = Equilibrio. La interpretación de estos coeficientes se facilita mediante su exponenciación: exp(β) representa el factor multiplicativo por el cual se modifica la tasa esperada de toques por minuto al pasar de la categoría de referencia al nivel alternativo del factor. La Tabla 4.4 presenta los coeficientes estimados y sus intervalos de credibilidad.

**Tabla 4.4.** Coeficientes del modelo M1 para *Total Touches / min* (escala log y exponenciada).

| Efecto | β | exp(β) | HDI 95 % de exp(β) | Creíble |
| --- | --- | --- | --- | --- |
| Intercepto (condición de referencia) | +0.466 | 1.594 | [1.532, 1.658] | — |
| Espacio: reducido *vs.* amplio | +0.329 | 1.389 | [1.334, 1.446] | Sí |
| Agrupación: pequeño *vs.* grande | +0.231 | 1.260 | [1.209, 1.313] | Sí |
| Polaridad: no polarizado *vs.* polarizado | +0.370 | 1.448 | [1.380, 1.518] | Sí |
| Equilibrio: desequilibrio *vs.* equilibrio | +0.046 | 1.047 | [0.998, 1.098] | No |

El **intercepto** exponenciado (1.594 toques/min) representa la media esperada de toques por minuto para un jugador típico en la condición de referencia: espacio amplio, agrupación grande, polarizado y en equilibrio. Esta constituye la línea base a partir de la cual operan los efectos multiplicativos de cada factor.

La **Polaridad** emerge como el efecto más potente. Pasar de una tarea polarizada a una no polarizada multiplica la tasa de toques por 1.448, lo que equivale a un incremento del 44.8 %. En una tarea polarizada, la estructura del juego orienta a los jugadores hacia direcciones o zonas específicas del campo (por ejemplo, hacia una portería), concentrando la participación en determinados roles y reduciendo la frecuencia de contactos para una parte de los participantes. En cambio, las tareas no polarizadas (como los rondos o los juegos de conservación sin orientación a gol) distribuyen la participación de forma más homogénea, incrementando la frecuencia de contacto individual.

El **Espacio** ocupa la segunda posición en magnitud de efecto. Reducir el espacio de juego multiplica los toques por minuto por 1.389 (+38.9 %). Este resultado es coherente con la lógica del entrenamiento en fútbol: un espacio reducido acorta las distancias entre jugadores, incrementa la densidad de interacciones y obliga a decisiones más rápidas, lo que se traduce en una mayor frecuencia de contactos con el balón. En espacios amplios, los jugadores disponen de más tiempo y distancia, lo que reduce la urgencia del contacto y dilata los intervalos entre toques.

La **Agrupación** presenta el tercer efecto en importancia. Jugar en agrupaciones pequeñas (4 contra 4 o menos) multiplica los toques por 1.260 (+26.0 %) respecto a agrupaciones grandes (5 contra 5 o más). La explicación es aritmética y táctica a la vez: con menos jugadores, el balón se reparte entre un número menor de participantes, aumentando mecánicamente la frecuencia de contacto individual. Además, las agrupaciones pequeñas típicamente generan situaciones de juego más directas y con menos opciones de pase, lo que puede acelerar la circulación.

El **Equilibrio** es el único factor cuyo efecto no resulta creíble en términos bayesianos. Su exp(β) de 1.047 indica un incremento marginal del 4.7 % al pasar de equilibrio a desequilibrio, pero su HDI al 95 % en escala exponenciada ([0.998, 1.098]) incluye el valor 1.0 (ausencia de efecto). Esto significa que la distribución posterior del efecto es compatible con la hipótesis de que el equilibrio numérico de la tarea no afecta de forma relevante a la tasa de toques por minuto. Desde una perspectiva práctica, las situaciones de superioridad o inferioridad numérica no parecen alterar sustancialmente la frecuencia global de contactos con el balón.

Una propiedad fundamental del modelo, derivada de su estructura aditiva en escala logarítmica, es que los efectos de los distintos factores se combinan de forma **multiplicativa** en la escala original. Para obtener la predicción de cualquier combinación factorial, basta con multiplicar el intercepto exponenciado por los factores exp(β) correspondientes a los niveles seleccionados. Por ejemplo, la tasa esperada para una tarea en espacio reducido, agrupación pequeña, no polarizada y en desequilibrio sería: 1.594 × 1.389 × 1.260 × 1.448 × 1.047 = 4.22 toques/min. Esta propiedad multiplicativa constituye la base de las predicciones factoriales que se presentan en la siguiente sección.

Para el entrenador, estos coeficientes ofrecen una guía cuantitativa clara para la manipulación del diseño de tareas. Si el objetivo de una sesión es maximizar el volumen de contactos técnicos (por ejemplo, en una sesión de recuperación activa o de trabajo técnico con carga física reducida), la prioridad debe ser: en primer lugar, eliminar la polarización de la tarea; en segundo lugar, reducir el espacio; y en tercer lugar, reducir el número de jugadores por equipo. El equilibrio numérico, en cambio, puede determinarse libremente en función de otros objetivos pedagógicos sin que afecte significativamente a la tasa de toques.

---

### 4.1.4. Predicciones de las 16 combinaciones factoriales

La combinación de las cuatro variables de diseño táctico (cada una con dos niveles) genera 2⁴ = 16 escenarios posibles. Gracias a la propiedad multiplicativa del modelo, la predicción para cada escenario se obtiene sumando los coeficientes β correspondientes en escala logarítmica y exponenciando el resultado. La Tabla 4.5 presenta estas 16 combinaciones ordenadas de mayor a menor tasa de toques predicha, incluyendo la proyección a una tarea de 10 minutos para facilitar la interpretación práctica.

**Tabla 4.5.** Predicciones del modelo M1 para las 16 combinaciones factoriales de diseño, ordenadas de mayor a menor *Total Touches / min*.

| # | Espacio | Agrupación | Polaridad | Equilibrio | log(μ) | Toques/min | En 10 min |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Reducido | Pequeño | No polarizado | Desequilibrio | 1.441 | **4.22** | 42.2 |
| 2 | Reducido | Pequeño | No polarizado | Equilibrio | 1.395 | 4.03 | 40.3 |
| 3 | Reducido | Grande | No polarizado | Desequilibrio | 1.210 | 3.35 | 33.5 |
| 4 | Reducido | Grande | No polarizado | Equilibrio | 1.164 | 3.20 | 32.0 |
| 5 | Amplio | Pequeño | No polarizado | Desequilibrio | 1.112 | 3.04 | 30.4 |
| 6 | Reducido | Pequeño | Polarizado | Desequilibrio | 1.071 | 2.92 | 29.2 |
| 7 | Amplio | Pequeño | No polarizado | Equilibrio | 1.066 | 2.90 | 29.0 |
| 8 | Reducido | Pequeño | Polarizado | Equilibrio | 1.025 | 2.79 | 27.9 |
| 9 | Amplio | Grande | No polarizado | Desequilibrio | 0.882 | 2.41 | 24.1 |
| 10 | Reducido | Grande | Polarizado | Desequilibrio | 0.840 | 2.32 | 23.2 |
| 11 | Amplio | Grande | No polarizado | Equilibrio | 0.836 | 2.31 | 23.1 |
| 12 | Reducido | Grande | Polarizado | Equilibrio | 0.794 | 2.21 | 22.1 |
| 13 | Amplio | Pequeño | Polarizado | Desequilibrio | 0.742 | 2.10 | 21.0 |
| 14 | Amplio | Pequeño | Polarizado | Equilibrio | 0.696 | 2.01 | 20.1 |
| 15 | Amplio | Grande | Polarizado | Desequilibrio | 0.512 | 1.67 | 16.7 |
| 16 | Amplio | Grande | Polarizado | Equilibrio | 0.466 | **1.59** | 15.9 |

El dato más impactante de esta tabla es el **ratio entre los escenarios extremos**: la combinación #1 (reducido, pequeño, no polarizado, desequilibrio) predice 4.22 toques/min, frente a los 1.59 toques/min de la combinación #16 (amplio, grande, polarizado, equilibrio). Esto supone un factor multiplicativo de 2.65, es decir, que la manipulación del diseño táctico puede multiplicar por 2.65 la tasa de contactos técnicos. Traducido a una tarea de 10 minutos, la diferencia es de 42 toques frente a 16 toques: un jugador en el escenario óptimo acumula más del doble de contactos que en el escenario menos favorable.

El análisis del ranking revela patrones claros que confirman la jerarquía de efectos identificada en los coeficientes:

1. **La Polaridad domina el ordenamiento.** Los cuatro primeros escenarios son todos no polarizados, y los cuatro últimos son todos polarizados. Esto significa que, independientemente de cómo se configuren los demás factores, una tarea no polarizada siempre producirá más toques que una tarea polarizada con la misma configuración de espacio, agrupación y equilibrio.
2. **El Espacio actúa como segundo discriminador.** Dentro de los escenarios no polarizados, las posiciones #1 a #4 corresponden a espacio reducido, mientras que las posiciones #5 a #7 corresponden a espacio amplio. El patrón se replica en el bloque polarizado: los escenarios reducidos (#6, #8, #10, #12) se sitúan por encima de los amplio (#13, #14, #15, #16).
3. **La Agrupación opera como tercer factor de separación.** En igualdad de polaridad y espacio, las agrupaciones pequeñas siempre superan a las grandes (por ejemplo, #1 vs. #3, #6 vs. #10, #13 vs. #15).
4. **El Equilibrio genera diferencias marginales.** Dentro de cada par de escenarios que solo difieren en equilibrio (por ejemplo, #1 vs. #2, #3 vs. #4), la diferencia es de apenas 0.19 toques/min, consistente con la ausencia de un efecto creíble para esta variable.

La forma en que se calculan estas predicciones es transparente: cada valor es simplemente exp(Σβ), donde la suma incluye el intercepto y los coeficientes de los niveles correspondientes. Por ejemplo, para el escenario #1: exp(0.466 + 0.329 + 0.231 + 0.370 + 0.046) = exp(1.441) = 4.22. Esta transparencia permite al entrenador anticipar con precisión el efecto de cualquier modificación en el diseño de la tarea.

Para la planificación del entrenamiento, estas predicciones ofrecen una herramienta cuantitativa valiosa. Si el objetivo de una sesión es acumular un alto volumen de contactos técnicos (por ejemplo, para la mejora de la técnica individual o la recuperación activa con balón), los escenarios de la parte superior de la tabla (#1 a #4) son los más indicados, y todos comparten dos características: espacio reducido y ausencia de polarización. Si, por el contrario, el objetivo es simular condiciones de partido más realistas con menor densidad de contactos (por ejemplo, para trabajar la toma de decisiones bajo presión temporal real), los escenarios de la parte inferior (#13 a #16) son más apropiados. La tabla funciona así como un menú de diseño táctico que el entrenador puede consultar para calibrar la carga técnica de cada tarea.

---

### 4.1.5. Comparación entre predicciones del modelo y medias observadas

Una cuestión legítima que puede plantearse es: si las medias observadas por celda factorial están disponibles, ¿qué aporta el modelo GLMM frente a una simple tabla de medias? La Tabla 4.6 confronta las medias reales con las predicciones del modelo M1 para algunas combinaciones representativas.

**Tabla 4.6.** Comparación entre medias observadas y predicciones del modelo M1 para combinaciones factoriales seleccionadas.

| # | Combinación | Media real | Predicción M1 | Diferencia | Error % | N obs |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | Red+Peq+NoPol+Deseq | 3.81 | 4.22 | +0.41 | +10.7 % | 435 |
| 2 | Red+Peq+NoPol+Equil | 4.23 | 4.03 | −0.20 | −4.8 % | 122 |
| 5 | Amp+Peq+NoPol+Deseq | 6.26 | 3.04 | −3.22 | −51.4 % | 23 |
| 7 | Amp+Peq+NoPol+Equil | 5.33 | 2.90 | −2.43 | −45.6 % | 23 |
| 16 | Amp+Gra+Pol+Equil | 1.62 | 1.59 | −0.03 | −2.0 % | 1 461 |

Las métricas globales de ajuste arrojan un MAE (error absoluto medio) de 0.514 toques/min, un MAPE (error porcentual absoluto medio) del 11.9 % y una correlación de Pearson entre predicciones y medias observadas de r = 0.6631.

La respuesta a por qué un GLMM es preferible a las medias observadas radica en tres argumentos fundamentales:

**Primero, el desbalanceo muestral.** El número de observaciones por celda factorial oscila drásticamente, desde apenas 23 observaciones en las combinaciones #5 y #7 hasta 1 461 en la combinación #16. Las medias observadas de celdas con pocas observaciones son inherentemente inestables: están fuertemente influidas por los jugadores concretos que participaron en esas tareas y por la variabilidad aleatoria de cada registro. No es casualidad que las mayores discrepancias entre el modelo y las medias observadas se concentren precisamente en las celdas con N = 23 (errores del 45-51 %), mientras que las celdas con N > 300 muestran errores inferiores al 5 %. La media observada de 6.26 toques/min en la combinación #5 (Amplio + Pequeño + NoPolarizado + Desequilibrio, N = 23) probablemente refleja la composición particular de los jugadores que participaron en esas escasas tareas más que una propiedad generalizable del diseño.

**Segundo, el control de las diferencias individuales.** Las medias observadas por celda amalgaman jugadores de distintas categorías de edad, niveles de habilidad y roles tácticos, sin controlar por estas fuentes de variación. Si una celda está poblada desproporcionadamente por jugadores de categoría Infantil (que, como se verá, presentan tasas de toques más altas), su media aparecerá inflada respecto a lo que cabría esperar si la composición fuese equilibrada. El GLMM, mediante el intercepto aleatorio por jugador, estima el efecto de cada nivel factorial controlando por las diferencias individuales, proporcionando una estimación del efecto **poblacional** del diseño táctico.

**Tercero, la ponderación de la incertidumbre.** El GLMM proporciona no solo estimaciones puntuales sino distribuciones posteriores completas con intervalos de credibilidad, que reflejan el grado de certeza sobre cada estimación. Las medias observadas, en cambio, carecen de esta estructura probabilística y pueden inducir a conclusiones erróneas cuando se basan en pocas observaciones.

En definitiva, la predicción del modelo debe interpretarse como una **estimación poblacional**: la tasa de toques por minuto que cabría esperar para un jugador típico (es decir, cuyo intercepto aleatorio sea cero, representando la tendencia central de la población) en una determinada combinación de factores tácticos. No pretende replicar la media muestral de cada celda, sino estimar el efecto subyacente del diseño descontaminado de las particularidades de la muestra. La correlación de 0.66 entre predicciones y medias observadas es razonable dado que el modelo no incluye interacciones y las celdas con tamaños extremadamente pequeños generan medias muestrales inestables.

---

### 4.1.6. Medias marginales estimadas (EMMs)

### 4.1.6.1. Concepto y cálculo de las EMMs

Las medias marginales estimadas (EMMs, por sus siglas en inglés: *Estimated Marginal Means*) constituyen una herramienta de interpretación que traduce los coeficientes del modelo —expresados en la escala logarítmica y difíciles de interpretar directamente— a la escala que resulta relevante para la práctica: **toques por minuto esperados** para cada nivel de un factor.

Una EMM se define como la media predicha por el modelo para un nivel determinado de un factor, **promediando (marginalizando) sobre todos los niveles de los demás factores con ponderación uniforme**. Esto las diferencia de las medias observadas en tres aspectos fundamentales: (a) están ajustadas por el modelo, incorporando el control por efectos aleatorios; (b) utilizan ponderación uniforme sobre los niveles de los factores marginalizados, de modo que no dependen de las frecuencias desiguales presentes en el dataset; y (c) incluyen la incertidumbre completa de la estimación, expresada mediante el HDI al 95 %.

El procedimiento de cálculo es el siguiente: para cada muestra de la distribución posterior, se calcula la predicción para todas las combinaciones de los factores marginalizados, se exponencia (para pasar a la escala natural de toques/min) y se promedia. De la distribución resultante de las 8 000 muestras se extrae la media puntual y el HDI al 95 %. Este enfoque bayesiano garantiza que la incertidumbre sobre los parámetros del modelo se propague correctamente a las EMMs.

### 4.1.6.2. EMMs generales del modelo M1

La Tabla 4.7 presenta las EMMs por variable de diseño, comparando el nivel que maximiza los toques (nivel favorable) con el nivel que los minimiza (nivel desfavorable).

**Tabla 4.7.** Medias marginales estimadas por variable de diseño (modelo M1).

| Factor | Nivel favorable | Nivel desfavorable | Δ absoluta | Ratio |
| --- | --- | --- | --- | --- |
| Polaridad | NoPolarizado: 3.186 | Polarizado: 2.201 | +0.985 | 1.45× |
| Espacio | Reducido: 3.132 | Amplio: 2.255 | +0.877 | 1.39× |
| Agrupación | Pequeño: 3.003 | Grande: 2.385 | +0.618 | 1.26× |
| Equilibrio | Desequilibrio: 2.756 | Equilibrio: 2.632 | +0.124 | 1.05× |

Estas EMMs proporcionan la interpretación más directa y accesible de los efectos del modelo. Un jugador típico en una tarea no polarizada acumula, en promedio, 3.19 toques por minuto, frente a los 2.20 de una tarea polarizada: una diferencia de prácticamente un toque adicional por minuto. En una tarea de 10 minutos, esta diferencia se traduce en aproximadamente 10 contactos técnicos adicionales, un volumen sustancial desde la perspectiva del entrenamiento de la técnica individual.

La EMM del espacio reducido (3.13 toques/min) frente al amplio (2.26 toques/min) refleja una diferencia de 0.88 toques/min, coherente con la noción de que los espacios más compactos generan interacciones más frecuentes. En la práctica, esto justifica el uso de rondos y juegos de posición en espacios reducidos cuando el objetivo es maximizar el contacto con el balón.

La agrupación pequeña (3.00 toques/min) supera a la grande (2.39 toques/min) en 0.62 toques/min, lo que confirma la recomendación habitual en el entrenamiento de fútbol formativo de emplear formatos reducidos (3v3, 4v4) para incrementar la participación técnica individual.

El equilibrio muestra una diferencia de apenas 0.12 toques/min entre desequilibrio y equilibrio, consistente con su efecto no creíble en el modelo.

### 4.1.6.3. EMMs de interacciones de segundo orden (M1)

Aunque M1 es un modelo estrictamente aditivo en escala logarítmica (sin términos de interacción), las EMMs de combinaciones de dos factores revelan un patrón importante derivado de la exponenciación. Al transformar efectos aditivos en escala log a la escala natural, los efectos se vuelven multiplicativos, lo que implica que las **diferencias absolutas** entre niveles de un factor cambian según el contexto del otro factor, aunque el **ratio** sea constante.

La Tabla 4.8 ilustra este fenómeno con la combinación Espacio × Agrupación.

**Tabla 4.8.** EMMs de la interacción Espacio × Agrupación (modelo M1).

| Combinación | EMM (toques/min) | Δ Espacio | Δ Agrupación |
| --- | --- | --- | --- |
| Amplio + Grande | 1.996 | — | — |
| Amplio + Pequeño | 2.514 | — | +0.518 |
| Reducido + Grande | 2.773 | +0.777 | — |
| Reducido + Pequeño | 3.492 | +0.978 | +0.719 |

El efecto de pasar de espacio amplio a reducido es de +0.777 toques/min cuando la agrupación es grande, pero asciende a +0.978 toques/min cuando la agrupación ya es pequeña. Simétricamente, el efecto de reducir la agrupación es de +0.518 en espacio amplio y de +0.719 en espacio reducido. Aunque los ratios son idénticos en ambos casos (1.39× para el espacio y 1.26× para la agrupación), las ganancias absolutas son mayores cuando los demás factores ya se encuentran en su nivel favorable. Esto se debe a que, en un modelo multiplicativo, el incremento absoluto es proporcional a la base sobre la que opera.

La interacción Espacio × Polaridad exhibe el rango más amplio entre combinaciones de dos factores: de 1.843 toques/min (amplio + polarizado) a 3.705 (reducido + no polarizado), un ratio de 2.01×. Estas dos variables combinadas, por sí solas, duplican la tasa de toques.

La implicación práctica de esta propiedad multiplicativa es que **la acumulación de factores favorables produce ganancias progresivamente mayores en términos absolutos**. Cada factor adicional en su nivel óptimo no solo suma su efecto, sino que lo multiplica sobre una base ya incrementada. Esto incentiva la optimización simultánea de múltiples variables de diseño cuando el objetivo es maximizar la actividad técnica.

### 4.1.6.4. EMMs condicionales a la categoría de edad (M2 — análisis descriptivo)

Utilizando el modelo M2 con fines descriptivos, es posible calcular las EMMs condicionadas a cada categoría de edad, lo que permite cuantificar tanto las diferencias basales entre grupos como la magnitud absoluta del efecto de cada variable tácticas en función de la edad.

La Tabla 4.9 muestra las EMMs por Espacio × GrupoEdad como ejemplo representativo.

**Tabla 4.9.** EMMs por Espacio condicionadas a GrupoEdad (modelo M2).

| GrupoEdad | Amplio | Reducido |
| --- | --- | --- |
| Infantil | 2.897 | 3.964 |
| Neskak | 2.441 | 3.340 |
| Cadete | 2.437 | 3.334 |
| Juvenil | 2.096 | 2.868 |
| Senior Masc. | 1.880 | 2.572 |

La tabla muestra que todas las categorías de edad se benefician de la reducción del espacio, pero la magnitud absoluta de ese beneficio varía según la línea base de cada grupo. La Tabla 4.10 sistematiza las diferencias absolutas por variable de diseño y categoría de edad.

**Tabla 4.10.** EMM global y diferencias absolutas (Δ) por variable de diseño × GrupoEdad (modelo M2).

| GrupoEdad | EMM global | Δ Polaridad | Δ Espacio | Δ Agrupación | Δ Equilibrio |
| --- | --- | --- | --- | --- | --- |
| Infantil | 3.43 | 1.288 | 1.067 | 0.854 | 0.154 |
| Neskak | 2.89 | 1.085 | 0.899 | 0.719 | 0.130 |
| Cadete | 2.89 | 1.084 | 0.897 | 0.718 | 0.130 |
| Juvenil | 2.48 | 0.932 | 0.772 | 0.617 | 0.112 |
| Senior Masc. | 2.23 | 0.836 | 0.692 | 0.554 | 0.100 |

La categoría Infantil presenta la EMM global más alta (3.43 toques/min) y, en consecuencia, los mayores impactos absolutos en todas las variables de diseño. Senior Masculino, con la EMM global más baja (2.23 toques/min), exhibe los menores Δ absolutos. Neskak y Cadete son prácticamente indistinguibles en todos los indicadores.

### 4.1.6.5. EMM global por GrupoEdad (M2)

La Tabla 4.11 presenta las EMMs globales de cada categoría de edad, marginalizando sobre las cuatro variables de diseño táctico.

**Tabla 4.11.** Medias marginales estimadas globales por GrupoEdad (modelo M2).

| GrupoEdad | EMM (toques/min) | HDI 95 % |
| --- | --- | --- |
| Infantil | 3.431 | [3.187, 3.693] |
| Neskak | 2.890 | [2.711, 3.061] |
| Cadete | 2.885 | [2.674, 3.093] |
| Juvenil | 2.482 | [2.318, 2.635] |
| Senior Masc. | 2.226 | [2.089, 2.355] |

Los contrastes entre categorías revelan una estructura ordenada por maduración:

- **Infantil** supera a todas las demás categorías con una probabilidad posterior del 100 % en todos los contrastes. Los jugadores más jóvenes presentan la mayor tasa de toques por minuto, lo que podría reflejar tanto las dinámicas propias de las tareas adaptadas a estas edades como una mayor predisposición al contacto frecuente con el balón característica del juego en etapas formativas.
- **Neskak y Cadete** son prácticamente indistinguibles (Δ = −0.005 toques/min, P(Δ > 0) = 48.5 %), lo que indica que estas dos categorías comparten un nivel similar de actividad técnica.
- **Juvenil** supera a Senior Masculino de forma creíble (Δ = +0.256, P(Δ > 0) = 99.6 %).
- **Senior Masculino** es la categoría con la menor media esperada de toques, consistente con la tendencia general observada de que los jugadores de mayor nivel competitivo y maduración presentan tasas de contacto más bajas, posiblemente debido a un juego más pausado, posicional y con mayor tiempo de análisis previo al contacto.

---

### 4.1.7. Análisis por categoría de edad: fichas y comparación

### 4.1.7.1. Los 16 escenarios factoriales por GrupoEdad

Utilizando el modelo M2, se calcularon las predicciones para los 16 escenarios combinados de diseño de tarea condicionados a cada categoría de edad. La Tabla 4.12 presenta los escenarios extremos y representativos.

**Tabla 4.12.** Predicciones de los 16 escenarios factoriales por GrupoEdad (modelo M2, toques/min).

| # | Escenario | ↑ | Infantil | Neskak | Cadete | Juvenil | Senior Masc. |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | NoPol·Red·Peq·Deseq | 4 | 5.41 | 4.56 | 4.55 | 3.92 | 3.51 |
| 2 | NoPol·Red·Peq·Equil | 3 | 5.18 | 4.36 | 4.35 | 3.74 | 3.36 |
| 3 | NoPol·Red·Gra·Deseq | 3 | 4.30 | 3.62 | 3.61 | 3.11 | 2.79 |
| 4 | NoPol·Red·Gra·Equil | 2 | 4.11 | 3.46 | 3.46 | 2.97 | 2.67 |
| ... | ... | ... | ... | ... | ... | ... | ... |
| 15 | Pol·Amp·Gra·Deseq | 1 | 2.11 | 1.78 | 1.77 | 1.53 | 1.37 |
| 16 | Pol·Amp·Gra·Equil | 0 | 2.01 | 1.70 | 1.69 | 1.46 | 1.31 |

El dato más notable de esta tabla es que el **ranking de los 16 escenarios es idéntico en las cinco categorías de edad**. El escenario #1 (NoPol·Red·Peq·Deseq) siempre ocupa la primera posición y el escenario #16 (Pol·Amp·Gra·Equil) siempre ocupa la última, independientemente del grupo de edad considerado. Esta invariancia no es casual, sino una propiedad matemática directa del modelo, que se explica en la siguiente subsección.

### 4.1.7.2. Impacto relativo constante e impacto absoluto proporcional

Una de las propiedades más relevantes del modelo M2 para la práctica del entrenamiento es la relación entre el impacto relativo (porcentual) y el impacto absoluto (en toques/min) de las variables de diseño a través de las categorías de edad. Esta propiedad se deriva directamente de la estructura aditiva del modelo en la escala logarítmica.

El modelo M2 especifica:

$\log(\mu) = \alpha + \beta_{\text{VI}} + \beta_{\text{GrupoEdad}} + u_j$

Al exponenciar:

$\mu = e^{\alpha + \beta_{\text{GE}} + u_j} \times e^{\beta_{\text{VI}}}$

**Impacto relativo.** El cociente entre las EMMs de dos niveles de una misma variable de diseño es:

$\frac{\text{EMM}(\text{nivel}A)}{\text{EMM}(\text{nivel}B)} = \frac{e^{\alpha + \beta_A + \beta{\text{GE}}}}{e^{\alpha + \beta_B + \beta{\text{GE}}}} = e^{\beta_A - \beta_B}$

El término β_GE se cancela en el cociente, de modo que el **cambio porcentual no depende de la categoría de edad**. La verificación empírica confirma que los porcentajes son idénticos en las cinco categorías: Polaridad +46.2 %, Espacio +36.8 %, Agrupación +28.4 %, Equilibrio +4.6 %.

**Impacto absoluto.** La diferencia absoluta entre niveles es:

$\Delta = \text{EMM}(A) - \text{EMM}(B) = e^{\alpha + \beta_{\text{GE}}} \times (e^{\beta_A} - e^{\beta_B})$

En esta expresión, el término $e^{\alpha + \beta_{\text{GE}}}$ (que equivale a la EMM global de cada grupo de edad) **no se cancela**. Por tanto, el impacto absoluto es directamente proporcional a la línea base de cada categoría. Un grupo con EMM global alta (como Infantil, 3.43 toques/min) experimenta mayores diferencias absolutas que un grupo con EMM global baja (como Senior Masculino, 2.23 toques/min), aunque el cambio porcentual sea idéntico.

Esta propiedad tiene varias consecuencias verificadas empíricamente:

- El rango total entre los escenarios #1 y #16 es del **+168.8 %** en todas las categorías, sin excepción.
- El promedio de los 4 escenarios superiores (Top-4) supera al promedio de los 4 inferiores (Bottom-4) en un **+100.1 %** en todas las categorías.
- Los Δ absolutos escalan linealmente con la EMM global: Infantil (Δ Polaridad = 1.29) > Neskak (1.09) ≈ Cadete (1.08) > Juvenil (0.93) > Senior Masc. (0.84), siguiendo exactamente el orden de las EMMs globales.

### 4.1.7.3. Implicaciones para la práctica por categoría de edad

**Infantil** (EMM global: 3.43 toques/min). Esta categoría presenta la línea base más alta y, en consecuencia, los mayores márgenes de manipulación absoluta. En el escenario óptimo (#1), un jugador infantil acumularía 5.41 toques/min (54 toques en 10 minutos), mientras que en el escenario menos favorable (#16) descendería a 2.01 toques/min (20 toques). La diferencia absoluta de 3.40 toques/min es la mayor de todas las categorías, lo que indica que las decisiones de diseño táctico tienen un impacto especialmente tangible en las etapas de formación inicial.

**Neskak y Cadete** (EMMs globales: 2.89 toques/min). Estas dos categorías son estadísticamente indistinguibles en todos los indicadores, lo que sugiere un patrón de actividad técnica compartido. Sus rangos de manipulación (de 1.70 a 4.56 toques/min para Neskak; de 1.69 a 4.55 para Cadete) son prácticamente idénticos.

**Juvenil** (EMM global: 2.48 toques/min). Con una línea base intermedia, los efectos absolutos del diseño táctico son moderados pero sustanciales (rango de 1.46 a 3.92 toques/min).

**Senior Masculino** (EMM global: 2.23 toques/min). Presenta la línea base más baja y, por tanto, los menores Δ absolutos. No obstante, los efectos relativos son idénticos: pasar de una tarea polarizada a una no polarizada sigue incrementando los toques en un 46.2 %, independientemente de que la ganancia absoluta sea de 0.84 toques/min (frente a 1.29 en Infantil). En el escenario óptimo, un jugador senior acumularía 3.51 toques/min (35 toques en 10 minutos), que, aunque inferior en términos absolutos a las categorías formativas, sigue siendo más del doble del escenario menos favorable (1.31 toques/min, 13 toques en 10 minutos).

### 4.1.7.4. Universalidad de las recomendaciones de diseño

La consecuencia más importante de esta estructura matemática es que **las recomendaciones de diseño táctico son universales para todas las categorías de edad**. La jerarquía de factores (Polaridad > Espacio > Agrupación > Equilibrio), el ranking de escenarios y las directrices para maximizar o minimizar la tasa de toques son idénticos independientemente de la categoría. Lo que cambia entre categorías es únicamente la magnitud absoluta del efecto, que escala proporcionalmente con la EMM global de cada grupo.

Esta universalidad es una propiedad del modelo aditivo sin interacciones GrupoEdad × VI. Si el modelo incluyera tales interacciones (y recordemos que M4, que las incluye, presentó sobreajuste y peor capacidad predictiva), los porcentajes podrían diferir entre categorías, indicando que la estructura de respuesta al diseño táctico varía con la edad. El hecho de que el modelo sin interacciones sea el preferido por LOO-CV sugiere que, al menos para los toques por minuto, la respuesta al diseño táctico es **homogénea a través de las categorías de edad**.

Esta conclusión tiene un valor práctico considerable: un cuerpo técnico que trabaje con múltiples categorías puede aplicar los mismos principios de diseño de tareas para modular la actividad técnica, adaptando únicamente las expectativas cuantitativas absolutas al nivel de base de cada grupo. Las recomendaciones son transferibles, lo que simplifica la planificación y facilita la comunicación de criterios de diseño a lo largo de las distintas etapas formativas del club.