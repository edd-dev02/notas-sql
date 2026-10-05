---
tags:
  - postgresql
  - sql/practica
  - sql/joins
  - curso/seccion-7
aliases:
  - Práctica projects_s7
---

# Laboratorio `projects_s7`

## Modelo utilizado

```text
countries_s6      departments_s6
      ▲                  ▲
      │                  │
      └──── employees_s6 │
                 ▲       │
                 │       └──── projects_s7
                 │                  ▲
                 └── employee_projects_s7
```

La tabla puente relaciona empleados con proyectos y registra horas.

## Todos los empleados, tengan proyecto o no

```sql
SELECT
    e.name AS employee,
    p.name AS project
FROM employees_s6 AS e
LEFT JOIN employee_projects_s7 AS ep
    ON ep.employee_id = e.id
LEFT JOIN projects_s7 AS p
    ON p.id = ep.project_id
ORDER BY e.name, p.name;
```

No se necesita `FULL OUTER JOIN`, porque el requerimiento solo exige preservar empleados.

## Horas acumuladas por empleado

```sql
SELECT
    e.id,
    e.name AS employee,
    SUM(ep.hours) AS total_hours
FROM employees_s6 AS e
JOIN employee_projects_s7 AS ep
    ON ep.employee_id = e.id
GROUP BY e.id, e.name
HAVING SUM(ep.hours) > 100
ORDER BY total_hours DESC;
```

## Presupuesto por departamento

```sql
SELECT
    d.id,
    d.name AS department,
    SUM(p.budget) AS total_budget,
    AVG(p.budget) AS average_budget
FROM departments_s6 AS d
JOIN projects_s7 AS p
    ON p.department_id = d.id
GROUP BY d.id, d.name
ORDER BY total_budget DESC;
```

No se une la tabla de asignaciones porque multiplicaría cada presupuesto por la cantidad de empleados.

## Métricas de actividad por departamento

```sql
SELECT
    d.id,
    d.name AS department,
    COUNT(*) AS total_assignments,
    COUNT(DISTINCT ep.employee_id) AS unique_employees,
    COUNT(DISTINCT ep.project_id) AS staffed_projects,
    SUM(ep.hours) AS total_hours
FROM employee_projects_s7 AS ep
JOIN projects_s7 AS p ON p.id = ep.project_id
JOIN departments_s6 AS d ON d.id = p.department_id
GROUP BY d.id, d.name
ORDER BY total_hours DESC;
```

## Empleados con más de un proyecto

```sql
SELECT
    e.id,
    e.name AS employee,
    COUNT(DISTINCT ep.project_id) AS total_projects
FROM employees_s6 AS e
JOIN employee_projects_s7 AS ep
    ON ep.employee_id = e.id
GROUP BY e.id, e.name
HAVING COUNT(DISTINCT ep.project_id) > 1
ORDER BY total_projects DESC, e.name;
```

## Todos los nombres en un solo listado

```sql
SELECT name, 'Employee' AS entity_type
FROM employees_s6
UNION ALL
SELECT name, 'Project' AS entity_type
FROM projects_s7
ORDER BY entity_type, name;
```

Como la columna `entity_type` distingue las fuentes, las filas no serán idénticas entre tipos; `UNION ALL` evita deduplicación innecesaria.

## Relacionado

- [[01 - Relaciones muchos a muchos y tablas puente|Tabla puente]]
- [[07 - Granularidad y agregaciones con JOIN|Granularidad]]

