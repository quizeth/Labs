# Laboratorio 2: Técnicas de prompting aplicadas a datos empresariales

## Introducción

La misma tarea puede producir resultados muy distintos según la cantidad de contexto, los ejemplos, las restricciones y la estructura incorporados al prompt. El objetivo principal de este laboratorio es aprender a controlar el comportamiento de Microsoft 365 Copilot mediante distintas estrategias de prompting.

Compararás cuatro técnicas utilizando los anexos del dossier proporcionado: **zero-shot**, **few-shot**, **superprompt** y **razonamiento verificable**. Analizarás cómo cada técnica afecta al formato, la consistencia, la trazabilidad y la justificación de las respuestas.

!!! note "Criterio de evaluación"
    No siempre existe una única respuesta válida. Diferentes respuestas pueden considerarse correctas si están justificadas por los datos, respetan las restricciones del prompt y mantienen una metodología coherente.

### Objetivos de aprendizaje

Al finalizar el laboratorio, podrás:

- Aplicar un prompt zero-shot para resolver una tarea sin proporcionar ejemplos previos.

- Utilizar ejemplos few-shot para controlar los criterios de clasificación y el formato de una respuesta.

- Construir un superprompt que integre rol, contexto, tarea, restricciones y formato de salida.

- Solicitar fórmulas, sustituciones numéricas y resultados intermedios para verificar un análisis cuantitativo.

- Diferenciar entre datos documentados, cálculos e inferencias generadas por Copilot.

- Evaluar la trazabilidad y la justificación de una respuesta con respecto a la fuente utilizada.

- Comparar las ventajas y limitaciones de distintas técnicas de prompting.

- Seleccionar una técnica de prompting adecuada según la tarea, el nivel de control requerido y el riesgo de error.

### Información del laboratorio

**Duración estimada:** 30 minutos.

**Material necesario:**

- Microsoft 365 Copilot Chat.

- El archivo [Documentos de Trabajo y Datasets para Cuadernos de Laboratorio](recursos/files/Documentos%20de%20Trabajo%20y%20Datasets%20para%20Cuadernos%20de%20Laboratorio.pdf).

- Anexo 1: Estado financiero y balance de situación.

- Anexo 2: Dataset de incidencias de soporte técnico y comercial.

- Anexo 3: Fichas de evaluación de proveedores TI.

!!! note "Recomendación"
    Trabaja en una única conversación mientras utilices el mismo anexo. Abre una conversación nueva cuando se indique para evitar que los ejemplos o las instrucciones anteriores condicionen la respuesta.

## Caso práctico

Debes analizar información empresarial relacionada con finanzas, incidencias de soporte y evaluación de proveedores. Para ello, utilizarás varias técnicas de prompting y compararás el grado de control que ofrecen sobre las respuestas de Copilot.

La evaluación no debe limitarse a comprobar si la respuesta coincide exactamente con un resultado esperado. También debes verificar:

- La metodología utilizada.

- La trazabilidad entre la respuesta y los datos del anexo.

- La justificación de clasificaciones, cálculos y conclusiones.

- El cumplimiento de las restricciones del prompt.

- La separación entre datos documentados e inferencias.

### Tarea 1: Zero-shot prompting

#### Objetivo

Aplicar un prompt sin ejemplos previos y detectar qué información, criterios o restricciones faltan para controlar la respuesta de Copilot.

Un prompt **zero-shot** plantea una tarea sin proporcionar ejemplos de entrada y salida. Esta técnica permite obtener resultados con rapidez, pero ofrece menos control sobre los criterios, los supuestos y el formato utilizados.

#### Pasos

1. Abre Microsoft 365 Copilot Chat.

2. Adjunta el dossier mediante **Agregar y administrar orígenes**.

3. Copia y envía el siguiente prompt:

    ```text
    Utiliza el Anexo 2 del documento adjunto.

    Clasifica los tickets TCK-105, TCK-106, TCK-107 y TCK-108 por categoría y severidad.
    ```

4. Revisa la respuesta generada por Copilot.

5. Anota las categorías utilizadas.

6. Registra los niveles de severidad asignados.

7. Identifica el formato elegido por Copilot.

8. Señala cualquier criterio o definición que Copilot haya supuesto sin que aparezca en el prompt.

#### Comprobación

Comprueba que:

- Se han procesado los cuatro tickets solicitados.

- Cada categoría y cada nivel de severidad pueden relacionarse con información presente en el Anexo 2.

- La metodología de clasificación es consistente entre los cuatro tickets.

- Puedes distinguir los datos extraídos del anexo de los criterios añadidos por Copilot.

- Los supuestos no definidos en el prompt están identificados.

- Las clasificaciones incluyen una justificación suficiente o, si no la incluyen, has registrado esa limitación.

- La evaluación se basa en la trazabilidad y la coherencia metodológica, no solo en la coincidencia exacta con una respuesta concreta.

#### Reflexiona

- ¿Qué información faltaba en el prompt para controlar mejor la clasificación?

- ¿Qué supuestos realizó Copilot sobre las categorías, los niveles de severidad o el formato?

- ¿La clasificación es consistente entre los cuatro tickets?

- ¿Qué elementos de la respuesta no estaban definidos en el prompt?

- ¿Sería posible reproducir la misma metodología con otros tickets utilizando únicamente este prompt?

### Tarea 2: Few-shot prompting

#### Objetivo

Utilizar ejemplos de entrada y salida para controlar la clasificación, las severidades, las justificaciones y el formato generado por Copilot.

El **few-shot prompting** incorpora ejemplos que muestran el patrón que Copilot debe seguir. Los ejemplos pueden mejorar la consistencia, pero también pueden transmitir errores, criterios incompletos o sesgos.

#### Pasos

1. Abre una conversación nueva en Microsoft 365 Copilot Chat.

2. Adjunta de nuevo el dossier.

3. Copia y envía el siguiente prompt:

    ```text
    Eres un clasificador de incidencias de soporte técnico y comercial.

    Clasifica cada entrada con estos campos:
    - categoria
    - severidad: Crítica, Alta, Media o Baja
    - justificacion

    Sigue el patrón de estos ejemplos del Anexo 2:

    [EJEMPLO 1]
    Entrada: TCK-101. La API de autenticación devuelve error 500 para todos los usuarios de la región EMEA desde hace 10 minutos.
    Salida: {"categoria":"Autenticación/IAM","severidad":"Crítica","justificacion":"Fallo generalizado de autenticación en producción para una región completa"}

    [EJEMPLO 2]
    Entrada: TCK-102. Se solicita acceso de solo lectura al repositorio de auditoría para una nueva incorporación antes del viernes.
    Salida: {"categoria":"Gestión de accesos","severidad":"Baja","justificacion":"Solicitud planificada sin interrupción de servicio"}

    [EJEMPLO 3]
    Entrada: TCK-103. El nodo secundario de la base de datos registra un 88 % de ocupación en /data. El nodo primario no presenta degradación.
    Salida: {"categoria":"Almacenamiento/Bases de datos","severidad":"Media","justificacion":"Riesgo de capacidad sin degradación actual del servicio principal"}

    [EJEMPLO 4]
    Entrada: TCK-104. Se detectan 4.000 intentos de fuerza bruta contra el servicio SSH perimetral desde un rango IP anómalo.
    Salida: {"categoria":"Seguridad perimetral","severidad":"Alta","justificacion":"Actividad hostil activa contra un servicio expuesto"}

    Ahora procesa individualmente los tickets TCK-105, TCK-106, TCK-107 y TCK-108.

    Devuelve únicamente un array JSON válido. No añadas introducción ni conclusión.
    ```

4. Comprueba si Copilot reproduce el patrón mostrado en los ejemplos.

5. Compara la respuesta con la obtenida mediante zero-shot.

6. Identifica los cambios producidos en las categorías, las severidades, las justificaciones y el formato.

7. Registra cualquier clasificación que parezca condicionada por los ejemplos en lugar de derivarse directamente del ticket.

#### Comprobación

Comprueba que:

- Copilot procesa los cuatro tickets solicitados.

- Los cuatro objetos mantienen los mismos campos.

- Se utilizan únicamente las severidades permitidas: `Crítica`, `Alta`, `Media` o `Baja`.

- Cada justificación se basa en el texto del ticket correspondiente.

- Las categorías siguen un criterio coherente con los ejemplos.

- El resultado es un array JSON válido.

- No se añade una introducción, una conclusión ni texto fuera del array JSON.

- Puede trazarse cada clasificación hasta los datos del ticket y el patrón proporcionado por los ejemplos.

- Las justificaciones explican el criterio aplicado y no se limitan a repetir la categoría.

- La metodología es consistente aunque la respuesta no coincida literalmente con otra clasificación posible.

#### Reflexiona

- ¿Los ejemplos han mejorado el formato, la clasificación o ambos?

- ¿Qué cambio concreto observas respecto al resultado zero-shot?

- ¿Cómo han influido los ejemplos en la elección de categorías y severidades?

- ¿Algún ejemplo ha podido inducir un sesgo o limitar categorías alternativas que también serían válidas?

- ¿Qué ocurriría si uno de los ejemplos contuviera una clasificación incorrecta?

- ¿Los ejemplos describen suficientemente el criterio general o Copilot se ha limitado a imitar patrones superficiales?

### Tarea 3: Superprompt

#### Objetivo

Construir y evaluar un prompt estructurado que controle el rol, el contexto, la tarea, las restricciones y el formato de una comparación de proveedores.

Un **superprompt** reúne en una sola instrucción los componentes necesarios para delimitar una tarea compleja. Resulta útil cuando se necesita una salida consistente, trazable y reutilizable.

#### Pasos

1. Abre una conversación nueva en Microsoft 365 Copilot Chat.

2. Adjunta el dossier.

3. Copia y envía el siguiente prompt:

    ```text
    [ROL]
    Actúa como consultor de compras tecnológicas especializado en evaluación de servicios cloud.

    [CONTEXTO]
    La organización debe comparar los tres proveedores descritos en el Anexo 3 del documento adjunto. La comparación se utilizará como borrador para una revisión interna de compras y cumplimiento.

    [TAREA]
    Compara TechCore Solutions, CloudScale Iberia y Nexus Global Operations según estos criterios:
    - SLA de disponibilidad.
    - Coste recurrente mensual sin IVA.
    - Tiempo y cobertura de respuesta para incidentes P1.
    - Penalización por incumplimiento.
    - Certificaciones declaradas.
    - Jurisdicción contractual.

    [RESTRICCIONES]
    1. Utiliza exclusivamente el Anexo 3.
    2. No emplees adjetivos subjetivos como "excelente", "robusto" o "mejor".
    3. No inventes ni completes datos ausentes.
    4. Si un dato no aparece, escribe "NO DECLARADO EN LA FUENTE".
    5. Separa los datos documentados de la valoración final.
    6. No presentes la valoración como una decisión definitiva de contratación.

    [FORMATO]
    1. Tabla Markdown con una fila por proveedor y una columna por criterio.
    2. Después de la tabla, incluye:
       - Opción de menor coste.
       - Opción con mayor SLA.
       - Principal riesgo contractual de cada proveedor.
    3. Finaliza con una comparación de coste-riesgo de un máximo de 80 palabras.
    ```

4. Revisa si Copilot incluye los tres proveedores.

5. Comprueba si la tabla contiene todos los criterios solicitados.

6. Contrasta cada dato de la respuesta con el Anexo 3.

7. Identifica cualquier dato completado, interpretado o inferido sin respaldo explícito en la fuente.

8. Comprueba si Copilot respeta las restricciones de lenguaje, separación de contenidos y extensión.

#### Comprobación

Comprueba que:

- Los importes coinciden con el Anexo 3.

- El coste se presenta como importe recurrente mensual sin IVA.

- Se distingue entre el tiempo de respuesta y la cobertura horaria para incidentes P1.

- El SLA de disponibilidad se reproduce sin modificar su significado.

- Las penalizaciones por incumplimiento se atribuyen al proveedor correcto.

- Las certificaciones se copian correctamente y no se completan con conocimientos externos.

- La jurisdicción aparece sin interpretaciones añadidas.

- Los datos ausentes se muestran como `NO DECLARADO EN LA FUENTE`.

- Los datos documentados están separados de la valoración final.

- La opción de menor coste y la opción con mayor SLA se justifican con datos visibles en la tabla.

- El principal riesgo contractual de cada proveedor puede trazarse hasta el Anexo 3.

- La comparación final se basa en los criterios anteriores.

- La comparación de coste-riesgo no supera las 80 palabras.

- La respuesta no presenta la valoración como una decisión definitiva de contratación.

- La metodología permite revisar cómo se ha obtenido cada conclusión.

#### Reflexiona

- ¿Qué componente ha tenido más impacto en el resultado: el rol, el contexto, las restricciones o el formato?

- ¿Qué evidencia de la respuesta permite atribuir ese impacto a un componente concreto?

- ¿El rol ha modificado el enfoque o solo el tono de la respuesta?

- ¿Qué restricción ha reducido más el riesgo de información inventada?

- ¿El formato ha mejorado únicamente la presentación o también la capacidad de verificar los datos?

- ¿Qué componente eliminarías primero si necesitaras acortar el prompt sin perder control esencial?

### Tarea 4: Razonamiento verificable

#### Objetivo

Solicitar fórmulas, sustituciones numéricas y resultados intermedios para comprobar un análisis cuantitativo y diferenciar entre datos, cálculos e inferencias.

En tareas cuantitativas, pedir únicamente una conclusión dificulta detectar errores. En lugar de solicitar el razonamiento interno ilimitado del modelo, esta técnica exige resultados observables y verificables: datos de entrada, fórmula aplicada, operación y resultado.

!!! note "Dato, cálculo e inferencia"
    Un **dato** procede directamente de la fuente. Un **cálculo** es el resultado de aplicar una operación explícita a los datos. Una **inferencia** es una interpretación o conclusión que no aparece literalmente en la fuente. No deben tratarse como equivalentes.

#### Pasos

1. Abre una conversación nueva en Microsoft 365 Copilot Chat.

2. Adjunta el dossier.

3. Copia y envía el siguiente prompt:

    ```text
    Utiliza exclusivamente el Anexo 1 del documento adjunto.

    Identifica las partidas de costes cuya desviación porcentual absoluta entre el Presupuesto 2025 y el Real 2025 sea estrictamente superior al 5 %.

    Para cada partida:
    1. Muestra el presupuesto y el importe real.
    2. Aplica esta fórmula:
       Desviación porcentual = (Real 2025 - Presupuesto 2025) / Presupuesto 2025 × 100
    3. Muestra la sustitución numérica y el resultado redondeado a dos decimales.
    4. Indica si supera o no el umbral del 5 % en valor absoluto.
    5. Incluye la causa únicamente si aparece expresamente en la Nota de la Dirección Financiera. Si no aparece, escribe "CAUSA NO DECLARADA".

    Presenta el resultado final en una tabla con estas columnas:

    | Partida | Presupuesto 2025 | Real 2025 | Operación | Desviación (%) | ¿Supera el 5 %? | Causa documentada |

    No incluyas partidas de ingresos, EBITDA ni totales agregados. No añadas causas inferidas.
    ```

4. Revisa que Copilot haya utilizado exclusivamente partidas de costes.

5. Comprueba que los importes de presupuesto y real coincidan con el Anexo 1.

6. Verifica manualmente al menos uno de los cálculos.

7. Utiliza la siguiente operación como referencia para **Infraestructura Cloud y Datacenters**:

    ```text
    (3.655.000 - 3.200.000) / 3.200.000 × 100 = 14,22 %
    ```

8. Comprueba que Copilot aplique el umbral al valor absoluto de la desviación.

9. Contrasta las causas incluidas con la Nota de la Dirección Financiera.

10. Clasifica los elementos de la respuesta como **dato**, **cálculo** o **inferencia**.

#### Comprobación

Comprueba que:

- Los valores de presupuesto y real son datos trazables al Anexo 1.

- La fórmula aplicada coincide con la indicada en el prompt.

- Cada operación muestra la sustitución numérica completa.

- Los resultados están redondeados a dos decimales.

- El signo positivo o negativo de la desviación se conserva correctamente.

- La aplicación del umbral se realiza sobre el valor absoluto.

- Solo se incluyen partidas cuya desviación absoluta es estrictamente superior al 5 %.

- No se incluyen desviaciones iguales al 5 %.

- No aparecen partidas de ingresos, EBITDA ni totales agregados.

- El cálculo manual coincide con el resultado de Copilot.

- Las causas documentadas pueden trazarse hasta la Nota de la Dirección Financiera.

- Cuando no existe una causa expresa, se utiliza `CAUSA NO DECLARADA`.

- No se presentan inferencias como si fueran datos de la fuente.

- La tabla permite distinguir claramente el dato original, el cálculo aplicado y la conclusión sobre el umbral.

#### Reflexiona

- ¿Mostrar la fórmula y la sustitución numérica facilita detectar errores?

- ¿Qué elementos de la respuesta son datos procedentes del anexo?

- ¿Qué elementos son cálculos reproducibles?

- ¿Qué elementos podrían considerarse inferencias?

- ¿Copilot ha respetado la diferencia entre una causa documentada y una causa inferida?

- ¿La conclusión sobre el umbral puede verificarse sin confiar únicamente en la respuesta de Copilot?

### Tarea 5: Crea y mejora tu propio prompt

#### Objetivo

Diseñar, ejecutar y mejorar un prompt propio mediante una técnica adecuada al caso, corrigiendo un problema observable de la primera respuesta.

En esta tarea aplicarás una de las cuatro técnicas sin copiar un prompt completo. Deberás tomar decisiones conscientes sobre el contexto, los ejemplos, las restricciones, la trazabilidad y el formato.

#### Pasos

1. Selecciona una de estas opciones del dossier:

    1. **Opción A, Anexo 1:** resumir la situación financiera de InnoTech Solutions para una audiencia directiva.

    2. **Opción B, Anexo 2:** clasificar uno o varios tickets por categoría, severidad y acción recomendada.

    3. **Opción C, Anexo 3:** comparar proveedores según los criterios que consideres relevantes.

2. Elige la técnica más adecuada para el caso:

    1. **Zero-shot**.

    2. **Few-shot**.

    3. **Superprompt**.

    4. **Razonamiento verificable**.

3. Redacta un prompt propio que incluya, cuando resulte pertinente:

    1. La fuente o el anexo que debe utilizar Copilot.

    2. La tarea concreta.

    3. El destinatario o el propósito del resultado.

    4. Las restricciones necesarias.

    5. El formato de salida.

    6. Ejemplos, si has elegido few-shot.

    7. Fórmulas y operaciones comprobables, si has elegido razonamiento verificable.

4. Utiliza, si lo necesitas, la siguiente plantilla:

    ```text
    [TÉCNICA ELEGIDA]

    [ANEXO O FUENTE]

    [OBJETIVO Y DESTINATARIO]

    [TAREA]

    [RESTRICCIONES]

    [FORMATO DE SALIDA]

    [EJEMPLOS O FÓRMULAS, SI SON NECESARIOS]
    ```

5. Envía el primer prompt a Copilot.

6. Revisa la respuesta y localiza un problema concreto.

7. Determina si el problema afecta a la metodología, la trazabilidad, la justificación, el formato o el cumplimiento de las restricciones.

8. Modifica el prompt para corregir ese problema.

9. Ejecuta el segundo prompt.

10. Compara las dos respuestas y registra el efecto observable de la modificación.

#### Comprobación

Comprueba que el segundo prompt:

- Reduce la ambigüedad detectada.

- Se mantiene fiel al anexo seleccionado.

- Define un resultado que pueda revisarse.

- Permite identificar la metodología aplicada.

- Facilita la trazabilidad entre las afirmaciones y la fuente.

- Exige justificaciones cuando la tarea contiene clasificaciones o valoraciones.

- Evita añadir información no documentada.

- Respeta las restricciones y el formato definidos.

- Corrige el problema concreto identificado en la primera respuesta.

- Mejora de forma observable la primera respuesta.

- No se considera mejor únicamente por ser más largo o más detallado.

#### Reflexiona

- ¿Qué cambio introdujiste en el segundo prompt?

- ¿Qué efecto concreto produjo ese cambio?

- ¿El cambio mejoró la metodología, la trazabilidad, la justificación, el formato o varios de estos aspectos?

- ¿La segunda respuesta corrigió el problema o solo lo ocultó mediante una presentación distinta?

- ¿Podrías reducir el segundo prompt sin perder el control conseguido?

### Tarea 6: Comparación final

#### Objetivo

Comparar las técnicas utilizadas y seleccionar la más adecuada según el tipo de tarea, el riesgo y el nivel de control necesario.

#### Pasos

1. Revisa los resultados obtenidos en las tareas anteriores.

2. Completa la tabla con observaciones derivadas de tus propias pruebas.

3. Describe una ventaja y una limitación observables para cada técnica.

4. Indica el tipo de caso en el que utilizarías cada técnica.

5. Evita respuestas genéricas que no puedan relacionarse con los ejercicios realizados.

| Técnica | Ventaja observada | Limitación observada | Mejor uso |
|---|---|---|---|
| Zero-shot |  |  |  |
| Few-shot |  |  |  |
| Superprompt |  |  |  |
| Razonamiento verificable |  |  |  |

#### Comprobación

Comprueba que:

- Las cuatro técnicas aparecen en la tabla.

- Cada ventaja se apoya en un resultado observado durante el laboratorio.

- Cada limitación se apoya en un problema o riesgo detectado.

- Los usos recomendados son coherentes con las características de cada técnica.

- La comparación considera el nivel de control requerido.

- La comparación tiene en cuenta la trazabilidad y la justificación de las respuestas.

- La selección de la técnica no se basa únicamente en la longitud del prompt.

- Las conclusiones admiten respuestas alternativas cuando están justificadas por los datos, respetan las restricciones y mantienen coherencia metodológica.

#### Reflexiona

- ¿Qué técnica seleccionarías para una tarea rápida y de bajo riesgo?

- ¿Qué técnica utilizarías cuando el formato debe ser estrictamente reutilizable?

- ¿Qué técnica aplicarías si necesitas condicionar la respuesta mediante ejemplos?

- ¿Qué técnica emplearías para cálculos que deban auditarse?

- ¿Qué factores deberían determinar la selección: complejidad, riesgo, formato, disponibilidad de ejemplos o necesidad de verificación?

- ¿Existe una técnica universalmente mejor o la elección depende del caso?

## Comprobación final

Comprueba que:

- Has completado los ejercicios de zero-shot, few-shot, superprompt y razonamiento verificable.

- Has utilizado una conversación nueva cuando era necesario evitar la influencia de instrucciones anteriores.

- Has contrastado las respuestas con los anexos correspondientes.

- Has diferenciado entre datos documentados, cálculos e inferencias.

- Has evaluado la metodología utilizada por Copilot.

- Has comprobado la trazabilidad de las clasificaciones, comparaciones y conclusiones.

- Has revisado si las justificaciones se apoyan en la fuente.

- Has detectado supuestos o información añadida sin respaldo documental.

- Has verificado manualmente al menos un cálculo.

- Has creado y mejorado un prompt propio.

- Has descrito un cambio observable entre la primera y la segunda versión de tu prompt.

- Has completado la comparación final de técnicas.

- Has aceptado respuestas alternativas únicamente cuando respetan los datos, las restricciones y una metodología coherente.

### Reflexión final

Selecciona uno de los ejercicios y responde:

1. ¿Qué nivel de control necesitaba la tarea?

2. ¿Qué riesgo existía si Copilot añadía supuestos no documentados?

3. ¿Qué técnica proporcionó el equilibrio más adecuado entre rapidez, control y capacidad de verificación?

4. ¿Qué evidencia de tus resultados respalda esa selección?

5. ¿En qué condiciones elegirías una técnica diferente?

## Resumen

| Técnica | Punto clave |
|---|---|
| **Zero-shot** | Resuelve una tarea sin ejemplos. Es rápido, pero ofrece menos control sobre los criterios, los supuestos y el formato. |
| **Few-shot** | Utiliza ejemplos para mostrar el patrón de entrada y salida esperado. Mejora la consistencia, pero puede transmitir sesgos o errores presentes en los ejemplos. |
| **Superprompt** | Reúne rol, contexto, tarea, restricciones y formato en una instrucción estructurada. Facilita el control de tareas complejas y salidas reutilizables. |
| **Razonamiento verificable** | Expone datos, fórmulas, operaciones y resultados comprobables. Facilita la detección de errores y la separación entre datos, cálculos e inferencias. |

La técnica adecuada depende del caso. Las tareas sencillas y de bajo riesgo pueden resolverse mediante zero-shot. Los ejemplos resultan útiles cuando es necesario reproducir un patrón. Los superprompts ofrecen mayor control en tareas con múltiples requisitos. El razonamiento verificable es preferible cuando los cálculos y las conclusiones deben poder auditarse.

## Recursos adicionales

- [Documentos de Trabajo y Datasets para Cuadernos de Laboratorio](recursos/files/Documentos%20de%20Trabajo%20y%20Datasets%20para%20Cuadernos%20de%20Laboratorio.pdf)

- Microsoft 365 Copilot Chat.

- Anexo 1: Estado financiero y balance de situación.

- Anexo 2: Dataset de incidencias de soporte técnico y comercial.

- Anexo 3: Fichas de evaluación de proveedores TI.
