---
tags:
  - postgresql
  - sql/ddl
  - curso/seccion-3
---

# Crear tablas y restricciones

`CREATE TABLE` define la estructura de una tabla: sus columnas, tipos y reglas de integridad.

## Ejemplo mínimo del curso

```sql
CREATE TABLE users (
    name varchar(10) UNIQUE
);
```

Esto crea una columna `name` que acepta texto de hasta 10 caracteres y no permite valores duplicados no nulos.

> [!warning] `UNIQUE` no implica `NOT NULL`
> En PostgreSQL pueden existir varias filas con `NULL` bajo una restricción `UNIQUE`, porque `NULL` representa un valor desconocido. Agrega `NOT NULL` si el nombre debe ser obligatorio.

## Versión más completa

```sql
CREATE TABLE users (
    id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name varchar(50) UNIQUE NOT NULL
);
```

| Elemento | Función |
|---|---|
| `varchar(50)` | Texto con un máximo de 50 caracteres |
| `UNIQUE` | Evita valores repetidos |
| `NOT NULL` | Obliga a proporcionar un valor |
| `PRIMARY KEY` | Identifica cada fila de manera única |
| `GENERATED ... AS IDENTITY` | Genera automáticamente el identificador |

## `SERIAL` e identidad

En material introductorio es común ver:

```sql
id serial PRIMARY KEY
```

`SERIAL` es una abreviatura histórica de PostgreSQL que crea una secuencia y un valor por defecto. Para tablas nuevas, las columnas identidad expresan mejor la intención:

```sql
id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY
```

## Modificar una tabla existente

Antes de ejecutar el ejercicio de [[07 - Separar nombre y apellido|separación de nombres]], las columnas nuevas deben existir:

```sql
ALTER TABLE users
ADD COLUMN first_name varchar(50),
ADD COLUMN last_name varchar(50);
```

## Preguntas de control

- ¿Qué diferencia existe entre `UNIQUE` y `PRIMARY KEY`?
- ¿Por qué `varchar(10)` podría ser demasiado corto para nombres reales?
- ¿Qué regla agregarías si `name` nunca debe estar vacío o ser nulo?

## Relacionado

- [[02 - INSERT y carga de datos|INSERT y carga de datos]]
- [[08 - DELETE TRUNCATE y DROP|DELETE, TRUNCATE y DROP]]

