---
tags:
  - postgresql
  - sql/practica
  - sql/constraints
  - curso/seccion-5
---

# Práctica y chuleta rápida

## Chuleta de constraints

```sql
ALTER TABLE table_name
ADD CONSTRAINT table_name_pkey
PRIMARY KEY (id);

ALTER TABLE table_name
ADD CONSTRAINT table_name_column_key
UNIQUE (column_name);

ALTER TABLE table_name
ADD CONSTRAINT table_name_rule_check
CHECK (numeric_column >= 0);

ALTER TABLE child_table
ADD CONSTRAINT child_parent_id_fkey
FOREIGN KEY (parent_id)
REFERENCES parent_table(id)
ON DELETE CASCADE
ON UPDATE CASCADE;

ALTER TABLE table_name
DROP CONSTRAINT constraint_name;
```

## Chuleta de índices

```sql
CREATE INDEX table_column_idx
ON table_name (column_name);

CREATE UNIQUE INDEX table_columns_uidx
ON table_name (column_a, column_b);

CREATE INDEX CONCURRENTLY table_column_idx
ON table_name (column_name);

DROP INDEX table_column_idx;
```

## Decisión rápida

| Pregunta | Respuesta probable |
|---|---|
| ¿Qué identifica la entidad? | `PRIMARY KEY` |
| ¿Qué dato alternativo no debe repetirse? | `UNIQUE` |
| ¿Qué valores son válidos? | Tipo + `CHECK` + `NOT NULL` |
| ¿Debe existir una fila relacionada? | `FOREIGN KEY` |
| ¿Qué pasa al borrar el padre? | Acción `ON DELETE` |
| ¿Cómo acelerar una consulta frecuente? | Medir y evaluar un índice |

## Retos

- [ ] Encontrar duplicados antes de agregar una PK.
- [ ] Diseñar una tabla `employees` con PK artificial y email único obligatorio.
- [ ] Crear una tabla `enrollments` con PK compuesta y dos FKs.
- [ ] Crear un `CHECK` que garantice `end_date >= start_date`.
- [ ] Encontrar filas huérfanas antes de agregar una FK.
- [ ] Explicar por qué `PRIMARY KEY (order_id, product_id)` no sustituye las FKs.
- [ ] Diseñar una estrategia de borrado para cliente → órdenes → detalles.
- [ ] Elegir un índice para una consulta frecuente y justificar el orden de columnas.

## Consultas de diagnóstico

### Duplicados

```sql
SELECT candidate_key, COUNT(*)
FROM table_name
GROUP BY candidate_key
HAVING COUNT(*) > 1;
```

### Valores fuera de rango

```sql
SELECT *
FROM table_name
WHERE numeric_column < 0;
```

### Huérfanos

```sql
SELECT child.*
FROM child_table AS child
LEFT JOIN parent_table AS parent
    ON parent.id = child.parent_id
WHERE parent.id IS NULL;
```

### Ver el plan

```sql
EXPLAIN
SELECT *
FROM orders_s5
WHERE customer_id = 15000;
```

## Preguntas de repaso

1. ¿Por qué una tabla solo tiene una PK, aunque esta pueda ser compuesta?
2. ¿Qué diferencia existe entre una clave candidata y una alternativa?
3. ¿Por qué `CHECK (price >= 0)` no hace obligatorio el precio?
4. ¿Qué protege una FK que no protege un JOIN?
5. ¿Por qué una cascada puede fallar aunque esté configurada entre dos tablas?
6. ¿Qué bloqueo intenta reducir principalmente `CREATE INDEX CONCURRENTLY`?

## Respuestas breves

1. Porque solo una clave candidata se elige como identidad principal; puede contener varias columnas.
2. Todas pueden identificar la fila; la alternativa es una candidata que no fue elegida como PK.
3. Porque una comparación con `NULL` es desconocida y `CHECK` no la rechaza; hace falta `NOT NULL`.
4. Impide insertar o conservar referencias hacia padres inexistentes.
5. Porque otra FK en una tabla descendiente puede impedir un borrado intermedio.
6. El bloqueo de escrituras durante la construcción del índice.

## Navegación

- [[00 - Sección 5 - Relaciones llaves y constraints|Volver al mapa de la sección]]
- [[SQL de cero - Tu guía práctica con PostgreSQL|Volver al índice general]]

