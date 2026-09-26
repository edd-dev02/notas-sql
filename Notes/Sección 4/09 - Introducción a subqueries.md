---
tags:
  - postgresql
  - sql/subqueries
  - curso/seccion-4
aliases:
  - Subconsultas
---

# Introducción a subqueries

Una subquery o subconsulta es una consulta anidada dentro de otra. Su resultado temporal puede funcionar como una tabla, un valor o un conjunto de valores.

## Subconsulta en `FROM`

```sql
SELECT
    email_domain,
    total_users
FROM (
    SELECT
        SUBSTRING(email FROM POSITION('@' IN email) + 1) AS email_domain,
        COUNT(*) AS total_users
    FROM users
    WHERE email IS NOT NULL
      AND POSITION('@' IN email) > 0
    GROUP BY SUBSTRING(email FROM POSITION('@' IN email) + 1)
    HAVING COUNT(*) > 1
) AS domain_summary
ORDER BY total_users DESC;
```

La consulta interna construye un resumen. La externa selecciona, filtra u ordena ese resultado.

> [!important] Alias obligatorio
> En PostgreSQL moderno es buena práctica dar un alias claro a la subconsulta de `FROM`, como `domain_summary`; además facilita referirse a sus columnas.

## Cuándo resulta útil

- Reutilizar una columna calculada en un nivel exterior.
- Filtrar sobre resultados intermedios.
- Dividir una consulta compleja en etapas conceptuales.
- Comparar una fila contra un agregado global.

## Subconsulta escalar

```sql
SELECT seller, amount
FROM sales_practice
WHERE amount > (
    SELECT AVG(amount)
    FROM sales_practice
)
ORDER BY amount DESC;
```

La subconsulta devuelve un solo valor: el promedio global. La consulta externa muestra ventas superiores a ese promedio.

## Subconsulta con `IN`

```sql
SELECT first_name, last_name, country
FROM users
WHERE country IN (
    SELECT country
    FROM users
    GROUP BY country
    HAVING COUNT(*) > 5
);
```

La subconsulta devuelve un conjunto de países y la consulta exterior recupera sus usuarios.

## No siempre es necesaria

Si una sola consulta con `GROUP BY`, `HAVING` y `ORDER BY` expresa claramente el objetivo, agregar un nivel extra solo aumenta la complejidad. Las subqueries son una herramienta, no un requisito.

## Relacionado

- [[02 - Anatomía y orden de ejecución de SELECT|Anatomía de SELECT]]
- [[10 - Patrones para reportes|Patrones para reportes]]

