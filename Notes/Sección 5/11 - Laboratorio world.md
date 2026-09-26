---
tags:
  - postgresql
  - sql/practica
  - modelado-de-datos
  - curso/seccion-5
aliases:
  - Práctica country city countrylanguage
---

# Laboratorio `world`

Las tablas `country`, `city` y `countrylanguage` permiten practicar integridad de entidad, dominio y referencia.

## 1. Claves primarias

```sql
ALTER TABLE country
ADD CONSTRAINT country_pkey
PRIMARY KEY (code);

ALTER TABLE city
ADD CONSTRAINT city_pkey
PRIMARY KEY (id);

ALTER TABLE countrylanguage
ADD CONSTRAINT countrylanguage_pkey
PRIMARY KEY (countrycode, language);
```

## 2. Validaciones

```sql
ALTER TABLE country
ADD CONSTRAINT country_surfacearea_check
CHECK (surfacearea >= 0);

ALTER TABLE city
ADD CONSTRAINT city_population_check
CHECK (population >= 0);

ALTER TABLE countrylanguage
ADD CONSTRAINT countrylanguage_percentage_check
CHECK (percentage BETWEEN 0 AND 100);
```

## 3. Continentes permitidos

Antes de diseñar la regla, conviene inspeccionar los valores existentes:

```sql
SELECT DISTINCT continent
FROM country
ORDER BY continent;
```

```sql
ALTER TABLE country
ADD CONSTRAINT country_continent_check
CHECK (
    continent IN (
        'Asia', 'Oceania', 'Antarctica', 'Europe',
        'Africa', 'South America', 'North America',
        'Central America'
    )
);
```

```sql
UPDATE country
SET continent = 'Central America'
WHERE region = 'Central America';
```

> [!note] Modelado
> En el dataset original, Central America puede estar modelada como región dentro de North America. Cambiarla a continente es una decisión didáctica o de negocio, no una verdad universal del esquema.

## 4. Reglas de unicidad e índices

```sql
ALTER TABLE country
ADD CONSTRAINT country_name_key
UNIQUE (name);

CREATE INDEX country_continent_idx
ON country (continent);

ALTER TABLE city
ADD CONSTRAINT city_name_country_district_key
UNIQUE (name, countrycode, district);

CREATE INDEX city_district_idx
ON city (district);
```

## 5. Relaciones

```sql
ALTER TABLE city
ADD CONSTRAINT city_countrycode_fkey
FOREIGN KEY (countrycode)
REFERENCES country(code);

ALTER TABLE countrylanguage
ADD CONSTRAINT countrylanguage_countrycode_fkey
FOREIGN KEY (countrycode)
REFERENCES country(code);
```

La segunda FK es necesaria si se quiere garantizar que cada idioma esté asociado a un país existente.

## 6. Diagnosticar el error AFG

Si una ciudad utiliza `AFG` y no existe el país correspondiente, la FK falla. Primero comprueba la ausencia:

```sql
SELECT *
FROM country
WHERE code = 'AFG';
```

Al insertar el país, es más seguro nombrar las columnas que depender del orden físico:

```sql
INSERT INTO country (
    code, name, continent, region, surfacearea,
    indepyear, population, lifeexpectancy, gnp,
    gnpold, localname, governmentform,
    headofstate, capital, code2
)
VALUES (
    'AFG', 'Afghanistan', 'Asia', 'Southern Asia', 652860,
    1919, 40000000, 62, 69000000,
    NULL, 'Afghanistan', 'Totalitarian',
    NULL, NULL, 'AF'
);
```

## 7. Cascadas

```sql
ALTER TABLE city
DROP CONSTRAINT city_countrycode_fkey;

ALTER TABLE city
ADD CONSTRAINT city_countrycode_fkey
FOREIGN KEY (countrycode)
REFERENCES country(code)
ON DELETE CASCADE
ON UPDATE CASCADE;
```

Esta configuración debe elegirse conscientemente: borrar un país puede eliminar muchas ciudades.

## Relacionado

- [[04 - CHECK y validación de dominio|CHECK]]
- [[07 - FOREIGN KEY e integridad referencial|FOREIGN KEY]]
- [[08 - Acciones referenciales|Acciones referenciales]]

