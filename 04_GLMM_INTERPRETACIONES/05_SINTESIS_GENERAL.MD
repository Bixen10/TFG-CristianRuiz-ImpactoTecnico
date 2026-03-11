# Síntesis integrada de los efectos del diseño táctico sobre el rendimiento técnico-físico

## Conclusiones transversales de los GLMM bayesianos por variable independiente

## Introducción

Los cuatro modelos GLMM bayesianos ajustados en las secciones precedentes —uno por cada variable dependiente (VD): **Toques/min**, **Golpeos +15 m/s / min**, **Distancia/min** y **HID/min** fueron analizados de forma independiente, lo que permitió identificar los efectos específicos de cada variable de diseño táctico sobre cada dimensión del rendimiento. Sin embargo, la lectura separada de cuatro modelos puede dificultar la visión de conjunto que necesita el profesional del entrenamiento para tomar decisiones de diseño informadas.
La presente sección ofrece una síntesis transversal organizada por variable independiente (VI), integrando los hallazgos de las cuatro VD en un marco unificado. El objetivo es responder, para cada factor de diseño táctico, a una pregunta global: ¿cómo afecta esta variable al rendimiento técnico-físico del jugador en su conjunto?


------------------------------------------------------------------------

# Tabla resumen de efectos

La Tabla 1 sintetiza los coeficientes exponenciados de los modelos finales para las cuatro VD. En los modelos Gamma (Toques, Distancia), `exp(β)` representa el factor multiplicativo sobre la media. En los modelos Hurdle Gamma (Golpeos, HID), se reportan por separado los efectos sobre el componente de probabilidad (ψ) y el componente de intensidad condicional (μ). Los valores en **negrita** indican efectos creíbles (HDI 95% que excluye el valor nulo).
  -------------------------------------------------------------------------------------------
  Variable     Toques/min   Golpeos/min ψ Golpeos/min μ Distancia/min   HID/min ψ  HID/min μ
               exp(β)       (OR)          exp(β)        exp(β)          (Δpp)      exp(β)
  ------------ ------------ ------------- ------------- --------------- ---------- ----------
  Espacio      1,389 ✓      0,565 ✓       0,851 ✓       0,957 ✓         −32,2 pp ✓ 0,292 ✓
  (red.                                                                            
  vs. amp.)                                                                        

  Agrupación   1,260 ✓      0,848 \~      1,363 ✓       0,783 ✓         −10,9 pp ✓ 1,225 ✓
  (peq.                                                                            
  vs. gra.)                                                                        

  Polaridad    1,448 ✓      0,432 ✓       0,972 ✗       1,066 ✓         +7,4 pp ✓  0,910 ✗
  (noPol                                                                           
  vs. pol.)                                                                        

  Equilibrio   1,047 ✗      0,883 ✗       1,057 ✗       0,952 ✓         +7,3 pp \~ 1,008 ✗
  (deseq.                                                                          
  vs. eq.)                                                                         
  -------------------------------------------------------------------------------------------

**Nota.**\
✓ = efecto creíble (HDI 95% excluye el valor nulo)\
✗ = no creíble\
\~ = marginalmente creíble

Red. = reducido\
Amp. = amplio\
Peq. = pequeño\
Gra. = grande\
NoPol = no polarizado\
Pol. = polarizado\
Deseq. = desequilibrio\
Eq. = equilibrio

Los modelos de **Toques** y **Distancia** son Gamma con enlace log; **Golpeos** y **HID** son Hurdle Gamma (`ψ = parte logística`, `μ = parte Gamma`).

------------------------------------------------------------------------

# Espacio (reducido vs. amplio)

El espacio es la única variable independiente que produce efectos creíbles en las cuatro variables dependientes sin excepción, y es además la única que, en los modelos Hurdle, actúa simultáneamente como interruptor —modificando la probabilidad de que ocurra el evento— y como modulador —modificando su intensidad condicional—. Sin embargo, la dirección favorable del efecto no es uniforme: cambia en función de la dimensión de rendimiento considerada.

## Efecto sobre las variables técnicas

En las variables de naturaleza técnica, el espacio reducido es el nivel favorable. Para Toques/min, la reducción del espacio multiplica la tasa de contactos por 1,389 (incremento del 38,9%), constituyendo el segundo efecto en importancia para esta VD. El mecanismo es coherente con la lógica del entrenamiento: un espacio reducido acorta las distancias entre jugadores, incrementa la densidad de interacciones y obliga a decisiones más rápidas, lo que se traduce en una mayor frecuencia de contactos con el balón. Para Golpeos/min, el espacio reducido actúa en ambos planos del modelo Hurdle con la misma dirección desfavorable: reduce tanto la probabilidad de que ocurra un golpeo potente (OR = 0,565; reducción del 43% en las odds) como su intensidad condicional (exp(β) = 0,851; reducción del 15%). La interpretación es mecánicamente clara: en espacios reducidos, los jugadores disponen de menos distancia de carrera libre antes de recibir el balón, lo que limita la acumulación de velocidad segmentaria antes del contacto.

## Efecto sobre las variables físicas

En las variables de naturaleza física, la dirección se invierte. El espacio amplio favorece tanto la distancia total recorrida (exp(β) = 1/0,957 ≈ 1,045; incremento del 4,3%) como, de forma mucho más pronunciada, la carrera de alta intensidad. Para HID/min, el espacio constituye el factor absolutamente dominante de todo el estudio: el espacio reducido reduce la probabilidad de sprint en 32,2 puntos porcentuales y la intensidad condicional en un 70,8%, generando un ratio entre los escenarios extremos de 9,47 veces. Ninguna otra variable dependiente del TFG exhibe una sensibilidad tan extrema a las condiciones espaciales de la tarea.

## Conclusión integrada

El espacio opera como una palanca de compromiso técnico-físico. Reducirlo maximiza la participación técnica (toques) a costa de suprimir casi por completo la carrera de alta intensidad; ampliarlo genera el efecto inverso. Esta tensión constituye el hallazgo transversal de mayor relevancia para la planificación del entrenamiento, porque obliga al profesional a priorizar explícitamente qué dimensión del rendimiento desea estimular en cada tarea. No existe un nivel de espacio universalmente favorable: la elección óptima depende del objetivo específico de la sesión.

------------------------------------------------------------------------

# Agrupación (pequeño vs. grande)

La agrupación produce efectos creíbles en las cuatro variables dependientes, pero con un patrón de oposición técnico-físico análogo al observado para el espacio. Este factor presenta además el perfil más complejo en los modelos Hurdle, con efectos de signo opuesto en los componentes de probabilidad e intensidad condicional.

## Efecto sobre la actividad técnica

Para Toques/min, los grupos pequeños incrementan la tasa de contactos en un 26,0% (exp(β) = 1,260), constituyendo el tercer efecto en importancia. La explicación es a la vez aritmética y táctica: con menos jugadores, el balón se reparte entre un número menor de participantes, aumentando mecánicamente la frecuencia de contacto individual. Para Golpeos/min, la agrupación actúa como modulador puro con dirección cruzada entre componentes: los grupos pequeños no aumentan la probabilidad de golpeo —incluso la reducen marginalmente (OR = 0,848)— pero incrementan la intensidad condicional en un 36% (exp(β) = 1,363), el mayor efecto sobre el componente μ de los golpeos. En grupos pequeños, cada jugador acumula más posesiones en posición de finalización, lo que eleva la tasa condicional de golpeos, aunque la menor diversidad de situaciones colectivas reduce ligeramente la probabilidad de que el evento se active.

## Efecto sobre la carga locomotora

El resultado más contundente de la agrupación se observa sobre la distancia recorrida, donde constituye el factor absolutamente dominante: los grupos grandes generan un 27,7% más de distancia por minuto que los pequeños (exp(β) = 0,783 para pequeño vs. grande), un efecto que casi triplica en magnitud al segundo factor. Los ocho escenarios con agrupación grande superan sin excepción a los ocho con agrupación pequeña en las predicciones factoriales, sin solapamiento alguno entre ambos bloques. Para HID/min, la agrupación replica el patrón de signo cruzado observado en los golpeos: los grupos pequeños reducen la probabilidad de sprint (−10,9 puntos porcentuales) pero incrementan la intensidad condicional (+22,5%), y el efecto neto sobre la esperanza marginal no resulta creíble porque ambas fuerzas se compensan.

## Conclusión integrada

La agrupación pequeña favorece la intensidad técnica individual —más toques por jugador, más golpeos condicionales por jugador— pero reduce la carga locomotora total. Los grupos grandes generan mayor volumen de desplazamiento y mayor probabilidad de que ocurran eventos de alta intensidad, aunque con menor frecuencia individual cuando estos eventos se producen. Para el planificador, la agrupación funciona como un regulador del equilibrio entre participación técnica individual y exigencia física colectiva: formatos reducidos (3v3, 4v4) para objetivos técnicos; formatos amplios (5v5 o más) para objetivos de carga locomotora.

------------------------------------------------------------------------

# Polaridad (no polarizado vs. polarizado)

La polaridad presenta el perfil más asimétrico de las cuatro variables independientes: es el factor dominante para la actividad técnica pero opera como interruptor puro —afectando a la probabilidad del evento sin modificar su intensidad condicional— para las variables de eventos discretos.

## Efecto sobre la actividad técnica y la distancia

Para Toques/min, la no polarización produce el mayor efecto del modelo: un incremento del 44,8% (exp(β) = 1,448), dominando el ranking de los 16 escenarios factoriales por encima de cualquier otro factor. La eliminación de la orientación a portería distribuye la participación de forma más homogénea entre jugadores, incrementando la frecuencia de contacto individual. Los cuatro primeros escenarios del ranking de toques son todos no polarizados, y los cuatro últimos son todos polarizados, independientemente de cómo se configuren los demás factores. Para Distancia/min, el efecto es más modesto pero creíble (exp(β) = 1,066; incremento del 6,6% a favor de tareas no polarizadas), coherente con la mayor movilidad omnidireccional que generan los ejercicios sin dirección preferencial de juego.

## Efecto sobre los eventos de alta intensidad

En los modelos Hurdle, la polaridad actúa exclusivamente sobre la probabilidad del evento, no sobre su intensidad condicional. Para Golpeos/min, el juego polarizado multiplica las odds de que ocurra al menos un golpeo potente por 2,31 (OR = 1/0,432), convirtiéndola en el interruptor más potente del modelo: sin portería como objetivo, desaparece el contexto de finalización que genera acciones de golpeo a alta velocidad. Sin embargo, una vez que el jugador golpea, la presencia o ausencia de portería no modifica la frecuencia condicional de golpeos (exp(β) = 0,972, no creíble). Para HID/min, el patrón se replica: el juego polarizado incrementa la probabilidad de sprint en 7,4 puntos porcentuales, sin afectar la intensidad condicional (exp(β) = 0,910, no creíble). En ambos casos, la polaridad determina si el evento ocurre, no cuán intenso es cuando ocurre.

## Conclusión integrada

La polaridad canaliza la naturaleza del estímulo de entrenamiento. Las tareas no polarizadas maximizan el volumen técnico y la distancia total recorrida; las tareas polarizadas activan los eventos de alta intensidad y potencia —golpeos fuertes y sprint—. El entrenador utiliza esta variable para decidir entre un estímulo técnico-participativo (rondos, juegos de conservación) o un estímulo competitivo orientado a la intensidad (juegos con finalización, transiciones a portería). La polaridad es, de las cuatro variables de diseño, la que más claramente define el carácter cualitativo de la tarea.

------------------------------------------------------------------------

# Equilibrio (desequilibrio vs. equilibrio)

El equilibrio es la variable independiente con menor influencia global sobre el rendimiento técnico-físico. De las ocho posibles combinaciones entre las cuatro VD y los componentes de los modelos Hurdle, el equilibrio solo alcanza credibilidad clara en una de ellas.

## Patrón de efectos por VD

Para Toques/min, el efecto del equilibrio no es creíble (exp(β) = 1,047; HDI 95% que incluye el valor 1). Para Golpeos/min, tampoco es creíble en ninguno de los dos componentes del modelo Hurdle (OR = 0,883 en ψ; exp(β) = 1,057 en μ; ambos con HDI que incluyen el valor nulo). Para HID/min, muestra un efecto marginal como interruptor débil en la parte ψ (+7,3 puntos porcentuales, marginalmente creíble), sin efecto sobre la intensidad condicional, y con un efecto neto sobre la esperanza marginal que no resulta creíble.
La única variable dependiente donde el equilibrio alcanza credibilidad clara es la distancia recorrida: las situaciones equilibradas generan un 4,8% más de distancia por minuto que las desequilibradas (exp(β) = 0,952 para desequilibrio vs. equilibrio). La interpretación es que la simetría numérica incentiva esfuerzos de cobertura y presión más distribuidos entre ambos equipos, incrementando el volumen locomotor total. Aun así, es el efecto más pequeño de los cuatro factores para esta VD.

## Conclusión integrada

La distribución numérica entre equipos es una variable de diseño de baja potencia para modular el rendimiento técnico-físico medido con sensores PlayerMaker. Esto no implica que el equilibrio sea irrelevante para la práctica del entrenamiento: puede tener efectos significativos sobre dimensiones no medidas en el presente estudio, como la toma de decisiones, el comportamiento táctico colectivo o los aspectos psicológicos de la tarea. Sin embargo, en lo que respecta a las cuatro métricas de rendimiento físico-técnico analizadas, el entrenador puede determinar el equilibrio numérico libremente en función de otros objetivos pedagógicos sin que ello afecte de forma significativa al perfil de carga técnico-física de la sesión.

# Síntesis global: la tensión técnico-física como hallazgo transversal

La integración de los cuatro modelos GLMM revela un patrón transversal que constituye el hallazgo de mayor relevancia conceptual del análisis: las condiciones de diseño táctico que maximizan la actividad técnica son, en gran medida, las opuestas a las que maximizan la intensidad física. El espacio reducido, la agrupación pequeña y la no polarización favorecen el volumen de contactos técnicos; el espacio amplio, la agrupación grande y la polarización favorecen la carga locomotora y los eventos de alta intensidad.
Esta tensión no es una limitación del análisis, sino una propiedad fundamental de la relación entre el diseño de tareas de entrenamiento y el rendimiento: no existe una configuración que maximice simultáneamente todas las dimensiones del rendimiento técnico-físico. La implicación práctica es que el diseño de la sesión de entrenamiento debe concebirse como un proceso de priorización consciente, en el que el cuerpo técnico selecciona qué dimensiones del rendimiento estimular en cada tarea en función de los objetivos específicos de la sesión, el microciclo y el periodo competitivo.


------------------------------------------------------------------------

# Jerarquía de efectos por variable dependiente

La Tabla 2 presenta la jerarquía de importancia de los factores de diseño táctico para cada VD, ordenados de mayor a menor magnitud de efecto.

  ----------------------------------------------------------------------------
  Rango          Toques/min     Golpeos/min     Distancia/min   HID/min
  -------------- -------------- --------------- --------------- --------------
  1.º            Polaridad      Polaridad       Agrupación      Espacio
                 (+44,8%)       (interruptor ψ) (+27,7%)        (−70,8% μ)

  2.º            Espacio        Agrupación      Polaridad       Agrupación (±,
                 (+38,9%)       (modulador μ)   (+6,6%)         neto n.s.)

  3.º            Agrupación     Espacio         Equilibrio      Polaridad
                 (+26,0%)       (bifactorial)   (+4,8%)         (interruptor
                                                                ψ)

  4.º            Equilibrio     Equilibrio      Espacio (+4,3%) Equilibrio
                 (n.s.)         (n.s.)                          (débil ψ)
  ----------------------------------------------------------------------------

**Nota.**

Nota. Los porcentajes corresponden al cambio relativo entre niveles. n.s. = no significativo (efecto no creíble). Para los modelos Hurdle, se indica el componente afectado (ψ = probabilidad, μ = intensidad condicional).

La lectura por columnas de la Tabla 2 revela que ningún factor de diseño mantiene la misma posición jerárquica en las cuatro VD: la Polaridad domina los toques pero es secundaria para la distancia; el Espacio domina el HID pero es el factor más débil para la distancia; la Agrupación domina la distancia pero es el tercer factor para los toques. Esta heterogeneidad refuerza la necesidad de concebir el rendimiento como un fenómeno multidimensional que no puede reducirse a un único indicador, y valida la estrategia metodológica de modelizar cada VD por separado antes de integrar las conclusiones.

------------------------------------------------------------------------

# Varianza explicada por el diseño táctico

La Tabla 3 compara la varianza explicada por el diseño táctico (R² marginal) en los cuatro modelos, junto con la contribución del componente inter-jugador.

  VD              R² marginal (diseño)   Varianza jugador   Residual   Modelo
  --------------- ---------------------- ------------------ ---------- -------------
  Toques/min      20,8%                  10,0%              69,1%      M1 (Gamma)
  Golpeos/min     3,0%                   11,4%              85,6%      M1 (Hurdle)
  Distancia/min   11,5%                  25,5%              60,5%      M2 (Gamma)
  HID/min         16,0%                  7,7%               66,8%      M2 (Hurdle)

La varianza explicada por el diseño táctico oscila entre el 3,0% (Golpeos) y el 20,8% (Toques), lo que refleja diferencias sustantivas en la sensibilidad de cada variable al contexto de la tarea. Los toques, como resultado del flujo continuo de participación, son altamente sensibles al diseño; los golpeos potentes, como eventos oportunistas, son resistentes a la manipulación táctica. Notablemente, para Distancia/min, el componente inter-jugador (25,5%) supera con creces al diseño táctico (11,5%), reflejando que la capacidad locomotora individual es la fuente de variabilidad dominante para esta métrica.