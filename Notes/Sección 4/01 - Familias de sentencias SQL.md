---
tags:
  - postgresql
  - sql/conceptos
  - curso/seccion-4
aliases:
  - DDL DML DQL TCL
---

# Familias de sentencias SQL

Las siglas DDL, DML, DQL y TCL clasifican sentencias según su propósito. Son categorías didácticas: la documentación o las herramientas pueden clasificarlas con pequeñas diferencias.

| Familia | Nombre | Propósito | Ejemplos |
|---|---|---|---|
| DDL | Data Definition Language | Definir estructuras | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` |
| DML | Data Manipulation Language | Insertar, modificar y eliminar datos | `INSERT`, `UPDATE`, `DELETE` |
| DQL | Data Query Language | Consultar datos | `SELECT` |
| TCL | Transaction Control Language | Controlar transacciones | `BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

## DDL: definición de datos

```sql
CREATE TABLE users (
    id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    country varchar(50)
);

ALTER TABLE users ADD COLUMN followers integer;
DROP TABLE users;
```

DDL modifica el esquema: tablas, columnas, restricciones y otros objetos.

## DML: manipulación de datos

```sql
INSERT INTO users (country, followers)
VALUES ('Mexico', 100);

UPDATE users
SET followers = 120
WHERE id = 1;

DELETE FROM users
WHERE id = 1;
```

## DQL: consulta de datos

```sql
SELECT country, COUNT(*)
FROM users
GROUP BY country;
```

Algunas clasificaciones consideran `SELECT` parte de DML; separar DQL resulta útil porque consultar no cambia las filas.

## TCL: control de transacciones

```sql
BEGIN;

UPDATE users
SET followers = followers + 1
WHERE id = 1;

ROLLBACK; -- descarta el cambio
-- COMMIT; -- lo confirma
```

Una transacción agrupa operaciones como una unidad: se confirman juntas o se revierten.

## Relacionado

- [[Notes/Sección 3/00 - Sección 3 - Fundamentos de SQL|Fundamentos de SQL]]
- [[02 - Anatomía y orden de ejecución de SELECT|Anatomía de SELECT]]
