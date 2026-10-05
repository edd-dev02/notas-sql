---
tags:
  - postgresql
  - sql/joins
  - curso/seccion-7
aliases:
  - LEFT JOIN IS NULL
  - Anti-join
---

# Anti-joins y registros sin relación

Un anti-join encuentra filas de una tabla para las que no existe una relación en otra.

## Empleados sin proyectos

```sql
SELECT e.id, e.name
FROM employees_s6 AS e
LEFT JOIN employee_projects_s7 AS ep
    ON ep.employee_id = e.id
WHERE ep.employee_id IS NULL;
```

## Proyectos sin empleados

```sql
SELECT p.id, p.name, p.budget
FROM projects_s7 AS p
LEFT JOIN employee_projects_s7 AS ep
    ON ep.project_id = p.id
WHERE ep.project_id IS NULL;
```

Se comprueba una columna no nula de la tabla relacionada, normalmente su PK o FK.

## Combinar con otros requisitos

```sql
SELECT p.id, p.name, p.budget
FROM projects_s7 AS p
LEFT JOIN employee_projects_s7 AS ep
    ON ep.project_id = p.id
WHERE ep.project_id IS NULL
  AND p.budget > 20000;
```

La condición de presupuesto no debe olvidarse por concentrarse en el JOIN.

## NOT EXISTS

La misma idea puede expresarse explícitamente:

```sql
SELECT p.id, p.name, p.budget
FROM projects_s7 AS p
WHERE p.budget > 20000
  AND NOT EXISTS (
      SELECT 1
      FROM employee_projects_s7 AS ep
      WHERE ep.project_id = p.id
  );
```

Ambos patrones son comunes. El planificador puede producir estrategias similares; la elección debe priorizar claridad y comprobarse con el plan cuando el rendimiento importe.

## Regla mental

```text
“Todos los A aunque no tengan B” → LEFT JOIN
“A que no tengan B”              → LEFT JOIN + B IS NULL
```

## Relacionado

- [[05 - LEFT RIGHT y FULL OUTER JOIN|Outer joins]]
- [[14 - Errores comunes y correcciones|Errores comunes]]

