# Laboratorio 2: Modelado de datos en Power BI

**ParcelCraft** quiere evolucionar su modelo analítico para ofrecer informes más robustos y escalables. Tras haber preparado los datos en Power Query, el siguiente paso es construir correctamente el **modelo semántico** en Power BI.

En este laboratorio trabajarás exclusivamente en la **vista de modelo**, creando relaciones, optimizando el diseño y comprobando cómo estas decisiones afectan directamente al comportamiento de los objetos visuales.

---

## Objetivos de aprendizaje

**Tiempo estimado total: 30 minutos**

Al finalizar este laboratorio serás capaz de:
- Crear relaciones manualmente entre tablas.
- Crear relaciones usando distintas técnicas.
- Diseñar y organizar el modelo en la vista de modelo.
- Comprender el impacto de las relaciones en los objetos visuales.
- Crear jerarquías y utilizarlas en visualizaciones.
  
> [!IMPORTANT]
> Antes de comenzar, asegúrate de que en las opciones de carga del archivo están deshabilitados:
> - Detección automática de relaciones
> - Creación automática de relaciones en segundo plano
>
> Tienes disponible un archivo inicial [Starter - ParcelCraft.pbix](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2002/Files/02%20Starter%20-%20ParcelCraft.pbix).

---

# Ejercicio

## Tarea 1. Verificar ausencia de relaciones automáticas

1. Abre tu archivo de Power BI del laboratorio anterior.
2. Ve a la **vista de modelo**.
3. Observa que todas las tablas aparecen **sin relaciones**.

Este es el comportamiento esperado, ya que la detección automática está deshabilitada. Para examinar el impacto de esta falta de relaciones, vamos a crear un objeto visual de tabla.

4. En la vista de informe, crea un objeto visual de Tabla con las columnas **Oficina[Oficina]** y **Envíos[Precio total]**.

Como ves, la tabla lista seis oficinas, pero el valor del precio total es el mismo para todas así como para el total.

## Tarea 2. Crear y modificar relaciones

1. En la vista de modelo, identifica la tabla **Envíos**.
2. Arrastra el campo **OficinaID** de la tabla Envíos hacia el campo **OficinaID** de la tabla Oficina.
- Verifica:
  - Cardinalidad: Varios a uno (*:1)
  - Dirección del filtro cruzado: Único
3. En la cinta de opciones, selecciona **Administrar relaciones**.
4. Haz clic en **Nuevo**.
5. Configura una relación manualmente seleccionando:
  - Tabla origen: Envíos
  - Columna origen: [ClienteID]
  - Tabla destino: Cliente
  - Columna destino: [ClienteID]
6. Guarda la configuración.
7. Desde el panel de **Administrar relaciones**, selecciona **Detección automática**.

## Tarea 3. Diseñar el modelo (layout)

- Vuelve a la vista de modelo.
- Organiza las tablas manualmente en forma de **modelo en estrella**:
  - Envíos en el centro
  - Dimensiones alrededor (Oficina, Cliente, Servicio)

- Ajusta el tamaño de las tablas para mejorar la legibilidad.
- Alinea las tablas visualmente.
- Evita cruces de relaciones.

El objetivo es que el modelo sea claro y comprensible para cualquier usuario.

<img width="1119" height="768" alt="image" src="https://github.com/user-attachments/assets/08be986d-56d4-48e0-bdbe-8042cdeea24c" />

Ahora, vuelve a la vista de informe. El objeto visual de tabla ahora muestra los valores correctos para cada oficina.


## Tarea 4. Crear y usar jerarquías

1. Ve a la vista de modelo.
2. En la tabla **Oficina**, haz clic derecho sobre el campo **Oficina (Región)**.
3. Selecciona **Crear jerarquía**.
4. En el panel **Propiedades** (a la izquierda del de **Datos**; puede estar colapsado), renombra la jerarquía como `Geografía`.
5. En este mismo panel, agrega y ordena las columnas de la jerarquía:
  - Región 
  - Ciudad
6. Vuelve a la vista de informe.
7. Inserta un **gráfico de columnas**.
8. Añade la jerarquía `Geografía` al eje X.
9. Añade **Precio total** al eje Y.
10. En el gráfico, activa el **modo detallado** (flecha hacia abajo) y navega entre niveles de la jerarquía. Observa cómo puedes analizar los datos de forma progresiva.

---
# Resumen
