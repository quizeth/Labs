# Lab: Creación de prompts efectivos con el marco RCTF

## Introducción

La calidad de las respuestas de una IA generativa depende directamente de la calidad de las instrucciones que recibe. Un prompt vago produce resultados genericos; un prompt estructurado produce resultados utilizables.

En este ejercicio aprenderas a:

- Aplicar el marco **RCTF** (Role, Context, Task, Format) para construir prompts profesionales.
- Diferenciar entre **superprompting** y **chain of thought (CoT)** y saber cuando usar cada tecnica.
- Incorporar buenas practicas de prompt engineering en tu flujo de trabajo diario.

**Duración estimada:** 15 minutos

---

## Tarea 1: El marco RCTF

### ¿Qué es RCTF?

RCTF es un marco de cuatro componentes que estructura cualquier prompt para obtener resultados consistentes y de alta calidad:

| Componente | Pregunta clave | Ejemplo |
|---|---|---|
| **R - Role** (Rol) | ¿Quién debe ser la IA? | "Eres un analista financiero senior con 10 anos de experiencia en logistica." |
| **C - Context** (Contexto) | ¿Cuál es la situacion? | "Nuestra empresa transporta vehiculos entre Europa y America Latina. El margen operativo ha caido un 8% en Q1." |
| **T - Task** (Tarea) | ¿Qué quieres exactamente? | "Identifica las 3 causas principales de la caida y propone acciones correctivas." |
| **F - Format** (Formato) | ¿Cómo debe verse la salida? | "Presenta el resultado en una tabla con columnas: Causa, Impacto estimado, Accion propuesta, Prioridad." |

> **Principio clave:** La IA no conoce tu situacion. Cuanto más contexto le des, mejor será el resultado. La estructura vence a la creatividad.

### Tarea práctica

1. **Abre Microsoft Copilot** (copilot.microsoft.com o la app integrada en M365).
2. **Copia y envía** el siguiente prompt **sin marco** y observa el resultado:

```
Ayudame a preparar una presentacion sobre transformacion digital.
```

3. **Ahora envia este prompt con RCTF** y compara:

```
Role: Eres un consultor senior de transformacion digital especializado en empresas de logistica.

Context: Estoy preparando una presentacion para el comite de direccion de una empresa
de transporte maritimo con 2.000 empleados. Actualmente usamos hojas de calculo para
la facturacion y el seguimiento de envios. El presupuesto aprobado es de 500K EUR y el
plazo es 18 meses.

Task: Crea un esquema de presentacion de 10 diapositivas que incluya: diagnostico actual,
propuesta de solucion, hoja de ruta, analisis de riesgos y ROI estimado.

Format: Lista numerada con el titulo de cada diapositiva y 3 bullet points con el contenido
clave de cada una.
```

4. **Reflexiona:** ¿Que diferencias observas en la profundidad, relevancia y estructura de ambas respuestas?

---

## Tarea 2: Superprompting vs. Chain of Thought

### Definiciones

| Tecnica | Que es | Cuando usarla |
|---|---|---|
| **Superprompting** | Un prompt extenso y detallado que incluye toda la informacion necesaria (contexto, objetivos, ejemplos, restricciones, formato) en una sola interaccion. | Tareas bien definidas donde puedes anticipar todo lo necesario: generacion de contenido, emails, informes. |
| **Chain of Thought (CoT)** | Una tecnica que guia a la IA a "pensar paso a paso" antes de dar una respuesta final, descomponiendo el razonamiento en etapas intermedias. | Tareas que requieren logica, calculo, analisis comparativo o resolucion de problemas complejos. |

### Comparativa rápida

| Aspecto | Superprompting | Chain of Thought |
|---|---|---|
| **Estructura** | Todo en un solo prompt | Razonamiento paso a paso |
| **Control** | Alto (defines todo desde el inicio) | Alto (puedes validar cada paso) |
| **Mejor para** | Contenido, redaccion, informes | Analisis, decisiones, calculos |
| **Riesgo** | Prompt muy largo puede diluir el foco | Requiere mas tokens y tiempo |
| **Combinable** | Si, con CoT y prompts secuenciales | Si, con superprompting y RCTF |

### Tarea práctica

**Paso A - Superprompting:** Copia y envia este prompt a Copilot:

```
Eres un director de operaciones de una empresa de logistica con sede en Madrid.
Necesito que redactes un email al CEO proponiendo la automatizacion del proceso
de facturacion. La empresa procesa 5.000 facturas/mes de forma manual con un
equipo de 4 personas. El error medio es del 3,2%. Incluye: situacion actual,
propuesta de solucion, beneficios esperados (ahorro de tiempo y reduccion de
errores), y una estimacion de coste. Tono profesional pero directo. Maximo
300 palabras. Formato de email corporativo con asunto incluido.
```

**Paso B - Chain of Thought:** Ahora envia este prompt:

```
Necesito decidir si automatizar nuestro proceso de facturacion. Actualmente
procesamos 5.000 facturas/mes de forma manual con 4 personas y una tasa de
error del 3,2%.

Piensa paso a paso:
1. Calcula el coste actual del proceso manual (asume un salario bruto de
   30.000 EUR/ano por persona).
2. Estima el coste de los errores (asume un coste medio de 45 EUR por
   factura con error).
3. Investiga el coste tipico de una solucion de automatizacion de facturas.
4. Compara ambos escenarios y calcula el ROI a 2 anos.
5. Recomienda si debemos proceder o no, justificando tu respuesta.
```

**Reflexiona:** ¿Que tecnica te dio un resultado mas util para cada situacion? ¿Por que?

---

## Ejercicio 3: Buenas prácticas

### Las 8 reglas de oro del prompting

| # | Buena práctica | Ejemplo |
|---|---|---|
| 1 | **Se especifico, no generico** | Malo: "Haz un informe." Bueno: "Crea un informe ejecutivo de 2 paginas sobre la reduccion de costes en Q1." |
| 2 | **Asigna un rol experto** | "Eres un analista de datos senior especializado en supply chain." |
| 3 | **Proporciona contexto suficiente** | Incluye datos, restricciones, audiencia y objetivo. |
| 4 | **Define el formato de salida** | "Tabla con 4 columnas", "Lista numerada", "Email de 200 palabras". |
| 5 | **Usa ejemplos cuando sea posible** | "Sigue este estilo: [pega un ejemplo]." |
| 6 | **Itera y refina** | Si el primer resultado no es perfecto, ajusta el prompt en lugar de empezar de cero. |
| 7 | **Divide tareas complejas** | En lugar de un mega-prompt, divide en pasos o usa chain of thought. |
| 8 | **Revisa siempre la salida** | La IA puede generar informacion incorrecta (alucinaciones). Tu eres el filtro final. |

### Tarea práctica

1. **Elige un caso real de tu trabajo** (un email, un informe, un analisis de datos).
2. **Escribe un prompt usando RCTF** y al menos 3 de las buenas practicas anteriores.
3. **Envialo a Copilot** y evalua el resultado.
4. **Itera al menos una vez:** ajusta el prompt para mejorar la respuesta.

---

## Resumen

| Concepto | Punto clave |
|---|---|
| **RCTF** | Estructura todo prompt en 4 bloques: Rol, Contexto, Tarea, Formato. |
| **Superprompting** | Un prompt completo y detallado para tareas bien definidas. |
| **Chain of Thought** | Razonamiento paso a paso para tareas que requieren logica o calculo. |
| **Buenas practicas** | Se especifico, da contexto, define formato, itera y revisa siempre. |

---

## Recursos adicionales

- [Prompt Gallery de Microsoft](https://adoption.microsoft.com/copilot/prompt-gallery/) - Biblioteca de prompts listos para usar.
- [Prompt Engineering Guide](https://www.promptingguide.ai/) - Guia academica de tecnicas de prompting.
- [The RCTF Prompt Framework](https://rupertchesman.com/article-rctf-prompt-framework.html) - Explicacion detallada del marco RCTF.
