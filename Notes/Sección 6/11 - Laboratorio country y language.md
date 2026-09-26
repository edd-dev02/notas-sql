---
tags:
  - postgresql
  - sql/practica
  - migracion-de-datos
  - normalizacion
  - curso/seccion-6
aliases:
  - Práctica continent language
---

# Laboratorio `country` y `language`

Este laboratorio extrae continentes e idiomas repetidos hacia tablas catálogo.

## Perfil inicial

```sql
SELECT DISTINCT continent
FROM country
ORDER BY continent;
```

Antes de cambiar la regla, se inspecciona y elimina el `CHECK` antiguo:

```sql
ALTER TABLE country
DROP CONSTRAINT country_continent_check;
```

El helper debe apuntar a la tabla cuyo constraint se busca; consultar `country_backup` no encuentra necesariamente los constraints de `country`.

## Migración recomendada de continentes

```sql
CREATE TABLE continent (
    code integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name text NOT NULL UNIQUE
);

INSERT INTO continent (name)
SELECT DISTINCT continent
FROM country
WHERE continent IS NOT NULL
ORDER BY continent;

ALTER TABLE country
ADD COLUMN continent_id integer;

UPDATE country AS c
SET continent_id = ct.code
FROM continent AS ct
WHERE ct.name = c.continent;
```

## Validar y proteger

```sql
SELECT
    c.code,
    c.continent AS original_continent,
    c.continent_id,
    ct.name AS reconstructed_continent
FROM country AS c
LEFT JOIN continent AS ct
    ON ct.code = c.continent_id
WHERE c.continent IS DISTINCT FROM ct.name;
```

Si devuelve cero filas:

```sql
ALTER TABLE country
ADD CONSTRAINT country_continent_id_fkey
FOREIGN KEY (continent_id)
REFERENCES continent(code);

ALTER TABLE country
ALTER COLUMN continent_id SET NOT NULL;
```

La versión del curso sobrescribió `continent` con códigos textuales y después aplicó `USING continent::integer`. Funciona con datos válidos, pero una columna paralela conserva el origen y permite reconciliarlo.

## Migración recomendada de idiomas

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

ALTER TABLE countrylanguage
ADD COLUMN languagecode integer;

UPDATE countrylanguage AS cl
SET languagecode = l.code
FROM language AS l
WHERE l.name = cl.language;
```

Crear `languagecode` como `integer` desde el inicio evita añadirlo como `varchar(3)` y convertirlo después.

## Validar y crear la FK

```sql
SELECT
    cl.countrycode,
    cl.language AS original_language,
    l.name AS reconstructed_language
FROM countrylanguage AS cl
LEFT JOIN language AS l
    ON l.code = cl.languagecode
WHERE cl.language IS DISTINCT FROM l.name;
```

```sql
ALTER TABLE countrylanguage
ADD CONSTRAINT countrylanguage_languagecode_fkey
FOREIGN KEY (languagecode)
REFERENCES language(code);

ALTER TABLE countrylanguage
ALTER COLUMN languagecode SET NOT NULL;
```

## Retirada final

Solo después de validar consumidores y restricciones:

```sql
ALTER TABLE countrylanguage
DROP COLUMN language;
```

La PK compuesta anterior incluía `language`; antes de eliminarla debe rediseñarse para utilizar `languagecode`.

```sql
ALTER TABLE countrylanguage
DROP CONSTRAINT countrylanguage_pkey;

ALTER TABLE countrylanguage
ADD CONSTRAINT countrylanguage_pkey
PRIMARY KEY (countrycode, languagecode);
```

## Relacionado

- [[06 - Backfill y actualizaciones masivas|Backfill]]
- [[07 - ALTER COLUMN TYPE y USING|Cambios de tipo]]
- [[10 - Validación y reconciliación|Validación]]

