# Lab 4: Ética en la IA

La inteligencia artificial generativa puede redactar informes, analizar datos y automatizar flujos de trabajo en segundos. Sin embargo, esa velocidad solo es útil si los resultados son justos, seguros y verificables. Comprender los principios de IA responsable y saber cuándo un ser humano debe intervenir es tan importante como saber escribir un buen prompt.

En este ejercicio aprenderás a:
- Identificar y aplicar los **seis principios de IA responsable de Microsoft** en situaciones cotidianas.
- Comprender el concepto de **HITL** (Human-in-the-Loop) y saber cuándo la supervisión humana es imprescindible.
- Evaluar críticamente las respuestas de una IA y detectar posibles sesgos, errores o riesgos éticos.
- Diseñar puntos de control humano en un flujo de trabajo con IA.

**Duración estimada:** 40 minutos

> [!NOTE]
> **Nota:** Para completar este laboratorio necesitas una suscripción a Microsoft 365. Estas tareas están diseñadas específicamente para usarse en **modo web** en Microsoft 365 Copilot Chat. Si tienes una licencia de Microsoft 365 Copilot, asegúrate de **cambiar manualmente al modo web** cuando abras Copilot Chat, ya que podría estar en modo trabajo por defecto. Usar el **modo web** garantiza que los prompts funcionen como se espera y obtengan información de contenido web público.

---

## Tarea 1: Los seis principios de IA responsable de Microsoft

### ¿Qué es la IA responsable?

La IA responsable es un conjunto de prácticas que garantizan que los sistemas de inteligencia artificial se diseñan, desarrollan y despliegan de forma ética, segura y transparente. Microsoft ha definido seis principios fundamentales que guian todo el ciclo de vida de sus productos de IA:

| # | Principio | Descripción | Ejemplo práctico |
|---|---|---|---|
| 1 | **Equidad** (Fairness) | Los sistemas de IA deben tratar a todas las personas de forma justa, sin discriminar por género, etnia, edad u otras características. | Al usar ChatGPT o Copilot para redactar una oferta de empleo, el usuario debe revisar que el texto no favorezca implícitamente a un género o grupo sobre otro. |
| 2 | **Fiabilidad y seguridad** (Reliability & Safety) | Los sistemas deben funcionar de forma predecible y segura, incluso en situaciones inesperadas. | Si un usuario consulta a Copilot sobre temas de salud, debe contrastar la información y no basar decisiones médicas únicamente en la respuesta generada. |
| 3 | **Privacidad y seguridad** (Privacy & Security) | Los sistemas deben proteger los datos personales y cumplir con la normativa de privacidad vigente. | Un usuario no debe introducir datos personales sensibles o confidenciales en ChatGPT o Copilot, especialmente si no sabe cómo se gestionan esos datos. |
| 4 | **Inclusividad** (Inclusiveness) | Los sistemas deben ser accesibles y útiles para todas las personas, independientemente de sus capacidades. | Un usuario puede pedir a ChatGPT que simplifique un texto o lo adapte para personas con dificultades de comprensión, mejorando la accesibilidad. |
| 5 | **Transparencia** (Transparency) | Los usuarios deben entender cómo funciona el sistema y por qué genera una determinada respuesta. | Al utilizar Copilot, el usuario debe revisar las fuentes o explicaciones proporcionadas para entender en qué se basa la respuesta. |
| 6 | **Responsabilidad** (Accountability) | Las personas (no las máquinas) son responsables del uso y las consecuencias de los sistemas de IA. | Un analista que usa ChatGPT o Copilot para generar un informe debe revisarlo y validarlo antes de compartirlo con su equipo o dirección. |

**Principio clave:** La IA es una herramienta, no un decisor. Tú eres el responsable final de cada resultado que compartes, publicas o ejecutas.

### Tarea práctica: Sesgos, equidad y fiabilidad

- **Abre Microsoft Copilot** (m365.cloud.microsoft o la app de escritorio de Microsoft 365 Copilot).
- **Copia y envia** el siguiente prompt:
````
Traduce las siguientes frases:

“The nurse yelled at the doctor because she was late. Who was late?”

“A doctor and a nurse eat at a restaurant. She paid because she makes more money. Who paid?”
````
- Revisa los sesgos de la respuesta. Por ejemplo, revisa (pero no te limites a):
  - ¿Qué sesgos de género presume para cada persona?
  - ¿Qué sesgos presenta al decidir quién gana más dinero?
    
- En un nuevo chat, reescribe el prompt incluyendo un prompt anti-sesgos.
````
Traduce las siguientes frases. No hagas suposiciones sesgadas:

“The nurse yelled at the doctor because she was late. Who was late?”

“A doctor and a nurse eat at a restaurant. She paid because she makes more money. Who paid?”
````
- Compara el resultado de ambas conversaciones.
  
- Explora el siguientes prompt y examina:
  - ¿Aparecen estereotipos (género, edad, nacionalidad)?  
  - ¿Se limita a un tipo de trayectoria o background?  
  - ¿Se asume un contexto cultural concreto?
 
````
Genera una imagen de un programador trabajando.
````
- Experimenta con variaciones de ese prompt para corregir los sesgos.

### Tarea práctica: Fiabilidad y control de alucinaciones
- En una nueva conversación, **Copia y envia** el siguiente prompt:
````
Resume el siguiente artículo y añade información del autor:
“Artículo: La empresa TechNova presentó un nuevo algoritmo de optimización energética que promete reducir el consumo eléctrico un 40% en centros de datos. El anuncio se realizó en una conferencia internacional en Berlín.”
````
- Este prompt tiene varios problemas:
  - No limita la invención de información.
  - Invita al modelo a completar datos inexistentes.
 
- Ahora, copia y envía el prompt mejorado:
````
Resume el siguiente artículo. Si falta información sobre el autor, indícalo explícitamente. No inventes datos:
“Artículo: La empresa TechNova presentó un nuevo algoritmo de optimización energética que promete reducir el consumo eléctrico un 40% en centros de datos. El anuncio se realizó en una conferencia internacional en Berlín.”
````
- Qué mejora:
  - Evita alucinaciones.
  - Fuerza al modelo a reconocer límites.
 
### Tarea práctica: Privacidad y seguridad
- En una nueva conversación, **Copia y envia** el siguiente prompt:
````
Genera un informe sobre un cliente llamado Juan Pérez que vive en Madrid. Incluye su DNI, dirección exacta y datos bancarios.
````
- Problemas:
  - Solicita datos altamente sensibles.
  - No distingue entre datos reales y ficticios.
  - Riesgo de mal uso de la información.

- Ahora, copia y envía el prompt mejorado:
````
Genera un ejemplo de informe de cliente utilizando datos completamente ficticios. No incluyas información personal real ni datos sensibles como DNI o cuentas bancarias.
````
- Qué mejora:
  - Protege la privacidad.
  - Define restricciones claras.
  - Reduce riesgos de uso indebido.
 
### Tarea práctica: Transparencia
- En una nueva conversación, **Copia y envia** el siguiente prompt:
````
¿Qué probabilidades hay de que el algoritmo TechNova reduzca el consumo un 40%? Da una respuesta precisa.
````
- Problemas:
  - Exige precisión sin contexto.
  - No permite expresar incertidumbre.
  - Favorece respuestas engañosamente seguras.

- Ahora, copia y envía el prompt mejorado:
````
¿Qué probabilidades hay de que el algoritmo TechNova reduzca el consumo un 40%? Explica tu nivel de certeza, los supuestos que haces y las limitaciones de la información disponible.
````
- Qué mejora:
  - Introduce transparencia.
  - Expone incertidumbre y supuestos.
  - Mejora la interpretabilidad.
 
### Reflexión final

- Evalúa en conjunto:
  - **Fiabilidad**: ¿El modelo evita inventar información?
  - **Privacidad/seguridad**: ¿protege datos sensibles?
  - **Transparencia**: ¿explica límites e incertidumbre?

- Preguntas guía:
  - ¿Qué diferencias observas entre los prompts malos y buenos?
  - ¿Qué cambios han reducido riesgos?
  - ¿Cómo aplicarías estas buenas prácticas en un caso real de negocio?
---

## Tarea 2: Human-in-the-Loop (HITL)

### ¿Qué es HITL?

**Human-in-the-Loop (HITL)** — o "humano en el bucle" — es un modelo de trabajo en el que un ser humano supervisa, valida o corrige las salidas de un sistema de IA en puntos críticos del proceso, en lugar de dejar que la IA actúe de forma completamente autónoma.

| Aspecto | Sin HITL (automatización total) | Con HITL (supervisión humana) |
|---|---|---|
| **Velocidad** | Máxima | Alta (con pausas controladas) |
| **Riesgo de error** | Alto en tareas ambiguas | Reducido por revisión humana |
| **Responsabilidad** | Diluida | Clara: el humano aprueba |
| **Confianza** | Baja en decisiones críticas | Alta: validación explícita |
| **Ejemplo** | Un bot envia emails a clientes sin revisión | Un humano revisa el borrador antes de enviarlo |

### ¿Cuando es imprescindible HITL?

| Escenario | ¿Por qué necesita HITL? |
|---|---|
| Decisiones financieras (aprobación de gastos, facturas) | Errores pueden tener impacto económico directo. |
| Comunicaciones externas (emails a clientes, proveedores) | Un tono inadecuado o dato incorrecto danñan la reputación. |
| Datos personales o sensibles (RGPD, informacion de empleados) | Obligación legal de proteger la privacidad. |
| Informes para dirección | La IA puede alucinar datos o malinterpretar el contexto. |
| Contratación y evaluación de personas | Riesgo de sesgos algorítmicos con impacto real en personas. |

**Principio clave:** La IA acelera el trabajo; el humano garantiza que el resultado es correcto, ético y adecuado al contexto. Automatiza lo repetitivo, supervisa lo crítico.

### Tarea práctica

**Paso A — Identificar el riesgo sin HITL:** Copia y envia este prompt a Copilot:
````
Eres un director de operaciones de una empresa de logística. Redacta un email al cliente Contoso informandole de un retraso de 5 dias en su envío. Tono profesional y empático. Máximo 150 palabras.
````
- **Revisa la respuesta** como si fueras el punto de control humano (HITL). Pregúntate:
  - ¿El tono es adecuado para este cliente?
  - ¿Hay datos inventados (fechas, números de referencia, motivos del retraso)?
  - ¿Enviarías este email tal cual? ¿Qué cambiarías?

**Paso B — Diseñar un flujo HITL:** Ahora envía este prompt:
````
Soy un analista de datos en una empresa de logística. Quiero automatizar el envío semanal de informes de KPIs a direcció usando IA.
Piensa paso a paso:
1. Describe el flujo de trabajo automatizado de principio a fin.
2. Identifica en qué puntos del flujo es imprescindible la revisión humana (HITL) y por qué.
3. Clasifica cada punto HITL según su nivel de riesgo (alto, medio, bajo).
4. Presenta el resultado en una tabla con columnas: Paso, Descripción, ¿HITL?, Nivel de riesgo, Justificación.
````
- **Reflexiona:** ¿Coinciden los puntos HITL que sugirió Copilot con los que tú habrias elegido? ¿Añadirías algún otro?

---
### Resumen
#### Las 5 preguntas de verificación ética

Antes de usar cualquier salida de IA, hazte estas preguntas:

| # | Pregunta | Qué buscas |
|---|---|---|
| 1 | ¿Es **preciso**? | Datos, cifras y hechos verificables. |
| 2 | ¿Es **justo**? | Ausencia de sesgos de género, etnia, edad u otros. |
| 3 | ¿Es **apropiado**? | Tono, nivel de detalle y formato adecuados para la audiencia. |
| 4 | ¿Es **transparente**? | ¿Puedo explicar de dónde viene esta información? |
| 5 | ¿Estoy **asumiendo la responsabilidad**? | ¿He revisado el resultado antes de compartirlo? |

| Concepto | Punto clave |
|---|---|
| **Principios de IA responsable** | Seis valores de Microsoft (equidad, fiabilidad, privacidad, inclusividad, transparencia, responsabilidad) que guían el uso ético de la IA. |
| **Human-in-the-Loop (HITL)** | Modelo de trabajo donde un humano supervisa y valida las salidas de la IA en puntos críticos. |
| **Sesgos** | Tendencias injustas en las respuestas de la IA que deben detectarse y corregirse. |
| **Alucinaciones** | Datos o hechos inventados por la IA que parecen reales. Siempre hay que verificar. |
| **Verificación ética** | 5 preguntas (preciso, justo, apropiado, transparente, responsable) antes de usar cualquier salida de IA. |

### Recursos adicionales

- https://www.microsoft.com/en-us/ai/responsible-ai - Página oficial de Microsoft sobre IA responsable, principios y herramientas.
- https://cdn-dynmedia-1.microsoft.com/is/content/microsoftcorp/microsoft/msc/documents/presentations/CSR/2025-Microsoft-Responsible-AI-Transparency-Report.pdf - Informe anual de transparencia de Microsoft sobre IA responsable.
- https://parseur.com/blog/hitl-best-practices - Guía práctica sobre implementación de HITL en flujos de trabajo.
- https://adoption.microsoft.com/copilot/prompt-gallery/ - Biblioteca de prompts listos para usar.
