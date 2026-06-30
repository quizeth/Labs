# Ejercicio 7: Identificar y filtrar duplicados por múltiples columnas

En este ejercicio detectarás duplicados de negocio basados en una combinación de columnas relevantes del conjunto de datos de ParcelCraft.

El objetivo no es eliminar filas vacías ni errores, sino identificar posibles registros repetidos del mismo envío y, si procede, conservar una versión única para análisis.

## Tarea 1: Crear consulta de auditoría de duplicados

1. Haz clic derecho en `ParcelCraft_CLEAN`.
2. Selecciona **Referencia**.
3. Cambia el nombre a `qa_DuplicadosNegocio`.
4. Mueve la consulta al grupo **Staging** y deshabilita su carga.
5. Conserva las columnas que definen la unicidad lógica de un envío:
   - `EnvíoID`
   - `FechaEnvío`
   - `FechaCreación`
   - `OficinaID`
   - `ClienteID`
   - `ServicioID`
   - `Código Postal (Destino)`

6. Conserva también la columna `ÚltimaExtracción`.
> La columna `ÚltimaExtracción` no se usa para identificar duplicados, pero sí puede usarse después para conservar el registro más reciente.

6. Renombra el paso aplicado como `Columnas de auditoría seleccionadas`.

## Tarea 3: Agrupar para detectar duplicados
1. Selecciona **Transformar > Agrupar por**.
2. En la ventana **Agrupar por**, selecciona **Uso avanzado**.
3. Agrega agrupaciones para las todas las columnas, excepto `ÚltimaExtracción`.
4. Crea una nueva columna con esta configuración:
  - **Nombre de nueva columna**: `ConteoFilas`
  - **Operación**: Recuento de filas

5. Agrega las siguientes columnas de agrupación (todas excepto `ÚltimaExtracción`) y selecciona **Aceptar**:.
   - `EnvíoID`
   - `FechaEnvío`
   - `FechaCreación`
   - `OficinaID`
   - `ClienteID`
   - `ServicioID`
   - `Código Postal (Destino)`

6. Selecciona el menú desplegable de filtros (icono de la flecha hacia abajo) en el encabezado de la columna **ConteoFilas**.
7. Haz clic en **Filtro de número > **Mayor que**
8. Configura el filtro para conservar filas en las que "ConteoFilas" **es mayor que 1** y selecciona Aceptar.
9. Renombra el paso aplicado como `Duplicados filtrados`.

> Si esta consulta devuelve filas, significa que existen registros repetidos para la misma combinación lógica de envío, cliente, servicio, oficina y destino.

## Tarea 4: Eliminar duplicados en la tabla limpia

En esta tarea eliminarás los duplicados directamente sobre la consulta `ParcelCraft_CLEAN`, sin crear una consulta adicional. El objetivo es conservar únicamente el registro más reciente de cada envío lógico.

> Importante: este enfoque modifica directamente la consulta limpia que usarás como base para el modelo. Si quieres conservar una versión de auditoría, crea antes una referencia o duplica la consulta.

1. Selecciona la consulta `ParcelCraft_CLEAN`
2. Ordena la columna `ÚltimaExtracción` en orden descendente.

> Esto coloca primero el registro más reciente de cada posible duplicado.

3. Renombra el paso aplicado como `Filas ordenadas por última extracción`.
4. Selecciona las columnas que definen la unicidad lógica de un envío:
   - `EnvíoID`
   - `FechaEnvío`
   - `FechaCreación`
   - `OficinaID`
   - `ClienteID`
   - `ServicioID`
   - `Código Postal (Destino)`

5. Con esas columnas seleccionadas, ve a la cinta y selecciona **Inicio > Quitar filas > Quitar duplicados**
6. Actualiza la  vista previa de la consulta.
7. Vuelve a `qa_DuplicadosNegocio` y pulsa **Actualizar vista previa**.
8. Si los duplicados se han eliminado correctamente, la consulta debería estar vacía. Si quitas el filtro de `ConteoFilas`, verás que ahora el único valor de la columna es **1**.

> Al ordenar primero por ÚltimaExtracción descendente, Power Query conserva la primera aparición de cada combinación duplicada. Como la primera aparición es la más reciente, se mantiene el registro más actualizado.
