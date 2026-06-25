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
2. Arrastra el campo **OficinaID** de la tabla Envíos hacia el campo **OficinaID** de la tabla Oficina. Verifica:
    - **Cardinalidad**: Varios a uno (*:1)
    - **Dirección del filtro cruzado**: Único
3. En la cinta de opciones, selecciona **Administrar relaciones**.
4. Haz clic en **Nuevo**.
5. Configura una relación manualmente seleccionando:
    - **Tabla origen**: Envíos
    - **Columna origen**: [ClienteID]
    - **Tabla destino**: Cliente
    - **Columna destino**: [ClienteID]
6. Guarda la configuración.
7. Desde el panel de **Administrar relaciones**, selecciona **Detección automática**.

## Tarea 3. Diseñar el modelo (layout)

1. Vuelve a la vista de modelo.
2. Organiza las tablas manualmente en forma de **modelo en estrella**:
    - Envíos en el centro
    - Dimensiones alrededor (Oficina, Cliente, Servicio)

3. Ajusta el tamaño de las tablas para mejorar la legibilidad.
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
    - Oficina
6. Vuelve a la vista de informe.
7. Inserta un **gráfico de columnas**.
8. Añade la jerarquía `Geografía` al eje X.
9. Añade **Precio total** al eje Y.
10. En el gráfico, activa la **exploración en profundidad**/**modo detallado** (flecha hacia abajo) y navega entre niveles de la jerarquía. Observa cómo puedes analizar los datos de forma progresiva.

## Tarea 5. Configurar propiedades de tablas y columnas
En esta tarea vas a mejorar la usabilidad del modelo configurando propiedades clave y ocultando campos técnicos.

> [!IMPORTANT]
> Asegúrate de que estás en la vista de modelo.
> Además, puedes seleccionar varios campos a la vez para aplicar cambios en lote.


### Tabla Cliente

1. Oculta las siguientes columnas técnicas:
    - Cliente[ClienteID]

### Tabla Envíos

1. Oculta las siguientes columnas técnicas:
    - Envíos[OficinaID]  
    - Envíos[ClienteID]  
    - Envíos[ServicioID]  
    - Envíos[RutaID]
    - Envíos[ÚltimaExtracción]

2. Configura la propiedad **Código Postal (Destino)**:
     - **Categoría de datos**: Código postal
     - **Resumir por**: Ninguno

4. Configura las propiedades **Precio total** y **Precio base** de la siguiente manera:
      - **Carpeta para mostrar**: Precios
      - **Formato**: Moneda
      - **Símbolo**: €
      - **Número de decimales**: 2
      - **Separador de miles**: Activado

#### Tabla Oficina

1. Configura las siguientes propiedades:
      - **Ciudad**
        - Categoría de datos: Ciudad
      - **Región**
        - Categoría de datos: País o región

2. Oculta las siguientes columnas técnicas:
    - Oficina[OficinaID]

#### Tabla Servicio

1. Oculta las siguientes columnas técnicas:
    - Servicio[ServicioID]

---
# Resumen
En este laboratorio hemos trabajado en la construcción y mejora del modelo semántico de ParcelCraft dentro de Power BI. Hemos comenzado verificando la ausencia de relaciones automáticas y analizando su impacto en los objetos visuales. Posteriormente, hemos aprendido a crear relaciones manualmente entre tablas utilizando distintas técnicas, así como a validar su configuración y efecto en el análisis.

Además, hemos diseñado el modelo siguiendo un esquema en estrella, organizando adecuadamente las tablas en la vista de modelo para mejorar su claridad y mantenibilidad. También hemos creado jerarquías que permiten explorar los datos a diferentes niveles de granularidad dentro de los informes.

Finalmente, hemos configurado propiedades clave de tablas y columnas, incluyendo formato, categorías de datos y ocultación de campos técnicos, para mejorar la experiencia de uso y facilitar la interpretación de los datos por parte de los usuarios de negocio.

### Recursos útiles
- Documentación de modelado en Power BI: https://learn.microsoft.com/power-bi/transform-model/
- Mejores prácticas de esquema en estrella: https://learn.microsoft.com/power-bi/guidance/star-schema
- Relaciones en Power BI: https://learn.microsoft.com/power-bi/transform-model/desktop-relationships-understand
- Jerarquías en Power BI: https://learn.microsoft.com/power-bi/create-reports/desktop-hierarchies

### 🎉 Fin del laboratorio
¡Enhorabuena! Has completado este laboratorio y ya tienes una base sólida en modelado de datos en Power BI. Este paso es fundamental para garantizar análisis fiables, modelos eficientes y una mejor experiencia de usuario en tus informes. ¡Nos vemos en el siguiente lab! 👏
