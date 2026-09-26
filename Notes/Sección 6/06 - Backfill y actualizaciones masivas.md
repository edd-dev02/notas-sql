---
tags:
  - postgresql
  - sql/dml
  - migracion-de-datos
  - curso/seccion-6
aliases:
  - Backfill
  - Actualización masiva
---

# Backfill y actualizaciones masivas

Un backfill rellena una columna nueva usando datos históricos ya existentes.

## Patrón seguro con columna paralela

```sql
ALTER TABLE employees_s6
ADD COLUMN department_id integer;
```

Primero se previsualiza la correspondencia:

```sql
SELECT
    e.id,
    e.name AS employee,
    e.department AS old_department,
    d.id AS new_department_id,
    d.name AS reconstructed_department
FROM employees_s6 AS e
LEFT JOIN departments_s6 AS d
    ON d.name = e.department
ORDER BY e.id;
```

Después se actualiza:

```sql
UPDATE employees_s6 AS e
SET department_id = d.id
FROM departments_s6 AS d
WHERE d.name = e.department
  AND e.department_id IS NULL;
```

`UPDATE ... FROM` suele ser más legible que repetir una subconsulta correlacionada en `SET`.

## Subconsulta correlacionada

La forma estudiada también es válida si devuelve como máximo una fila:

```sql
UPDATE employees_s6 AS e
SET department_id = (
    SELECT d.id
    FROM departments_s6 AS d
    WHERE d.name = e.department
);
```

Si no hay coincidencia, asigna `NULL`; si hay varias coincidencias, la subconsulta falla. Por eso el nombre del catálogo debe ser único y la validación previa es esencial.

## No sobrescribir el dato original

Actualizar directamente una columna textual con IDs puede funcionar:

```text
Mexico → '3' → 3
```

pero elimina la referencia original antes de comprobar que `3` reconstruye `Mexico`. Una columna paralela permite comparar ambas representaciones y revertir con mayor facilidad.

## Revisar el alcance

```sql
SELECT COUNT(*)
FROM employees_s6 AS e
JOIN departments_s6 AS d
    ON d.name = e.department
WHERE e.department_id IS NULL;
```

Este conteo estima cuántas filas serán actualizadas.

## RETURNING

En una muestra pequeña puede revisarse lo modificado:

```sql
UPDATE employees_s6 AS e
SET department_id = d.id
FROM departments_s6 AS d
WHERE d.name = e.department
  AND e.department_id IS NULL
RETURNING e.id, e.name, e.department, e.department_id;
```

En millones de filas, devolver todo el resultado puede ser costoso; se prefieren métricas y muestreos controlados.

## Relacionado

- [[02 - Ciclo de una migración segura|Ciclo de migración]]
- [[10 - Validación y reconciliación|Validación]]
- [[13 - Migraciones de gran volumen|Gran volumen]]

