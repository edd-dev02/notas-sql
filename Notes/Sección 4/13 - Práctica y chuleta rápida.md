---
tags:
  - postgresql
  - sql/practica
  - curso/seccion-4
---

# Práctica y chuleta rápida

## Chuleta

```sql
SELECT
    grouping_column,
    COUNT(*) AS row_count,
    MIN(numeric_column) AS minimum_value,
    MAX(numeric_column) AS maximum_value,
    SUM(numeric_column) AS total_value,
    ROUND(AVG(numeric_column)::numeric, 2) AS average_value
FROM table_name
WHERE row_condition
GROUP BY grouping_column
HAVING aggregate_condition
ORDER BY total_value DESC
LIMIT 10 OFFSET 0;
```

## Decisión rápida

| Necesidad | Herramienta |
|---|---|
| Filtrar una venta individual | `WHERE` |
| Comprobar un rango inclusivo | `BETWEEN` |
| Comparar con una lista | `IN` |
| Eliminar duplicados del resultado | `DISTINCT` |
| Calcular una métrica global | Función agregada sin `GROUP BY` |
| Calcular una métrica por categoría | Función agregada con `GROUP BY` |
| Filtrar por conteo, suma o promedio | `HAVING` |
| Ordenar un ranking | `ORDER BY ... DESC` |
| Trabajar en etapas | Subquery |

## Retos de práctica

- [ ] Contar cuántos países diferentes existen en `users`.
- [ ] Mostrar el mínimo, máximo y promedio de seguidores en una sola fila.
- [ ] Mostrar países con al menos cinco usuarios, ordenados por cantidad.
- [ ] Encontrar vendedores cuya venta promedio supere el promedio global.
- [ ] Contar ventas por país únicamente para montos entre 2000 y 8000.
- [ ] Mostrar dominios de correo que aparecen más de dos veces.
- [ ] Crear un ranking de vendedores por monto total y usar el número de ventas para desempatar.
- [ ] Explicar por qué `WHERE COUNT(*) > 2` no es válido.

## Soluciones propuestas

```sql
-- Países diferentes
SELECT COUNT(DISTINCT country) AS country_count
FROM users;

-- Estadísticas de seguidores
SELECT
    MIN(followers) AS minimum_followers,
    MAX(followers) AS maximum_followers,
    ROUND(AVG(followers), 2) AS average_followers
FROM users;

-- Países con al menos cinco usuarios
SELECT country, COUNT(*) AS total_users
FROM users
GROUP BY country
HAVING COUNT(*) >= 5
ORDER BY total_users DESC, country;

-- Vendedores por encima del promedio global
SELECT seller, ROUND(AVG(amount)::numeric, 2) AS average_sale
FROM sales_practice
GROUP BY seller
HAVING AVG(amount) > (SELECT AVG(amount) FROM sales_practice)
ORDER BY average_sale DESC;

-- Ventas dentro del rango, agrupadas por país
SELECT country, COUNT(*) AS sales_count
FROM sales_practice
WHERE amount BETWEEN 2000 AND 8000
GROUP BY country
ORDER BY sales_count DESC, country;

-- Dominios repetidos
SELECT
    SPLIT_PART(customer_email, '@', 2) AS domain,
    COUNT(*) AS occurrences
FROM sales_practice
WHERE customer_email LIKE '%@%'
GROUP BY SPLIT_PART(customer_email, '@', 2)
HAVING COUNT(*) > 2
ORDER BY occurrences DESC, domain;

-- Ranking por vendedor
SELECT
    seller,
    SUM(amount) AS total_amount,
    COUNT(*) AS sales_count
FROM sales_practice
GROUP BY seller
ORDER BY total_amount DESC, sales_count DESC, seller;
```

## Preguntas de repaso

1. ¿Qué diferencia existe entre `COUNT(*)` y `COUNT(email)`?
2. ¿Cuándo produce una función agregada más de una fila?
3. ¿Qué cláusula se ejecuta conceptualmente primero: `WHERE` o `GROUP BY`?
4. ¿Por qué `BETWEEN 10 AND 20` también acepta 10 y 20?
5. ¿Qué diferencia hay entre `DISTINCT country` y `GROUP BY country` con `COUNT(*)`?

## Respuestas breves

1. El primero cuenta filas; el segundo ignora los correos `NULL`.
2. Cuando se definen grupos, normalmente mediante `GROUP BY`.
3. `WHERE`, porque decide qué filas participan en la agrupación.
4. Porque `BETWEEN` es inclusivo en ambos extremos.
5. El primero lista países únicos; el segundo permite calcular métricas por país.

## Navegación

- [[00 - Sección 4 - Agregaciones agrupaciones y reportes|Volver al mapa de la sección]]
- [[SQL de cero - Tu guía práctica con PostgreSQL|Volver al índice general]]

