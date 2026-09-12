---
tags:
  - postgresql
  - sql/dml
  - curso/seccion-3
---

# INSERT y carga de datos

`INSERT` agrega una o varias filas a una tabla.

## Insertar varios registros

```sql
INSERT INTO users (name)
VALUES
    ('Jorge'),
    ('Valeria'),
    ('Michelle');
```

La lista `(name)` indica las columnas que recibirán valores. Cada paréntesis de `VALUES` representa una fila.

## Confirmar lo insertado

PostgreSQL permite devolver las filas creadas sin lanzar otro `SELECT`:

```sql
INSERT INTO users (name)
VALUES ('Jorge'), ('Valeria'), ('Michelle')
RETURNING *;
```

## Relación con las restricciones

Si `name` tiene `UNIQUE`, intentar insertar un nombre existente produce un error. Si además tiene `NOT NULL`, tampoco acepta un valor nulo.

```sql
-- Falla si 'Jorge' ya existe
INSERT INTO users (name)
VALUES ('Jorge');
```

> [!tip]
> Indicar siempre las columnas hace que la sentencia sea más clara y resistente a cambios en la estructura de la tabla.

## Relacionado

- [[01 - Crear tablas y restricciones|Crear tablas y restricciones]]
- [[03 - SELECT filtros y paginación|SELECT, filtros y paginación]]

