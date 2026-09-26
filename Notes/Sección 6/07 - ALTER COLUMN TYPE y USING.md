---
tags:
  - postgresql
  - sql/ddl
  - conversion-de-tipos
  - curso/seccion-6
aliases:
  - ALTER COLUMN TYPE
  - USING
---

# ALTER COLUMN TYPE y USING

`ALTER COLUMN ... TYPE` cambia el tipo declarado de una columna y transforma sus valores existentes.

## Cuándo puede funcionar sin USING

PostgreSQL puede aplicar el cambio directamente cuando dispone de una conversión implícita o de asignación adecuada.

Ejemplos habituales:

```sql
-- Ampliar la longitud permitida
ALTER TABLE people
ALTER COLUMN name TYPE varchar(200);

-- Ampliar un entero
ALTER TABLE metrics
ALTER COLUMN quantity TYPE bigint;
```

La compatibilidad exacta depende de los tipos origen y destino, defaults, índices, constraints y expresiones relacionadas.

## Cuándo se necesita USING

Una columna `text` con valores como `'1'`, `'2'` y `'3'` no se convierte automáticamente a entero:

```sql
ALTER TABLE country
ALTER COLUMN continent TYPE integer;
```

PostgreSQL solicita una expresión explícita:

```sql
ALTER TABLE country
ALTER COLUMN continent TYPE integer
USING continent::integer;
```

`USING` significa: “para calcular cada nuevo valor, aplica esta expresión al valor anterior”.

## Transformaciones personalizadas

```sql
-- Texto con espacios a entero
ALTER TABLE employees_s6
ALTER COLUMN salary TYPE integer
USING btrim(salary)::integer;

-- Centavos enteros a monto decimal
ALTER TABLE payments
ALTER COLUMN amount_cents TYPE numeric(12,2)
USING amount_cents::numeric / 100;
```

## Cuándo falla incluso con USING

```text
'25000'
'22000'
'28K'
'21000'
```

```sql
USING salary::integer
```

falla porque `'28K'::integer` no es válido. La sentencia completa falla; no deja la columna parcialmente convertida.

## Validar antes

```sql
SELECT id, salary
FROM employees_s6
WHERE salary IS NULL
   OR btrim(salary) !~ '^[+-]?[0-9]+$';
```

Después de limpiar los valores, puede realizarse el cambio.

## Otros objetos afectados

El cambio puede requerir atender:

- defaults que no se convierten al nuevo tipo;
- índices y constraints;
- vistas y funciones dependientes;
- FKs cuyos tipos deben ser compatibles;
- reescritura de la tabla y bloqueos.

> [!warning]
> Que exista una expresión `USING` válida no significa que el cambio sea barato. En tablas grandes puede reescribir muchos datos y requerir una estrategia operativa específica.

## Relacionado

- [[03 - Perfilado y limpieza previa|Perfilado]]
- [[13 - Migraciones de gran volumen|Gran volumen]]

