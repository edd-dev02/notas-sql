---
tags:
  - postgresql
  - sql/select
  - curso/seccion-3
---

# SELECT, filtros y paginación

`SELECT` recupera datos. Puede elegir columnas, filtrar filas, ordenar resultados y limitar cuántos registros devuelve.

## Consultar todos los campos

```sql
SELECT *
FROM users;
```

Para consultas de aplicación suele ser preferible nombrar las columnas:

```sql
SELECT id, name
FROM users;
```

## Filtrar con `WHERE`

```sql
SELECT *
FROM users
WHERE name = 'Yareli';
```

El operador `=` exige coincidencia exacta. Para patrones se usa [[04 - LIKE y búsqueda por patrones|LIKE]].

## Paginar con `LIMIT` y `OFFSET`

```sql
SELECT *
FROM users
ORDER BY id
LIMIT 5
OFFSET 2;
```

- `OFFSET 2` omite las primeras dos filas del resultado ordenado.
- `LIMIT 5` devuelve como máximo las cinco siguientes.

> [!warning] Usa `ORDER BY`
> Sin un orden explícito, PostgreSQL no garantiza qué filas se consideran “las primeras”. Una paginación estable necesita `ORDER BY`, normalmente por una columna única como `id`.

## Orden lógico de lectura

```sql
SELECT columnas
FROM tabla
WHERE condición
ORDER BY columna
LIMIT cantidad
OFFSET cantidad;
```

## Ejemplo: segunda página

Para páginas de cinco elementos, la página 2 omite cinco filas:

```sql
SELECT id, name
FROM users
ORDER BY id
LIMIT 5 OFFSET 5;
```

La fórmula general es:

```text
OFFSET = (número_de_página - 1) × tamaño_de_página
```

## Relacionado

- [[04 - LIKE y búsqueda por patrones|LIKE y búsqueda por patrones]]
- [[06 - Funciones y operadores de texto|Funciones y operadores de texto]]

