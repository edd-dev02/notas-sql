---
tags:
  - postgresql
  - calidad-de-datos
  - migracion-de-datos
  - curso/seccion-6
aliases:
  - Data profiling
---

# Perfilado y limpieza previa

`SELECT DISTINCT` descubre valores, pero una migración segura necesita investigar también calidad, frecuencia y convertibilidad.

## Descubrir valores y frecuencias

```sql
SELECT
    country,
    COUNT(*) AS occurrences
FROM employees_s6
GROUP BY country
ORDER BY occurrences DESC, country;
```

La frecuencia ayuda a detectar valores raros que un listado alfabético puede ocultar.

## Buscar nulos y vacíos

```sql
SELECT *
FROM employees_s6
WHERE country IS NULL
   OR btrim(country) = '';
```

## Detectar variantes

```sql
SELECT
    lower(btrim(country)) AS normalized_candidate,
    array_agg(DISTINCT country ORDER BY country) AS original_values,
    COUNT(*) AS occurrences
FROM employees_s6
GROUP BY lower(btrim(country))
ORDER BY occurrences DESC;
```

Esto puede revelar:

```text
Mexico
MEXICO
 Mexico
```

Normalizar formato no resuelve automáticamente sinónimos como `Mexico`, `México` y `MX`; hace falta una regla de negocio o tabla de equivalencias.

## Validar texto numérico

Antes de convertir `salary`:

```sql
SELECT id, salary
FROM employees_s6
WHERE salary IS NULL
   OR btrim(salary) !~ '^[+-]?[0-9]+$';
```

Si aparece `28K`, debe corregirse o definirse explícitamente cómo interpretarlo.

> [!warning]
> La expresión regular valida la forma, no garantiza que el número esté dentro del rango de `integer`. Para importes grandes puede ser necesario validar límites o convertir primero a `numeric`.

## Comparar cardinalidades

```sql
SELECT
    COUNT(*) AS rows_total,
    COUNT(country) AS rows_with_country,
    COUNT(DISTINCT country) AS raw_distinct_values,
    COUNT(DISTINCT lower(btrim(country))) AS normalized_distinct_values
FROM employees_s6;
```

Una diferencia entre los dos últimos conteos señala variantes de espacios o capitalización.

## Regla central

> [!important]
> Crear un ID para cada valor sucio solo normaliza la estructura física; no normaliza el significado de los datos.

## Relacionado

- [[01 - Normalización y tablas catálogo|Tablas catálogo]]
- [[07 - ALTER COLUMN TYPE y USING|Conversión de tipos]]
- [[13 - Migraciones de gran volumen|Migraciones grandes]]

