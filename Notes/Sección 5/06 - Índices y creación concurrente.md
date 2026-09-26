---
tags:
  - postgresql
  - sql/indexes
  - performance
  - curso/seccion-5
aliases:
  - CREATE INDEX CONCURRENTLY
---

# Índices y creación concurrente

Un índice es una estructura auxiliar que permite localizar filas sin revisar necesariamente toda la tabla.

## Diseñar desde la consulta

Si la operación frecuente es:

```sql
SELECT *
FROM orders_s5
WHERE customer_id = 15000;
```

el primer índice a evaluar es:

```sql
CREATE INDEX orders_s5_customer_id_idx
ON orders_s5 (customer_id);
```

La columna indexada nace del patrón de acceso real, no de crear índices indiscriminadamente.

## Ejemplos de la sección

```sql
CREATE INDEX country_continent_idx
ON country (continent);

CREATE INDEX city_district_idx
ON city (district);

CREATE INDEX products_s5_category_idx
ON products_s5 (category);
```

## Índice compuesto y orden de columnas

```sql
CREATE INDEX city_countrycode_name_idx
ON city (countrycode, name);
```

Ayuda naturalmente a consultas por `countrycode` y por `countrycode + name`. No necesariamente ayuda igual a una consulta solo por `name`, porque el orden de las columnas importa.

## Costos

Los índices:

- ocupan espacio;
- deben actualizarse durante `INSERT`, `UPDATE` y `DELETE`;
- pueden hacer más costosas las escrituras;
- requieren mantenimiento;
- no garantizan que el optimizador los use.

## Verificar con EXPLAIN

```sql
EXPLAIN
SELECT *
FROM orders_s5
WHERE customer_id = 15000;
```

`EXPLAIN` muestra el plan estimado. Para medir la ejecución real existe `EXPLAIN ANALYZE`, pero este sí ejecuta la consulta; úsalo con cuidado, especialmente con escrituras.

## CREATE INDEX normal

Un `CREATE INDEX` normal permite lecturas, pero bloquea escrituras sobre la tabla mientras se construye.

## CREATE INDEX CONCURRENTLY

```sql
CREATE INDEX CONCURRENTLY orders_s5_customer_id_idx
ON orders_s5 (customer_id);
```

Reduce el bloqueo de `INSERT`, `UPDATE` y `DELETE` durante la construcción, por lo que es valioso en tablas activas de producción.

Sus contrapartidas:

- suele tardar más y hacer más trabajo;
- no puede ejecutarse dentro de un bloque de transacción explícito;
- si falla, puede dejar un índice inválido que debe revisarse o eliminarse;
- no significa ausencia absoluta de bloqueos.

> [!important] Corrección conceptual
> El objetivo principal de `CONCURRENTLY` no es habilitar lecturas: un `CREATE INDEX` normal ya permite `SELECT`. Busca reducir la interrupción de escrituras.

## FOREIGN KEY e índices

PostgreSQL indexa la PK o clave única referenciada, pero no crea automáticamente un índice en la columna hija de cada FK. Indexar `orders_s5.customer_id` suele mejorar búsquedas por cliente y comprobaciones al modificar o eliminar padres.

## Relacionado

- [[05 - UNIQUE y diferencia entre constraint e índice|UNIQUE frente a índice]]
- [[13 - Criterios de diseño para Data Engineering|Criterios para Data Engineering]]

