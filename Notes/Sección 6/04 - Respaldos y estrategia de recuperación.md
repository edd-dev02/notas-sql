---
tags:
  - postgresql
  - backup
  - migracion-de-datos
  - curso/seccion-6
aliases:
  - Plan de recuperación
---

# Respaldos y estrategia de recuperación

Antes de una actualización masiva debe existir una forma probada de regresar a un estado correcto.

## Coordinar con el DBA

En un entorno real, la estrategia puede incluir:

- respaldo administrado y restauración probada;
- snapshot del almacenamiento;
- réplica o recuperación a un punto en el tiempo;
- ventana de mantenimiento;
- script inverso o rollback;
- conservación temporal de las columnas originales.

La opción adecuada depende del volumen, disponibilidad, infraestructura y criticidad de los datos.

## Copia de tabla: útil, pero limitada

```sql
CREATE TABLE country_backup
(LIKE country INCLUDING ALL);

INSERT INTO country_backup (
    code, name, continent, region, surfacearea,
    indepyear, population, lifeexpectancy, gnp,
    gnpold, localname, governmentform,
    headofstate, capital, code2
)
SELECT
    code, name, continent, region, surfacearea,
    indepyear, population, lifeexpectancy, gnp,
    gnpold, localname, governmentform,
    headofstate, capital, code2
FROM country;
```

`LIKE ... INCLUDING ALL` copia muchas propiedades de la tabla, pero una copia dentro de la misma base no protege frente a la pérdida completa de esa base o del servidor.

## CREATE TABLE AS

```sql
CREATE TABLE country_backup AS
SELECT *
FROM country;
```

Es cómodo para copiar filas y columnas, pero no replica automáticamente PKs, FKs, índices, triggers ni todos los defaults. No debe asumirse que es un clon estructural completo.

## Evitar SELECT * en volcados controlados

```sql
INSERT INTO country_backup
SELECT * FROM country;
```

depende del mismo número, orden y tipos de columnas. Enumerarlas hace el contrato visible y reduce errores tras cambios de esquema.

## Validar la copia

```sql
SELECT
    (SELECT COUNT(*) FROM country) AS source_rows,
    (SELECT COUNT(*) FROM country_backup) AS backup_rows;
```

Comprobar ambas direcciones detecta diferencias de contenido:

```sql
(SELECT * FROM country EXCEPT SELECT * FROM country_backup)
UNION ALL
(SELECT * FROM country_backup EXCEPT SELECT * FROM country);
```

El resultado esperado es cero filas.

## Transacción frente a respaldo

PostgreSQL permite revertir muchas operaciones DDL y DML dentro de una transacción:

```sql
BEGIN;
-- cambios y validaciones
ROLLBACK; -- ensayo
```

Pero una transacción larga también retiene recursos y no sustituye una estrategia de recuperación. Además, `CREATE INDEX CONCURRENTLY` no puede ejecutarse dentro de ella.

## Relacionado

- [[02 - Ciclo de una migración segura|Ciclo de migración]]
- [[10 - Validación y reconciliación|Validación]]

