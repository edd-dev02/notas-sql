---
tags:
  - postgresql
  - sql/joins
  - curso/seccion-7
aliases:
  - OUTER JOIN
  - LEFT JOIN
  - RIGHT JOIN
  - FULL OUTER JOIN
---

# LEFT, RIGHT y FULL OUTER JOIN

Los outer joins conservan filas aunque no exista una coincidencia.

## LEFT JOIN

Conserva todas las filas de la tabla izquierda:

```sql
SELECT
    e.name AS employee,
    p.name AS project
FROM employees_s6 AS e
LEFT JOIN employee_projects_s7 AS ep
    ON ep.employee_id = e.id
LEFT JOIN projects_s7 AS p
    ON p.id = ep.project_id;
```

Un empleado sin proyecto aparece con `project = NULL`.

## RIGHT JOIN

Conserva todas las filas de la tabla derecha:

```sql
SELECT
    ep.employee_id,
    p.name AS project
FROM employee_projects_s7 AS ep
RIGHT JOIN projects_s7 AS p
    ON p.id = ep.project_id;
```

Un proyecto sin asignaciones aparece con `employee_id = NULL`.

La misma intención puede expresarse normalmente con `LEFT JOIN` invirtiendo el orden; muchos equipos lo prefieren por legibilidad.

## FULL OUTER JOIN

Conserva filas sin coincidencia de ambos lados:

```sql
SELECT
    c.name AS country,
    ct.name AS continent
FROM country AS c
FULL OUTER JOIN continent AS ct
    ON ct.code = c.continent;
```

Incluye:

- países con continente;
- países sin continente relacionado;
- continentes sin países.

## Elegir el JOIN mínimo necesario

```text
Solo coincidencias                         → INNER JOIN
Todos los A, tengan B o no                → A LEFT JOIN B
Todos los B, tengan A o no                → A RIGHT JOIN B
Todo de ambos lados, coincida o no         → FULL OUTER JOIN
```

Usar `FULL OUTER JOIN` para “todos los empleados” es excesivo si también introduce proyectos sin empleados que no fueron solicitados.

## Contar hijos conservando padres vacíos

```sql
SELECT
    ct.name AS continent,
    COUNT(c.code) AS total_countries
FROM continent AS ct
LEFT JOIN country AS c
    ON c.continent = ct.code
GROUP BY ct.code, ct.name
ORDER BY total_countries;
```

Se cuenta la PK de la tabla hija. Para un continente sin países, `c.code` es `NULL` y el conteo devuelve `0`. `COUNT(*)` devolvería `1` porque la fila preservada del outer join sí existe.

## Relacionado

- [[06 - Anti-joins y registros sin relación|Anti-joins]]
- [[08 - COUNT DISTINCT en relaciones|Conteos]]

