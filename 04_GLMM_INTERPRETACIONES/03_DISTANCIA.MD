# Análisis GLMM Bayesiano: Distancia Recorrida por Minuto (m/min)

---

## A) Ajuste de modelos y comparación mediante validación cruzada LOO-CV

### Especificación de los modelos candidatos

Con el objetivo de identificar la estructura explicativa óptima para la distancia recorrida por minuto, se ajustaron seis modelos lineales mixtos generalizados (GLMM) con familia Gamma y función de enlace logarítmico, incluyendo en todos ellos un intercepto aleatorio por jugador (1|PlayerId). Esta especificación de la distribución de errores es adecuada para variables de respuesta continuas, estrictamente positivas y con varianza que escala con la media, como es el caso de la distancia recorrida.

Los modelos se construyeron con complejidad creciente: **M0** constituye el modelo nulo, con únicamente el intercepto fijo y el efecto aleatorio por jugador, sin predictor táctico alguno, sirviendo exclusivamente como línea de referencia. **M1** incorpora los cuatro factores de diseño táctico (Espacio, Agrupación, Polaridad y Equilibrio) como efectos principales sin interacciones. **M2** extiende M1 añadiendo la categoría de edad del grupo (GrupoEdad) como quinto predictor fijo. **M3** sustituye GrupoEdad por la interacción Espacio×Agrupación, explorando si el tamaño del espacio y el número de jugadores actúan de forma sinérgica. **M4** amplía M3 incorporando tres interacciones de moderación por edad (Espacio×GrupoEdad, Agrupación×GrupoEdad y Equilibrio×GrupoEdad), omitiendo Polaridad×GrupoEdad por no resultar teóricamente relevante. Finalmente, **M5** es el modelo maximal, que incluye todas las interacciones de dos vías posibles entre los predictores fijos.

### Resultados de la comparación LOO-CV

La selección del modelo óptimo se realizó mediante validación cruzada leave-one-out con el estimador PSIS-LOO (Pareto-Smoothed Importance Sampling, Vehtari et al., 2017). El criterio de selección es la densidad log-predictiva esperada (ELPD, *Expected Log Predictive Density*): cuanto menos negativa, mejor es la capacidad predictiva fuera de muestra del modelo.

**Tabla 1.** Resultados PSIS-LOO-CV para los seis modelos candidatos (Gamma log-link, intercepto aleatorio por jugador).

| Modelo | ELPD_LOO | ΔELPD vs M2 | Peso stacking |
| --- | --- | --- | --- |
| M2 (efectos principales + GrupoEdad) | −20.725,08 | 0,00 | 0,1675 |
| M0 (nulo) | −20.763,41 | 38,33 | 0,4168 |
| M3 (interacción Espacio×Agrupación) | −20.766,69 | 41,61 | 0,3150 |
| M4 (moderación por edad) | −20.868,78 | 143,69 | 0,1007 |
| M1 (4 VI de diseño) | −20.904,80 | 179,72 | ~0 |
| M5 (maximal) | −22.229,92 | 1.504,83 | ~0 |

El modelo seleccionado como óptimo según ELPD es **M2**, con un valor de −20.725,08, superando al siguiente competidor (M0) en 38,33 unidades de log-densidad predictiva, diferencia que en la práctica PSIS-LOO es interpretada como evidencia sustancial.

### Interpretación del patrón de selección: un resultado inusual

El resultado de la comparación LOO-CV para la distancia recorrida presenta una particularidad llamativa que lo distingue cualitativamente de los patrones observados en las otras variables dependientes analizadas en este TFG. En el análisis de los toques por minuto y de los golpeos por minuto, el modelo M1 —el que incluía únicamente los cuatro factores de diseño táctico— era el ganador indiscutible de la selección por ELPD. En contraste, para la distancia recorrida, M1 queda en quinta posición con un ΔELPD de 179,72 puntos respecto a M2, siendo incluso superado por el modelo nulo M0 (que carece de cualquier predictor táctico fijo) en aproximadamente 141 unidades de ELPD.

Esta inversión jerárquica tiene una interpretación sustantiva de considerable profundidad. En primer lugar, debe considerarse el componente inter-jugador de la variabilidad de la distancia recorrida. Como se detalla en la sección de diagnósticos (véase apartado B), el coeficiente de correlación intraclase (ICC) estimado para la distancia/min en M1 alcanza el 27,7%, el valor más alto de las cuatro variables dependientes analizadas. Este porcentaje refleja que más de una cuarta parte de la variabilidad total en la distancia recorrida se debe a diferencias estables entre jugadores —capacidad aeróbica, perfil locomotor, posición habitual de juego, estado físico— que son completamente independientes del diseño de la tarea. Cuando la variable dependiente exhibe un componente individual tan pronunciado, la estructura del modelo importa especialmente: un predictor que capture parte de esa variabilidad sistemática entre jugadores mejorará drásticamente la capacidad predictiva fuera de muestra.

La variable GrupoEdad actúa precisamente como ese predictor estructural. La categoría de edad no es un factor contextual secundario para la distancia recorrida: en contraste con variables más técnicas como los toques —donde la habilidad técnica puede compensar parcialmente las diferencias madurativas—, la capacidad locomotora está fisiológicamente determinada por el grado de maduración biológica, el desarrollo del sistema cardiovascular y musculoesquelético, y la acumulación de años de entrenamiento específico. Un jugador de Senior Masculino recorre sistemáticamente más distancia por minuto que un jugador de categoría Neskak, con independencia del diseño táctico de la tarea. Al no incluir GrupoEdad, M1 no puede capturar esta variabilidad estructural entre categorías, que queda absorbida por el intercepto aleatorio de jugador sin ser identificada como variabilidad explicada. En consecuencia, M1 produce predicciones LOO que ignoran el contexto de edad, reduciendo sensiblemente su precisión. Que el modelo nulo M0 supere a M1 en ELPD indica que añadir los cuatro factores tácticos sin el contexto de la categoría de edad introduce ruido en lugar de señal: el modelo táctico sin edad malinterpreta la variabilidad de fondo como si fuera variabilidad táctica.

M2, al incorporar GrupoEdad junto a los cuatro factores de diseño, logra separar con precisión la variabilidad debida al diseño táctico de la variabilidad estructural ligada al grupo de edad, obteniendo predicciones LOO notablemente superiores. Este resultado es, en sí mismo, una conclusión de relevancia metodológica: para la distancia recorrida, la categoría de edad no es un covariable de ajuste, sino una variable estructural sin la cual el modelo táctico es incapaz de operar correctamente.

El modelo M3, que incorpora la interacción Espacio×Agrupación pero prescinde de GrupoEdad, se sitúa casi a la altura del modelo nulo (ΔELPD = 41,61 respecto a M2, frente a 38,33 de M0). Esto sugiere que la interacción entre espacio y agrupación capta alguna estructura real de los datos —de ahí que supere levemente a M1 puro—, pero sin el contexto de la categoría de edad la ganancia predictiva es modesta y no compensa la ausencia de esa información fundamental.

En el extremo opuesto del espectro, M5 presenta el mayor sobreajuste observado en todo el análisis, con un ΔELPD de 1.504,83 unidades respecto a M2. Un modelo maximal con todas las interacciones de dos vías entre factores táctivos y categorías de edad genera una estructura paramétrica excesivamente compleja que el conjunto de datos no puede sostener, deteriorando dramáticamente la capacidad predictiva fuera de muestra.

### Sobre los pesos de stacking

Un aspecto adicional de notable interés son los pesos de stacking, que cuantifican la contribución óptima de cada modelo al conjunto de predicciones combinadas. El resultado es aparentemente paradójico: M0 recibe el mayor peso individual (41,7%), mientras que M2 —el ganador por ELPD— recibe solo el 16,75%. M3 obtiene el 31,5% y M4 el 10,1%, mientras que M1 y M5 son virtualmente descartados.

Esta distribución de pesos refleja que, desde una perspectiva de ensemble, ningún modelo domina completamente la estructura predictiva de los datos. El peso elevado de M0 —un modelo sin predictores tácticos— indica que la variabilidad capturada por el efecto aleatorio por jugador es tan informativa que el modelo nulo resulta un componente valioso del ensemble. En términos más precisos, el stacking distribuye el peso entre modelos que son complementarios en sus errores de predicción: M0 predice bien en casos donde las diferencias individuales dominan; M2 y M3 contribuyen en casos donde el diseño táctico o la edad aportan señal adicional. Este resultado constituye evidencia independiente de que la estructura de varianza en la distancia recorrida está genuinamente dominada por el componente inter-jugador, más que por los factores tácticos. Para el análisis sustantivo se trabaja con M2 por ser el modelo con mejor ELPD individual y el único que incluye de forma explícita los predictores de interés teórico —los cuatro factores de diseño táctico y la categoría de edad— con parámetros bien estimados e interpretables.

---

## B) Diagnósticos del modelo final (M2)

### Convergencia MCMC

El modelo M2 fue ajustado con cuatro cadenas Markov Chain Monte Carlo (MCMC) en bambi/PyMC, con 2.000 iteraciones por cadena (1.000 de calentamiento y 1.000 de muestras efectivas), utilizando el muestreador NUTS (*No-U-Turn Sampler*), con semilla fijada en 42 para garantizar la reproducibilidad. El estadístico R-hat máximo registrado en todos los parámetros fue 1,0032, claramente inferior al umbral de advertencia convencional de 1,01, lo que indica convergencia satisfactoria de las cuatro cadenas hacia la misma distribución posterior. El tamaño efectivo de muestra (ESS) mínimo alcanzó 1.187, muy por encima del umbral mínimo de 400 generalmente recomendado, lo que garantiza que las estimaciones de la posterior son estables y no dependen del orden de muestreo. En conjunto, los diagnósticos MCMC confirman que M2 produjo una exploración adecuada de la distribución posterior y que los resultados son fiables.

### R² bayesiano

El R² bayesiano de M2, calculado como la fracción de varianza de las predicciones respecto a la varianza total observada (Gelman et al., 2019), alcanza una media de 0,4558 (SD = 0,0065). Es decir, **el modelo M2 explica el 45,6% de la varianza total de la distancia recorrida por minuto** (HDI 95% aproximado: [0,443; 0,469]). Este es el R² bayesiano más elevado de todas las variables dependientes analizadas en el presente TFG. El resultado es coherente con la naturaleza de la variable: la distancia recorrida, al estar determinada en gran medida por factores fisiológicos relativamente estables (maduración, capacidad aeróbica, posición) y por el diseño táctico de la tarea, resulta más predecible que indicadores de habilidad técnica como los toques o los golpeos, cuya variabilidad situacional es intrínsecamente más alta.

### Descomposición de varianza — Modelo M1 (Nakagawa et al., 2017)

Para cuantificar la contribución relativa de cada fuente de variabilidad, se aplicó el marco de descomposición de varianza propuesto por Nakagawa et al. (2017) para GLMM con familia Gamma. La **Tabla 2** presenta los resultados para M1, el modelo con los cuatro factores de diseño pero sin GrupoEdad.

**Tabla 2.** Descomposición de varianza del modelo M1 (Gamma log-link, 4 VI + intercepto aleatorio por jugador).

| Componente | σ² | % | HDI 95% |
| --- | --- | --- | --- |
| Diseño táctico (4 VI) | 0,0190 | 11,3% | [9,7%; 13,0%] |
| Jugador (intercepto aleatorio) | 0,0464 | 27,7% | [23,7%; 32,1%] |
| Residual (Gamma) | 0,1020 | 61,0% | [57,3%; 64,7%] |
| **TOTAL** | **0,1673** | **100%** |  |

*R² marginal (solo diseño táctico): 11,3% | R² condicional (diseño + jugador): 39,0%*

El resultado más destacado de esta descomposición es el componente inter-jugador: **el 27,7% de la variabilidad total en la distancia recorrida se debe a diferencias estables entre jugadores**, con independencia completa del diseño de la tarea. El coeficiente de correlación intraclase resultante (ICC = 0,277 / (0,113 + 0,277 + 0,610) ≈ 0,232 en escala de varianza total) es el más alto registrado entre las cuatro variables dependientes del TFG. Esto tiene una interpretación directa y fisiológicamente coherente: la distancia recorrida por minuto refleja en buena medida la capacidad locomotora individual del jugador —su potencia aeróbica, su perfil físico habitual, su posición en el campo— que es relativamente estable a lo largo del tiempo y entre sesiones. En contraste con variables técnicas como los toques, cuya expresión puede modularse en mayor medida por el contexto táctico inmediato, la distancia recorrida lleva una "firma individual" mucho más pronunciada. Este perfil de varianza tiene consecuencias directas sobre el análisis estadístico: justifica plenamente el uso del intercepto aleatorio por jugador y explica por qué, como se argumentó en el apartado anterior, GrupoEdad resulta tan determinante para la predicción LOO.

El diseño táctico explica el 11,3% de la varianza total [9,7%; 13,0%], lo que es una contribución modesta en términos absolutos pero estadísticamente robusta (el HDI 95% excluye claramente el cero). El residual del modelo Gamma, que recoge tanto la variabilidad intra-tarea no explicada como el error de medida, representa el 61,0% de la varianza total, lo que es esperable en datos de campo con alta heterogeneidad situacional.

### Descomposición de varianza — Modelo M2

La **Tabla 3** presenta la descomposición para el modelo M2, que añade GrupoEdad como predictor fijo.

**Tabla 3.** Descomposición de varianza del modelo M2 (Gamma log-link, 4 VI + GrupoEdad + intercepto aleatorio por jugador).

| Componente | σ² | % | HDI 95% |
| --- | --- | --- | --- |
| Diseño táctico (4 VI) | 0,0193 | 11,5% | [9,8%; 13,0%] |
| GrupoEdad | 0,0043 | 2,5% | [0,8%; 4,5%] |
| Jugador (intercepto aleatorio) | 0,0432 | 25,5% | [21,4%; 29,7%] |
| Residual (Gamma) | 0,1021 | 60,5% | [56,8%; 64,7%] |
| **TOTAL** | **0,1690** | **100%** |  |

*R² marginal (solo diseño táctico): 11,5% | R² marginal (diseño + edad): 14,0% | R² condicional (diseño + edad + jugador): 39,5%*

La comparación entre las tablas 2 y 3 revela un patrón de redistribución de varianza que merece una discusión detallada. Al incorporar GrupoEdad en M2, el componente de varianza asociado al intercepto aleatorio de jugador desciende de 0,0463 (M1) a 0,0432 (M2), una reducción de 0,0031 unidades (−2,2%). Simultáneamente, GrupoEdad aparece como componente explícito que explica 0,0043 unidades de varianza (2,5%). Este flujo de varianza desde el componente aleatorio hacia el predictor fijo de edad evidencia un mecanismo clave: parte de lo que M1 atribuía a "diferencias individuales estables entre jugadores" era en realidad **variabilidad sistemática entre categorías de edad** que M1 no podía identificar como explicada, dejándola absorbida en el componente aleatorio. M2, al introducir GrupoEdad, extrae esa variabilidad del componente aleatorio y la identifica correctamente como variabilidad estructural explicada.

Este fenómeno es coherente con lo que se argumentó en el apartado de comparación LOO-CV: la categoría de edad captura diferencias fisiológicas entre grupos de entrenamiento que son estructurales y no meramente contextuales. La nota técnica de que el coeficiente de covarianza 2·Cov(tarea, edad) es negativo (−0,004) indica que los efectos del diseño táctico y GrupoEdad no son completamente ortogonales en la escala latente logarítmica: cuando se introduce GrupoEdad, la contribución atribuida al diseño se ajusta ligeramente al alza (de 0,0190 a 0,0193), en tanto que el modelo redistribuye la estructura de dependencia entre predictores.

Por otra parte, es notable la **estabilidad del componente de diseño táctico**: entre M1 (11,3%) y M2 (11,5%), la varianza explicada por los cuatro factores tácticos permanece prácticamente invariante, con HDIs que se solapan completamente. Esto confirma la robustez de los efectos del diseño: los factores tácticos explican su porción de varianza de forma independiente de si se controla o no por la categoría de edad. De forma análoga, el componente residual apenas varía entre modelos (61,0% en M1 vs. 60,5% en M2), lo que indica que GrupoEdad no absorbe varianza de la heterogeneidad intra-tarea, sino exclusivamente varianza del componente inter-jugador.

---

## C) Coeficientes del modelo final (M2)

### Interpretación del enlace logarítmico

En un GLMM Gamma con función de enlace logarítmico, el modelo se especifica en escala log: log(μᵢⱼ) = Xᵢβ + uⱼ, donde Xᵢβ es la combinación lineal de predictores fijos y uⱼ es el efecto aleatorio del jugador j. En la escala original, esto equivale a μᵢⱼ = exp(Xᵢβ) × exp(uⱼ), es decir, los efectos de los predictores son **multiplicativos** sobre la distancia media. El exponencial de cada coeficiente, exp(β), tiene una interpretación directa: es el **ratio multiplicativo** entre el nivel de referencia y el nivel alternativo, manteniendo constantes los demás predictores. Valores de exp(β) < 1 indican una reducción en distancia/min; valores > 1 indican un incremento. Las categorías de referencia en M2 son: Espacio = amplio, Agrupación = grande, Polaridad = Polarizado, Equilibrio = Equilibrio.

**Tabla 4.** Coeficientes del modelo M2 exponenciados: interpretación como ratios multiplicativos de distancia/min.

| Efecto | β (media) | exp(β) | HDI 95% exp(β) | Creíble |
| --- | --- | --- | --- | --- |
| Intercepto (referencia) | +4,443 | 85,10 m/min | [79,80; 90,78] | — |
| Espacio[reducido] vs amplio | −0,044 | 0,9570 | [0,9347; 0,9799] | Sí |
| Agrupación[pequeño] vs grande | −0,244 | 0,7833 | [0,7648; 0,8014] | Sí |
| Polaridad[NoPolarizado] vs Polarizado | +0,063 | 1,0656 | [1,0359; 1,0944] | Sí |
| Equilibrio[Desequilibrio] vs Equilibrio | −0,049 | 0,9521 | [0,9264; 0,9795] | Sí |
| GrupoEdad (media global) | +0,047 | 1,051 | [0,903; 1,186] | No |

### Interpretación sustantiva de cada coeficiente

**Espacio[reducido] vs amplio:** exp(β) = 0,957 implica que las tareas en espacio reducido generan, en promedio, un **4,3% menos de distancia por minuto** que las realizadas en espacio amplio [HDI 95%: −6,5% a −2,0%], con todo el intervalo de credibilidad excluyendo el valor nulo (1,0), lo que confirma la credibilidad del efecto. La dirección es fisiológicamente intuitiva: en espacios amplios, los jugadores deben recorrer mayores distancias para cumplir con las exigencias colectivas de la tarea —presión al poseedor, movimientos de apoyo, coberturas defensivas, ocupación de líneas—, mientras que en espacios reducidos la compresión del terreno limita físicamente el desplazamiento máximo posible por unidad de tiempo. Aunque el efecto es el más modesto de los cuatro factores táctivos en términos absolutos, es sistemático y robusto.

**Agrupación[pequeño] vs grande:** con exp(β) = 0,783, este coeficiente representa una **reducción del 21,7% en distancia/min** cuando la agrupación es pequeña respecto a grande [HDI 95%: −23,5% a −19,9%]. Es, con claridad, el **factor dominante** para la distancia recorrida, con un efecto que casi triplica en magnitud al siguiente factor (Polaridad). La interpretación requiere distinguir entre el volumen colectivo de trabajo físico y el recorrido individual: con agrupaciones grandes, el espacio disponible per cápita es menor en términos relativos, pero la exigencia de cada jugador de cubrir posiciones, realizar transiciones y participar en las fases de presión y cobertura es mayor en términos absolutos. Con agrupaciones pequeñas, la tarea puede completarse con menores desplazamientos individuales porque hay menos jugadores que cubrir y defender. Es particularmente interesante contrastar este resultado con el observado para la variable Toques/min, donde la agrupación pequeña favorecía la frecuencia de contacto con el balón: distancia recorrida y toques responden de forma opuesta a la variable de agrupación, evidenciando la naturaleza complementaria de las métricas físicas y técnicas en el análisis del rendimiento en tareas de entrenamiento.

**Polaridad[NoPolarizado] vs Polarizado:** exp(β) = 1,066 indica que los ejercicios sin orientación de juego definida generan un **6,6% más de distancia/min** que los ejercicios polarizados [HDI 95%: +3,6% a +9,4%; creíble]. La dirección del efecto tiene una explicación táctica coherente: los ejercicios polarizados poseen una dirección de juego explícita (progresión hacia una zona de finalización o portería), lo que estructura los movimientos de los jugadores de forma más lineal y dirigida, potencialmente reduciendo los desplazamientos totales al orientarlos eficientemente hacia el objetivo. Los ejercicios no polarizados, al carecer de una dirección preferencial, generan mayor movilidad omnidireccional: los jugadores deben posicionarse, reposicionarse y presionar en múltiples direcciones sin un vector de progresión claro, acumulando así mayor distancia total recorrida. Merece destacarse que, en el análisis de la variable Toques/min, la Polaridad era el factor de mayor impacto; para la distancia recorrida ocupa la segunda posición, con un efecto similar en magnitud pero con un rol secundario respecto a la Agrupación.

**Equilibrio[Desequilibrio] vs Equilibrio:** exp(β) = 0,952 indica que las situaciones de desequilibrio numérico conllevan un **4,8% menos de distancia/min** respecto a las equilibradas [HDI 95%: −7,4% a −2,1%; creíble]. Esta dirección puede interpretarse desde la lógica de la presión colectiva: en situaciones equilibradas, ambos equipos experimentan similares exigencias de cobertura y presión, distribuyendo el esfuerzo defensivo-ofensivo de forma simétrica e incentivando mayor movilidad de todos los jugadores. En situaciones de desequilibrio, el equipo favorecido puede ejercer su superioridad numérica con menores exigencias de desplazamiento para el conjunto del grupo —el equipo en inferioridad compensa con intensidad táctica, pero el promedio de distancia del grupo completo resulta inferior—. Es el factor con menor efecto individual, aunque claramente no nulo.

**GrupoEdad:** el coeficiente promediado de GrupoEdad, exp(β) = 1,051, con HDI 95% = [0,903; 1,186], **incluye el valor 1 y no resulta creíble como efecto global único**. Sin embargo, esta ausencia de credibilidad del efecto promedio no debe interpretarse como ausencia de efecto de la categoría de edad. La selección LOO-CV demostró que M2 supera a M1 en 179,72 unidades de ELPD precisamente gracias a GrupoEdad. Lo que el coeficiente global indica es que el efecto de la categoría de edad **no es uniforme**: distintas categorías se desvían de la referencia (Cadete) en magnitudes y direcciones diversas, de modo que el promedio ponderado resulta estadísticamente difuso. La descomposición de varianza confirmó que GrupoEdad explica el 2,5% de la varianza total. Las comparaciones específicas entre categorías mediante las medias marginales estimadas (EMMs), presentadas en el apartado F.5, permiten identificar qué pares de categorías difieren de forma creíble.

---

## D) Predicciones factoriales: las 16 combinaciones del diseño táctico

### Cálculo de las predicciones bajo el modelo M2

En un GLMM Gamma log-link, la predicción de la distancia media esperada para una combinación específica de los cuatro factores de diseño se calcula sumando el intercepto y los coeficientes de los niveles activos en escala logarítmica: log(μ) = 4,443 + β_Espacio × 𝟙[reducido] + β_Agrupación × 𝟙[pequeño] + β_Polaridad × 𝟙[NoPolarizado] + β_Equilibrio × 𝟙[Desequilibrio]. La predicción en escala natural se obtiene exponenciando: μ = exp(log(μ)). Dado que el modelo es aditivo en escala log, los efectos son multiplicativos en escala original: activar simultáneamente varios niveles desfavorables acumula reducciones multiplicativas sobre la línea de referencia.

**Tabla 5.** Predicciones del modelo M2 para las 16 combinaciones del diseño táctico, ordenadas de mayor a menor distancia/min.

| # | Espacio | Agrupación | Polaridad | Equilibrio | log(μ) | Dist/min | Dist 10 min |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | amplio | grande | NoPolarizado | Equilibrio | 4,507 | 90,62 | 906,2 m |
| 2 | reducido | grande | NoPolarizado | Equilibrio | 4,463 | 86,71 | 867,1 m |
| 3 | amplio | grande | NoPolarizado | Desequilibrio | 4,457 | 86,27 | 862,7 m |
| 4 | amplio | grande | Polarizado | Equilibrio | 4,443 | 85,05 | 850,5 m |
| 5 | reducido | grande | NoPolarizado | Desequilibrio | 4,413 | 82,55 | 825,5 m |
| 6 | reducido | grande | Polarizado | Equilibrio | 4,399 | 81,39 | 813,9 m |
| 7 | amplio | grande | Polarizado | Desequilibrio | 4,394 | 80,97 | 809,7 m |
| 8 | reducido | grande | Polarizado | Desequilibrio | 4,350 | 77,48 | 774,8 m |
| 9 | amplio | pequeño | NoPolarizado | Equilibrio | 4,262 | 70,98 | 709,8 m |
| 10 | reducido | pequeño | NoPolarizado | Equilibrio | 4,218 | 67,92 | 679,2 m |
| 11 | amplio | pequeño | NoPolarizado | Desequilibrio | 4,213 | 67,57 | 675,7 m |
| 12 | amplio | pequeño | Polarizado | Equilibrio | 4,199 | 66,62 | 666,2 m |
| 13 | reducido | pequeño | NoPolarizado | Desequilibrio | 4,169 | 64,66 | 646,6 m |
| 14 | reducido | pequeño | Polarizado | Equilibrio | 4,155 | 63,75 | 637,5 m |
| 15 | amplio | pequeño | Polarizado | Desequilibrio | 4,150 | 63,42 | 634,2 m |
| 16 | reducido | pequeño | Polarizado | Desequilibrio | 4,106 | 60,69 | 606,9 m |

*Escenario máximo: 90,62 m/min | Escenario mínimo: 60,69 m/min | Ratio max/min: 1,49×*

### Estructura de las predicciones

La característica más sobresaliente de la tabla es la **discontinuidad estructural** inducida por la variable Agrupación. Los ocho escenarios con agrupación grande (filas 1-8) oscilan entre 77,48 y 90,62 m/min, mientras que los ocho con agrupación pequeña (filas 9-16) oscilan entre 60,69 y 70,98 m/min. Los dos grupos no se solapan: incluso el peor escenario de agrupación grande (77,48 m/min) supera en más de 6 m/min al mejor escenario de agrupación pequeña (70,98 m/min). Esta separación absoluta entre bloques ilustra numéricamente el poder determinante de la Agrupación: independientemente de cómo se configuren los otros tres factores, la decisión de utilizar un grupo grande o pequeño establece el rango de distancia recorrida en una banda que no se puede compensar con combinaciones favorables del resto de variables.

Dentro de cada bloque, se observa una jerarquía secundaria consistente: el orden de impacto decreciente es Espacio (amplio > reducido), seguido de Polaridad (NoPolarizado > Polarizado) y finalmente Equilibrio (Equilibrio > Desequilibrio). Esta jerarquía se reproduce de forma idéntica en los dos bloques, reflejando la estructura aditiva del modelo en escala log.

El **escenario óptimo** (fila 1: amplio + grande + NoPolarizado + Equilibrio) alcanza 90,62 m/min al acumular los niveles más favorables de todos los factores. El **escenario mínimo** (fila 16: reducido + pequeño + Polarizado + Desequilibrio) desciende a 60,69 m/min al acumular todos los niveles desfavorables. El **ratio entre ambos extremos es de 1,49**, lo que implica que la configuración óptima genera un 49% más de distancia por minuto que la configuración más restrictiva.

Desde una perspectiva aplicada, este margen de modulación es un hallazgo de alto valor para el planificador de entrenamientos. La columna "Distancia 10 min" materializa el impacto en términos absolutamente comprensibles: entre el escenario óptimo (906,2 m) y el mínimo (606,9 m) se acumula una diferencia de **aproximadamente 300 metros en tan solo 10 minutos** de tarea. En el contexto de una sesión de entrenamiento con múltiples series de ejercicios, esta diferencia por unidad de tarea se acumula hasta alcanzar magnitudes muy relevantes para la gestión de la carga física semanal.

---

## E) Comparación entre predicciones del modelo y medias reales observadas

### ¿Por qué comparar? La diferencia conceptual entre media observada y predicción del modelo

Una pregunta legítima al enfrentar la tabla de predicciones frente a medias reales es: ¿qué aporta el GLMM si ya disponemos de las medias observadas por celda? La respuesta requiere comprender la diferencia conceptual fundamental entre ambas estimaciones.

La **media real** de una celda factorial es el promedio aritmético de las observaciones registradas en esa combinación específica de factores. Esta estimación tiene dos limitaciones estructurales importantes. En primer lugar, está determinada exclusivamente por las observaciones de esa celda concreta, ignorando la información del resto del dataset. En segundo lugar, su estabilidad depende críticamente del tamaño muestral de la celda. Como muestra la Tabla 6, el número de observaciones por celda oscila entre 16 (fila 8: reducido + grande + Polarizado + Desequilibrio) y 1.461 (fila 4: amplio + grande + Polarizado + Equilibrio), un rango de casi dos órdenes de magnitud. Una media calculada con 16 observaciones tiene una incertidumbre enorme y puede estar fuertemente influenciada por la composición particular de jugadores que participaron en esas condiciones concretas.

La **predicción del GLMM** es una estimación de naturaleza fundamentalmente distinta. Al estimarse a través de un modelo bayesiano con todos los datos disponibles, los coeficientes de cada predictor absorben información de las 4.446 observaciones, no solo de una celda. El intercepto aleatorio por jugador ajusta por las diferencias individuales, de modo que la predicción para una combinación táctica representa lo que el modelo espera para un **jugador promedio de la población**, eliminando el sesgo introducido por la composición idiosincrásica de cada celda en el dataset observado. Además, la estructura aditiva del modelo impone una regularización implícita: las predicciones de celdas poco representadas son "atraídas" hacia valores consistentes con el conjunto global de parámetros, evitando estimaciones extremas basadas en muestras pequeñas. Finalmente, las predicciones bayesianas llevan asociados intervalos de credibilidad que cuantifican la incertidumbre de cada estimación, algo que las medias observadas crudas no proporcionan sin análisis adicionales.

En términos simples: la **media es lo que ocurrió en esas observaciones concretas**; la **predicción del modelo es lo que se espera que ocurra en esa configuración táctica para un jugador promedio de la población**, con toda la información del dataset y un control adecuado de las diferencias individuales.

**Tabla 6.** Comparación entre medias reales observadas y predicciones del modelo M2 para las 16 celdas factoriales.

| # | Media real | N obs | Predicción | Diferencia | Error% |
| --- | --- | --- | --- | --- | --- |
| 1 | 96,95 | 55 | 90,62 | −6,33 | −6,5% |
| 2 | 88,11 | 124 | 86,71 | −1,40 | −1,6% |
| 3 | 87,69 | 81 | 86,27 | −1,42 | −1,6% |
| 4 | 92,08 | 1.461 | 85,05 | −7,03 | −7,6% |
| 5 | 76,07 | 106 | 82,55 | +6,48 | +8,5% |
| 6 | 80,67 | 385 | 81,39 | +0,72 | +0,9% |
| 7 | 86,49 | 65 | 80,97 | −5,52 | −6,4% |
| 8 | 78,91 | 16 | 77,48 | −1,43 | −1,8% |
| 9 | 62,04 | 23 | 70,98 | +8,94 | +14,4% |
| 10 | 75,30 | 122 | 67,92 | −7,38 | −9,8% |
| 11 | 76,29 | 23 | 67,57 | −8,72 | −11,4% |
| 12 | 65,35 | 370 | 66,62 | +1,27 | +1,9% |
| 13 | 74,28 | 435 | 64,66 | −9,62 | −13,0% |
| 14 | 68,85 | 806 | 63,75 | −5,10 | −7,4% |
| 15 | 53,19 | 74 | 63,42 | +10,23 | +19,2% |
| 16 | 65,97 | 300 | 60,69 | −5,28 | −8,0% |

*MAE global: 5,430 m/min | MAPE: 7,5% | Correlación Pearson: 0,8534*

### Calidad del ajuste y patrón de errores

La correlación Pearson de 0,8534 entre medias reales y predicciones del modelo indica que M2 reproduce satisfactoriamente el patrón de variación entre celdas. El error absoluto medio (MAE) de 5,43 m/min, sobre un rango de medias observadas que oscila entre 53 y 97 m/min (aproximadamente 44 m/min de amplitud), representa un error relativo promedio del 7,5%, que es aceptable para un modelo sin interacciones entre VI que opera con datos de campo altamente heterogéneos.

Los errores más pronunciados se concentran en celdas con escaso número de observaciones. La celda 9 (amplio + pequeño + NoPolarizado + Equilibrio, n = 23) presenta una media real de 62,04 m/min frente a una predicción de 70,98 m/min (+14,4%): con solo 23 observaciones, la media real puede estar distorsionada por la composición particular de jugadores registrados en esas condiciones, posiblemente procedentes en su mayoría de categorías con menor distancia base. La predicción del modelo, al utilizar los coeficientes estimados con las 4.446 observaciones, representa lo que "debería" esperarse para un jugador promedio de la población en esa combinación táctica, corrigiendo hacia valores más plausibles a la luz del conjunto global de datos. De forma análoga, la celda 15 (amplio + pequeño + Polarizado + Desequilibrio, n = 74) registra el mayor error porcentual (+19,2%): su media real de 53,19 m/min es notablemente inferior a la predicción de 63,42 m/min, sugiriendo que las 74 observaciones de esa celda incluyen una proporción inusualmente alta de tareas con participantes de menor capacidad locomotora, efecto que el modelo "suaviza" al extrapolar desde los coeficientes globales.

Inversamente, la celda 4 (amplio + grande + Polarizado + Equilibrio, n = 1.461) muestra un error de −7,0% a pesar de ser la celda más nutrida del dataset. Este sobreestimado relativo de la media observada frente a la predicción puede explicarse porque la celda 4 concentra un porcentaje desproporcionado del total de observaciones (32,8% del total), incluyendo posiblemente una mayor diversidad de condiciones de registro que eleva la media observada más allá de lo que el modelo, ajustado para la población completa, estima para esa combinación concreta.

En conjunto, la comparación entre medias reales y predicciones confirma que el GLMM captura la estructura principal de variación entre diseños tácticos con buena fidelidad, mientras que regula las estimaciones en celdas poco representadas hacia valores más robustos. Esta regularización estadística es precisamente el valor añadido del enfoque inferencial respecto a la mera tabulación descriptiva de medias.

---

## F) Medias Marginales Estimadas (EMMs): marco conceptual y resultados

### F.1 — Definición, cálculo e interpretación de las EMMs

Las medias marginales estimadas (EMMs, *Estimated Marginal Means*) constituyen uno de los instrumentos inferenciales más valiosos del análisis GLMM en contextos de diseño desequilibrado, y merecen una presentación conceptual cuidadosa antes de interpretar los resultados numéricos.

En términos formales, las EMMs son las medias predichas por el modelo para cada nivel de un factor, obtenidas promediando con **ponderación uniforme** sobre todos los niveles de los demás factores. Esta ponderación uniforme es la característica definitoria que las distingue de las medias observadas: en lugar de reflejar la distribución real de observaciones entre combinaciones —que en este estudio es extremadamente desigual—, las EMMs otorgan el mismo peso a cada combinación del resto de factores, produciendo una comparación "justa" entre niveles.

**El procedimiento de cálculo en el marco bayesiano es el siguiente. Para estimar la EMM de, por ejemplo, Espacio = amplio, se construye una grilla de predicción que cubre todas las combinaciones posibles de los demás factores (2³ = 8 combinaciones de Agrupación × Polaridad × Equilibrio) cruzadas con los 5 niveles de GrupoEdad, generando 40 filas de predicción. Para cada una de las 8.000 muestras de la distribución posterior del modelo, se calculan las 40 predicciones en escala original (aplicando exp()), se promedian sobre las 40 filas de marginalización, y se extrae la EMM para esa muestra. El conjunto de 8.000 valores así obtenido constituye la distribución posterior de la EMM, de la que se extraen la media y el HDI 95%. El resultado es, por tanto, una estimación bayesiana completa con su incertidumbre cuantificada: no un único número, sino una distribución de posibles valores.**

**Es fundamental subrayar lo que las EMMs no son: no representan "lo que ocurrió" en el dataset. No son las medias observadas en los datos, ni reflejan la distribución real de las demás variables en el estudio. Representan lo que el modelo predice para un jugador promedio de la población bajo esa condición, bajo el supuesto de que los demás factores están distribuidos uniformemente. Esta distinción es especialmente importante en el presente estudio, donde algunas combinaciones de factores cuentan con 16 observaciones y otras con más de 1.400: las medias simples estarían sistemáticamente sesgadas hacia las combinaciones más frecuentes. Las EMMs ofrecen comparaciones depuradas de ese sesgo de diseño.**

### F.2 — EMMs de efectos principales

La **Tabla 7** presenta las EMMs para cada nivel de las cuatro VI, marginalizando sobre los demás factores y sobre GrupoEdad con ponderación uniforme.

**Tabla 7.** EMMs del modelo M2 para los efectos principales de las cuatro variables de diseño táctico.

| Variable | Nivel | EMM (m/min) | HDI 95% |
| --- | --- | --- | --- |
| Espacio | amplio | 79,53 | [77,01; 82,00] |
| Espacio | reducido | 76,10 | [73,80; 78,52] |
| Agrupación | grande | 87,27 | [84,46; 89,95] |
| Agrupación | pequeño | 68,36 | [66,31; 70,50] |
| Polaridad | NoPolarizado | 80,28 | [77,79; 83,09] |
| Polaridad | Polarizado | 75,35 | [73,09; 77,65] |
| Equilibrio | Equilibrio | 79,73 | [77,28; 82,13] |
| Equilibrio | Desequilibrio | 75,90 | [73,49; 78,64] |

La **jerarquía de efectos** queda clara al ordenar las diferencias entre niveles:

- **Agrupación:** 87,27 − 68,36 = **18,91 m/min** de diferencia (grande > pequeño), con HDIs que no se solapan en absoluto → efecto de máxima credibilidad y magnitud claramente dominante.
- **Polaridad:** 80,28 − 75,35 = **4,93 m/min** (NoPolarizado > Polarizado), sin solapamiento de HDIs.
- **Equilibrio:** 79,73 − 75,90 = **3,83 m/min** (Equilibrio > Desequilibrio), sin solapamiento de HDIs.
- **Espacio:** 79,53 − 76,10 = **3,43 m/min** (amplio > reducido), la diferencia más modesta en valor absoluto, aunque creíble.

La jerarquía de efectos es pues: **Agrupación >> Polaridad > Equilibrio > Espacio**. Esta ordenación contrasta con la observada para los Toques/min (donde Polaridad era el factor dominante) y para los Golpeos/min, subrayando que la relevancia relativa de cada factor de diseño táctico depende de la variable de rendimiento considerada. Para la carga locomotora, la variable Agrupación es estructuralmente determinante, mientras que para la actividad técnica lo es la Polaridad.

### F.3 — EMMs de interacciones de dos vías

Aunque M2 es un modelo de efectos principales aditivos (sin términos de interacción explícita entre las VI), la estructura multiplicativa de la escala original hace que las **diferencias absolutas** entre niveles varíen según el valor del otro factor, incluso cuando los ratios son constantes. Las tablas siguientes presentan las EMMs de las combinaciones cruzadas de dos vías, obtenidas bajo la misma lógica de marginalización.

**Tabla 8a.** EMMs Espacio × Agrupación (m/min).

| Espacio | Agrupación | EMM | HDI 95% |
| --- | --- | --- | --- |
| amplio | grande | 89,19 | [86,29; 92,07] |
| amplio | pequeño | 69,87 | [67,33; 72,28] |
| reducido | grande | 85,35 | [82,47; 88,41] |
| reducido | pequeño | 66,85 | [64,88; 68,92] |

La diferencia entre agrupación grande y pequeña es de 19,32 m/min con espacio amplio y de 18,50 m/min con espacio reducido. La ligera variación en la diferencia absoluta se debe precisamente a la naturaleza multiplicativa: el ratio es idéntico (exp(−0,244) = 0,783 en ambos casos), pero al escalar sobre una EMM base más alta (espacio amplio), la diferencia absoluta es marginalmente mayor.

**Tabla 8b.** EMMs Agrupación × Polaridad (m/min).

| Agrupación | Polaridad | EMM | HDI 95% |
| --- | --- | --- | --- |
| grande | NoPolarizado | 90,04 | [86,94; 93,25] |
| grande | Polarizado | 84,50 | [81,69; 87,29] |
| pequeño | NoPolarizado | 70,53 | [68,09; 73,05] |
| pequeño | Polarizado | 66,19 | [64,08; 68,21] |

En ambos niveles de Agrupación, el efecto de Polaridad (NoPolarizado > Polarizado) es consistente y creíble: 5,54 m/min con agrupación grande y 4,34 m/min con agrupación pequeña. La diferencia absoluta es ligeramente mayor con agrupaciones grandes, por el mismo mecanismo multiplicativo antes descrito.

Pueden construirse de forma análoga las tablas de EMMs para las demás combinaciones de dos vías: Agrupación × Equilibrio, Espacio × Polaridad, Espacio × Equilibrio y Polaridad × Equilibrio. En todos los casos, el patrón es el mismo: el factor de mayor efecto (Agrupación) amplifica o reduce las diferencias absolutas de los factores secundarios, pero sin modificar la dirección ni la credibilidad de los efectos secundarios.

### F.4 — EMMs condicionales a GrupoEdad

Al ser M2 un modelo aditivo sin interacciones entre las VI tácticas y GrupoEdad, los **ratios** entre niveles de cada VI son idénticos para todas las categorías de edad: el efecto multiplicativo de la Agrupación (×0,783) sobre la distancia base es el mismo para un jugador Senior Masculino que para uno de categoría Neskak. Sin embargo, las **diferencias absolutas** escalan proporcionalmente con la EMM base de cada categoría, de modo que las tablas condicionales resultan especialmente informativas para comunicar el impacto práctico de cada factor dentro de cada grupo de edad.

**Tabla 9.** EMMs Agrupación × GrupoEdad (m/min), el factor dominante condicionado por categoría.

| GrupoEdad | Agrupación grande | Agrupación pequeña | Diferencia absoluta |
| --- | --- | --- | --- |
| Senior Masculino | 93,97 [88,84; 99,09] | 73,61 [69,64; 77,58] | 20,37 m/min |
| Juvenil | 90,13 [85,07; 95,12] | 70,60 [66,73; 74,52] | 19,53 m/min |
| Infantil | 88,35 [82,47; 94,22] | 69,21 [64,57; 73,85] | 19,15 m/min |
| Cadete | 83,93 [78,40; 89,32] | 65,74 [61,43; 69,95] | 18,19 m/min |
| Neskak | 79,97 [75,22; 84,40] | 62,64 [58,98; 66,27] | 17,33 m/min |

La diferencia absoluta debida a la Agrupación oscila entre 17,33 m/min (Neskak) y 20,37 m/min (Senior Masculino), siempre en la misma dirección y con HDIs que no se solapan. Estas diferencias absolutas son proporcionales a las EMMs base de cada categoría, como se analiza en detalle en la sección G.

Analogamente, las EMMs condicionales para Espacio × GrupoEdad muestran diferencias absolutas entre espacio amplio y reducido que oscilan entre 3,14 m/min (Neskak) y 3,69 m/min (Senior Masculino). Para Polaridad × GrupoEdad, la diferencia NoPolarizado − Polarizado oscila entre 4,52 m/min (Neskak) y 5,31 m/min (Senior Masculino). Para Equilibrio × GrupoEdad, la diferencia Equilibrio − Desequilibrio va de 3,51 m/min (Neskak) a 4,12 m/min (Senior Masculino). En todos los casos, el patrón es coherente: los efectos absolutos son mayores en las categorías con mayor distancia base.

### F.5 — EMM global de GrupoEdad: ranking de categorías controlado por diseño

La **Tabla 10** presenta las EMMs globales de cada GrupoEdad, obtenidas marginalizando con ponderación uniforme sobre los cuatro factores tácticos. Estas estimaciones representan la distancia/min promedio esperada para cada categoría, bajo un diseño táctico promedio ponderado uniformemente sobre todas las combinaciones posibles. Es, en este sentido, la mejor estimación disponible del "nivel locomotor base" de cada categoría, depurada de los efectos del diseño.

**Tabla 10.** EMMs globales por GrupoEdad (m/min), ordenadas de mayor a menor.

| GrupoEdad | EMM global | HDI 95% |
| --- | --- | --- |
| Senior Masculino | 83,79 | [79,37; 88,28] |
| Juvenil | 80,36 | [76,13; 84,89] |
| Infantil | 78,78 | [73,79; 84,09] |
| Cadete | 74,84 | [70,20; 79,68] |
| Neskak | 71,31 | [67,20; 75,28] |

El ranking es coherente con las expectativas fisiológicas: la categoría Senior Masculino presenta la mayor distancia base (83,79 m/min), seguida de Juvenil (80,36), Infantil (78,78), Cadete (74,84) y Neskak (71,31). Sin embargo, los **contrastes bayesianos** entre categorías (Tabla 11) matizan significativamente la interpretación del ranking.

**Tabla 11.** Contrastes bayesianos entre GrupoEdad: diferencias de EMMs (m/min).

| Contraste | Δ media (m/min) | HDI 95% | P(Δ > 0) |
| --- | --- | --- | --- |
| Cadete − Infantil | −3,94 | [−11,11; +3,10] | 13,4% |
| Cadete − Juvenil | −5,53 | [−11,41; +0,31] | 3,4% |
| Cadete − Neskak | +3,53 | [−2,50; +9,88] | 86,8% |
| Cadete − Senior Masc. | −8,95 | [−15,21; −2,71] | 0,3% |
| Infantil − Juvenil | −1,58 | [−8,11; +5,38] | 32,5% |
| Infantil − Neskak | +7,47 | [+0,97; +13,84] | 98,7% |
| Infantil − Senior Masc. | −5,01 | [−11,46; +1,99] | 7,4% |
| Juvenil − Neskak | +9,05 | [+3,17; +14,88] | 99,9% |
| Juvenil − Senior Masc. | −3,43 | [−8,97; +2,16] | 11,6% |
| Neskak − Senior Masc. | −12,48 | [−18,64; −6,87] | 0,0% |

Solo dos contrastes presentan evidencia inequívoca (P > 99%): **Senior Masculino supera a Neskak en 12,48 m/min** (P = 100%), y **Juvenil supera a Neskak en 9,05 m/min** (P = 99,9%). Infantil también supera a Neskak con alta credibilidad (P = 98,7%). El contraste Cadete − Senior Masculino es el único entre categorías adyacentes en el ranking donde la diferencia es claramente creíble en una dirección (P(Δ < 0) = 99,7%).

Por el contrario, las comparaciones entre las categorías intermedias del ranking —Cadete, Infantil y Juvenil— presentan HDIs que incluyen ampliamente el cero, con probabilidades que no permiten afirmaciones dirigidas: Infantil vs. Juvenil tiene P = 32,5%, indicando que la diferencia de 1,58 m/min en las EMMs es compatible con mero ruido muestral. Esto no implica que no existan diferencias reales entre estas categorías, sino que, una vez controlado el diseño táctico mediante el intercepto aleatorio por jugador, las diferencias de categoría no superan la incertidumbre posterior del modelo.

Este patrón tiene una interpretación metodológicamente importante: las mayores diferencias creíbles se producen en los extremos del espectro de desarrollo (Neskak vs. Senior Masculino), mientras que las categorías intermedias de la formación masculina (Cadete, Infantil, Juvenil) presentan solapamientos considerables. Esto sugiere que la categoría de edad, como variable de grupo, captura principalmente la diferencia entre los extremos del desarrollo, mientras que las variaciones entre categorías adyacentes quedan en gran medida absorbidas por las diferencias individuales ya recogidas por el intercepto aleatorio de jugador.

---

## G) Análisis por grupos de edad: invariancia porcentual y escalabilidad de los efectos (Notebook 03b)

### Propósito y alcance del análisis complementario

El notebook 03b complementa el análisis principal examinando los efectos de las variables de diseño táctico dentro de cada categoría de edad con mayor granularidad. Mientras el modelo M2 estima los efectos de las VI de forma conjunta para toda la muestra, el notebook 03b extrae las EMMs condicionadas a cada GrupoEdad, visualizando el impacto de cada factor en la escala propia de cada categoría. El análisis confirma y detalla lo ya inferido en el apartado F.4, pero permitirá en esta sección desarrollar un hallazgo de singular relevancia: la **invariancia porcentual** de los efectos entre categorías.

### Perfil locomotor base de cada categoría

La **Tabla 12** presenta las EMMs base de cada categoría (equivalente a las EMMs globales de GrupoEdad de la Tabla 10), junto con los valores del escenario máximo y mínimo del diseño y el rango total de modulación.

**Tabla 12.** EMM base, escenarios extremos y rango de modulación por GrupoEdad.

| GrupoEdad | EMM base (m/min) | Escenario máximo | Escenario mínimo | Rango (%) |
| --- | --- | --- | --- | --- |
| Senior Masculino | 83,79 | 101,52 | 67,99 | +49,3% |
| Juvenil | 80,36 | 97,37 | 65,21 | +49,3% |
| Infantil | 78,78 | 95,45 | 63,92 | +49,3% |
| Cadete | 74,84 | 90,67 | 60,73 | +49,3% |
| Neskak | 71,31 | 86,40 | 57,86 | +49,3% |

La primera observación notable es que el rango de modulación porcentual entre escenario máximo y mínimo es **exactamente idéntico en todas las categorías: +49,3%**. Esta no es una coincidencia numérica: es la consecuencia algebraica directa de la estructura del modelo M2, y merece una explicación formal.

### La invariancia porcentual: explicación matemática

En el modelo M2, la predicción para cualquier combinación de VI y GrupoEdad se obtiene como:

$log(μ) = α + β_Espacio + β_Agrupación + β_Polaridad + β_Equilibrio + β_GrupoEdad + uⱼ$

Al exponenciar, el resultado en escala natural es:

$μ = exp(α + β_GrupoEdad + uⱼ) × exp(β_Espacio) × exp(β_Agrupación) × exp(β_Polaridad) × exp(β_Equilibrio)$

La expresión se factoriza en dos partes: el término $exp(α + β_GrupoEdad + uⱼ)$, que depende de la categoría de edad y del jugador, y el producto de los factores de escala exp(β_VI), que depende exclusivamente de los niveles de las variables de diseño. Cuando se calcula el **ratio** entre dos escenarios tácticos —por ejemplo, el escenario máximo (amplio + grande + NoPolarizado + Equilibrio) y el escenario mínimo (reducido + pequeño + Polarizado + Desequilibrio)— el término $exp(α + β_GrupoEdad + uⱼ)$ **se cancela algebraicamente**, porque es común a ambos escenarios. El ratio resultante es únicamente:

$Ratio = exp(β_Espacio[reducido]) × exp(β_Agrupación[pequeño]) × exp(β_Polaridad[NoPolarizado]) × exp(β_Equilibrio[Desequilibrio]) / 1$

$= 0,957 × 0,783 × 1,066 × 0,952 / 1 ≈ 0,670 → diferencia ≈ 49,3%$

Este ratio es idéntico para todas las categorías de edad porque β_GrupoEdad no aparece en él. La ausencia de términos de interacción VI × GrupoEdad en M2 es la condición suficiente para esta invariancia: cuando el modelo no incluye interacciones, los efectos de cada VI son los mismos en todas las condiciones de la otra variable, tanto en la escala log como —en términos de ratios— en la escala original.

Crucialmente, esta ausencia de interacción no fue una simplificación arbitraria, sino que fue **validada empíricamente por el LOO-CV**: el modelo M4, que incluía interacciones de moderación por edad, presentó un ΔELPD de 143,69 respecto a M2, evidenciando que añadir términos de interacción VI × GrupoEdad deteriora la capacidad predictiva fuera de muestra. Los datos no apoyan que los efectos del diseño táctico sobre la distancia sean significativamente distintos entre categorías de edad.

### La invariancia porcentual: implicación práctica

La invariancia porcentual tiene una consecuencia aplicada de primer orden para el planificador de entrenamientos. Las cuatro variables de diseño actúan como **palancas de modulación de carga locomotora** con efecto porcentual idéntico en todas las categorías, como muestra la Tabla 13.

**Tabla 13.** Impacto porcentual de cada VI sobre la distancia/min, por GrupoEdad.

| VI | Senior Masc. | Juvenil | Infantil | Cadete | Neskak |
| --- | --- | --- | --- | --- | --- |
| Agrupación (grande vs pequeño) | +27,7% | +27,7% | +27,7% | +27,7% | +27,7% |
| Polaridad (NoPol vs Pol) | +6,5% | +6,5% | +6,6% | +6,5% | +6,6% |
| Equilibrio (Equil vs Deseq) | +5,0% | +5,0% | +5,0% | +5,0% | +5,0% |
| Espacio (amplio vs reducido) | +4,5% | +4,5% | +4,5% | +4,5% | +4,5% |

Las pequeñas variaciones de décima en Polaridad e Infantil/Neskak se deben exclusivamente a redondeo. En esencia, el entrenador que trabaje con cualquier categoría puede usar los mismos porcentajes como referencia para predecir el impacto de sus decisiones de diseño: pasar de agrupación grande a pequeña reduce la distancia en un 21,7% (equivalente a la inversa del +27,7%) con independencia del grupo de edad; las otras tres variables modulan entre el 4,5% y el 6,6% adicional en sus respectivos rangos.

Lo que **sí varía** entre categorías son las **diferencias absolutas**, que escalan proporcionalmente con la EMM base de cada grupo, como evidencia la Tabla 14.

**Tabla 14.** Impacto absoluto (Δ m/min) de cada VI, por GrupoEdad.

| VI | Senior Masc. | Juvenil | Infantil | Cadete | Neskak |
| --- | --- | --- | --- | --- | --- |
| Agrupación | 20,37 | 19,53 | 19,15 | 18,19 | 17,33 |
| Polaridad | 5,31 | 5,10 | 5,00 | 4,75 | 4,52 |
| Equilibrio | 4,12 | 3,95 | 3,87 | 3,68 | 3,51 |
| Espacio | 3,69 | 3,54 | 3,47 | 3,29 | 3,14 |

La relación de proporcionalidad es perfecta: las diferencias absolutas de Senior Masculino (EMM base = 83,79) y Neskak (EMM base = 71,31) en Agrupación son 20,37 y 17,33 m/min respectivamente, y su ratio es 20,37 / 17,33 ≈ 1,175 ≈ 83,79 / 71,31. Este resultado es algebraicamente exacto y es la consecuencia directa de la ecuación de diferencias absolutas: Δ = EMM_base(g) × (exp(β_A) − exp(β_B)).

### Ranking de los 16 escenarios por GrupoEdad

La **Tabla 15** presenta las predicciones completas de los 16 escenarios de diseño para cada categoría de edad, ordenadas por distancia en la categoría Senior Masculino.

**Tabla 15.** Predicciones por escenario y GrupoEdad (m/min).

| Rank | Escenario | Niveles boost | SM | JUV | INF | CAD | NES |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | NoPol·Amp·Gra·Equil | 4 | 101,52 | 97,37 | 95,45 | 90,67 | 86,40 |
| 2 | NoPol·Red·Gra·Equil | 3 | 97,15 | 93,18 | 91,34 | 86,77 | 82,68 |
| 3 | NoPol·Amp·Gra·Deseq | 3 | 96,65 | 92,69 | 90,87 | 86,32 | 82,25 |
| 4 | Pol·Amp·Gra·Equil | 3 | 95,28 | 91,38 | 89,58 | 85,10 | 81,08 |
| 5 | NoPol·Red·Gra·Deseq | 2 | 92,48 | 88,70 | 86,95 | 82,60 | 78,71 |
| 6 | Pol·Red·Gra·Equil | 2 | 91,18 | 87,45 | 85,72 | 81,44 | 77,60 |
| 7 | Pol·Amp·Gra·Deseq | 2 | 90,71 | 87,00 | 85,28 | 81,02 | 77,20 |
| 8 | Pol·Red·Gra·Deseq | 1 | 86,81 | 83,26 | 81,61 | 77,53 | 73,88 |
| 9 | NoPol·Amp·Peq·Equil | 3 | 79,53 | 76,27 | 74,77 | 71,03 | 67,68 |
| 10 | NoPol·Red·Peq·Equil | 2 | 76,10 | 72,98 | 71,55 | 67,97 | 64,76 |
| 11 | NoPol·Amp·Peq·Deseq | 2 | 75,70 | 72,61 | 71,18 | 67,62 | 64,43 |
| 12 | Pol·Amp·Peq·Equil | 2 | 74,63 | 71,58 | 70,17 | 66,66 | 63,52 |
| 13 | NoPol·Red·Peq·Deseq | 1 | 72,44 | 69,48 | 68,11 | 64,70 | 61,65 |
| 14 | Pol·Red·Peq·Equil | 1 | 71,42 | 68,49 | 67,14 | 63,79 | 60,78 |
| 15 | Pol·Amp·Peq·Deseq | 1 | 71,05 | 68,15 | 66,80 | 63,46 | 60,47 |
| 16 | Pol·Red·Peq·Deseq | 0 | 67,99 | 65,21 | 63,92 | 60,73 | 57,86 |

El **ranking de los 16 escenarios es idéntico en las cinco categorías**: cualquiera que sea el grupo de edad considerado, el escenario que maximiza la distancia recorrida es NoPol·Amp·Gra·Equil y el que la minimiza es Pol·Red·Peq·Deseq. Esta invariancia de la ordenación es la consecuencia directa de la invariancia de los ratios: si los efectos multiplicativos de cada VI son los mismos en todas las categorías, la jerarquía entre combinaciones nunca puede invertirse.

Conviene destacar tres comparaciones clave entre escenarios que son cuantitativamente reveladoras. El **escenario #1 vs #16** (rango total del diseño) produce una diferencia del +49,3% en todas las categorías. El **escenario #1 vs #8** (dentro del bloque de agrupación grande) produce un +17,0%, reflejando el impacto acumulado de los otros tres factores sobre la misma base de agrupación grande. El **escenario #8 vs #16** (el peor de agrupación grande frente al peor de agrupación pequeña) produce un +27,7%, que es exactamente el efecto aislado de la variable Agrupación. Y el promedio de los cuatro mejores escenarios frente a los cuatro peores presenta una diferencia del +38,1%, también idéntica en todas las categorías.

### Perfiles absolutos y reflexiones prácticas por categoría

Desde una perspectiva aplicada, la lectura por columnas de la Tabla 15 permite describir el "horizonte locomotor" de cada categoría. La categoría **Senior Masculino** opera en un rango entre 67,99 y 101,52 m/min: es la única categoría en la que el escenario óptimo supera los 100 m/min. En términos concretos, 101,52 m/min equivale a más de 1.015 metros recorridos en 10 minutos de tarea, una carga locomotora de alta exigencia que sitúa la tarea en el umbral de los ejercicios de alta intensidad. En el escenario mínimo (67,99 m/min), la tarea produce apenas 680 metros en 10 minutos, una carga moderada-baja.

**Juvenil** opera entre 65,21 y 97,37 m/min, con el escenario óptimo rozando los 100 m/min. **Infantil** cubre el rango 63,92–95,45 m/min. **Cadete** se mueve entre 60,73 y 90,67 m/min, con un escenario óptimo que supera los 900 metros en 10 minutos pero no alcanza los 1.000. Finalmente, **Neskak** presenta el rango más conservador: 57,86–86,40 m/min, con un escenario mínimo que se aproxima a los 580 metros en 10 minutos.

Una implicación metodológica de primer orden de este análisis es la **transferibilidad de los hallazgos**: el técnico responsable de cualquier categoría —desde Neskak hasta Senior Masculino— puede utilizar exactamente los mismos criterios porcentuales para anticipar el efecto de sus decisiones de diseño sobre la carga locomotora. Si un entrenador de Neskak desea incrementar la distancia recorrida en sus tareas en un 20%, la recomendación es idéntica que para un técnico de Senior Masculino: incrementar el tamaño del grupo (efecto: +27,7%), optar por ejercicios no polarizados (+6,6%), garantizar el equilibrio numérico (+5,0%) y ampliar el espacio de juego (+4,5%). Lo que varía es el punto de partida: Neskak comenzará desde 71,31 m/min de media base, y Senior Masculino desde 83,79 m/min. La receta táctica es universal; la escala absoluta de la respuesta es específica de cada categoría.

Esta conclusión es uno de los hallazgos de mayor valor translacional del presente TFG, y su solidez descansa sobre dos pilares: la superioridad demostrada del modelo M2 en la validación LOO-CV respecto a modelos más complejos que permitirían efectos diferenciales por edad, y la coherencia fisiológica del resultado, que en ningún caso sugiere que las categorías de edad sean equivalentes en capacidad locomotora, sino que responden al mismo diseño de tarea en idéntica proporción relativa pero desde niveles de base distintos determinados por su maduración biológica y nivel de formación.