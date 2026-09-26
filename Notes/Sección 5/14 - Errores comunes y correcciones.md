---
tags:
  - postgresql
  - sql/debugging
  - sql/constraints
  - curso/seccion-5
aliases:
  - Errores frecuentes de constraints
---

# Errores comunes y correcciones

## 1. PRIMARY KEY no es simplemente UNIQUE

```text
products.id     → PRIMARY KEY porque identifica el producto
customers.email → UNIQUE porque es una clave alternativa
```

`UNIQUE` impide duplicados; la PK además declara el identificador principal y no admite `NULL`.

## 2. Una PK compuesta no crea relaciones

```sql
PRIMARY KEY (order_id, product_id)
```

Evita repetir la combinación, pero no garantiza que esos IDs existan. Se necesitan FKs independientes:

```sql
FOREIGN KEY (order_id) REFERENCES orders_s5(id)
FOREIGN KEY (product_id) REFERENCES products_s5(id)
```

## 3. CHECK permite NULL

```sql
CHECK (price >= 0)
```

rechaza números negativos, pero no un valor nulo. Agrega `NOT NULL` si el precio es obligatorio.

## 4. OR sobrante en CHECK

Esta forma tiene error de sintaxis:

```sql
CHECK (
    continent = 'Africa' OR
    continent = 'Europe' OR
)
```

La versión mantenible es:

```sql
CHECK (continent IN ('Africa', 'Europe'))
```

## 5. Índice UNIQUE sobre una categoría repetible

```sql
CREATE UNIQUE INDEX ON products_s5(category);
```

impediría dos productos de `Technology`. Para acelerar búsquedas sin imponer unicidad:

```sql
CREATE INDEX ON products_s5(category);
```

## 6. Confundir regla con rendimiento

- “El correo no debe repetirse” → constraint `UNIQUE`.
- “Buscamos frecuentemente por categoría” → índice normal.

Un índice único también tiene efecto de rendimiento, pero la intención principal sigue siendo la regla de integridad.

## 7. Explicar mal CONCURRENTLY

`CREATE INDEX CONCURRENTLY` busca reducir el bloqueo de escrituras. Las lecturas ya pueden continuar durante un `CREATE INDEX` normal.

No es gratuito: tarda más, realiza más trabajo y no puede ejecutarse dentro de una transacción explícita.

## 8. Cascada incompleta

Configurar `customers → orders` con cascada no basta si `order_details` impide borrar las órdenes. Debe analizarse toda la cadena.

## 9. Asumir que una FK es obligatoria

La FK valida valores no nulos, pero permite `NULL`. Para exigir un padre:

```sql
customer_id integer NOT NULL
    REFERENCES customers_s5(id)
```

## 10. Omitir columnas en INSERT

```sql
INSERT INTO country VALUES (...);
```

depende del orden exacto de todas las columnas. Es más resistente y legible escribir:

```sql
INSERT INTO country (code, name, continent, ...)
VALUES (...);
```

## 11. Dar por hecho que toda FK está indexada

PostgreSQL no crea automáticamente un índice en la columna hija. Evalúa un índice en `orders.customer_id` cuando se consulta por cliente o se modifican padres con frecuencia.

## 12. Olvidar una regla tras un fallo del gestor

El helper mostró PKs y checks recuperados, pero no apareció:

```sql
CHECK (quantity > 0)
```

en `order_details_s5`. Conviene revisar el catálogo y volver a crearla si falta.

## Lista de comprobación

- ¿La identidad usa `PRIMARY KEY`?
- ¿Las claves alternativas importantes usan `UNIQUE`?
- ¿Los campos obligatorios tienen `NOT NULL`?
- ¿Los rangos tienen `CHECK`?
- ¿Cada relación está protegida por una FK?
- ¿Las acciones referenciales reflejan el ciclo de vida real?
- ¿Los índices responden a consultas observadas?
- ¿Los datos actuales cumplen la nueva regla?

## Relacionado

- [[10 - Inspeccionar y modificar constraints|Inspeccionar constraints]]
- [[12 - Laboratorio de comercio electrónico|Laboratorio de comercio electrónico]]

