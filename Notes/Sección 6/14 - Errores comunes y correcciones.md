---
tags:
  - postgresql
  - sql/debugging
  - migracion-de-datos
  - curso/seccion-6
aliases:
  - Errores frecuentes de migración
---

# Errores comunes y correcciones

## 1. Sobrescribir el origen antes de validar

```sql
UPDATE employees_s6 AS e
SET country = (
    SELECT c.id
    FROM countries_s6 AS c
    WHERE c.name = e.country
);
```

Puede funcionar porque el ID se almacena temporalmente como texto, pero destruye `Mexico`, `Colombia` y `Argentina` antes de verificar la transformación.

Mejor:

```sql
ALTER TABLE employees_s6 ADD COLUMN country_id integer;
```

y conservar ambas columnas hasta reconciliarlas.

## 2. Inspección visual como única validación

`SELECT *` sirve con ocho filas, pero no demuestra que ocho millones sean convertibles. Usa consultas que localicen excepciones, conteos y diferencias.

## 3. Crear el catálogo desde datos sucios

```sql
SELECT DISTINCT customer_country
```

puede producir entradas separadas para `Mexico`, `México`, `MEXICO`, ` Mexico`, `MX`, vacío y `NULL`. Primero define valores canónicos.

## 4. Confiar en USING para cualquier dato

```sql
USING salary::integer
```

explica cómo convertir, pero no vuelve convertible a `28K`. Los datos incompatibles deben localizarse y tratarse antes.

## 5. Crear la columna nueva con el tipo equivocado

Si `language.code` es `integer`, conviene crear `languagecode integer` desde el principio, no `varchar(3)` para convertirlo después.

## 6. Omitir UNIQUE en la tabla catálogo

Sin `UNIQUE (name)`, una subconsulta por nombre podría devolver varios IDs y el catálogo admitiría duplicados conceptuales.

## 7. Crear FK antes de completar el backfill

Una FK admite `NULL`, pero fallará si hay IDs inexistentes. Es preferible poblar, validar, agregar FK y finalmente establecer `NOT NULL` cuando la relación sea obligatoria.

## 8. Confundir FK con NOT NULL

- FK: un valor no nulo debe existir en el padre.
- `NOT NULL`: toda fila debe tener valor.

Se necesitan ambas para una relación obligatoria.

## 9. Usar SELECT * para copiar tablas

Depende del orden físico de columnas. Enumera columnas y valida el resultado.

## 10. Considerar una copia local como respaldo completo

`country_backup` ayuda a deshacer una transformación puntual, pero no protege ante pérdida de la base completa ni garantiza una restauración operativa.

## 11. Consultar el constraint en la tabla equivocada

Si se quiere eliminar `country_continent_check` de `country`, el helper debe filtrar `country`, no solamente `country_backup`.

## 12. Eliminar una columna incluida en una PK

`countrylanguage.language` formaba parte de la PK compuesta. Antes de borrarla debe migrarse la PK a `(countrycode, languagecode)`.

## 13. Crear un índice con un nombre ya existente

Los nombres de índices deben ser únicos dentro del esquema. Usa nombres específicos como:

```text
country_backup_name_uidx
country_backup_continent_idx
```

## 14. Ejecutar un UPDATE gigante sin plan operativo

Con millones de filas deben considerarse lotes, locks, WAL, réplicas, espacio, monitoreo y rollback. Un único `UPDATE` correcto puede seguir siendo una mala operación de producción.

## Relacionado

- [[03 - Perfilado y limpieza previa|Perfilado]]
- [[10 - Validación y reconciliación|Validación]]
- [[13 - Migraciones de gran volumen|Gran volumen]]

