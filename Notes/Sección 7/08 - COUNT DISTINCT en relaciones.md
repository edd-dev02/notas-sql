---
tags:
  - postgresql
  - sql/aggregation
  - sql/joins
  - curso/seccion-7
aliases:
  - COUNT DISTINCT
---

# COUNT DISTINCT en relaciones

Los JOINs pueden repetir una entidad en múltiples filas. `COUNT(DISTINCT ...)` cuenta entidades únicas, no ocurrencias.

## Tres conteos diferentes

```sql
SELECT
    d.id,
    d.name AS department,
    COUNT(*) AS total_assignments,
    COUNT(DISTINCT ep.employee_id) AS unique_employees,
    COUNT(DISTINCT ep.project_id) AS staffed_projects
FROM employee_projects_s7 AS ep
JOIN projects_s7 AS p
    ON p.id = ep.project_id
JOIN departments_s6 AS d
    ON d.id = p.department_id
GROUP BY d.id, d.name;
```

- `COUNT(*)`: filas de asignación.
- `COUNT(DISTINCT ep.employee_id)`: personas diferentes.
- `COUNT(DISTINCT ep.project_id)`: proyectos diferentes con asignaciones.

## Contar una columna identificadora

Prefiere:

```sql
COUNT(DISTINCT p.id)
```

frente a:

```sql
COUNT(DISTINCT p.*)
```

El ID expresa con precisión qué entidad se está contando y evita comparar filas compuestas completas.

## DISTINCT fuera de COUNT no cambia el agregado

```sql
SELECT DISTINCT
    COUNT(cl.language),
    ct.name
...
GROUP BY ct.name;
```

`COUNT` ya produjo una fila por continente; el `DISTINCT` exterior solo elimina filas agregadas idénticas completas. No hace que el conteo considere idiomas únicos.

La forma correcta es:

```sql
COUNT(DISTINCT cl.languagecode)
```

## COUNT(columna) y NULL

`COUNT(columna)` ignora `NULL`; `COUNT(*)` cuenta filas. Esta diferencia es crucial con outer joins.

## Relacionado

- [[07 - Granularidad y agregaciones con JOIN|Granularidad]]
- [[11 - Laboratorio world e idiomas oficiales|Idiomas oficiales]]

