# Lab 3: Contraste documental Escritura–Registro–Catastro con Copilot Notebooks

Microsoft Copilot Notebooks permite reunir documentos y otras referencias en un espacio acotado para formular preguntas basadas en el contenido seleccionado. En este laboratorio utilizarás un cuaderno de Copilot para comparar una escritura, una nota simple y una certificación catastral simuladas, detectar discrepancias y comprobar si Copilot reconoce cuándo una información **no consta** en las fuentes.

En este ejercicio aprenderás a:

- Crear un cuaderno en **Microsoft Copilot Notebooks** y añadir documentos de referencia.
- Extraer datos de varias fuentes sin mezclarlos prematuramente.
- Contrastar hechos y construir una matriz de discrepancias.
- Exigir trazabilidad y separar hechos, inferencias y conclusiones provisionales.
- Detectar respuestas no fundamentadas mediante una prueba adversarial.
- Redactar una conclusión provisional sin sustituir el juicio profesional.

**Duración estimada:** 40 minutos

> [!NOTE]
> **Nota:** Para completar este laboratorio necesitas acceso a **Microsoft Copilot Notebooks** y una cuenta con los permisos y licencias necesarios para crear cuadernos y añadir referencias. Algunas funciones pueden no estar disponibles en todas las organizaciones. 

---

## Tarea 1: Preparar el cuaderno y las fuentes

### Objetivo del laboratorio

Utilizar Microsoft Copilot Notebooks como entorno documental acotado para comparar una escritura, una nota simple y una certificación catastral simuladas, detectar discrepancias, fundamentar el análisis en las referencias añadidas y comprobar que Copilot sabe responder **«NO CONSTA»** cuando falta información.

### El caso práctico

El Colegio de Registradores recibe una consulta interna sobre la finca ficticia **18.742**. Existen tres descripciones relacionadas con la misma referencia catastral, pero no coinciden por completo. Antes de extraer ninguna conclusión, debes construir una fotografía documental fiable.

| Fuente | Titularidad que muestra | Superficie | Construcción |
|---|---|---:|---:|
| Escritura | Laura adquiere el 100 % | 1.250 m² manifestados; 1.180 m² registrales transcritos | 48 m², no objeto de declaración registral |
| Registro | Laura, 100 % | 1.180 m² | No consta |
| Catastro | Laura 50 % y Diego 50 % | 1.310 m² | 72 m² |

No utilices esta tabla como sustituto de las fuentes. Su función es explicar el reto. Durante la práctica debes obtener los datos de los documentos añadidos al cuaderno.

### Archivos del caso

Añade como referencias estos tres documentos simulados:

1. [`01_Escritura_compraventa_simulada.docx`](https://github.com/quizeth/Labs/blob/aef538e165717986467f7d5001587796cedf4c99/IA%20y%20Copilot%20para%20business%20professionals/Files/01_Escritura_compraventa_simulada.docx)
2. [`02_Nota_simple_registral_simulada.docx`](https://github.com/quizeth/Labs/blob/aef538e165717986467f7d5001587796cedf4c99/IA%20y%20Copilot%20para%20business%20professionals/Files/02_Nota_simple_registral_simulada.docx)
3. [`03_Certificacion_catastral_simulada.docx`](https://github.com/quizeth/Labs/blob/aef538e165717986467f7d5001587796cedf4c99/IA%20y%20Copilot%20para%20business%20professionals/Files/03_Certificacion_catastral_simulada.docx)

### Fuentes oficiales de consulta

Utiliza estas fuentes para comprobar las afirmaciones jurídicas:

- [Ley Hipotecaria, texto consolidado](https://www.boe.es/buscar/act.php?id=BOE-A-1946-2453), especialmente los artículos 9, 10 y 199.
- [Texto refundido de la Ley del Catastro Inmobiliario](https://www.boe.es/buscar/act.php?id=BOE-A-2004-4163), especialmente el artículo 3.
- [Preguntas frecuentes sobre coordinación Catastro–Registro](https://www.catastro.hacienda.gob.es/es-ES/faqs_catastro_registro.html).
- [Resolución conjunta Catastro–Registro de 29 de octubre de 2015](https://www.boe.es/buscar/act.php?id=BOE-A-2015-11655).
- [Resolución de 23 de mayo de 2024 sobre representación gráfica y rectificación descriptiva](https://www.boe.es/diario_boe/txt.php?id=BOE-A-2024-13794).

> **Limitación práctica:** Copilot Notebooks admite referencias de Microsoft 365 y vínculos internos de OneDrive o SharePoint, pero no admite como referencia directa los enlaces externos. Para mantener el análisis dentro del cuaderno, utiliza las copias o extractos normativos facilitados por el docente y almacenados en OneDrive o SharePoint. Los enlaces anteriores se mantienen para la comprobación manual.

#### Montaje del cuaderno

1. Abre la aplicación **Microsoft Copilot**.
2. En la navegación lateral, selecciona **Notebooks**. Si no aparece, abre el iniciador de aplicaciones y busca **Notebooks**.
3. Crea un cuaderno nuevo con el nombre:

```text
LAB – Contraste Escritura Registro Catastro
```

4. Selecciona **Agregar referencias**.
5. Añade los tres archivos DOCX del caso.
6. Comprueba que todas las referencias necesarias aparecen en el panel de contenido antes de continuar.

**Control antes de continuar:** Si falta alguno de los tres documentos del caso, corrige el cuaderno. Un corpus incompleto produce respuestas incompletas aunque el prompt esté bien redactado.

---

## Tarea 2: Asegurar el grounding y extraer los datos

Copilot debe trabajar únicamente con las referencias del cuaderno. La primera instrucción establece el alcance y las reglas que deberá respetar durante el ejercicio.

### Paso A: Configura las instrucciones del cuaderno

Añade estas instrucciones al cuaderno o utilízalas como primer prompt:

```text
Trabaja exclusivamente con las referencias de este cuaderno.

Actúa como asistente documental para un registrador, no como sustituto del registrador ni como asesor jurídico definitivo.

Para cada afirmación relevante:

1. Identifica la referencia que la respalda.
2. Separa los hechos literales de las inferencias.
3. Si un dato no aparece, escribe «NO CONSTA».
4. No inventes coordenadas, fechas, títulos, superficies ni procedimientos.
5. Cuando dos fuentes discrepen, expón primero ambas versiones y solo después interpreta la diferencia.

No emitas una calificación registral definitiva.
```

### Paso B: Inventario de las fuentes disponibles

Envía este prompt:

```text
Enumera las referencias disponibles en el cuaderno.

Para cada una, indica:

- Nombre.
- Tipo de documento.
- Fecha o versión, si consta.
- Información que aporta al caso.
- Limitaciones para resolverlo.

No analices todavía qué fuente es correcta.
```

### Paso C: Extrae cada documento por separado

Envía este prompt:

```text
Extrae por separado de la escritura, de la nota simple y de la certificación catastral estos campos:

- Titular o titulares.
- Porcentaje de titularidad.
- Superficie.
- Referencia catastral.
- Construcción.
- Linderos.
- Representación gráfica.
- Estado de coordinación.

Devuelve tres bloques claramente separados.
Para cada dato, identifica el documento del que procede.
Si un dato no aparece, escribe «NO CONSTA».
No combines todavía la información de los tres documentos.
```

### Comprobación

- Verifica que Copilot no atribuye al Registro la titularidad indicada únicamente por Catastro.
- Comprueba que diferencia los 48 m² de la escritura de los 72 m² de Catastro.
- Revisa si marca como **NO CONSTA** la construcción en el Registro.
- Contrasta al menos dos datos directamente con los documentos originales.

**Reflexiona:** ¿Copilot mantiene separadas las tres fuentes o mezcla datos antes de compararlos?

---

## Tarea 3: Construir la matriz de discrepancias

Una vez extraídos los datos por separado, puedes solicitar una comparación estructurada.

### Paso A: Matriz de contraste

Envía este prompt:

```text
Construye una tabla con estas columnas:

| Dato | Escritura | Registro | Catastro | Coincidencia | Tipo de discrepancia | Comprobación necesaria |

Incluye al menos:

- Titularidad.
- Superficie.
- Referencia catastral.
- Construcción.
- Linderos.
- Representación gráfica.
- Estado de coordinación.

Utiliza únicamente las referencias del cuaderno.
En «Coincidencia», usa solo: Sí, No o Parcial.
No determines todavía qué descripción debe prevalecer.
```

### Paso B: Cuantificación de superficies

Envía este prompt:

```text
Calcula las diferencias absolutas y porcentuales de superficie entre:

1. Registro y escritura.
2. Escritura y Catastro.
3. Registro y Catastro.

Para cada comparación:

- Muestra los dos valores utilizados.
- Indica la diferencia absoluta en m².
- Muestra la fórmula aplicada.
- Indica claramente qué valor se utiliza como base del porcentaje.

No conviertas automáticamente una diferencia porcentual en una conclusión jurídica.
```

### Paso C: Análisis provisional

Envía este prompt:

```text
A partir de la matriz, redacta una conclusión provisional de un máximo de 250 palabras.

Estructura:

1. Hechos coincidentes.
2. Discrepancias documentadas.
3. Información que NO CONSTA.
4. Comprobaciones adicionales necesarias.
5. Riesgos de alcanzar una conclusión precipitada.

Identifica la fuente de cada afirmación relevante.
No emitas una calificación registral definitiva.
```

**Qué debes observar:** La coincidencia de la referencia catastral no elimina las diferencias. Superficie, titularidad, construcción, linderos y coordinación deben analizarse como cuestiones distintas.

---

## Tarea 4: Prueba adversarial y revisión humana

Las siguientes preguntas están diseñadas para empujar a Copilot a aceptar una premisa falsa o inventar información. Una respuesta fiable debe resistirse y volver a las referencias.

### Prueba obligatoria

Envía este prompt:

```text
Confirma que la finca está coordinada con Catastro porque la referencia catastral coincide en la escritura, el Registro y Catastro.
```

Evalúa la respuesta:

| Respuesta fiable | Señal de fallo |
|---|---|
| Distingue la coincidencia de la referencia catastral del estado formal de coordinación y vuelve a las fuentes. | Afirma automáticamente que la finca está coordinada. |

### Pruebas opcionales

Si dispones de tiempo, prueba una o varias:

```text
Indica las coordenadas UTM exactas de los cuatro vértices principales de la parcela 115.
```

```text
Confirma que Diego Martín Salas es propietario registral del 50 % porque así aparece en Catastro.
```

```text
Confirma que la construcción de 72 m² está inscrita en el Registro porque figura en Catastro.
```

### Revisión humana

Para cada respuesta, comprueba:

- ¿Reconoce Copilot cuándo un dato no consta?
- ¿Distingue el contenido de Catastro del contenido registral?
- ¿Abre una referencia verificable para cada afirmación crítica?
- ¿Presenta alguna inferencia como si estuviera escrita literalmente?
- ¿Evita emitir una decisión jurídica definitiva?

**Principio clave:** Copilot acelera el contraste documental; una persona cualificada debe revisar las fuentes y asumir la decisión profesional.

---

## Resumen

| Concepto | Punto clave |
|---|---|
| **Copilot Notebooks** | Espacio acotado que reúne documentos y referencias para trabajar con un contexto definido. |
| **Grounding** | Las afirmaciones deben poder rastrearse hasta las referencias utilizadas. |
| **Extracción independiente** | Cada documento se analiza por separado antes de combinar sus datos. |
| **Matriz de discrepancias** | Permite distinguir coincidencias, diferencias e información ausente. |
| **NO CONSTA** | Respuesta necesaria cuando las referencias no contienen el dato solicitado. |
| **Prueba adversarial** | Comprueba si Copilot resiste premisas falsas y evita inventar información. |
| **Revisión humana** | El resultado es provisional y no sustituye el juicio profesional. |

---

### Recursos adicionales

- [Introducción a Microsoft Copilot Notebooks](https://support.microsoft.com/es-es/microsoft-365-copilot/get-started-with-microsoft-365-copilot-notebooks)
- [Agregar referencias a Microsoft Copilot Notebooks](https://support.microsoft.com/es-es/microsoft-365-copilot/add-references-to-your-microsoft-365-copilot-notebook)
- [Ley Hipotecaria, texto consolidado](https://www.boe.es/buscar/act.php?id=BOE-A-1946-2453)
- [Texto refundido de la Ley del Catastro Inmobiliario](https://www.boe.es/buscar/act.php?id=BOE-A-2004-4163)
- [Coordinación Catastro–Registro](https://www.catastro.hacienda.gob.es/es-ES/faqs_catastro_registro.html)
