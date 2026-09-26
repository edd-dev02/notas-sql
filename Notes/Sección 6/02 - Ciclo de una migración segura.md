---
tags:
  - postgresql
  - migracion-de-datos
  - data-engineering
  - curso/seccion-6
aliases:
  - Migración segura
---

# Ciclo de una migración segura

Una migración cambia datos y estructura mientras preserva significado, integridad y capacidad de recuperación.

## Secuencia recomendada

### 1. Definir el resultado esperado

Documenta el modelo final, sus FKs, nulabilidad, índices y consumidores.

### 2. Perfilar los datos

Busca `NULL`, vacíos, variantes, duplicados, valores inválidos y conversiones imposibles. Consulta [[03 - Perfilado y limpieza previa|Perfilado y limpieza previa]].

### 3. Preparar recuperación

Coordina respaldo, snapshot, réplica o estrategia de rollback. Una copia manual dentro de la misma base no sustituye necesariamente un respaldo administrado.

### 4. Crear la estructura nueva

```sql
CREATE TABLE countries_s6 (...);
ALTER TABLE employees_s6 ADD COLUMN country_id integer;
```

La columna nueva comienza aceptando `NULL` para permitir el backfill.

### 5. Poblar el catálogo

```sql
INSERT INTO countries_s6 (name)
SELECT DISTINCT country
FROM employees_s6;
```

### 6. Previsualizar la correspondencia

```sql
SELECT
    e.id,
    e.country AS old_country,
    c.id AS new_country_id,
    c.name AS reconstructed_country
FROM employees_s6 AS e
LEFT JOIN countries_s6 AS c
    ON c.name = e.country;
```

### 7. Ejecutar el backfill

```sql
UPDATE employees_s6 AS e
SET country_id = c.id
FROM countries_s6 AS c
WHERE c.name = e.country;
```

### 8. Validar

Comprueba nulos, huérfanos, conteos, duplicados y equivalencia semántica.

### 9. Proteger

Agrega FK, `NOT NULL`, índices y otras reglas cuando los datos ya las satisfagan.

### 10. Cambiar consumidores

Aplicaciones, reportes, ETL y vistas deben comenzar a leer y escribir la nueva columna.

### 11. Retirar lo anterior

Elimina la columna antigua solo después de un periodo de validación y con rollback definido.

## Expansión y contracción

Este patrón también se conoce como expand/contract:

```text
Expandir  → agregar estructura compatible
Migrar    → copiar y validar datos
Cambiar   → mover consumidores
Contraer  → retirar estructura antigua
```

Permite separar un cambio riesgoso en pasos reversibles.

## Relacionado

- [[04 - Respaldos y estrategia de recuperación|Recuperación]]
- [[06 - Backfill y actualizaciones masivas|Backfill]]
- [[10 - Validación y reconciliación|Validación]]

