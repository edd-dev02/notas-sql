---
tags:
  - postgresql
  - sql/problem-solving
  - sql/joins
  - curso/seccion-7
aliases:
  - Resolver consultas complejas
---

# Construir consultas complejas paso a paso

Una consulta de cinco tablas se vuelve manejable si se construye y verifica de forma incremental.

## Requerimiento

Obtener:

```text
employee | country | department | project | hours
```

## 1. Elegir la tabla de hechos

`employee_projects_s7` contiene la asignación y la medida `hours`.

```sql
SELECT ep.*
FROM employee_projects_s7 AS ep;
```

## 2. Agregar el empleado

```sql
SELECT ep.*, e.name AS employee
FROM employee_projects_s7 AS ep
JOIN employees_s6 AS e
    ON e.id = ep.employee_id;
```

Comprueba conteos y una muestra antes de continuar.

## 3. Agregar país y departamento

```sql
SELECT
    ep.hours,
    e.name AS employee,
    c.name AS country,
    d.name AS department
FROM employee_projects_s7 AS ep
JOIN employees_s6 AS e
    ON e.id = ep.employee_id
JOIN countries_s6 AS c
    ON c.id = e.country_id
JOIN departments_s6 AS d
    ON d.id = e.department_id;
```

Si el esquema todavía usa `employees_s6.country` como FK, ajusta el `ON` a esa columna.

## 4. Agregar proyecto

```sql
SELECT
    e.name AS employee,
    c.name AS country,
    d.name AS department,
    p.name AS project,
    ep.hours
FROM employee_projects_s7 AS ep
JOIN employees_s6 AS e ON e.id = ep.employee_id
JOIN countries_s6 AS c ON c.id = e.country_id
JOIN departments_s6 AS d ON d.id = e.department_id
JOIN projects_s7 AS p ON p.id = ep.project_id;
```

## 5. Filtrar al final de la construcción

```sql
WHERE c.name = 'Mexico'
  AND d.name = 'Technology'
  AND ep.hours > 50;
```

## Controles después de cada JOIN

- ¿Cuántas filas había y cuántas hay ahora?
- ¿El JOIN debía eliminar filas?
- ¿Se multiplicaron inesperadamente?
- ¿Hay valores `NULL`?
- ¿La relación utiliza PK y FK correctas?
- ¿Qué representa ahora una fila?

## Filosofía

```text
Relacionar dos tablas
        ↓
Comprobar
        ↓
Agregar otra tabla
        ↓
Comprobar
        ↓
Definir agrupación y medida
        ↓
Filtrar y ordenar
```

## Relacionado

- [[03 - Sintaxis JOIN y condición ON|JOIN y ON]]
- [[07 - Granularidad y agregaciones con JOIN|Granularidad]]

