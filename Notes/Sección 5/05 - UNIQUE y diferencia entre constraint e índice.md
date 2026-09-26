---
tags:
  - postgresql
  - sql/constraints
  - sql/indexes
  - curso/seccion-5
aliases:
  - UNIQUE constraint
---

# UNIQUE y diferencia entre constraint e índice

`UNIQUE` expresa una regla de integridad: ciertos valores o combinaciones no deben repetirse.

## Email único

```sql
ALTER TABLE customers_s5
ADD CONSTRAINT customers_s5_email_key
UNIQUE (email);
```

PostgreSQL crea un índice único para hacer cumplir la restricción.

## Constraint frente a índice único

Las dos formas pueden impedir duplicados:

```sql
ALTER TABLE country
ADD CONSTRAINT country_name_key
UNIQUE (name);
```

```sql
CREATE UNIQUE INDEX unique_country_name
ON country (name);
```

La primera comunica una regla del modelo: “el nombre de país debe ser único”. La segunda crea directamente una estructura de índice y resulta útil en casos más especializados, por ejemplo índices únicos parciales o basados en expresiones.

## Cuándo un índice no debe ser UNIQUE

```sql
CREATE INDEX products_s5_category_idx
ON products_s5 (category);
```

Muchos productos pueden pertenecer a `Technology`. Si el índice fuera único, el segundo producto de la misma categoría fallaría.

```text
101 | Laptop | Technology
102 | Mouse  | Technology  ← válido con índice normal
```

## UNIQUE compuesto

```sql
CREATE UNIQUE INDEX city_name_country_district_uidx
ON city (name, countrycode, district);
```

La combinación completa debe ser única; cada columna individual puede repetirse.

Si es una regla central del modelo, también puede expresarse como constraint:

```sql
ALTER TABLE city
ADD CONSTRAINT city_name_country_district_key
UNIQUE (name, countrycode, district);
```

## Comportamiento con NULL

Por defecto, PostgreSQL considera que valores `NULL` distintos no son iguales, por lo que una restricción `UNIQUE` puede admitir varios `NULL`. Si el dato debe existir, agrega `NOT NULL`.

En versiones modernas de PostgreSQL también existe `UNIQUE NULLS NOT DISTINCT` para tratar los nulos como iguales cuando esa sea la regla deseada.

## Regla mental

```text
Constraint → declara qué datos son válidos.
Index      → organiza cómo acceder a ellos.
```

Aunque un constraint `PRIMARY KEY` o `UNIQUE` se apoye en un índice, la intención del diseño sigue siendo diferente.

## Relacionado

- [[03 - PRIMARY KEY y claves compuestas|PRIMARY KEY]]
- [[06 - Índices y creación concurrente|Índices]]

