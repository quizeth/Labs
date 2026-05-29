## Lab: Ética en la IA

### Introduccion

La inteligencia artificial generativa puede redactar informes, analizar datos y automatizar flujos de trabajo en segundos. Sin embargo, esa velocidad solo es util si los resultados son justos, seguros y verificables. Comprender los principios de IA responsable y saber cuando un ser humano debe intervenir es tan importante como saber escribir un buen prompt.

En este ejercicio aprenderas a:
- Identificar y aplicar los **seis principios de IA responsable de Microsoft** en situaciones cotidianas.
- Comprender el concepto de **HITL** (Human-in-the-Loop) y saber cuando la supervision humana es imprescindible.
- Evaluar criticamente las respuestas de una IA y detectar posibles sesgos, errores o riesgos eticos.
- Disenar puntos de control humano en un flujo de trabajo con IA.

**Duracion estimada:** 15 minutos

### Tarea 1: Los seis principios de IA responsable de Microsoft

#### ¿Que es la IA responsable?

La IA responsable es un conjunto de practicas que garantizan que los sistemas de inteligencia artificial se disenan, desarrollan y despliegan de forma etica, segura y transparente. Microsoft ha definido seis principios fundamentales que guian todo el ciclo de vida de sus productos de IA:

| # | Principio | Descripcion | Ejemplo practico |
|---|---|---|---|
| 1 | **Equidad** (Fairness) | Los sistemas de IA deben tratar a todas las personas de forma justa, sin discriminar por genero, etnia, edad u otras caracteristicas. | Un modelo de seleccion de personal no debe favorecer candidatos de un genero sobre otro. |
| 2 | **Fiabilidad y seguridad** (Reliability & Safety) | Los sistemas deben funcionar de forma predecible y segura, incluso en situaciones inesperadas. | Un chatbot de atencion al cliente no debe proporcionar informacion medica incorrecta. |
| 3 | **Privacidad y seguridad** (Privacy & Security) | Los sistemas deben proteger los datos personales y cumplir con la normativa de privacidad vigente. | Copilot solo accede a los datos para los que el usuario tiene permisos. |
| 4 | **Inclusividad** (Inclusiveness) | Los sistemas deben ser accesibles y utiles para todas las personas, independientemente de sus capacidades. | Las interfaces de IA deben ser compatibles con lectores de pantalla y otras tecnologias de asistencia. |
| 5 | **Transparencia** (Transparency) | Los usuarios deben entender como funciona el sistema y por que genera una determinada respuesta. | Copilot muestra las fuentes en las que basa sus respuestas para que el usuario pueda verificarlas. |
| 6 | **Responsabilidad** (Accountability) | Las personas (no las maquinas) son responsables del uso y las consecuencias de los sistemas de IA. | Un analista debe revisar y validar cualquier informe generado por IA antes de enviarlo a direccion. |

**Principio clave:** La IA es una herramienta, no un decisor. Tu eres el responsable final de cada resultado que compartes, publicas o ejecutas.

#### Tarea practica

- **Abre Microsoft Copilot** (copilot.microsoft.com o la app integrada en M365).
- **Copia y envia** el siguiente prompt:

  Eres un consultor de etica en IA. Explica los seis principios de IA responsable de Microsoft con un ejemplo practico de cada uno aplicado a una empresa de logistica. Formato: tabla con columnas Principio, Definicion, Ejemplo en logistica.

- Revisa la respuesta de Copilot y **compara** los ejemplos generados con los de la tabla anterior. Preguntate:
  - ¿Son los ejemplos realistas y aplicables a tu contexto?
  - ¿Falta algun matiz importante?
- **Envia este prompt de seguimiento** para poner a prueba el principio de equidad:

  Redacta una oferta de empleo para un puesto de analista de datos en una empresa de logistica. Asegurate de que el texto sea inclusivo y no contenga sesgos de genero, edad o procedencia.

- **Reflexiona:** ¿Detectas algun sesgo sutil en la respuesta? ¿Que ajustarias?

### Tarea 2: Human-in-the-Loop (HITL)

#### ¿Que es HITL?

**Human-in-the-Loop (HITL)** — o "humano en el bucle" — es un modelo de trabajo en el que un ser humano supervisa, valida o corrige las salidas de un sistema de IA en puntos criticos del proceso, en lugar de dejar que la IA actue de forma completamente autonoma.

| Aspecto | Sin HITL (automatizacion total) | Con HITL (supervision humana) |
|---|---|---|
| **Velocidad** | Maxima | Alta (con pausas controladas) |
| **Riesgo de error** | Alto en tareas ambiguas | Reducido por revision humana |
| **Responsabilidad** | Diluida | Clara: el humano aprueba |
| **Confianza** | Baja en decisiones criticas | Alta: validacion explicita |
| **Ejemplo** | Un bot envia emails a clientes sin revision | Un humano revisa el borrador antes de enviarlo |

#### ¿Cuando es imprescindible HITL?

| Escenario | ¿Por que necesita HITL? |
|---|---|
| Decisiones financieras (aprobacion de gastos, facturas) | Errores pueden tener impacto economico directo. |
| Comunicaciones externas (emails a clientes, proveedores) | Un tono inadecuado o dato incorrecto danan la reputacion. |
| Datos personales o sensibles (RGPD, informacion de empleados) | Obligacion legal de proteger la privacidad. |
| Informes para direccion | La IA puede alucinar datos o malinterpretar el contexto. |
| Contratacion y evaluacion de personas | Riesgo de sesgos algoritmicos con impacto real en personas. |

**Principio clave:** La IA acelera el trabajo; el humano garantiza que el resultado es correcto, etico y adecuado al contexto. Automatiza lo repetitivo, supervisa lo critico.

#### Tarea practica

**Paso A — Identificar el riesgo sin HITL:** Copia y envia este prompt a Copilot:

  Eres un director de operaciones de una empresa de logistica. Redacta un email al cliente Contoso informandole de un retraso de 5 dias en su envio. Tono profesional y empatico. Maximo 150 palabras.

- **Revisa la respuesta** como si fueras el punto de control humano (HITL). Preguntate:
  - ¿El tono es adecuado para este cliente?
  - ¿Hay datos inventados (fechas, numeros de referencia, motivos del retraso)?
  - ¿Enviarias este email tal cual? ¿Que cambiarias?

**Paso B — Disenar un flujo HITL:** Ahora envia este prompt:

  Soy un analista de datos en una empresa de logistica. Quiero automatizar el envio semanal de informes de KPIs a direccion usando IA.
  Piensa paso a paso:
  1. Describe el flujo de trabajo automatizado de principio a fin.
  2. Identifica en que puntos del flujo es imprescindible la revision humana (HITL) y por que.
  3. Clasifica cada punto HITL segun su nivel de riesgo (alto, medio, bajo).
  4. Presenta el resultado en una tabla con columnas: Paso, Descripcion, ¿HITL?, Nivel de riesgo, Justificacion.

- **Reflexiona:** ¿Coinciden los puntos HITL que sugirio Copilot con los que tu habrias elegido? ¿Anadirías algun otro?

### Tarea 3: Deteccion de sesgos y alucinaciones

#### Conceptos clave

| Concepto | Que es | Por que importa |
|---|---|---|
| **Sesgo** (Bias) | Tendencia sistematica de la IA a favorecer o perjudicar a un grupo. Puede provenir de los datos de entrenamiento o del diseno del prompt. | Decisiones injustas que afectan a personas reales (contratacion, credito, atencion al cliente). |
| **Alucinacion** (Hallucination) | La IA genera informacion que parece correcta pero es inventada o falsa (datos, citas, hechos). | Informes, emails o decisiones basados en datos ficticios pueden tener consecuencias graves. |
| **Verificacion humana** | El proceso de comprobar manualmente la exactitud y adecuacion de las salidas de la IA. | Es la ultima linea de defensa antes de que un resultado llegue a su destinatario. |

#### Las 5 preguntas de verificacion etica

Antes de usar cualquier salida de IA, hazte estas preguntas:

| # | Pregunta | Que buscas |
|---|---|---|
| 1 | ¿Es **preciso**? | Datos, cifras y hechos verificables. |
| 2 | ¿Es **justo**? | Ausencia de sesgos de genero, etnia, edad u otros. |
| 3 | ¿Es **apropiado**? | Tono, nivel de detalle y formato adecuados para la audiencia. |
| 4 | ¿Es **transparente**? | ¿Puedo explicar de donde viene esta informacion? |
| 5 | ¿Estoy **asumiendo la responsabilidad**? | ¿He revisado el resultado antes de compartirlo? |

#### Tarea practica

- **Copia y envia** este prompt a Copilot:

  Genera un parrafo de 100 palabras sobre los resultados financieros de Contoso en el ultimo trimestre, incluyendo cifras de ingresos, margen operativo y crecimiento interanual.

- **Analiza la respuesta:**
  - ¿Los datos son reales o inventados? (Contoso es una empresa ficticia de Microsoft, por lo que cualquier cifra sera una alucinacion).
  - ¿Copilot advierte de que los datos no son verificables?
- **Envia este prompt de seguimiento:**

  ¿Los datos que acabas de proporcionar son reales y verificables? ¿De que fuentes proceden?

- **Reflexiona:** ¿Que habria pasado si hubieras incluido esas cifras en un informe para direccion sin verificarlas?

### Resumen

| Concepto | Punto clave |
|---|---|
| **Principios de IA responsable** | Seis valores de Microsoft (equidad, fiabilidad, privacidad, inclusividad, transparencia, responsabilidad) que guian el uso etico de la IA. |
| **Human-in-the-Loop (HITL)** | Modelo de trabajo donde un humano supervisa y valida las salidas de la IA en puntos criticos. |
| **Sesgos** | Tendencias injustas en las respuestas de la IA que deben detectarse y corregirse. |
| **Alucinaciones** | Datos o hechos inventados por la IA que parecen reales. Siempre hay que verificar. |
| **Verificacion etica** | 5 preguntas (preciso, justo, apropiado, transparente, responsable) antes de usar cualquier salida de IA. |

### Recursos adicionales

- [Responsible AI - Microsoft](https://www.microsoft.com/en-us/ai/responsible-ai) - Pagina oficial de Microsoft sobre IA responsable, principios y herramientas.
- [2025 Responsible AI Transparency Report](https://cdn-dynmedia-1.microsoft.com/is/content/microsoftcorp/microsoft/msc/documents/presentations/CSR/2025-Microsoft-Responsible-AI-Transparency-Report.pdf) - Informe anual de transparencia de Microsoft sobre IA responsable.
- [Human-in-the-Loop AI: Definition, Benefits & Trends](https://parseur.com/blog/hitl-best-practices) - Guia practica sobre implementacion de HITL en flujos de trabajo.
- [Prompt Gallery de Microsoft](https://adoption.microsoft.com/copilot/prompt-gallery/) - Biblioteca de prompts listos para usar.

**Fin del laboratorio**
