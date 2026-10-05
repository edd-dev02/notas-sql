---
tags:
  - postgresql
  - sql/aggregation
  - sql/reporting
  - curso/seccion-7
aliases:
  - Top N SQL
---

# Rankings, SUM, MAX y LIMIT

Los rankings requieren definir la entidad, calcular su métrica, ordenar y limitar.

## Proyecto con más horas acumuladas

```sql
SELECT
    p.id,
    p.name AS project,
    SUM(ep.hours) AS total_hours
FROM projects_s7 AS p
JOIN employee_projects_s7 AS ep
    ON ep.project_id = p.id
GROUP BY p.id, p.name
ORDER BY total_hours DESC
LIMIT 1;
```

## SUM frente a MAX

Para `Data Warehouse`:

```text
Eduardo  → 120
Valeria  → 140

SUM → 260 horas del proyecto
MAX → 140 horas de la mayor asignación individual
```

La palabra “total” normalmente apunta a `SUM`, no a `MAX`.

## País con más ciudades

```sql
SELECT
    c.code,
    c.name AS country,
    COUNT(ci.id) AS total_cities
FROM country AS c
JOIN city AS ci
    ON ci.countrycode = c.code
GROUP BY c.code, c.name
ORDER BY total_cities DESC
LIMIT 1;
```

Agrupar también por la PK evita mezclar países homónimos.

## Departamento con más empleados diferentes

```sql
SELECT
    d.id,
    d.name AS department,
    COUNT(DISTINCT ep.employee_id) AS unique_employees
FROM employee_projects_s7 AS ep
JOIN projects_s7 AS p
    ON p.id = ep.project_id
JOIN departments_s6 AS d
    ON d.id = p.department_id
GROUP BY d.id, d.name
ORDER BY unique_employees DESC
LIMIT 1;
```

## Empates

`LIMIT 1` devuelve una sola fila incluso si varias entidades comparten el máximo. Para un resultado determinista añade un desempate:

```sql
ORDER BY total_hours DESC, p.id ASC
```

Si el requerimiento exige mostrar todos los empates, más adelante pueden utilizarse subqueries o window functions.

## Relacionado

- [[07 - Granularidad y agregaciones con JOIN|Granularidad]]
- [[08 - COUNT DISTINCT en relaciones|COUNT DISTINCT]]

