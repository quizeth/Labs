# Lab 4: Crear y probar un agente Legal-Financiero con M365 Copilot

Los agentes de Microsoft 365 Copilot permiten configurar un comportamiento especializado y reutilizable mediante instrucciones, fuentes de conocimiento e indicaciones sugeridas. En este laboratorio crearás un agente para realizar una primera revisión interna de contratos SaaS, comprobarás su comportamiento con un caso de riesgo y reutilizarás el mismo método con un segundo expediente.

En este ejercicio aprenderás a:

- Crear un agente mediante lenguaje natural con Agent Builder.
- Distinguir entre instrucciones, conocimiento permanente y archivos de sesión.
- Configurar un método de revisión jurídica y financiera reutilizable.
- Probar el agente antes de utilizarlo.
- Detectar y corregir un fallo en su comportamiento.
- Reutilizar el mismo agente con expedientes diferentes.
- Verificar fuentes, cálculos y límites de revisión humana.

!!! note "Nota"

   Para completar este laboratorio necesitas acceso a Microsoft 365 Copilot y permisos para crear agentes con Agent Builder. Utiliza únicamente los archivos ficticios proporcionados. No cargues contratos reales ni información confidencial.


---

### El caso práctico

Lumen Retail España, S.A. compra con frecuencia aplicaciones SaaS. Los equipos de Legal y Finanzas necesitan una primera revisión homogénea antes de dedicar tiempo a una negociación.

El agente no aprobará contratos ni autorizará gasto. Su función será:

- Detectar condiciones jurídicas y financieras relevantes.
- Calcular la exposición económica.
- Comparar el expediente con las reglas internas.
- Clasificar los hallazgos.
- Señalar qué debe negociar o escalar una persona.

### Materiales

Todos los archivos están en la carpeta /Files del curso.

| Archivo | Uso |
|---|---|
| [`01_Playbook_Juridico_SaaS.docx`](recursos/files/01_Playbook_Juridico_SaaS.docx) | Conocimiento permanente del agente |
| [`02_Politica_Financiera_Aprobaciones_SaaS.docx`](recursos/files/02_Politica_Financiera_Aprobaciones_SaaS.docx) | Conocimiento permanente del agente |
| [`03_Contrato_OrionCloud.docx`](recursos/files/03_Contrato_OrionCloud.docx) | Primer caso de prueba |
| [`04_Solicitud_Compra_OrionCloud.docx`](recursos/files/04_Solicitud_Compra_OrionCloud.docx) | Datos económicos y de negocio del primer caso |
| [`05_Contrato_NovaDesk.docx`](recursos/files/05_Contrato_NovaDesk.docx) | Segundo caso de prueba |
| [`06_Solicitud_Compra_NovaDesk.docx`](recursos/files/06_Solicitud_Compra_NovaDesk.docx) | Datos económicos y de negocio del segundo caso |

> **Nota importante:** Los archivos 01 y 02 contienen reglas estables para el conocimiento del agente. Si tienes una licencia básica de Microsoft 365 Copilot y no puedes subir directamente archivos a la base de conocimiento del agente, utiliza [conocimiento.md](../conocimiento.md) en su lugar.

---

## Tarea 1: Crear el agente

Un agente organiza un comportamiento reutilizable: qué debe hacer, cómo debe hacerlo, qué conocimiento estable debe consultar y qué límites debe respetar.

### Tarea práctica

1. Abre Microsoft 365 Copilot.
2. Selecciona **Agentes**.
3. Selecciona **Nuevo agente** o **Crear agente**.
4. Utiliza la creación mediante lenguaje natural.
5. Copia y envía la siguiente descripción:

```text
Crea un agente llamado «Revisor Legal-Financiero SaaS» para realizar una primera revisión interna de contratos de proveedores SaaS de Lumen Retail España.

Debe:

1. Extraer las condiciones jurídicas y financieras relevantes del contrato y de la solicitud de compra que adjunte el usuario.
2. Compararlas con el playbook jurídico y la política financiera incluidos en su conocimiento.
3. Separar claramente HECHO, CÁLCULO e INTERPRETACIÓN.
4. Calcular el coste recurrente anual y el compromiso total cuando existan datos suficientes, mostrando la fórmula.
5. Clasificar cada hallazgo como VERDE, ÁMBAR o ROJO según las reglas internas.
6. Indicar qué aprobación interna o escalado sería necesario.
7. Preparar una tabla final con estas columnas: tema, condición encontrada, regla interna, clasificación, recomendación y fuente.
8. Terminar con «Preguntas abiertas», «Siguientes pasos» y «Revisión humana necesaria».

Nunca debe aprobar contratos, autorizar gasto, afirmar que un contrato es legal o seguro ni sustituir a Legal, Finanzas, DPO, Seguridad o Compras.

Si falta información, debe escribir «NO CONSTA» y solicitarla.

Debe identificar la fuente concreta de cada conclusión importante.
```

6. Abre la pestaña **Configurar**.
7. Revisa el nombre, la descripción y las instrucciones generadas.
8. Corrige cualquier formulación que permita al agente aprobar, autorizar o decidir.

**Resultado esperado:** Agent Builder debe generar una primera configuración con el nombre, la descripción y las instrucciones del agente.

---

## Tarea 2: Configurar el conocimiento permanente

El conocimiento permanente contiene las reglas que el agente debe reutilizar en todos los expedientes. Los contratos concretos no pertenecen a esta sección porque cambian en cada ejecución.

!!! note "Si tienes la licencia básica de Microsoft 365 Copilot"

   Utiliza el archivo [conocimiento.md](../conocimiento.md) como fuente de conocimiento, en lugar de cargar por separado los archivos 01 y 02.

### Tarea práctica

1. En la pestaña **Configurar**, localiza **Conocimiento**.
2. Si no tienes la licencia básica, añade estos dos archivos:
   - `01_Playbook_Juridico_SaaS.docx`
   - `02_Politica_Financiera_Aprobaciones_SaaS.docx`
3. Comprueba que los archivos 01 y 02, o `conocimiento.md` según tu licencia, aparecen como fuente de conocimiento.
4. No añadas los contratos OrionCloud o NovaDesk.
5. En **Indicaciones sugeridas**, añade:

```text
Revisa un contrato SaaS y dime qué debo negociar o escalar.
```

```text
Calcula el compromiso económico y dime qué aprobaciones financieras requiere.
```

```text
Resume los riesgos jurídicos y financieros para una reunión con Compras.
```

**Punto de control:** Antes de continuar, confirma que el conocimiento permanente incluye el playbook jurídico y la política financiera, ya sea mediante los archivos 01 y 02 o mediante `conocimiento.md`.

---

## Tarea 3: Probar el agente con OrionCloud

OrionCloud es el caso de mayor riesgo. La prueba sirve para comprobar si el agente aplica el método configurado, utiliza las reglas internas y evita emitir decisiones definitivas.

### Tarea práctica

1. Abre la pestaña **Probar** o **Vista previa**.
2. Adjunta:
   - `03_Contrato_OrionCloud.docx`
   - `04_Solicitud_Compra_OrionCloud.docx`
3. Envía el siguiente prompt:

```text
Haz la revisión completa de este caso.

Incluye:

- Resumen ejecutivo.
- Tabla de riesgos Legal + Finanzas.
- Cálculos financieros con fórmula.
- Aprobaciones necesarias.
- Cinco puntos prioritarios para negociar con el proveedor.
- Preguntas abiertas.
- Revisión humana necesaria.

Aplica estrictamente tu conocimiento y muestra la fuente de cada conclusión importante.
```

### Validación mínima de OrionCloud

| Comprobación | Resultado esperado |
|---|---|
| Coste anual | 220.000 EUR; requiere CFO según la política financiera |
| Compromiso total inicial | `220.000 × 3 + 60.000 = 720.000 EUR` |
| Plazo | 36 meses no cancelables; condición no estándar |
| Incremento | 6 % anual; supera el límite indicado |
| Renovación | 30 días; inferior al estándar de 60 días |
| Cambios unilaterales | Riesgo ROJO |
| Responsabilidad | 50 % de la tarifa anual; riesgo ROJO |
| Privacidad | DPA pendiente y transferencias no cerradas |
| Incidentes | Notificación en cinco días laborables; supera el estándar de 72 horas |
| Propiedad intelectual | No existe indemnidad específica |
| Jurisdicción | Nueva York; requiere escalado |

No busques una redacción idéntica. Evalúa el método:

- ¿Ha detectado los riesgos principales?
- ¿Ha mostrado la fórmula financiera?
- ¿Ha separado hechos, cálculos e interpretaciones?
- ¿Ha identificado las fuentes?
- ¿Ha evitado aprobar el contrato o el gasto?

---

## Tarea 4: Corregir el comportamiento

Una primera configuración rara vez es perfecta. Debes detectar un problema observable y modificar las instrucciones del agente, no limitarte a reformular el prompt de prueba.

### Tarea práctica

1. Identifica un fallo concreto en la respuesta de OrionCloud. Por ejemplo:
   - Omite el cálculo del compromiso total.
   - No identifica las fuentes.
   - Mezcla hechos e interpretaciones.
   - Presenta una aprobación como decisión final.
   - No incluye la revisión humana.
2. Vuelve a la edición del agente.
3. Envía esta instrucción:

```text
Modifica el agente para que nunca emita una aprobación final.

Debe:

- Separar HECHO, CÁLCULO e INTERPRETACIÓN.
- Mostrar las fórmulas financieras.
- Identificar la fuente de cada conclusión importante.
- Terminar siempre con aprobaciones requeridas, preguntas abiertas y revisión humana necesaria.
```

4. Regresa a **Probar** o **Vista previa**.
5. Ejecuta de nuevo el caso OrionCloud.
6. Compara la segunda respuesta con la primera.

**Criterio de éxito:** La segunda respuesta debe corregir el fallo identificado sin perder los elementos que ya funcionaban.

---

## Tarea 5: Crear y reutilizar el agente con NovaDesk

La reutilización demuestra la diferencia entre un agente y una conversación aislada. Las reglas permanecen; el expediente cambia.

### Tarea práctica

1. Cuando la prueba sea satisfactoria, selecciona **Crear**.
2. Abre el agente **Revisor Legal-Financiero SaaS**.
3. Inicia una conversación nueva.
4. Adjunta:
   - `05_Contrato_NovaDesk.docx`
   - `06_Solicitud_Compra_NovaDesk.docx`
5. Envía el siguiente prompt:

```text
Realiza la misma revisión Legal + Finanzas aplicada al caso anterior.

Usa exactamente el mismo método y las mismas reglas internas.

Muestra los cálculos, la clasificación, las fuentes y la revisión humana necesaria.
```

### Validación mínima de NovaDesk

| Tema | Resultado esperado |
|---|---|
| Coste recurrente | 85.000 EUR |
| Coste del primer año | `85.000 + 15.000 = 100.000 EUR` |
| Plazo | 12 meses |
| Incremento | 2,5 % |
| Renovación | 90 días |
| Cambios materiales | No hay cambios materiales unilaterales |
| Responsabilidad | 1x general y 2x para datos y propiedad intelectual |
| Datos | DPA incorporado y producción en la UE |
| Incidentes | Notificación en 48 horas |
| Propiedad intelectual | Existe indemnidad |
| Ley aplicable | España/Madrid |

**Conclusión didáctica:** OrionCloud debería producir varios hallazgos ÁMBAR o ROJO. NovaDesk debería resultar mayoritariamente VERDE. La diferencia procede de los expedientes, no del método de revisión.

---

## Reflexión final

Responde brevemente:

1. ¿Qué información permanece configurada en el agente?
2. ¿Qué información cambia en cada expediente?
3. ¿Por qué los contratos no deben añadirse al conocimiento permanente?
4. ¿Qué mejoró después de modificar las instrucciones?
5. ¿Qué decisiones siguen necesitando revisión humana?

---

## Resumen

| Concepto | Punto clave |
|---|---|
| **Copilot Chat** | Adecuado para tareas puntuales y flexibles |
| **Copilot Notebooks** | Adecuado para investigar un conjunto acotado de referencias |
| **Agente** | Mantiene un método reutilizable con instrucciones y conocimiento persistentes |
| **Agent Builder** | Permite crear y configurar agentes declarativos mediante lenguaje natural |
| **Conocimiento permanente** | Contiene reglas estables que deben aplicarse a todos los casos |
| **Archivos de sesión** | Contienen la información variable de cada expediente |
| **Vista previa** | Permite probar y refinar el agente antes de utilizarlo |
| **Revisión humana** | El agente asiste en el análisis, pero no aprueba contratos ni autoriza gasto |