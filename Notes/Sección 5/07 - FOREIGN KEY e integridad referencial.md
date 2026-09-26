---
tags:
  - postgresql
  - sql/constraints
  - sql/keys
  - curso/seccion-5
aliases:
  - Llaves foráneas
  - Integridad referencial
---

# FOREIGN KEY e integridad referencial

Una `FOREIGN KEY` exige que un valor de la tabla hija exista en una clave primaria o única de la tabla padre.

## País y ciudades

```sql
ALTER TABLE city
ADD CONSTRAINT city_countrycode_fkey
FOREIGN KEY (countrycode)
REFERENCES country(code);
```

```text
country.code PK  1 ─────── N  city.countrycode FK
```

## Comercio electrónico

```sql
ALTER TABLE orders_s5
ADD CONSTRAINT orders_s5_customer_id_fkey
FOREIGN KEY (customer_id)
REFERENCES customers_s5(id);

ALTER TABLE order_details_s5
ADD CONSTRAINT order_details_s5_order_id_fkey
FOREIGN KEY (order_id)
REFERENCES orders_s5(id);

ALTER TABLE order_details_s5
ADD CONSTRAINT order_details_s5_product_id_fkey
FOREIGN KEY (product_id)
REFERENCES products_s5(id);
```

## Qué protege

Este intento falla si el cliente `999` no existe:

```sql
INSERT INTO orders_s5 (id, customer_id, order_date, status)
VALUES (1005, 999, DATE '2026-09-14', 'Pending');
```

El error no es un obstáculo accidental: la base está evitando una orden huérfana.

## NULL y obligatoriedad

Una FK acepta `NULL` salvo que la columna también tenga `NOT NULL`.

```sql
customer_id integer NOT NULL
```

Por tanto, si “cada orden pertenece obligatoriamente a un cliente existente”, se necesitan ambas ideas:

```text
NOT NULL    → debe haber un valor
FOREIGN KEY → ese valor debe existir en customers
```

## Requisitos de la columna padre

La columna o combinación referenciada debe estar protegida por `PRIMARY KEY`, `UNIQUE` o un índice único compatible. Así PostgreSQL sabe que la referencia apunta como máximo a una fila.

## Datos preexistentes

Agregar una FK valida normalmente las filas actuales y falla si existen huérfanos:

```sql
SELECT ci.*
FROM city AS ci
LEFT JOIN country AS c
    ON c.code = ci.countrycode
WHERE c.code IS NULL;
```

Esta consulta permite diagnosticar las referencias faltantes antes de crear el constraint.

## Relacionado

- [[01 - Relaciones e integridad de datos|Relaciones e integridad]]
- [[08 - Acciones referenciales|Acciones referenciales]]
- [[09 - Cadenas de dependencias y CASCADE|Cadenas de dependencias]]

