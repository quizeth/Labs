# Lab 2: Técnicas de prompting aplicadas a datos empresariales

La misma tarea puede producir resultados muy distintos según la cantidad de contexto, los ejemplos y la estructura incorporados al prompt. En este laboratorio compararás cuatro técnicas de prompting utilizando los anexos del dossier proporcionado: **zero-shot**, **few-shot**, **superprompt** y **chain of thought**, reformulada aquí como razonamiento verificable.

En este ejercicio aprenderás a:

- Utilizar un prompt zero-shot para resolver una tarea sin ejemplos previos.
- Aplicar few-shot prompting para controlar una clasificación mediante ejemplos.
- Construir un superprompt con contexto, restricciones y formato de salida.
- Solicitar fórmulas y cálculos intermedios para verificar un análisis cuantitativo.
- Comparar las ventajas y limitaciones de cada técnica.

**Duración estimada:** 30 minutos

**Material necesario:**

!!! note "Nota"

   Para completar este laboratorio necesitas una suscripción a Microsoft 365. Estas tareas están diseñadas específicamente para usarse en **modo web** en Microsoft 365 Copilot Chat. Si tienes una licencia de Microsoft 365 Copilot, asegúrate de **cambiar manualmente al modo web** cuando abras Copilot Chat, ya que podría estar en modo trabajo por defecto. Usar el **modo web** garantiza que los prompts funcionen como se espera y obtengan información de contenido web público.

- Microsoft 365 Copilot Chat.
- El archivo [**Documentos de Trabajo y Datasets para Cuadernos de Laboratorio.pdf**](recursos/files/Documentos%20de%20Trabajo%20y%20Datasets%20para%20Cuadernos%20de%20Laboratorio.pdf).
- Anexo 1: Estado financiero y balance de situación.
- Anexo 2: Dataset de incidencias de soporte técnico y comercial.
- Anexo 3: Fichas de evaluación de proveedores TI.



!!! note "Nota"

   Trabaja en una única conversación mientras utilices el mismo anexo. Abre una conversación nueva cuando se indique para evitar que los ejemplos o instrucciones anteriores condicionen la respuesta.




---

## Tarea 1: Zero-shot prompting

### ¿Qué es zero-shot?

En un prompt **zero-shot**, Copilot recibe una tarea sin ejemplos de respuesta. La técnica es rápida, pero ofrece menos control sobre los criterios y el formato utilizados.

### Tarea práctica

1. Abre Microsoft 365 Copilot Chat.
2. Adjunta el dossier mediante **Agregar y administrar orígenes**.
3. Copia y envía este prompt:

```text
Utiliza el Anexo 2 del documento adjunto.

Clasifica los tickets TCK-105, TCK-106, TCK-107 y TCK-108 por categoría y severidad.
```

4. Revisa la respuesta y anota:
   - Las categorías utilizadas.
   - Los niveles de severidad asignados.
   - El formato elegido por Copilot.
   - Cualquier criterio que Copilot haya supuesto sin que estuviera indicado.

**Reflexiona:** ¿La clasificación es consistente entre los cuatro tickets? ¿Qué elementos del resultado no estaban definidos en el prompt?

---

## Tarea 2: Few-shot prompting

### ¿Qué es few-shot?

El **few-shot prompting** incorpora ejemplos de entrada y salida para mostrar a Copilot el patrón que debe seguir. Los ejemplos ayudan a controlar la estructura y los criterios, pero deben revisarse porque también pueden transmitir errores o sesgos.

### Tarea práctica

1. Abre una conversación nueva.
2. Adjunta de nuevo el dossier.
3. Copia y envía este prompt:

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

4. Compara la respuesta con la obtenida mediante zero-shot.

### Comprobación

- ¿Copilot mantiene los mismos campos en los cuatro objetos?
- ¿Utiliza únicamente las severidades permitidas?
- ¿La justificación se basa en el texto de cada ticket?
- ¿El resultado es JSON válido?

**Reflexiona:** ¿Los ejemplos han mejorado el formato, la clasificación o ambos? Identifica un cambio concreto respecto al resultado zero-shot.

---

## Tarea 3: Superprompt

### ¿Qué es un superprompt?

Un **superprompt** reúne en una sola instrucción el rol, el contexto, la tarea, las restricciones, los criterios de evaluación y el formato esperado. Resulta útil cuando la tarea está bien definida y se necesita una salida reutilizable.

### Tarea práctica

1. Abre una conversación nueva.
2. Adjunta el dossier.
3. Copia y envía este prompt:

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

4. Revisa si Copilot respeta todas las instrucciones.

### Comprobación

- ¿Los importes coinciden con el Anexo 3?
- ¿Se distingue entre tiempo de respuesta y cobertura horaria?
- ¿Las certificaciones están copiadas correctamente?
- ¿La jurisdicción aparece sin interpretaciones añadidas?
- ¿La comparación final se basa en los criterios anteriores?

**Reflexiona:** ¿Qué parte del superprompt ha ejercido mayor control sobre el resultado: el contexto, las restricciones o el formato?

---

## Tarea 4: Chain of thought y razonamiento verificable

### ¿Qué se practica en esta tarea?

En tareas de cálculo, pedir simplemente una conclusión dificulta detectar errores. En lugar de solicitar el razonamiento interno ilimitado del modelo, pedirás **fórmulas, operaciones intermedias y resultados verificables**.

#### Tarea práctica

1. Abre una conversación nueva.
2. Adjunta el dossier.
3. Copia y envía este prompt:

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

4. Comprueba manualmente al menos uno de los cálculos.

### Verificación sugerida

Para **Infraestructura Cloud y Datacenters**:

```text
(3.655.000 - 3.200.000) / 3.200.000 × 100 = 14,22 %
```

**Reflexiona:** ¿Mostrar la fórmula y la sustitución numérica facilita detectar errores? ¿Copilot ha respetado la diferencia entre causa documentada y causa inferida?

---

## Tarea 5: Crea y mejora tu propio prompt

En esta tarea aplicarás una de las cuatro técnicas sin copiar un prompt completo. El objetivo es que tomes decisiones conscientes sobre el contexto, los ejemplos, las restricciones y el formato.

### Elige un caso

Selecciona **una** de estas opciones del dossier:

- **Opción A - Anexo 1:** resumir la situación financiera de InnoTech Solutions para una audiencia directiva.
- **Opción B - Anexo 2:** clasificar uno o varios tickets por categoría, severidad y acción recomendada.
- **Opción C - Anexo 3:** comparar proveedores según los criterios que consideres relevantes.

### Redacta tu prompt

1. Elige la técnica más adecuada: **zero-shot**, **few-shot**, **superprompt** o **razonamiento verificable**.
2. Escribe un prompt propio que incluya, cuando resulte pertinente:
   - La fuente o el anexo que debe utilizar Copilot.
   - La tarea concreta.
   - El destinatario o propósito del resultado.
   - Las restricciones necesarias.
   - El formato de salida.
   - Ejemplos, si has elegido few-shot.
   - Fórmulas y operaciones comprobables, si has elegido razonamiento verificable.
3. Envía el prompt a Copilot y revisa la respuesta.
4. Identifica **un problema concreto** en el resultado.
5. Modifica el prompt para corregir ese problema y ejecútalo de nuevo.

### Plantilla opcional

```text
[TÉCNICA ELEGIDA]

[ANEXO O FUENTE]

[OBJETIVO Y DESTINATARIO]

[TAREA]

[RESTRICCIONES]

[FORMATO DE SALIDA]

[EJEMPLOS O FÓRMULAS, SI SON NECESARIOS]
```

### Evaluación

Comprueba si el segundo prompt:

- Reduce la ambigüedad detectada.
- Se mantiene fiel al anexo.
- Define un resultado que pueda revisarse.
- Evita añadir información no documentada.
- Mejora de forma observable la primera respuesta.

**Reflexiona:** ¿Qué cambio introdujiste en el segundo prompt y qué efecto concreto produjo? No respondas únicamente que el resultado es “mejor”.

---

## Tarea 6: Comparación final

Completa esta tabla a partir de tus resultados:

| Técnica | Ventaja observada | Limitación observada | Mejor uso |
|---|---|---|---|
| Zero-shot |  |  |  |
| Few-shot |  |  |  |
| Superprompt |  |  |  |
| Chain of thought / razonamiento verificable |  |  |  |

## Resumen

| Técnica | Punto clave |
|---|---|
| **Zero-shot** | Resuelve una tarea sin ejemplos; es rápido, pero ofrece menos control. |
| **Few-shot** | Utiliza ejemplos para enseñar el patrón de entrada y salida esperado. |
| **Superprompt** | Reúne contexto, tarea, restricciones y formato en una única instrucción detallada. |
| **Chain of thought / razonamiento verificable** | Descompone una tarea cuantitativa en fórmulas, operaciones y resultados comprobables. |