# Prueba Orbidi
# Parte 2: Consultas 
## 1. ¿Cuántos proyectos activos hay por producto?
```SQL
SELECT
    DP.product_id,
    DP.product_name,
    COUNT(DISTINCT task_id) as Proyectos_activos
FROM 
    fct_tasks FT
LEFT JOIN 
    dim_prodcuts DP ON FT.product_id = DP.product_id
WHERE 
    date_closed iS NULL
GROUP BY 
    1,2
ORDER BY 
    2;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/dbaf9d57-b431-4d52-9734-b0d2c8ce4ba1)

## 2. ¿Cuántos proyectos activos hay por Equipo?
```SQL
SELECT
    DE._equipo_id,
    DE.nombre_equipo,
    COUNT(DISTINCT task_id) as Proyectos_activos
FROM 
    fct_tasks FT
LEFT JOIN 
    dim_equipos DE ON FT._equipo_id = DE._equipo_id
WHERE 
    date_closed iS NULL
GROUP BY 
    1,2
ORDER BY 
    2;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/61f275db-2118-4702-b8fd-8ef20558ce6e)

## 3. La eficiencia corresponde a la proporción del tiempo utilizado / tiempo estimado, con base en esto, calculé la eficiencia, por tarea padre, con base en esta medida, responder las siguientes preguntas a nivel de producto y equipo:
### a. ¿Cual es la tarea padre con la eficiencia más alta?   
```SQL
SELECT
    parent,
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate))*100 as Eficiencia
FROM 
    fct_tasks FT
GROUP BY 
    1
HAVING 
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate)) > 0
ORDER BY 
    2 desc
LIMIT 1;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/fbdee49d-f63c-4bbb-927e-e75cadc4652b)

### b. ¿Cual es la tarea padre con la eficiencia más baja?
```SQL
SELECT
    parent,
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate))*100 as Eficiencia
FROM 
    fct_tasks FT
GROUP BY 
    1
HAVING 
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate)) > 0
ORDER BY 
    2 asc
LIMIT 1;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/ccf9e0bf-3e74-448b-8b65-c60c4b203f74)

### c. ¿Calcular la desviación estándar del total de tareas padre?

```SQL
SELECT 
    STDDEV(Eficiencia) Desviacion_Estandar
FROM(
    SELECT
        parent,
        SUM(time_spent_in_miliseconds)/SUM(time_estimate) as Eficiencia
    FROM 
        fct_tasks FT
    GROUP BY 
        1
    HAVING 
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate)) > 0
    ORDER BY 
        1
    )base;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/875d4450-66b0-4825-9937-449e4423acc6)

#### i. ¿Cómo se puede interpretar este resultado?
Si la desviación estándar es pequeha (como en este caso, 7.34), significa que los datos tienden a agruparse más cerca del valor promedio de 50.1, En otras palabras, la variabilidad entre los datos baja, y la mayoría de los valores se encuentran relativamente cerca d promedio, Esto sugiere que los datos son más consistentes y estables.

Si la desviación estándar es grande, los datos se dispersan más ampliamente alrededor del promedio de 50,1, La variabilidad entre lo datos es alta, y los valores individuales se encuentran más lejos del promedio.
### d. ¿Cual es el promedio de las sub-tareas ( tareas hijas ) por tareas padre?

```SQL
SELECT
    parent,
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate))*100 as Eficiencia
FROM 
    fct_tasks FT
GROUP BY 
    1
HAVING 
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate)) > 0
ORDER BY 
    2 asc;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/aed89351-1f68-4e64-b42c-cef8ba6e16ae)

### f. ¿Calcular el percentil 25 y el percentil 75 por tarea padre?
```SQL
SELECT 
    parent, 
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY time_spent_in_miliseconds / time_estimate) AS Percentil25
FROM 
    fct_tasks FT
WHERE 
    time_spent_in_miliseconds IS NOT NULL
    AND time_estimate IS NOT NULL
GROUP BY 
    1
ORDER BY 
    1 asc;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/24a3eebb-510c-4605-b0df-a34394f827d7)
```SQL
SELECT 
    parent, 
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY time_spent_in_miliseconds / time_estimate) AS Percentil75
FROM 
    fct_tasks FT
WHERE 
    time_spent_in_miliseconds IS NOT NULL
    AND time_estimate IS NOT NULL
GROUP BY 
    1
ORDER BY 
    1 asc;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/0886622a-5b44-48da-b013-9178520e7c5d)

## g. Calcular la eficiencia de todas las tareas, por usuario asignado.
```SQL
SELECT
    user_assignes_initials,
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate))*100 as Eficiencia
FROM 
    fct_tasks FT
GROUP BY 
    1
HAVING 
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate)) > 0
ORDER BY 
    2 desc;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/b978e605-1183-4c92-a909-d29719e64f86)

#### i. ¿Quién es el usuario con mayor eficiencia?
```SQL
SELECT
    user_assignes_initials,
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate))*100 as Eficiencia
FROM 
    fct_tasks FT
GROUP BY 
    1
HAVING 
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate)) > 0
ORDER BY 
    2 desc
LIMIT 1;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/25270620-1796-4786-b4eb-fd25733aad8a)

#### ii. ¿Cuál es el tiempo promedio en el qué un usuario completa una tarea?
```SQL
SELECT
    user_assignes_initials,
    SUM(time_spent_in_miliseconds)/count(task_id) as tiempo_promedio
FROM 
    fct_tasks FT
WHERE 
    date_done IS NOT NULL
GROUP BY 
    1
HAVING 
    (SUM(time_spent_in_miliseconds)/SUM(time_estimate)) > 0
ORDER BY 
    2 asc;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/ab6d004c-427c-41ca-b2cb-95c652ce3ea1)

## 4. ¿Qué porcentaje de tareas completadas existen? ( en general, por producto, por equipo)
```SQL
SELECT--Por producto
    DP.product_id,
    DP.product_name,
    COUNT(CASE WHEN date_done iS NOT NULL THEN task_id END)/COUNT(*) as tareas_completadas
FROM 
    fct_tasks FT
LEFT JOIN 
    dim_prodcuts DP ON FT.product_id = DP.product_id
GROUP BY 
    1,2
ORDER BY 
    2;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/e05b07e1-ccf0-43e6-a720-b93ecfc0e0e0)
```SQL
SELECT--Por Equipo
    DE._equipo_id,
    DE.nombre_equipo,
    COUNT(CASE WHEN date_done iS NOT NULL THEN task_id END)/COUNT(*) as tareas_completadas
FROM 
    fct_tasks FT
LEFT JOIN 
    dim_equipos DE ON FT._equipo_id = DE._equipo_id
GROUP BY 
    1,2
ORDER BY 
    2;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/36ecd19f-30ad-4726-ac82-0441252399da)

## 5. Calcular el porcentaje de tareas por cada estado, del total de tareas padre que existen, ¿que porcentaje hay en cada estado? ( en general, por producto, por equipo)
```SQL
SELECT--Por producto
    DP.product_id,
    DP.product_name,
    COUNT(CASE WHEN date_done iS NULL AND date_closed iS NOT NULL THEN task_id END)/COUNT(*) as tareas_cerradas_sin_completar,
    COUNT(CASE WHEN date_closed iS NOT NULL THEN task_id END)/COUNT(*) as tareas_cerradas,
    COUNT(CASE WHEN date_closed iS NULL THEN task_id END)/COUNT(*) as tareas_abiertas
FROM 
    fct_tasks FT
LEFT JOIN 
    dim_prodcuts DP ON FT.product_id = DP.product_id
GROUP BY 
    1,2
ORDER BY 
    2;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/c9ef53da-eacd-41cd-8b81-703b03011530)
```SQL
SELECT--Por Equipo
    DE._equipo_id,
    DE.nombre_equipo,
    COUNT(CASE WHEN date_done iS NULL AND date_closed iS NOT NULL THEN task_id END)/COUNT(*) as tareas_cerradas_sin_completar,
    COUNT(CASE WHEN date_closed iS NOT NULL THEN task_id END)/COUNT(*) as tareas_cerradas,
    COUNT(CASE WHEN date_closed iS NULL THEN task_id END)/COUNT(*) as tareas_abiertas
FROM 
    fct_tasks FT
LEFT JOIN 
    dim_equipos DE ON FT._equipo_id = DE._equipo_id
GROUP BY 
    1,2
ORDER BY 
    2;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/e41f1838-a743-4b6f-84f0-ad03f5a03544)

## 6. Calcular la cantidad de tareas padre ( proyectos ) por semana de creación.

```SQL
SELECT
    DATE_TRUNC('week',DATE_CREATED)::date as Week,
    COUNT(DISTINCT parent) as tareas_padre
FROM 
    fct_tasks FT
GROUP BY 
    1
ORDER BY 
    1 asc;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/2e5bfd54-dad3-4af4-ba40-f8b0d14d9a72)

## 7. Calcular la cantidad de tareas padre ( proyectos ) por mes de creación.

```SQL
SELECT
    DATE_TRUNC('month',DATE_CREATED)::date as Month,
    COUNT(DISTINCT parent) as tareas_padre
FROM 
    fct_tasks FT
GROUP BY 
    1
ORDER BY 
    1 asc;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/34e0c2f4-e803-49f6-8e5c-3f8d5907f965)

## 8. Calcular la cantidad de tareas padre ( proyectos ) por trimestre de creación
```SQL
SELECT
    DATE_TRUNC('quarter',DATE_CREATED)::date as Quarter,
    COUNT(DISTINCT parent) as tareas_padre
FROM 
    fct_tasks FT
GROUP BY 
    1
ORDER BY 
    1 asc;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/1220db3a-2118-4362-9321-2c4d6f32943a)

## 9. ¿Qué relaciones encuentras entre las variables?

Relación entre Producto y Proyectos:
Se puede observar cuántos proyectos activos existen para cada producto, lo que nos proporciona información sobre cómo se distribuyen los proyectos entre los diferentes productos.

Relación entre Equipo y Proyectos:
De manera similar, se puede analizar la cantidad de proyectos activos asignados a cada equipo, lo que permite entender la distribución del trabajo entre los equipos.

Relación entre Eficiencia y Tarea Padre:
Al calcular la eficiencia por tarea padre, podemos identificar qué tareas padres tienen un rendimiento más alto o más bajo en términos de cumplimiento de los tiempos estimados. Esta relación nos permite comprender qué tareas padre tienen un mejor desempeño en comparación con otras.

Relación entre Eficiencia y Usuario Asignado:
Calcular la eficiencia por usuario asignado nos proporciona información sobre cómo se desempeñan los distintos usuarios en términos de cumplimiento de tiempos estimados. Esto puede ayudar a identificar qué usuarios son más eficientes y cuáles pueden necesitar más apoyo o entrenamiento.

Relación entre Eficiencia y Desviación Estándar:
El cálculo de la desviación estándar de la eficiencia nos permite comprender la variabilidad de las eficiencias en función del tiempo estimado. Una desviación estándar alta indica que las eficiencias varían significativamente, mientras que una desviación baja indica que están más cercanas al promedio.

Relación entre Eficiencia y Percentiles:
Calcular los percentiles de la eficiencia nos proporciona información sobre cómo se distribuyen las eficiencias en diferentes niveles. Podemos identificar si existen tareas padre con eficiencias extremadamente altas o bajas, así como comprender el comportamiento de las eficiencias en diferentes cuartiles.

Relación entre Tiempo Promedio y Usuario Asignado:
Al calcular el tiempo promedio en el que un usuario completa una tarea, podemos identificar si hay diferencias significativas entre los usuarios en términos de tiempo de ejecución. Esto nos permite evaluar el rendimiento individual de cada usuario y su impacto en la eficiencia general del equipo o producto.

# Parte 3: Consulta en Snowflake
## La tabla type_config está compuesta por dos columnas, ID y TYPE_CONFIG. Esta última tiene una estructura de tipo JSON. Tu tarea es extraer todas las claves y valores de esta tabla, así como las filas incluidas, y crear una vista con los resultados obtenidos. El tipo de dato para la columna type_config que se debe importar a Snowflake puede ser JSON o VARIANT
```SQL
CREATE OR REPLACE VIEW Vista_Type_Config AS
SELECT 
  ID,
  TYPE_CONFIG:"new_drop_down"::BOOLEAN AS new_drop_down,
  TYPE_CONFIG:"options"[0]:color::VARCHAR AS color_1,
  TYPE_CONFIG:"options"[0]:id::VARCHAR AS id_1,
  TYPE_CONFIG:"options"[0]:name::VARCHAR AS name_1,
  TYPE_CONFIG:"options"[0]:orderindex::INT AS orderindex_1,
  TYPE_CONFIG:"options"[1]:color::VARCHAR AS color_2,
  TYPE_CONFIG:"options"[1]:id::VARCHAR AS id_2,
  TYPE_CONFIG:"options"[1]:name::VARCHAR AS name_2,
  TYPE_CONFIG:"options"[1]:orderindex::INT AS orderindex_2
FROM type_config;
```
![image](https://github.com/Julianav97/PruebaOrbidi/assets/140353018/537d0f8c-40a0-46b2-a11b-6666127a31fb)
