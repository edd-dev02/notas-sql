---
tags:
  - postgresql
  - sql/dml
  - curso/seccion-3
---

# UPDATE y cambios seguros

`UPDATE` modifica las filas que cumplen una condición.

## Actualizar un nombre

```sql
UPDATE users
SET name = 'Eduardo A'
WHERE name = 'Eduardo';
```

- `SET` indica el nuevo valor.
- `WHERE` determina qué filas serán modificadas.

> [!danger] Un `UPDATE` sin `WHERE` afecta todas las filas
> Antes de actualizar, ejecuta un `SELECT` con la misma condición para comprobar el alcance.

```sql
SELECT *
FROM users
WHERE name = 'Eduardo';
```

## Ver el resultado inmediatamente

```sql
UPDATE users
SET name = 'Eduardo A'
WHERE name = 'Eduardo'
RETURNING *;
```

## Cambio seguro con transacción

```sql
BEGIN;

UPDATE users
SET name = 'Eduardo A'
WHERE name = 'Eduardo'
RETURNING *;

-- Si el resultado es correcto:
COMMIT;

-- Si no lo es, usa ROLLBACK en lugar de COMMIT.
```

## Actualizar varias columnas

```sql
UPDATE users
SET
    first_name = 'Eduardo',
    last_name = 'A'
WHERE id = 1;
```

Cuando exista una clave primaria, filtrar por ella suele expresar mejor que se desea modificar una fila concreta.

## Relacionado

- [[07 - Separar nombre y apellido|Separar nombre y apellido]]
- [[08 - DELETE TRUNCATE y DROP|DELETE, TRUNCATE y DROP]]

