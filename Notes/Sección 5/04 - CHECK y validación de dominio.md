---
tags:
  - postgresql
  - sql/constraints
  - curso/seccion-5
aliases:
  - CHECK constraint
---

# CHECK y validación de dominio

`CHECK` exige que una expresión sea verdadera o desconocida para cada fila nueva o modificada.

## Valores no negativos

```sql
ALTER TABLE country
ADD CONSTRAINT country_surfacearea_check
CHECK (surfacearea >= 0);

ALTER TABLE city
ADD CONSTRAINT city_population_check
CHECK (population >= 0);
```

## Rango permitido

```sql
ALTER TABLE countrylanguage
ADD CONSTRAINT countrylanguage_percentage_check
CHECK (percentage BETWEEN 0 AND 100);
```

`BETWEEN` incluye 0 y 100.

## Lista de valores permitidos

En lugar de encadenar muchos `OR`, usa `IN`:

```sql
ALTER TABLE country
ADD CONSTRAINT country_continent_check
CHECK (
    continent IN (
        'Asia',
        'Oceania',
        'Antarctica',
        'Europe',
        'Africa',
        'South America',
        'North America',
        'Central America'
    )
);
```

También puede aplicarse a estados:

```sql
ALTER TABLE orders_s5
ADD CONSTRAINT orders_s5_status_check
CHECK (status IN ('Pending', 'Completed', 'Cancelled'));
```

PostgreSQL puede mostrar internamente este último constraint mediante `ANY (ARRAY[...])`; es otra representación de la misma regla.

## `CHECK` no reemplaza `NOT NULL`

En SQL, una comparación con `NULL` produce “desconocido”, y `CHECK` acepta ese resultado. Por tanto:

```sql
price numeric CHECK (price >= 0)
```

todavía admite `NULL`. Si el dato es obligatorio:

```sql
price numeric NOT NULL CHECK (price >= 0)
```

## Constraints de columna y de tabla

Una regla de una sola columna puede declararse junto a ella. Una regla que compara columnas se expresa naturalmente a nivel de tabla:

```sql
CREATE TABLE promotions (
    starts_at date NOT NULL,
    ends_at date NOT NULL,
    CONSTRAINT promotions_dates_check
        CHECK (ends_at >= starts_at)
);
```

## Datos existentes

Al agregar un `CHECK`, PostgreSQL verifica normalmente las filas existentes. Si alguna incumple la regla, el `ALTER TABLE` falla.

En tablas grandes puede separarse la creación de la validación histórica:

```sql
ALTER TABLE city
ADD CONSTRAINT city_population_check
CHECK (population >= 0) NOT VALID;

ALTER TABLE city
VALIDATE CONSTRAINT city_population_check;
```

`NOT VALID` sigue comprobando filas nuevas o modificadas; la segunda sentencia valida las anteriores.

## Relacionado

- [[10 - Inspeccionar y modificar constraints|Inspeccionar constraints]]
- [[14 - Errores comunes y correcciones|Errores comunes]]

