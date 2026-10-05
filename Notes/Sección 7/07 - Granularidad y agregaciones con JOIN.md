---
tags:
  - postgresql
  - sql/joins
  - sql/aggregation
  - curso/seccion-7
aliases:
  - Granularidad SQL
---

# Granularidad y agregaciones con JOIN

La granularidad responde: **¿qué representa una fila del resultado?**

## Una fila por proyecto

```sql
SELECT
    p.id,
    p.name AS project,
    SUM(ep.hours) AS total_hours
FROM projects_s7 AS p
JOIN employee_projects_s7 AS ep
    ON ep.project_id = p.id
GROUP BY p.id, p.name;
```

## Una fila por departamento

```sql
SELECT
    d.id,
    d.name AS department,
    SUM(ep.hours) AS total_hours
FROM employee_projects_s7 AS ep
JOIN projects_s7 AS p
    ON p.id = ep.project_id
JOIN departments_s6 AS d
    ON d.id = p.department_id
GROUP BY d.id, d.name;
```

Si también se agrega `p.name` al `GROUP BY`, la granularidad cambia a una fila por proyecto y departamento.

## Elegir la medida

| Pregunta | Agregación |
|---|---|
| ¿Cuántas asignaciones hay? | `COUNT(*)` |
| ¿Cuántos empleados diferentes? | `COUNT(DISTINCT employee_id)` |
| ¿Cuántas horas totales? | `SUM(hours)` |
| ¿Cuál fue la mayor asignación individual? | `MAX(hours)` |
| ¿Cuál es el promedio por proyecto? | `AVG(hours)` |

## WHERE frente a HAVING

```sql
-- Filas individuales mayores a 100 horas
WHERE ep.hours > 100

-- Empleados con más de 100 horas acumuladas
GROUP BY e.id, e.name
HAVING SUM(ep.hours) > 100
```

## Proceso mental

```text
¿Qué quiero medir?
→ SUM(hours)

¿Una fila por qué?
→ departamento

¿Qué camino conecta la medida con el grupo?
→ employee_projects → projects → departments
```

## Evitar doble conteo

Un proyecto con tres empleados produce tres filas al unirse con la tabla puente. Sumar `p.budget` en ese nivel triplica el presupuesto. Para presupuestos por departamento, agrega directamente `projects_s7` por departamento o deduplica en una etapa claramente definida.

## Relacionado

- [[08 - COUNT DISTINCT en relaciones|COUNT DISTINCT]]
- [[10 - Rankings SUM MAX y LIMIT|Rankings]]

