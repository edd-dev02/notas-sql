---
tags:
  - postgresql
  - sql/practica
  - migracion-de-datos
  - curso/seccion-6
aliases:
  - Práctica employees_s6
---

# Laboratorio `employees_s6`

La práctica normaliza departamentos y países, y convierte salarios textuales en números.

## Estado inicial

```sql
CREATE TABLE employees_s6 (
    id integer PRIMARY KEY,
    name varchar(60),
    department varchar(40),
    country varchar(40),
    salary varchar(20)
);
```

Problemas intencionales:

- `department` y `country` repiten texto.
- `salary` usa un tipo que no representa su naturaleza numérica.

## Analizar

```sql
SELECT department, COUNT(*) AS employees
FROM employees_s6
GROUP BY department
ORDER BY department;

SELECT country, COUNT(*) AS employees
FROM employees_s6
GROUP BY country
ORDER BY country;
```

## Normalizar department

```sql
CREATE TABLE departments_s6 (
    id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name varchar(40) NOT NULL UNIQUE
);

INSERT INTO departments_s6 (name)
SELECT DISTINCT department
FROM employees_s6
WHERE department IS NOT NULL;

ALTER TABLE employees_s6
ADD COLUMN department_id integer;

UPDATE employees_s6 AS e
SET department_id = d.id
FROM departments_s6 AS d
WHERE d.name = e.department;
```

Después de validar:

```sql
ALTER TABLE employees_s6
ADD CONSTRAINT employees_s6_department_id_fkey
FOREIGN KEY (department_id)
REFERENCES departments_s6(id);

ALTER TABLE employees_s6
ALTER COLUMN department_id SET NOT NULL;
```

## Normalizar country

Debe repetirse la estrategia con una columna nueva `country_id`; sobrescribir `country` destruye prematuramente el valor original.

```sql
CREATE TABLE countries_s6 (
    id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name varchar(40) NOT NULL UNIQUE
);

INSERT INTO countries_s6 (name)
SELECT DISTINCT country
FROM employees_s6
WHERE country IS NOT NULL;

ALTER TABLE employees_s6
ADD COLUMN country_id integer;

UPDATE employees_s6 AS e
SET country_id = c.id
FROM countries_s6 AS c
WHERE c.name = e.country;
```

## Convertir salary

Primero encuentra valores incompatibles:

```sql
SELECT id, salary
FROM employees_s6
WHERE salary IS NULL
   OR btrim(salary) !~ '^[+-]?[0-9]+$';
```

Si el resultado está limpio:

```sql
ALTER TABLE employees_s6
ALTER COLUMN salary TYPE integer
USING btrim(salary)::integer;
```

```sql
SELECT
    MIN(salary) AS minimum_salary,
    MAX(salary) AS maximum_salary,
    AVG(salary) AS average_salary
FROM employees_s6;
```

## Vista legible final

```sql
SELECT
    e.name AS employee,
    d.name AS department,
    c.name AS country,
    e.salary
FROM employees_s6 AS e
JOIN departments_s6 AS d
    ON d.id = e.department_id
JOIN countries_s6 AS c
    ON c.id = e.country_id
ORDER BY e.id;
```

## Retirar columnas antiguas

Tras reconciliar y migrar consumidores:

```sql
ALTER TABLE employees_s6
DROP COLUMN department,
DROP COLUMN country;
```

## Relacionado

- [[02 - Ciclo de una migración segura|Ciclo de migración]]
- [[15 - Retroalimentación del laboratorio|Retroalimentación]]

