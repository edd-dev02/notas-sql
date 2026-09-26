---
tags:
  - postgresql
  - sql/agregaciones
  - curso/seccion-4
aliases:
  - Funciones agregadas
---

# Funciones agregadas y ROUND

Las funciones agregadas reciben un conjunto de filas y producen un solo valor por conjunto o por grupo.

## Funciones principales

```sql
SELECT
    COUNT(*) AS total_sales,
    MIN(amount) AS minimum_sale,
    MAX(amount) AS maximum_sale,
    AVG(amount) AS average_sale,
    SUM(amount) AS total_amount
FROM sales_practice;
```

| Función | Pregunta que responde |
|---|---|
| `COUNT` | ¿Cuántos registros o valores hay? |
| `MIN` | ¿Cuál es el valor mínimo? |
| `MAX` | ¿Cuál es el valor máximo? |
| `AVG` | ¿Cuál es el promedio? |
| `SUM` | ¿Cuál es el total acumulado? |

## `COUNT(*)` frente a `COUNT(columna)`

- `COUNT(*)` cuenta todas las filas.
- `COUNT(columna)` cuenta solo las filas donde esa columna no es `NULL`.
- `COUNT(DISTINCT columna)` cuenta valores distintos no nulos.

```sql
SELECT
    COUNT(*) AS rows_total,
    COUNT(email) AS rows_with_email,
    COUNT(DISTINCT country) AS unique_countries
FROM users;
```

## Tratamiento de `NULL`

`MIN`, `MAX`, `AVG` y `SUM` ignoran valores `NULL`. Si no hay ninguna fila válida, normalmente devuelven `NULL`; `COUNT` devuelve `0`.

## Redondear con `ROUND`

```sql
SELECT ROUND(AVG(followers)) AS average_followers
FROM users;
```

Con dos decimales:

```sql
SELECT ROUND(AVG(amount)::numeric, 2) AS average_sale
FROM sales_practice;
```

`ROUND` cambia la presentación numérica del resultado, no las filas originales.

## Promedio manual: por qué evitarlo

```sql
SUM(followers) / COUNT(followers)
```

Aunque expresa la fórmula, `AVG(followers)` es más claro, maneja `NULL` correctamente y evita sorpresas de división entre enteros según los tipos involucrados.

## Una fila frente a varias filas

Sin `GROUP BY`, la consulta produce una fila resumen:

```sql
SELECT COUNT(*) AS total_users
FROM users;
```

Con `GROUP BY`, produce una fila por grupo:

```sql
SELECT country, COUNT(*) AS total_users
FROM users
GROUP BY country;
```

## Relacionado

- [[05 - GROUP BY|GROUP BY]]
- [[12 - Errores comunes y correcciones|Errores comunes y correcciones]]

