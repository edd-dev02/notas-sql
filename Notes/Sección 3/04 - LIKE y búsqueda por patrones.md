---
tags:
  - postgresql
  - sql/select
  - curso/seccion-3
---

# LIKE y búsqueda por patrones

`LIKE` filtra texto según un patrón.

## Comodines

| Comodín | Significado |
|---|---|
| `%` | Cero o más caracteres |
| `_` | Exactamente un carácter |

## Ejemplos

```sql
-- Nombres que terminan en "o"
SELECT * FROM users WHERE name LIKE '%o';

-- Nombres que comienzan con "A"
SELECT * FROM users WHERE name LIKE 'A%';

-- Un carácter cualquiera seguido de "areli"
SELECT * FROM users WHERE name LIKE '_areli';

-- Nombres que contienen una "a"
SELECT * FROM users WHERE name LIKE '%a%';
```

> [!important] Mayúsculas y minúsculas
> En PostgreSQL, `LIKE` distingue mayúsculas de minúsculas. `LIKE '%a%'` no encuentra una `A`. Para una comparación sin esa distinción puede usarse `ILIKE`.

```sql
SELECT *
FROM users
WHERE name ILIKE '%a%';
```

## Coincidencia exacta frente a patrón

```sql
-- Exacta
WHERE name = 'Yareli'

-- Por patrón
WHERE name LIKE 'Y%'
```

## Rendimiento

Una búsqueda con comodín al principio, como `LIKE '%a%'`, normalmente no puede aprovechar un índice B-tree convencional y puede revisar muchas filas. En tablas pequeñas no suele importar; en tablas grandes conviene revisar el plan de ejecución y considerar índices especializados.

## Relacionado

- [[03 - SELECT filtros y paginación|SELECT, filtros y paginación]]
- [[06 - Funciones y operadores de texto|Funciones y operadores de texto]]

