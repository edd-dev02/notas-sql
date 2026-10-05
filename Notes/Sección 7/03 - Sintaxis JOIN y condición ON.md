---
tags:
  - postgresql
  - sql/joins
  - curso/seccion-7
aliases:
  - JOIN ON
---

# Sintaxis JOIN y condición ON

Un `JOIN` combina columnas de filas relacionadas. La cláusula `ON` define cuándo dos filas coinciden.

## Sintaxis explícita

```sql
SELECT
    c.name AS country,
    ct.name AS continent
FROM country AS c
JOIN continent AS ct
    ON ct.code = c.continent;
```

La relación es visible y queda separada de los filtros del reporte.

## Sintaxis antigua con coma

```sql
SELECT c.name, ct.name
FROM country AS c, continent AS ct
WHERE c.continent = ct.code;
```

Produce un producto cartesiano y luego lo filtra. Puede funcionar como inner join, pero es fácil olvidar la condición y mezcla relaciones con filtros. Se prefiere la sintaxis explícita.

## ON frente a WHERE en outer joins

En un `LEFT JOIN`, mover una condición puede cambiar el resultado:

```sql
-- Conserva todos los empleados; solo relaciona proyectos de más de 50 horas
FROM employees_s6 AS e
LEFT JOIN employee_projects_s7 AS ep
    ON ep.employee_id = e.id
   AND ep.hours > 50
```

```sql
-- El WHERE elimina las filas sin asignación y se comporta como coincidencia obligatoria
FROM employees_s6 AS e
LEFT JOIN employee_projects_s7 AS ep
    ON ep.employee_id = e.id
WHERE ep.hours > 50
```

`ON` controla la coincidencia; `WHERE` filtra el resultado del JOIN.

## Alias claros

Prefiere alias relacionados con la tabla:

```sql
FROM employee_projects_s7 AS ep
JOIN employees_s6 AS e ON e.id = ep.employee_id
JOIN projects_s7 AS p ON p.id = ep.project_id
```

Ayudan a leer consultas largas mejor que `a`, `b`, `c`.

## Corrección de secuencia

La sintaxis correcta para reiniciar una secuencia es:

```sql
ALTER SEQUENCE continent_code_seq RESTART WITH 8;
```

No se utiliza `name` ni se escribe el nombre de la secuencia como literal de texto.

> [!warning]
> Reiniciar una secuencia puede provocar colisiones si ya existen filas con IDs iguales o superiores. Comprueba `MAX(id)` y el propósito del cambio.

## Relacionado

- [[04 - INNER JOIN|INNER JOIN]]
- [[05 - LEFT RIGHT y FULL OUTER JOIN|Outer joins]]

