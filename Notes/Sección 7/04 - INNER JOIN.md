---
tags:
  - postgresql
  - sql/joins
  - curso/seccion-7
aliases:
  - INNER JOIN
---

# INNER JOIN

`INNER JOIN` devuelve únicamente combinaciones que cumplen la condición de relación en ambos lados.

## País y continente

```sql
SELECT
    c.name AS country,
    ct.name AS continent
FROM country AS c
INNER JOIN continent AS ct
    ON ct.code = c.continent;
```

Un continente sin países no aparece, y un país sin continente válido tampoco.

## Empleado, proyecto y horas

```sql
SELECT
    e.name AS employee,
    p.name AS project,
    ep.hours
FROM employee_projects_s7 AS ep
INNER JOIN employees_s6 AS e
    ON e.id = ep.employee_id
INNER JOIN projects_s7 AS p
    ON p.id = ep.project_id;
```

La tabla puente conecta las dos entidades.

## Filtrar por atributos legibles

```sql
SELECT p.name AS project
FROM projects_s7 AS p
JOIN departments_s6 AS d
    ON d.id = p.department_id
WHERE d.name = 'Technology';
```

Filtrar por nombre evita depender de un ID generado cuyo valor puede variar entre ambientes.

## JOIN no valida la relación

El JOIN consulta coincidencias. La `FOREIGN KEY` impide referencias inválidas al escribir. Se complementan, pero no son equivalentes.

## INNER JOIN y multiplicación de filas

Si un empleado tiene dos proyectos, aparecerá dos veces. Esto no es un duplicado accidental: refleja dos filas de la relación. Antes de agregar, identifica la granularidad resultante.

## Relacionado

- [[01 - Relaciones muchos a muchos y tablas puente|Tablas puente]]
- [[07 - Granularidad y agregaciones con JOIN|Granularidad]]

