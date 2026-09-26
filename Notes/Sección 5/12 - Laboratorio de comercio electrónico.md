---
tags:
  - postgresql
  - sql/practica
  - modelado-de-datos
  - curso/seccion-5
aliases:
  - Práctica sección 5 ecommerce
---

# Laboratorio de comercio electrónico

## Modelo

```text
customers_s5 1 ─── N orders_s5 1 ─── N order_details_s5 N ─── 1 products_s5
```

## Reglas por tabla

### customers_s5

```sql
ALTER TABLE customers_s5
ADD CONSTRAINT customers_s5_pkey PRIMARY KEY (id),
ADD CONSTRAINT customers_s5_email_key UNIQUE (email);
```

Si cada cliente debe tener correo, agrega también `NOT NULL` a la columna.

### products_s5

```sql
ALTER TABLE products_s5
ADD CONSTRAINT products_s5_pkey PRIMARY KEY (id),
ADD CONSTRAINT products_s5_values_check
    CHECK (price >= 0 AND stock >= 0);
```

### orders_s5

```sql
ALTER TABLE orders_s5
ADD CONSTRAINT orders_s5_pkey PRIMARY KEY (id),
ADD CONSTRAINT orders_s5_status_check
    CHECK (status IN ('Pending', 'Completed', 'Cancelled')),
ADD CONSTRAINT orders_s5_customer_id_fkey
    FOREIGN KEY (customer_id)
    REFERENCES customers_s5(id)
    ON DELETE CASCADE;
```

Para que cada pedido tenga cliente obligatoriamente, `customer_id` también debe ser `NOT NULL`.

### order_details_s5

```sql
ALTER TABLE order_details_s5
ADD CONSTRAINT order_details_s5_pkey
    PRIMARY KEY (order_id, product_id),
ADD CONSTRAINT order_details_s5_quantity_check
    CHECK (quantity > 0),
ADD CONSTRAINT order_details_s5_order_id_fkey
    FOREIGN KEY (order_id)
    REFERENCES orders_s5(id)
    ON DELETE CASCADE,
ADD CONSTRAINT order_details_s5_product_id_fkey
    FOREIGN KEY (product_id)
    REFERENCES products_s5(id);
```

`quantity > 0` fue una de las reglas que no apareció en los constraints recuperados después del fallo del gestor; conviene verificarla.

## Índices de consulta

```sql
CREATE INDEX products_s5_category_idx
ON products_s5 (category);

CREATE INDEX orders_s5_customer_id_idx
ON orders_s5 (customer_id);
```

El segundo también ayuda a la relación padre-hijo porque PostgreSQL no crea automáticamente un índice en la columna FK.

## Probar integridad referencial

```sql
INSERT INTO orders_s5 (id, customer_id, order_date, status)
VALUES (1005, 999, DATE '2026-09-14', 'Pending');
```

Debe fallar si el cliente `999` no existe. El constraint está cumpliendo su propósito.

## Probar la cascada sin conservar el borrado

```sql
BEGIN;

DELETE FROM customers_s5
WHERE id = 1
RETURNING *;

SELECT * FROM orders_s5 WHERE customer_id = 1;

ROLLBACK;
```

## Qué protege cada regla

| Requisito | Solución |
|---|---|
| Producto con identidad | `PRIMARY KEY (id)` |
| Precio y stock válidos | `CHECK` + posiblemente `NOT NULL` |
| Pedido con cliente existente | `FOREIGN KEY` |
| Pedido con cliente obligatorio | `NOT NULL` + `FOREIGN KEY` |
| Detalle con orden y producto existentes | Dos `FOREIGN KEY` |
| No repetir producto dentro de una orden | PK compuesta |
| Email no repetido | `UNIQUE` |
| Buscar por categoría | Índice no único |

## Relacionado

- [[09 - Cadenas de dependencias y CASCADE|Cadenas de dependencias]]
- [[14 - Errores comunes y correcciones|Errores comunes]]

