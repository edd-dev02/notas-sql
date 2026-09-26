---
tags:
  - postgresql
  - sql/practica
  - migracion-de-datos
  - curso/seccion-6
---

# Práctica y chuleta rápida

## Chuleta de migración de catálogo

```sql
-- 1. Catálogo
CREATE TABLE catalog (
    id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name text NOT NULL UNIQUE
);

-- 2. Valores actuales
INSERT INTO catalog (name)
SELECT DISTINCT old_value
FROM source_table
WHERE old_value IS NOT NULL;

-- 3. Columna paralela
ALTER TABLE source_table
ADD COLUMN catalog_id integer;

-- 4. Previsualización
SELECT s.id, s.old_value, c.id, c.name
FROM source_table AS s
LEFT JOIN catalog AS c
    ON c.name = s.old_value;

-- 5. Backfill
UPDATE source_table AS s
SET catalog_id = c.id
FROM catalog AS c
WHERE c.name = s.old_value;

-- 6. Reconciliación
SELECT s.*
FROM source_table AS s
LEFT JOIN catalog AS c
    ON c.id = s.catalog_id
WHERE s.old_value IS DISTINCT FROM c.name;

-- 7. Protección
ALTER TABLE source_table
ADD CONSTRAINT source_table_catalog_id_fkey
FOREIGN KEY (catalog_id)
REFERENCES catalog(id);

ALTER TABLE source_table
ALTER COLUMN catalog_id SET NOT NULL;

-- 8. Retirada, solo después del cutover
ALTER TABLE source_table
DROP COLUMN old_value;
```

## Chuleta de cambio de tipo

```sql
-- Localizar excepciones
SELECT id, text_number
FROM table_name
WHERE text_number IS NULL
   OR btrim(text_number) !~ '^[+-]?[0-9]+$';

-- Convertir
ALTER TABLE table_name
ALTER COLUMN text_number TYPE integer
USING btrim(text_number)::integer;
```

## Retos

- [ ] Normalizar `payment_method` sin sobrescribir el dato original.
- [ ] Detectar variantes por espacios y mayúsculas antes de crear el catálogo.
- [ ] Demostrar con un query que la nueva relación reconstruye exactamente el valor antiguo.
- [ ] Migrar una PK compuesta que actualmente contiene texto hacia el nuevo ID del catálogo.
- [ ] Diseñar un rollback para una actualización masiva.
- [ ] Explicar por qué una copia dentro de la misma base no equivale a disaster recovery.
- [ ] Proponer métricas para observar un backfill de millones de filas.

## Preguntas de repaso

1. ¿Por qué se agrega primero una columna nullable?
2. ¿Qué expresa `USING` en un cambio de tipo?
3. ¿Qué pasa si una sola fila no puede convertirse?
4. ¿Por qué se conserva temporalmente el dato original?
5. ¿Qué diferencia hay entre crear el catálogo y normalizar el significado?
6. ¿Qué debe ocurrir antes de ejecutar `DROP COLUMN`?

## Respuestas breves

1. Permite desplegar la estructura y completar el histórico antes de exigir el valor.
2. La expresión que calcula el valor nuevo a partir del anterior.
3. La sentencia falla completa; no queda una conversión parcial de ese `ALTER TABLE`.
4. Permite reconciliar, diagnosticar y revertir la transformación.
5. El catálogo centraliza valores; la normalización semántica también resuelve variantes y equivalencias.
6. Reconciliación, constraints, migración de consumidores y recuperación comprobada.

## Navegación

- [[00 - Sección 6 - Migración y normalización de datos|Volver al mapa de la sección]]
- [[SQL de cero - Tu guía práctica con PostgreSQL|Volver al índice general]]

