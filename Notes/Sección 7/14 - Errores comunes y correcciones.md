---
tags:
  - postgresql
  - sql/debugging
  - sql/joins
  - curso/seccion-7
aliases:
  - Errores frecuentes de JOIN
---

# Errores comunes y correcciones

## 1. Usar FULL OUTER JOIN cuando solo debe preservarse un lado

“Todos los empleados, incluso sin proyecto” requiere:

```sql
employees LEFT JOIN assignments LEFT JOIN projects
```

`FULL OUTER JOIN` también puede introducir proyectos sin empleados, ampliando el resultado más allá de lo solicitado.

## 2. Agrupar por proyecto cuando se pide departamento

```sql
GROUP BY project_id, project_name
```

produce una fila por proyecto. Para una fila por departamento, se debe llegar hasta `departments` y agrupar únicamente por su identidad y nombre.

## 3. Contar proyectos en lugar de asignaciones

```sql
COUNT(DISTINCT project_id)
```

responde “proyectos diferentes”. Para `total_assignments`, cuenta filas de la tabla puente:

```sql
COUNT(*)
```

## 4. Usar DISTINCT fuera del agregado

```sql
SELECT DISTINCT COUNT(language), continent
```

no cuenta idiomas distintos. La forma correcta es:

```sql
COUNT(DISTINCT languagecode)
```

## 5. SUM frente a MAX

```sql
MAX(hours)
```

devuelve la mayor asignación individual. “Horas totales del proyecto” requiere:

```sql
SUM(hours)
```

## 6. Añadir una dimensión innecesaria al GROUP BY

Agrupar por proyecto y departamento produce una fila por combinación. Si el reporte pide horas por departamento, el proyecto no pertenece a la granularidad final.

## 7. Olvidar HAVING

Contar proyectos por empleado no completa “empleados con más de un proyecto”:

```sql
HAVING COUNT(DISTINCT project_id) > 1
```

## 8. Resolver el JOIN y olvidar otra condición

“Proyectos sin empleados con presupuesto superior a 20000” exige tanto el anti-join como:

```sql
AND p.budget > 20000
```

## 9. Filtrar un UNION ilustrativo

Si el requerimiento pide todos los empleados y proyectos, no deben agregarse filtros solo porque el ejemplo muestre algunos nombres.

## 10. COUNT(DISTINCT fila completa)

`COUNT(DISTINCT p.*)` puede funcionar, pero `COUNT(DISTINCT p.id)` comunica mejor que se cuentan proyectos.

## 11. Indexar una columna en la tabla equivocada

`hours` vive en `employee_projects_s7`, no en `projects_s7`. Verifica el modelo antes de proponer índices.

## 12. Crear índices sin medir

Más criterios en la consulta no implican automáticamente más índices. Hay que revisar el plan, la carga de escrituras y el beneficio real.

## 13. Utilizar IDs mágicos

```sql
WHERE continent = 5
```

depende de un ID generado. Para expresar Europa en un reporte, une el catálogo y filtra:

```sql
WHERE continent.name = 'Europe'
```

## Lista de comprobación

- ¿Qué representa una fila final?
- ¿Qué entidad debe conservarse sin coincidencia?
- ¿Cuento filas o entidades distintas?
- ¿Necesito `SUM`, `MAX`, `AVG` o `COUNT`?
- ¿El filtro actúa antes o después de agrupar?
- ¿Cada condición del requerimiento está presente?
- ¿El `GROUP BY` contiene solo la granularidad necesaria?

## Relacionado

- [[07 - Granularidad y agregaciones con JOIN|Granularidad]]
- [[15 - Retroalimentación del laboratorio|Retroalimentación]]

