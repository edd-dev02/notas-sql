---
tags:
  - postgresql
  - sql
  - curso/seccion-3
aliases:
  - Sección 3
  - Fundamentos de SQL
---

# Sección 3 — Fundamentos de SQL

## Objetivo

Aprender a crear una tabla sencilla y ejecutar las operaciones esenciales sobre sus registros: insertar, consultar, actualizar y eliminar. Además, transformar texto con funciones de PostgreSQL.

## Mapa de la sección

1. [[01 - Crear tablas y restricciones|Crear tablas y restricciones]]
2. [[02 - INSERT y carga de datos|Insertar registros]]
3. [[03 - SELECT filtros y paginación|Consultar, filtrar y paginar]]
4. [[04 - LIKE y búsqueda por patrones|Buscar por patrones con LIKE]]
5. [[05 - UPDATE y cambios seguros|Actualizar registros de forma segura]]
6. [[06 - Funciones y operadores de texto|Transformar y concatenar texto]]
7. [[07 - Separar nombre y apellido|Separar nombre y apellido]]
8. [[08 - DELETE TRUNCATE y DROP|Distinguir DELETE, TRUNCATE y DROP]]
9. [[09 - Práctica y chuleta rápida|Repasar y practicar]]

## Modelo mental

| Grupo | Propósito | Sentencias vistas |
|---|---|---|
| DDL | Define o elimina estructuras | `CREATE TABLE`, `ALTER TABLE`, `TRUNCATE`, `DROP TABLE` |
| DML | Inserta, modifica o elimina filas | `INSERT`, `UPDATE`, `DELETE` |
| Consulta | Recupera y transforma datos | `SELECT`, `WHERE`, `LIKE`, `LIMIT`, `OFFSET` |

> [!note] Sobre `TRUNCATE`
> Aunque suele agruparse con DDL por su efecto sobre la tabla, PostgreSQL permite usarlo dentro de una transacción y revertirlo antes del `COMMIT`.

## Flujo recomendado al practicar

```sql
-- 1. Crear la estructura
CREATE TABLE users (
    id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name varchar(50) UNIQUE NOT NULL
);

-- 2. Insertar datos
INSERT INTO users (name)
VALUES ('Jorge'), ('Valeria'), ('Michelle');

-- 3. Consultar
SELECT *
FROM users
ORDER BY id;

-- 4. Modificar de forma dirigida
UPDATE users
SET name = 'Eduardo A'
WHERE name = 'Eduardo';
```

## Conceptos que conviene recordar

- Las palabras clave SQL no distinguen mayúsculas, pero escribirlas en mayúsculas mejora la lectura.
- Un `WHERE` ausente en `UPDATE` o `DELETE` afecta todas las filas.
- Sin `ORDER BY`, la base de datos no garantiza el orden del resultado.
- Las restricciones evitan que entren datos inválidos; no son solo documentación.

