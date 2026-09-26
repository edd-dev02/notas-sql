---
tags:
  - postgresql
  - sql/metadata
  - sql/constraints
  - curso/seccion-5
aliases:
  - Helper pg_constraint
---

# Inspeccionar y modificar constraints

PostgreSQL guarda metadatos del esquema en sus catálogos del sistema. Esta consulta permite revisar constraints sin memorizar sus nombres.

## Helper con pg_constraint

```sql
SELECT
    t.relname AS table_name,
    c.conname AS constraint_name,
    c.contype AS constraint_type,
    pg_get_constraintdef(c.oid) AS definition
FROM pg_constraint AS c
JOIN pg_class AS t
    ON c.conrelid = t.oid
JOIN pg_namespace AS n
    ON n.oid = t.relnamespace
WHERE n.nspname = 'public'
  AND t.relname IN (
      'country',
      'city',
      'countrylanguage'
  )
ORDER BY t.relname, c.conname;
```

El filtro por esquema evita mezclar tablas del mismo nombre ubicadas en esquemas diferentes.

## Códigos frecuentes de contype

| Código | Constraint |
|---|---|
| `p` | Primary key |
| `f` | Foreign key |
| `u` | Unique |
| `c` | Check |
| `x` | Exclusion |

## Ver constraints de la práctica

```sql
SELECT
    t.relname AS table_name,
    c.conname AS constraint_name,
    c.contype AS constraint_type,
    pg_get_constraintdef(c.oid) AS definition
FROM pg_constraint AS c
JOIN pg_class AS t ON t.oid = c.conrelid
WHERE t.relname IN (
    'customers_s5',
    'products_s5',
    'orders_s5',
    'order_details_s5'
)
ORDER BY t.relname, c.conname;
```

## Eliminar un constraint

```sql
ALTER TABLE country
DROP CONSTRAINT country_continent_check;
```

## Reemplazar una FOREIGN KEY

PostgreSQL no modifica directamente todas las propiedades de una FK existente; normalmente se elimina y se crea nuevamente:

```sql
BEGIN;

ALTER TABLE orders_s5
DROP CONSTRAINT orders_s5_customer_id_fkey;

ALTER TABLE orders_s5
ADD CONSTRAINT orders_s5_customer_id_fkey
FOREIGN KEY (customer_id)
REFERENCES customers_s5(id)
ON DELETE CASCADE;

COMMIT;
```

La transacción evita dejar la tabla sin protección si la segunda operación falla.

## Nombres útiles

Una convención consistente acelera el diagnóstico:

```text
<tabla>_pkey
<tabla>_<columna>_key
<tabla>_<columna>_fkey
<tabla>_<regla>_check
<tabla>_<columnas>_idx
```

> [!note]
> No es necesario memorizar los catálogos. Lo importante es saber que los constraints son objetos inspeccionables y conservar el helper para cuando haga falta.

## Relacionado

- [[04 - CHECK y validación de dominio|CHECK]]
- [[07 - FOREIGN KEY e integridad referencial|FOREIGN KEY]]

