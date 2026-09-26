---
tags:
  - postgresql
  - integridad-referencial
  - sql/constraints
  - curso/seccion-5
---

# Cadenas de dependencias y CASCADE

Una cascada debe evaluarse a través de todo el grafo de relaciones, no solamente entre dos tablas.

## Caso del laboratorio

```text
customers_s5
     │
     │ 1:N
     ▼
 orders_s5
     │
     │ 1:N
     ▼
order_details_s5 ───── N:1 ─────► products_s5
```

Al configurar únicamente:

```text
customers ── ON DELETE CASCADE ──► orders
```

el borrado de un cliente intenta borrar sus órdenes. Sin embargo, si `order_details` todavía referencia esas órdenes con la acción predeterminada, toda la operación falla.

## Primera cascada

```sql
ALTER TABLE orders_s5
DROP CONSTRAINT orders_s5_customer_id_fkey;

ALTER TABLE orders_s5
ADD CONSTRAINT orders_s5_customer_id_fkey
FOREIGN KEY (customer_id)
REFERENCES customers_s5(id)
ON DELETE CASCADE;
```

## Completar la cadena

```sql
ALTER TABLE order_details_s5
DROP CONSTRAINT order_details_s5_order_id_fkey;

ALTER TABLE order_details_s5
ADD CONSTRAINT order_details_s5_order_id_fkey
FOREIGN KEY (order_id)
REFERENCES orders_s5(id)
ON DELETE CASCADE;
```

Ahora:

```text
DELETE customer 1
       ↓
DELETE sus orders
       ↓
DELETE sus order_details
```

La FK hacia `products_s5` no necesita cascada para este flujo: borrar los detalles no borra los productos.

## La sentencia es atómica

Si una relación intermedia impide el borrado, PostgreSQL no deja la operación “a medias”. La sentencia completa falla y conserva la consistencia.

## Comprobar antes de borrar

```sql
SELECT *
FROM orders_s5
WHERE customer_id = 1;

SELECT od.*
FROM order_details_s5 AS od
JOIN orders_s5 AS o
    ON o.id = od.order_id
WHERE o.customer_id = 1;
```

## Practicar con una transacción

```sql
BEGIN;

DELETE FROM customers_s5
WHERE id = 1
RETURNING *;

-- Revisar las tablas relacionadas.
ROLLBACK;
```

`ROLLBACK` permite observar el comportamiento sin conservar el borrado.

> [!tip]
> Antes de aprobar una cascada, pregunta cuántas tablas puede recorrer, cuántas filas puede afectar y si esos datos deben conservarse por auditoría o historia.

## Relacionado

- [[08 - Acciones referenciales|Acciones referenciales]]
- [[12 - Laboratorio de comercio electrónico|Laboratorio de comercio electrónico]]

