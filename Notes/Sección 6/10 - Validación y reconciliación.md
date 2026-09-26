---
tags:
  - postgresql
  - calidad-de-datos
  - migracion-de-datos
  - curso/seccion-6
aliases:
  - Reconciliación de datos
---

# Validación y reconciliación

Validar significa demostrar que la transformación es completa, correcta y consistente; revisar unas cuantas filas visualmente no basta para volúmenes grandes.

## 1. Conteo de filas

```sql
SELECT COUNT(*)
FROM employees_s6;
```

El backfill no debería cambiar el número de empleados.

## 2. Cobertura

```sql
SELECT COUNT(*) AS missing_ids
FROM employees_s6
WHERE department_id IS NULL;
```

## 3. Correspondencia exacta

```sql
SELECT
    e.id,
    e.department AS original_value,
    d.name AS reconstructed_value
FROM employees_s6 AS e
LEFT JOIN departments_s6 AS d
    ON d.id = e.department_id
WHERE e.department IS DISTINCT FROM d.name;
```

`IS DISTINCT FROM` compara de forma segura incluso cuando intervienen valores `NULL`. El resultado esperado es cero filas.

## 4. Huérfanos

```sql
SELECT e.*
FROM employees_s6 AS e
LEFT JOIN departments_s6 AS d
    ON d.id = e.department_id
WHERE e.department_id IS NOT NULL
  AND d.id IS NULL;
```

## 5. Duplicados en catálogo

```sql
SELECT name, COUNT(*)
FROM departments_s6
GROUP BY name
HAVING COUNT(*) > 1;
```

La restricción `UNIQUE` debe impedirlos.

## 6. Distribuciones antes y después

```sql
SELECT department, COUNT(*)
FROM employees_s6
GROUP BY department
ORDER BY department;

SELECT d.name, COUNT(*)
FROM employees_s6 AS e
JOIN departments_s6 AS d
    ON d.id = e.department_id
GROUP BY d.name
ORDER BY d.name;
```

Ambas distribuciones deben coincidir.

## 7. Constraints e índices

Confirma que existan PK, `UNIQUE`, FK, `NOT NULL` e índices previstos. Puede reutilizarse el helper de [[Notes/Sección 5/10 - Inspeccionar y modificar constraints|pg_constraint]].

## 8. Validación funcional

Ejecuta consultas y procesos consumidores: reportes, joins, escrituras y cargas ETL. Una transformación puede ser correcta en datos pero incompatible con una aplicación antigua.

## Antes de DROP COLUMN

- Cero IDs nulos inesperados.
- Cero huérfanos.
- Cero diferencias entre valor original y reconstruido.
- Conteos y distribuciones consistentes.
- Constraints e índices presentes.
- Consumidores migrados.
- Recuperación definida.

> [!important]
> Una migración no está terminada porque el `UPDATE` haya finalizado; está terminada cuando la reconciliación demuestra que preservó el significado.

## Relacionado

- [[02 - Ciclo de una migración segura|Ciclo de migración]]
- [[04 - Respaldos y estrategia de recuperación|Recuperación]]

