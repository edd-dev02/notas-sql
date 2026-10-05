---
tags:
  - postgresql
  - sql/set-operations
  - curso/seccion-7
aliases:
  - UNION
---

# UNION y UNION ALL

Las operaciones de conjuntos apilan las filas producidas por varias consultas.

## UNION

```sql
SELECT name, 'Employee' AS entity_type
FROM employees_s6

UNION

SELECT name, 'Project' AS entity_type
FROM projects_s7

ORDER BY entity_type, name;
```

`UNION` elimina filas duplicadas del resultado combinado.

## UNION ALL

```sql
SELECT name, 'Employee' AS entity_type
FROM employees_s6

UNION ALL

SELECT name, 'Project' AS entity_type
FROM projects_s7;
```

`UNION ALL` conserva duplicados y evita el trabajo de deduplicación. Úsalo cuando los duplicados son significativos o sabes que las ramas no se solapan.

## Reglas de compatibilidad

Cada `SELECT` debe devolver:

- el mismo número de columnas;
- tipos compatibles por posición;
- valores con significado comparable en cada posición.

```sql
-- Error: tres columnas frente a dos
SELECT id, name, salary FROM employees_s6
UNION
SELECT id, name FROM projects_s7;
```

Los nombres de las columnas del resultado provienen del primer `SELECT`.

## ORDER BY

El ordenamiento del resultado completo se coloca al final. Las ramas no conservan un orden útil por sí mismas:

```sql
SELECT name FROM projects_s7 WHERE budget > 100000
UNION
SELECT name FROM projects_s7 WHERE name LIKE '%System%'
ORDER BY name;
```

## UNION frente a OR

Cuando ambas condiciones consultan la misma tabla y devuelven las mismas columnas, un solo `WHERE` puede ser más claro:

```sql
SELECT name
FROM projects_s7
WHERE budget > 100000
   OR name LIKE '%System%';
```

`UNION` resulta especialmente útil cuando las ramas provienen de fuentes diferentes o necesitan transformaciones distintas.

## Consolidar continentes de América

El ejercicio puede resolverse con `UNION`, pero una sola agrupación expresa directamente el nuevo grupo:

```sql
SELECT
    CASE
        WHEN ct.name LIKE '%America%' THEN 'America'
        ELSE ct.name
    END AS continent_group,
    COUNT(c.code) AS total_countries
FROM continent AS ct
LEFT JOIN country AS c
    ON c.continent = ct.code
GROUP BY
    CASE
        WHEN ct.name LIKE '%America%' THEN 'America'
        ELSE ct.name
    END
ORDER BY total_countries;
```

## Relacionado

- [[14 - Errores comunes y correcciones|Errores comunes]]
- [[16 - Práctica y chuleta rápida|Chuleta]]

