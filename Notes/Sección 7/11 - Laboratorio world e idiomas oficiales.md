---
tags:
  - postgresql
  - sql/practica
  - sql/joins
  - curso/seccion-7
aliases:
  - Idiomas oficiales por continente
---

# Laboratorio `world` e idiomas oficiales

## Continentes sin países

```sql
SELECT ct.code, ct.name AS continent
FROM continent AS ct
LEFT JOIN country AS c
    ON c.continent = ct.code
WHERE c.code IS NULL;
```

## Países por continente, incluyendo cero

```sql
SELECT
    ct.code,
    ct.name AS continent,
    COUNT(c.code) AS total_countries
FROM continent AS ct
LEFT JOIN country AS c
    ON c.continent = ct.code
GROUP BY ct.code, ct.name
ORDER BY total_countries, ct.name;
```

## Idiomas oficiales distintos por continente

La consulta original usaba:

```sql
SELECT DISTINCT COUNT(cl.language), ct.name
...
GROUP BY ct.name;
```

Esto cuenta filas de país–idioma oficial. `DISTINCT` se aplica después del agregado y no vuelve únicos los idiomas dentro del conteo.

Solución directa:

```sql
SELECT
    ct.code,
    ct.name AS continent,
    COUNT(DISTINCT cl.languagecode) AS official_languages
FROM countrylanguage AS cl
JOIN country AS c
    ON c.code = cl.countrycode
JOIN continent AS ct
    ON ct.code = c.continent
WHERE cl.isofficial = true
GROUP BY ct.code, ct.name
ORDER BY ct.name;
```

La alternativa del instructor primero obtiene pares distintos idioma–continente en una subquery y después los cuenta. Ambas estrategias responden a “idiomas diferentes”, no a “registros oficiales”.

## Idioma oficial presente en más países de Europa

```sql
SELECT
    l.code AS language_code,
    l.name AS language,
    COUNT(DISTINCT c.code) AS countries
FROM countrylanguage AS cl
JOIN country AS c
    ON c.code = cl.countrycode
JOIN continent AS ct
    ON ct.code = c.continent
JOIN language AS l
    ON l.code = cl.languagecode
WHERE cl.isofficial = true
  AND ct.name = 'Europe'
GROUP BY l.code, l.name
ORDER BY countries DESC, l.name
LIMIT 1;
```

Se filtra por el nombre del continente en lugar de depender del ID mágico `5`.

## Países donde es oficial un idioma conocido

Después de identificar el código:

```sql
SELECT
    c.code,
    c.name AS country,
    l.name AS official_language
FROM countrylanguage AS cl
JOIN country AS c ON c.code = cl.countrycode
JOIN continent AS ct ON ct.code = c.continent
JOIN language AS l ON l.code = cl.languagecode
WHERE cl.isofficial = true
  AND ct.name = 'Europe'
  AND l.code = 1109
ORDER BY c.name;
```

Una solución completamente dinámica requeriría reutilizar el ranking mediante una subquery o CTE, temas que se profundizarán posteriormente.

## Relacionado

- [[08 - COUNT DISTINCT en relaciones|COUNT DISTINCT]]
- [[10 - Rankings SUM MAX y LIMIT|Rankings]]

