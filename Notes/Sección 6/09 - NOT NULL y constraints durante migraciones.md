---
tags:
  - postgresql
  - sql/constraints
  - migracion-de-datos
  - curso/seccion-6
aliases:
  - SET NOT NULL
---

# NOT NULL y constraints durante migraciones

Durante un backfill, una columna nueva suele aceptar `NULL`. Cuando todas las filas han sido pobladas y validadas, puede hacerse obligatoria.

## Verificar antes

```sql
SELECT COUNT(*) AS missing_department_ids
FROM employees_s6
WHERE department_id IS NULL;
```

El resultado debe ser `0`.

También conviene buscar referencias inválidas:

```sql
SELECT e.*
FROM employees_s6 AS e
LEFT JOIN departments_s6 AS d
    ON d.id = e.department_id
WHERE e.department_id IS NOT NULL
  AND d.id IS NULL;
```

## Convertir la columna en NOT NULL

La opción “Nullable: No” del gestor equivale a:

```sql
ALTER TABLE employees_s6
ALTER COLUMN department_id SET NOT NULL;
```

Si existe alguna fila nula, la sentencia falla.

Para permitir nuevamente `NULL`:

```sql
ALTER TABLE employees_s6
ALTER COLUMN department_id DROP NOT NULL;
```

`NOT NULL` es una propiedad de la columna; por eso no se elimina mediante `DROP CONSTRAINT nombre` como un `CHECK` convencional.

## Agregar la FK

```sql
ALTER TABLE employees_s6
ADD CONSTRAINT employees_s6_department_id_fkey
FOREIGN KEY (department_id)
REFERENCES departments_s6(id);
```

La FK y `NOT NULL` protegen reglas diferentes:

```text
NOT NULL    → debe existir un valor
FOREIGN KEY → el valor debe existir en el catálogo
```

## Tabla grande: validación por etapas

Una estrategia disponible en PostgreSQL es agregar una FK sin validar el histórico inmediatamente:

```sql
ALTER TABLE employees_s6
ADD CONSTRAINT employees_s6_department_id_fkey
FOREIGN KEY (department_id)
REFERENCES departments_s6(id)
NOT VALID;

ALTER TABLE employees_s6
VALIDATE CONSTRAINT employees_s6_department_id_fkey;
```

`NOT VALID` sigue comprobando filas nuevas o modificadas. Después se valida el histórico de forma separada. No elimina toda necesidad de coordinación ni todos los bloqueos.

## Índice de la columna hija

PostgreSQL no crea automáticamente un índice en `employees_s6.department_id` al agregar la FK:

```sql
CREATE INDEX employees_s6_department_id_idx
ON employees_s6 (department_id);
```

Su utilidad depende de las consultas y operaciones reales.

## Relacionado

- [[Notes/Sección 5/07 - FOREIGN KEY e integridad referencial|FOREIGN KEY]]
- [[06 - Backfill y actualizaciones masivas|Backfill]]

