---
tags:
  - postgresql
  - sql/constraints
  - sql/keys
  - curso/seccion-5
---

# PRIMARY KEY y claves compuestas

Una `PRIMARY KEY` identifica inequívocamente cada fila. En PostgreSQL implica `UNIQUE`, `NOT NULL` y crea automáticamente un índice único.

## Agregar una PK simple

```sql
ALTER TABLE country
ADD CONSTRAINT country_pkey
PRIMARY KEY (code);
```

```sql
ALTER TABLE city
ADD CONSTRAINT city_pkey
PRIMARY KEY (id);
```

Nombrar el constraint explícitamente facilita identificarlo y modificarlo después.

## PK compuesta

```sql
ALTER TABLE countrylanguage
ADD CONSTRAINT countrylanguage_pkey
PRIMARY KEY (countrycode, language);
```

Esto permite:

```text
MEX | Spanish
MEX | English
USA | English
```

Pero impide repetir exactamente:

```text
MEX | Spanish
MEX | Spanish  ← duplicado
```

## Tabla de detalle

```sql
ALTER TABLE order_details_s5
ADD CONSTRAINT order_details_s5_pkey
PRIMARY KEY (order_id, product_id);
```

La regla significa “un producto aparece como máximo una vez en una orden”. Si el modelo necesita varias líneas del mismo producto con propiedades diferentes, quizá convenga un `id` propio o incluir otra columna en la clave.

> [!warning] La PK compuesta no garantiza existencia
> `PRIMARY KEY (order_id, product_id)` evita repetir la combinación, pero no comprueba que la orden y el producto existan. Para ello hacen falta dos [[07 - FOREIGN KEY e integridad referencial|FOREIGN KEY]].

## Agregar una PK a datos existentes

La operación falla si ya existen valores duplicados o nulos. Conviene diagnosticar antes:

```sql
SELECT code, COUNT(*)
FROM country
GROUP BY code
HAVING COUNT(*) > 1;

SELECT COUNT(*)
FROM country
WHERE code IS NULL;
```

## PK frente a UNIQUE

- `products.id`: identidad de la entidad → `PRIMARY KEY`.
- `customers.email`: dato alternativo que no debe repetirse → `UNIQUE`.

Una tabla puede tener muchas restricciones `UNIQUE`, pero una sola PK.

## Relacionado

- [[02 - Tipos de llaves|Tipos de llaves]]
- [[05 - UNIQUE y diferencia entre constraint e índice|UNIQUE]]
- [[07 - FOREIGN KEY e integridad referencial|FOREIGN KEY]]

