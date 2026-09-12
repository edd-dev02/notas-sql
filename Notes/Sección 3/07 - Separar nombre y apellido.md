---
tags:
  - postgresql
  - sql/funciones
  - sql/dml
  - curso/seccion-3
---

# Separar nombre y apellido

El ejercicio parte de una columna `name` que contiene nombre y apellido separados por un espacio.

## Explorar la transformación

Conviene probar primero la lógica con `SELECT`:

```sql
SELECT
    name,
    POSITION(' ' IN name) AS space_position,
    SUBSTRING(name FROM 1 FOR POSITION(' ' IN name) - 1) AS first_name,
    SUBSTRING(name FROM POSITION(' ' IN name) + 1) AS last_name
FROM users;
```

Para `Jorge López`:

- `POSITION` encuentra el espacio.
- El primer `SUBSTRING` toma lo anterior al espacio.
- El segundo toma lo que aparece después.

## Preparar las columnas

```sql
ALTER TABLE users
ADD COLUMN first_name varchar(50),
ADD COLUMN last_name varchar(50);
```

## Guardar los valores derivados

```sql
UPDATE users
SET
    first_name = SUBSTRING(name FROM 1 FOR POSITION(' ' IN name) - 1),
    last_name = SUBSTRING(name FROM POSITION(' ' IN name) + 1)
WHERE POSITION(' ' IN name) > 0
RETURNING id, name, first_name, last_name;
```

El `WHERE` evita aplicar esta versión de la lógica a valores sin espacios.

## Alternativa idiomática de PostgreSQL

Para este caso sencillo, `split_part` es más fácil de leer:

```sql
SELECT
    name,
    SPLIT_PART(name, ' ', 1) AS first_name,
    SPLIT_PART(name, ' ', 2) AS last_name
FROM users;
```

## Limitaciones del ejercicio

Esta solución supone exactamente un nombre y un apellido. Casos como `María José López`, apellidos compuestos, espacios dobles o un solo nombre necesitan reglas adicionales. Por eso, en un sistema real conviene definir desde el inicio si el nombre completo se guardará junto o por componentes.

## Relacionado

- [[05 - UPDATE y cambios seguros|UPDATE y cambios seguros]]
- [[06 - Funciones y operadores de texto|Funciones y operadores de texto]]

