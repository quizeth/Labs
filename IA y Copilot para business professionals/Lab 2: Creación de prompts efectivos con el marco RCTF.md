# Lab 2: Creación de prompts efectivos con el marco RCTF

La calidad de las respuestas de una IA generativa depende directamente de la calidad de las instrucciones que recibe. Un prompt vago produce resultados genéricos; un prompt estructurado produce resultados utilizables.

En este ejercicio aprenderás a:
- Aplicar el marco **RCTF** (Role, Context, Task, Format) para construir prompts profesionales.
- Diferenciar entre **superprompting** y **chain of thought (CoT)** y saber cuándo usar cada técnica.
- Incorporar buenas prácticas de prompt engineering en tu flujo de trabajo diario.

**Duración estimada:** 15 minutos

> [!NOTE]
> **Nota:** Para completar este laboratorio necesitas una suscripción a Microsoft 365. Estas tareas están diseñadas específicamente para usarse en **modo web** en Microsoft 365 Copilot Chat. Si tienes una licencia de Microsoft 365 Copilot, asegúrate de **cambiar manualmente al modo web** cuando abras Copilot Chat, ya que podría estar en modo trabajo por defecto. Usar el **modo web** garantiza que los prompts funcionen como se espera y obtengan información de contenido web público.

---

## Tarea 1: El marco RCTF

### ¿Qué es RCTF?

RCTF es un marco de cuatro componentes que estructura cualquier prompt para obtener resultados consistentes y de alta calidad:

| Componente | Pregunta clave | Ejemplo |
|---|---|---|
| **R - Role** (Rol) | ¿Quién debe ser la IA? | "Eres un analista financiero senior con 10 años de experiencia en logística." |
| **C - Context** (Contexto) | ¿Cuál es la situación? | "Nuestra empresa transporta vehículos entre Europa y América Latina. El margen operativo ha caído un 8% en Q1." |
| **T - Task** (Tarea) | ¿Qué quieres exactamente? | "Identifica las 3 causas principales de la caída y propone acciones correctivas." |
| **F - Format** (Formato) | ¿Cómo debe verse la salida? | "Presenta el resultado en una tabla con columnas: Causa, Impacto estimado, Acción propuesta, Prioridad." |

> **Principio clave:** La IA no conoce tu situación. Cuanto más contexto le des, mejor será el resultado. La estructura vence a la creatividad.

### Tarea práctica

- **Abre Microsoft Copilot** (m365.cloud.microsoft o la app de escritorio de Microsoft 365 Copilot).

- **Copia y envía** el siguiente prompt **sin contexto** y observa el resultado:

```
Ayúdame a preparar una presentación sobre transformación digital.
```

- Ahora envía este prompt **aplicando el marco RCTF** y compara:

```
Eres un consultor senior de transformación digital especializado en empresas de logística.

Estoy preparando una presentación para el comité de dirección de una empresa
de transporte marítimo con 2.000 empleados. Actualmente usamos hojas de cálculo para
la facturación y el seguimiento de envíos. El presupuesto aprobado es de 500K EUR y el
plazo es 18 meses.

Crea un esquema de presentación de 10 diapositivas que incluya: diagnóstico actual,
propuesta de solución, hoja de ruta, análisis de riesgos y ROI estimado.

Muestra el esquema como una lista numerada con el título de cada diapositiva y 3 bullet points con el contenido clave de cada una.
```

- **Reflexiona:** ¿Que diferencias observas en la profundidad, relevancia y estructura de ambas respuestas?

---

## Tarea 2: Superprompting vs. Chain of Thought

### Definiciones

| Técnica | Qué es | Cuándo usarla |
|---|---|---|
| **Superprompting** | Un prompt extenso y detallado que incluye toda la información necesaria (contexto, objetivos, ejemplos, restricciones, formato) en una sola interacción. | Tareas bien definidas donde puedes anticipar todo lo necesario: generación de contenido, emails, informes. |
| **Chain of Thought (CoT)** | Una técnica que guía a la IA a "pensar paso a paso" antes de dar una respuesta final, descomponiendo el razonamiento en etapas intermedias. | Tareas que requieren lógica, cálculo, análisis comparativo o resolución de problemas complejos. |

### Comparativa rápida

| Aspecto | Superprompting | Chain of Thought |
|---|---|---|
| **Estructura** | Todo en un solo prompt | Razonamiento paso a paso |
| **Control** | Alto (defines todo desde el inicio) | Alto (puedes validar cada paso) |
| **Mejor para** | Contenido, redacción, informes | Análisis, decisiones, cálculos |
| **Riesgo** | Prompt muy largo puede diluir el foco | Requiere más tokens y tiempo |

### Tarea práctica

**Paso A - Superprompting:** Copia y envía este prompt a Copilot:

```
Eres un director de operaciones de una empresa de logística con sede en Madrid.
Necesito que redactes un email al CEO proponiendo la automatización del proceso
de facturación. La empresa procesa 5.000 facturas/mes de forma manual con un
equipo de 4 personas. El error medio es del 3,2%. Incluye: situación actual,
propuesta de solución, beneficios esperados (ahorro de tiempo y reducción de
errores), y una estimación de coste. Tono profesional pero directo. Máximo
300 palabras. Formato de email corporativo con asunto incluido.
```

**Paso B - Chain of Thought:** Abre una nueva conversación y envía este prompt:

```
Necesito decidir si automatizar nuestro proceso de facturación. Actualmente
procesamos 5.000 facturas/mes de forma manual con 4 personas y una tasa de
error del 3,2%.

Piensa paso a paso:
1. Calcula el coste actual del proceso manual (asume un salario bruto de
   30.000 EUR/año por persona).
2. Estima el coste de los errores (asume un coste medio de 45 EUR por
   factura con error).
3. Investiga el coste típico de una solución de automatización de facturas.
4. Compara ambos escenarios y calcula el ROI a 2 años.
5. Recomienda si debemos proceder o no, justificando tu respuesta.
```

**Reflexiona:** ¿Qué técnica te ha dado un resultado más ñutil para cada situación? ¿Por qué?

---

## Ejercicio 3: Buenas prácticas

### Las 8 reglas de oro del prompting

| # | Buena práctica | Ejemplo |
|---|---|---|
| 1 | **Sé específico, no genérico** | Malo: "Haz un informe." Bueno: "Crea un informe ejecutivo de 2 páginas sobre la reducción de costes en el Q1." |
| 2 | **Asigna un rol experto** | "Eres un analista de datos senior especializado en supply chain." |
| 3 | **Proporciona contexto suficiente** | Incluye datos, restricciones, audiencia y objetivo. |
| 4 | **Define el formato de salida** | "Tabla con 4 columnas", "Lista numerada", "Email de 200 palabras". |
| 5 | **Usa ejemplos cuando sea posible** | "Sigue este estilo: [pega un ejemplo]." |
| 6 | **Itera y refina** | Si el primer resultado no es perfecto, ajusta el prompt en lugar de empezar de cero. |
| 7 | **Divide tareas complejas** | En lugar de un mega-prompt, divide en pasos o usa chain of thought. |
| 8 | **Revisa siempre la salida** | La IA puede generar información incorrecta (alucinaciones). Tú eres el filtro final. |

### Tarea práctica

- **Elige un caso real de tu trabajo** (un email, un informe, un analisis de datos).

- **Escribe un prompt usando RCTF** y al menos 3 de las buenas practicas anteriores.

- **Envialo a Copilot** y evalua el resultado.

- **Itera al menos una vez:** ajusta el prompt para mejorar la respuesta.

---

## Resumen

| Concepto | Punto clave |
|---|---|
| **RCTF** | Estructura todo prompt en 4 bloques: Rol, Contexto, Tarea, Formato. |
| **Superprompting** | Un prompt completo y detallado para tareas bien definidas. |
| **Chain of Thought** | Razonamiento paso a paso para tareas que requieren lógica o cálculo. |
| **Buenas prácticas** | Se específico, da contexto, define formato, itera y revisa siempre. |

---

## Recursos adicionales

- [Prompt Gallery de Microsoft](https://adoption.microsoft.com/copilot/prompt-gallery/) - Biblioteca de prompts listos para usar.
- [Prompt Engineering Guide](https://www.promptingguide.ai/) - Guia académica de tecnicas de prompting.
- [The RCTF Prompt Framework](https://rupertchesman.com/article-rctf-prompt-framework.html) - Explicación detallada del marco RCTF.
