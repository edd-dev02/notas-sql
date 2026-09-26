---
tags:
  - postgresql
  - sql/filtros
  - sql/agrupaciones
  - curso/seccion-4
aliases:
  - WHERE vs HAVING
---

# WHERE frente a HAVING

Ambas cláusulas filtran, pero actúan en momentos diferentes.

| Cláusula | Filtra | Momento | Ejemplo |
|---|---|---|---|
| `WHERE` | Filas individuales | Antes de agrupar | `amount > 2000` |
| `HAVING` | Grupos ya calculados | Después de agrupar | `COUNT(*) > 2` |

## Solo `WHERE`

Obtener ventas individuales mayores a 5000:

```sql
SELECT *
FROM sales_practice
WHERE amount > 5000;
```

## Solo `HAVING`

Mostrar países con más de tres ventas:

```sql
SELECT country, COUNT(*) AS total_sales
FROM sales_practice
GROUP BY country
HAVING COUNT(*) > 3;
```

## Usar ambas

Mostrar vendedores que hicieron más de dos ventas mayores a 2000:

```sql
SELECT
    seller,
    COUNT(*) AS qualifying_sales,
    SUM(amount) AS qualifying_amount
FROM sales_practice
WHERE amount > 2000
GROUP BY seller
HAVING COUNT(*) > 2
ORDER BY qualifying_amount DESC;
```

La secuencia conceptual es:

1. `WHERE` descarta ventas de 2000 o menos.
2. `GROUP BY` reúne las restantes por vendedor.
3. `HAVING` conserva los vendedores con más de dos ventas calificadas.

> [!tip] Prueba verbal
> “Ventas mayores a 2000” describe filas: `WHERE`. “Vendedores con más de dos ventas” describe grupos: `HAVING`.

## Lo que no debe hacerse

```sql
-- No se puede usar una función agregada de esta forma
WHERE COUNT(*) > 3
```

En esa etapa todavía no existen los grupos ni su conteo.

## Relacionado

- [[02 - Anatomía y orden de ejecución de SELECT|Orden de ejecución de SELECT]]
- [[05 - GROUP BY|GROUP BY]]

