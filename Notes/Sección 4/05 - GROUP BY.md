---
tags:
  - postgresql
  - sql/agrupaciones
  - curso/seccion-4
---

# GROUP BY

`GROUP BY` reúne filas que comparten uno o más valores para calcular estadísticas por conjunto.

## Contar usuarios por país

```sql
SELECT
    country,
    COUNT(*) AS total_users
FROM users
GROUP BY country
ORDER BY total_users DESC;
```

La salida contiene una fila por país, no una fila por usuario.

## Regla de agrupación

En una consulta agrupada, cada expresión seleccionada debe:

- aparecer en `GROUP BY`, o
- estar dentro de una función agregada.

Correcto:

```sql
SELECT country, COUNT(*)
FROM users
GROUP BY country;
```

Incorrecto porque `email` no identifica un único valor por país:

```sql
SELECT country, email, COUNT(*)
FROM users
GROUP BY country;
```

## Agrupar por varias columnas

```sql
SELECT
    country,
    category,
    SUM(amount) AS total_amount
FROM sales_practice
GROUP BY country, category
ORDER BY country, total_amount DESC;
```

Cada combinación distinta de país y categoría forma un grupo.

## Estadísticas por entidad

```sql
SELECT
    seller,
    SUM(amount) AS generated_amount,
    COUNT(*) AS sales_count,
    ROUND(AVG(amount)::numeric, 2) AS average_sale
FROM sales_practice
GROUP BY seller
ORDER BY generated_amount DESC;
```

Este patrón produce un reporte compacto por vendedor.

## `GROUP BY ALL`

En PostgreSQL, `ALL` es el comportamiento predeterminado de `GROUP BY` y rara vez se escribe. Solo adquiere relevancia avanzada con conjuntos de agrupación que podrían generar grupos duplicados; no significa “agrupar por todas las columnas”.

## Agrupar por expresión

También puede agruparse por el resultado de una función, como se explica en [[08 - Agrupar por partes de strings|agrupar por partes de strings]].

## Relacionado

- [[03 - Funciones agregadas y ROUND|Funciones agregadas]]
- [[06 - WHERE frente a HAVING|WHERE frente a HAVING]]

