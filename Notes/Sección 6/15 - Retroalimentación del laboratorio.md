---
tags:
  - postgresql
  - aprendizaje
  - migracion-de-datos
  - curso/seccion-6
aliases:
  - Resumen del chat sección 6
---

# Retroalimentación del laboratorio

Esta nota resume la revisión detallada realizada en el chat **Ejercicios SQL prácticos** sobre la sección 6.

## Resultado general

La práctica fue evaluada con **8.8/10** y la sección quedó aprobada. El avance más importante fue pasar de escribir consultas aisladas a razonar sobre migraciones, conservación del dato original y validación.

## Fortalezas observadas

### Análisis inicial

Se usaron correctamente `DISTINCT`, `GROUP BY` y conteos para descubrir departamentos y países existentes.

### Creación de catálogos

La carga mediante:

```sql
INSERT INTO departments_s6 (name)
SELECT DISTINCT department
FROM employees_s6;
```

mostró comprensión de `INSERT ... SELECT` y evitó inserciones manuales.

### Previsualización antes del UPDATE

Se construyó un `SELECT` para obtener el ID correspondiente antes de actualizar. Esta fue una de las mejores decisiones de la práctica: primero observar la transformación y después ejecutarla.

### Relaciones y consultas finales

La FK de departamentos y el `JOIN` final fueron correctos. La vista legible demostró cómo una tabla puede almacenar IDs y recuperar descripciones mediante relaciones.

### Conversión de salary

La conversión con `USING salary::integer` fue correcta para datos numéricos, y las funciones `MIN`, `MAX` y `AVG` demostraron el beneficio de usar el tipo apropiado.

### Atomicidad del cambio de tipo

Se razonó correctamente que un valor como `28K` hace fallar toda la sentencia de conversión; no quedan algunas filas convertidas y otras no.

## Puntos que faltaron o debían reforzarse

### Verificar nulos antes de NOT NULL

Después del backfill faltó comprobar explícitamente:

```sql
SELECT COUNT(*)
FROM employees_s6
WHERE department_id IS NULL;
```

La secuencia recomendada quedó:

```text
UPDATE masivo
      ↓
verificar NULL
      ↓
crear FK
      ↓
SET NOT NULL
```

### Conservar country original

El principal error estratégico fue reutilizar `country` para almacenar IDs. La consulta funcionó, pero destruyó el texto original antes de validar la nueva relación.

La mejora es crear `country_id`, mantener ambas columnas y comprobar:

```text
Mexico    → 3 → Mexico
Colombia  → 2 → Colombia
Argentina → 1 → Argentina
```

### Validar convertibilidad de forma robusta

`SELECT *` permitió revisar ocho salarios, pero no escala. Debe buscarse expresamente cualquier valor que no cumpla el formato numérico.

### Validar antes de eliminar columnas

Se propuso una CTE, lo cual es posible, pero no obligatoria. Un `JOIN` puede comprobar:

- IDs nulos;
- referencias sin catálogo;
- número de registros;
- reconstrucción exacta del texto original;
- existencia de constraints y FKs.

### Plan de recuperación

En el reto de 80 millones de filas faltó mencionar inicialmente backup o rollback, aun cuando el curso ya había resaltado ese punto.

### Datos sucios

Antes de crear el catálogo deben analizarse variantes como `Mexico`, `México`, `MEXICO`, espacios, abreviaturas, vacíos y nulos.

### Escala operativa

Se valoró positivamente considerar `CREATE INDEX CONCURRENTLY`, pero también se señaló que 80 millones de filas obligan a evaluar backfill por lotes, locks, WAL, almacenamiento, replicación, duración y concurrencia.

## Enseñanza principal

> [!important]
> Una migración no está terminada porque el `UPDATE` haya devuelto “OK”. Está terminada cuando verificaste que el resultado representa correctamente los datos originales.

## Evolución observada

```text
Primeras secciones → correcciones de sintaxis SQL
Sección 5          → diseño e integridad
Sección 6          → decisiones sobre datos y migraciones seguras
```

## Siguiente foco

Continuar con el curso manteniendo práctica acumulativa de:

- `JOIN`;
- agregaciones;
- constraints;
- perfilado;
- migraciones sin pérdida del dato original;
- validaciones antes del cutover.

## Relacionado

- [[12 - Laboratorio employees_s6|Laboratorio employees_s6]]
- [[13 - Migraciones de gran volumen|Migraciones grandes]]
- [[14 - Errores comunes y correcciones|Errores comunes]]

