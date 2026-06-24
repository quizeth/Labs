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

- Abre tu archivo de Power BI del laboratorio anterior.
- Ve a la **vista de modelo**.
- Observa que todas las tablas aparecen **sin relaciones**.

Este es el comportamiento esperado, ya que la detección automática está deshabilitada.


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
8. Elimina la relación Destino[Ciudad (Destino)] → Oficina[Ciudad].
9. Selecciona la relación. Destino[Ciudad (Destino)] → Envíos[(Código Postal (Destino))] y haz clic en **Editar**.
10. Cambia la cardinalidad a **Uno a varios(1:*)** y la dirección del filtro **único**.

## Tarea 3. Diseñar el modelo (layout)

- Vuelve a la vista de modelo.
- Organiza las tablas manualmente en forma de **modelo en estrella**:
  - Envíos en el centro
  - Dimensiones alrededor (Oficina, Cliente, Servicio, Destino)

- Ajusta el tamaño de las tablas para mejorar la legibilidad.
- Alinea las tablas visualmente.
- Evita cruces de relaciones.

El objetivo es que el modelo sea claro y comprensible para cualquier usuario.

<img width="991" height="725" alt="image" src="https://github.com/user-attachments/assets/c4cab811-3b54-417e-a181-a3ed4e03be3a" />

### Tarea 6. Comprobar el impacto de una relación en un visual

- Ve a la **vista de informe**.
- Crea una tabla visual.
- Añade:
  - Oficina[Oficina]
  - Envíos[Precio total]

Observa que los datos se agregan correctamente.

Ahora:
- Vuelve a la vista de modelo.
- Elimina la relación entre Envíos y Oficina.

Regresa al informe.

Observa:
- Los datos dejan de comportarse correctamente (repeticiones o agregaciones incorrectas).

Esto demuestra la importancia de las relaciones.

---

### Tarea 7. Crear jerarquías

- Ve a la vista de modelo.
- En la tabla **Destino**, haz clic derecho sobre el campo **Región (Destino)**.
- Selecciona **Crear jerarquía**.

- Añade los siguientes campos a la jerarquía:
  - Región (Destino)
  - Ciudad (Destino)
  - Código Postal (Destino)

Renombra la jerarquía como:

**Jerarquía Geográfica Destino**

---

### Tarea 8. Usar jerarquías en un visual

- Ve a la vista de informe.
- Inserta un **gráfico de columnas**.
- Añade la jerarquía creada al eje.
- Añade **Precio total** como valor.

- Activa la opción de **drill down**.
- Navega entre niveles de la jerarquía.

Observa cómo puedes analizar los datos de forma progresiva.

---

### Tarea 9. Validación final del modelo

Comprueba que:

- Todas las tablas están relacionadas correctamente.
- El modelo sigue un diseño en estrella.
- No existen relaciones redundantes.
- Los objetos visuales responden correctamente a los filtros.

---

Este laboratorio refuerza la importancia del modelado en Power BI. Un modelo bien diseñado es clave para obtener análisis correctos, eficientes y mantenibles.
