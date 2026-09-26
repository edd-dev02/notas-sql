---
tags:
  - postgresql
  - sql/dml
  - normalizacion
  - curso/seccion-6
aliases:
  - INSERT SELECT
---

# INSERT SELECT y carga de catálogos

`INSERT ... SELECT` carga filas calculadas por una consulta, sin escribir manualmente cada valor.

## Crear el catálogo

```sql
CREATE TABLE continent (
    code integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name text NOT NULL UNIQUE
);
```

## Insertar valores existentes

```sql
INSERT INTO continent (name)
SELECT DISTINCT continent
FROM country
WHERE continent IS NOT NULL
ORDER BY continent;
```

`DISTINCT` evita duplicados provenientes de la tabla fuente; `UNIQUE` protege el catálogo después de la carga.

## Evitar duplicados al repetir el script

```sql
INSERT INTO continent (name)
SELECT DISTINCT c.continent
FROM country AS c
WHERE c.continent IS NOT NULL
ON CONFLICT (name) DO NOTHING;
```

Esta variante hace que la carga sea repetible respecto a nombres ya existentes.

## Idiomas

```sql
CREATE TABLE language (
    code integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name text NOT NULL UNIQUE
);

INSERT INTO language (name)
SELECT DISTINCT language
FROM countrylanguage
WHERE language IS NOT NULL
ORDER BY language;
```

La restricción `UNIQUE (name)` es importante: una subconsulta o un `JOIN` debe encontrar como máximo un ID por nombre.

## Sobre ORDER BY e IDs

Ordenar la carga puede producir IDs visualmente ordenados en ese momento, pero el ID no debe adquirir significado de negocio. Inserciones futuras, eliminaciones y secuencias pueden crear huecos.

## Validar el catálogo

```sql
SELECT name, COUNT(*)
FROM language
GROUP BY name
HAVING COUNT(*) > 1;
```

Con `UNIQUE`, la consulta debe permanecer vacía.

## Relacionado

- [[01 - Normalización y tablas catálogo|Tablas catálogo]]
- [[06 - Backfill y actualizaciones masivas|Backfill]]
- [[08 - Secuencias SERIAL e IDENTITY|Generación de IDs]]

