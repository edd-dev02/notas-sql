---
tags:
  - postgresql
  - sql/practica
  - sql/reportes
  - curso/seccion-4
aliases:
  - Práctica sales_practice
---

# Laboratorio `sales_practice`

Esta práctica avanza desde filtros sobre filas hasta reportes agrupados con varias métricas.

## Nivel 1 — Filtrar y ordenar filas

```sql
-- Ventas mayores a 5000
SELECT *
FROM sales_practice
WHERE amount > 5000;

-- Ventas dentro de un rango inclusivo
SELECT *
FROM sales_practice
WHERE amount BETWEEN 2000 AND 8000;

-- Países pertenecientes a una lista
SELECT *
FROM sales_practice
WHERE country IN ('Mexico', 'Argentina');

-- Tecnología con monto superior a 3000
SELECT *
FROM sales_practice
WHERE category = 'Technology'
  AND amount > 3000
ORDER BY amount DESC;
```

En este nivel cada fila del resultado sigue representando una venta.

## Nivel 2 — Resumir toda la tabla

```sql
SELECT
    COUNT(*) AS total_sales,
    MIN(amount) AS minimum_sale,
    MAX(amount) AS maximum_sale,
    ROUND(AVG(amount)::numeric, 2) AS average_sale,
    SUM(amount) AS total_amount,
    SUM(units) AS total_units
FROM sales_practice;
```

Sin `GROUP BY`, todas las filas forman un solo conjunto y el resultado es una fila.

Un filtro puede aplicarse antes de agregar:

```sql
SELECT COUNT(*) AS furniture_sales
FROM sales_practice
WHERE category = 'Furniture';
```

## Nivel 3 — Resumir por grupos

```sql
-- Número de ventas por país
SELECT country, COUNT(*) AS sales_count
FROM sales_practice
GROUP BY country
ORDER BY sales_count DESC;

-- Monto total por país
SELECT country, SUM(amount) AS total_amount
FROM sales_practice
GROUP BY country
ORDER BY total_amount DESC;

-- Venta promedio por categoría
SELECT
    category,
    ROUND(AVG(amount)::numeric, 2) AS average_sale
FROM sales_practice
GROUP BY category
ORDER BY average_sale DESC;

-- Unidades vendidas por vendedor
SELECT seller, SUM(units) AS total_units
FROM sales_practice
GROUP BY seller
ORDER BY total_units DESC;
```

## Nivel 4 — Extremos y rankings por grupo

```sql
-- Venta individual más alta de cada categoría
SELECT category, MAX(amount) AS maximum_sale
FROM sales_practice
GROUP BY category
ORDER BY maximum_sale DESC;

-- Promedio por país
SELECT
    country,
    ROUND(AVG(amount)::numeric, 2) AS average_sale
FROM sales_practice
GROUP BY country
ORDER BY average_sale DESC;
```

`MAX(amount)` representa la venta individual más alta, no el total vendido. Para el total se usa `SUM(amount)`.

## Nivel 5 — Filtrar grupos

```sql
-- Países con más de tres ventas
SELECT country, COUNT(*) AS sales_count
FROM sales_practice
GROUP BY country
HAVING COUNT(*) > 3;

-- Países con más de 10000 de monto acumulado
SELECT country, SUM(amount) AS total_amount
FROM sales_practice
GROUP BY country
HAVING SUM(amount) > 10000
ORDER BY total_amount DESC;

-- Categorías cuyo promedio supera 4000
SELECT
    category,
    ROUND(AVG(amount)::numeric, 2) AS average_sale
FROM sales_practice
GROUP BY category
HAVING AVG(amount) > 4000
ORDER BY average_sale DESC;
```

## Nivel 6 — Valores únicos

```sql
SELECT DISTINCT country
FROM sales_practice
ORDER BY country;

SELECT DISTINCT seller
FROM sales_practice
ORDER BY seller;

SELECT DISTINCT
    SUBSTRING(customer_email FROM POSITION('@' IN customer_email) + 1) AS domain
FROM sales_practice
ORDER BY domain;
```

## Nivel 7 — Agrupar por dominio

```sql
SELECT
    SUBSTRING(customer_email FROM POSITION('@' IN customer_email) + 1) AS email_domain,
    COUNT(*) AS customer_count
FROM sales_practice
WHERE customer_email IS NOT NULL
  AND POSITION('@' IN customer_email) > 0
GROUP BY SUBSTRING(customer_email FROM POSITION('@' IN customer_email) + 1)
HAVING COUNT(*) > 2
ORDER BY customer_count DESC, email_domain;
```

## Nivel 8 — Combinar condiciones

```sql
-- Tecnología de Mexico o Colombia
SELECT country, product, category
FROM sales_practice
WHERE country IN ('Mexico', 'Colombia')
  AND category = 'Technology';

-- Vendedores seleccionados con más de 2000 acumulados
SELECT
    seller,
    SUM(amount) AS total_amount
FROM sales_practice
WHERE seller IN ('Eduardo', 'Andrea', 'Valeria')
GROUP BY seller
HAVING SUM(amount) > 2000
ORDER BY total_amount DESC;
```

## Reto — Reporte de rendimiento por vendedor

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

## Reto — Países con monto relevante

```sql
SELECT
    country,
    SUM(amount) AS total_amount,
    COUNT(*) AS sales_count
FROM sales_practice
GROUP BY country
HAVING SUM(amount) > 10000
ORDER BY total_amount DESC;
```

## Lectura conceptual del laboratorio

- `WHERE amount > 5000`: selecciona ventas individuales.
- `GROUP BY country`: forma un conjunto por país.
- `SUM(amount)`: calcula una métrica para cada grupo.
- `HAVING SUM(amount) > 10000`: conserva grupos según esa métrica.
- `ORDER BY total_amount DESC`: construye un ranking.

## Relacionado

- [[03 - Funciones agregadas y ROUND|Funciones agregadas]]
- [[05 - GROUP BY|GROUP BY]]
- [[06 - WHERE frente a HAVING|WHERE frente a HAVING]]

