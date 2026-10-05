---
tags:
  - postgresql
  - aprendizaje
  - sql/joins
  - curso/seccion-7
aliases:
  - Resumen del chat sección 7
---

# Retroalimentación del laboratorio

Esta nota resume la revisión de las 29 respuestas realizada en el chat **Ejercicios SQL prácticos**.

## Resultado

La práctica obtuvo **8.7/10** y la sección quedó aprobada. Los JOINs fundamentales están sólidos; las correcciones se concentraron en granularidad, elección de agregación y lectura completa del requerimiento.

## Fortalezas

### INNER JOIN y relaciones muchos-a-muchos

Los ejercicios iniciales fueron correctos, incluido `employee | project | hours` a través de tres tablas.

### LEFT JOIN + IS NULL

Se resolvieron correctamente empleados sin proyectos y proyectos sin empleados. El patrón ya está bien comprendido.

### Agregaciones y HAVING

Las horas acumuladas por empleado utilizaron correctamente:

```sql
GROUP BY employee
HAVING SUM(hours) > 100
```

sin confundirlo con un filtro sobre una asignación individual.

### JOINs de cinco tablas

Se relacionaron correctamente empleados, países, departamentos, asignaciones y proyectos, y se filtró por nombres en lugar de IDs mágicos.

### Reto estructural

Se resolvió correctamente el departamento cuyos proyectos involucran más empleados diferentes:

```text
JOINs
→ GROUP BY departamento
→ COUNT(DISTINCT empleado)
→ ORDER BY DESC
→ LIMIT 1
```

Este reto replicaba la estructura de la tarea de idiomas que no se había podido completar durante el curso, lo que muestra que el concepto comenzó a asentarse.

## Correcciones por ejercicios

### 5 y 7 — JOIN excesivo

`FULL OUTER JOIN` preservaba información adicional. Un `LEFT JOIN` desde la entidad requerida expresaba mejor el objetivo.

### 15 — Nivel de agrupación incorrecto

Se contaron empleados por proyecto, aunque se pedían empleados por departamento. El conteo era correcto; faltaba llegar a la dimensión adecuada.

### 16 — Entidad contada

`COUNT(DISTINCT project.*)` funcionó, pero es preferible contar `project.id`.

### 17 — Asignaciones frente a proyectos

`total_assignments` debía contar filas de la tabla puente, no proyectos distintos.

### 18 — Filtros no solicitados

La estructura de `UNION` fue correcta, pero el listado debía incluir todos los empleados y proyectos.

### 24 — Acumulado frente a máximo individual

Se utilizó `MAX(hours)` cuando el requerimiento pedía `SUM(hours)` por proyecto.

### 25 — Granularidad demasiado detallada

Agrupar por proyecto y departamento produjo una fila por proyecto. El requerimiento pedía una fila por departamento.

### 26 — Faltó filtrar el agregado

El conteo de proyectos estaba bien, pero faltó `HAVING COUNT(DISTINCT project_id) > 1`.

### 27 — Condición incompleta

Se identificaron proyectos sin asignaciones, pero faltó el presupuesto superior a 20000 y se utilizaron JOINs innecesarios.

### 29 — Rendimiento

La intuición de indexar `employee_projects_s7.project_id` fue correcta. La columna `hours` se ubicó por error en `projects_s7`; pertenece a la tabla puente. También se reforzó que los índices deben justificarse con consultas y planes reales.

## Diagnóstico del nivel

La conversación posterior situó el nivel actual como:

> **SQL básico–intermedio con fundamentos sólidos y capacidad práctica; avanzando hacia un nivel intermedio completo.**

Fortalezas consolidadas:

- filtros y CRUD;
- agregaciones y `GROUP BY`;
- `INNER` y `LEFT JOIN`;
- JOINs de tres a cinco tablas;
- constraints y relaciones;
- fundamentos de normalización y migraciones.

Áreas todavía en desarrollo:

- granularidad y `COUNT(DISTINCT)`;
- subqueries más complejas;
- CTEs;
- window functions;
- transacciones;
- `EXPLAIN ANALYZE` y optimización.

## Preguntas mentales recomendadas

1. ¿Qué representa una fila del resultado?
2. ¿Qué quiero medir?
3. ¿Qué tablas contienen la información?
4. ¿Cómo se relacionan?
5. ¿Filtro antes o después de agrupar?

## Relacionado

- [[07 - Granularidad y agregaciones con JOIN|Granularidad]]
- [[14 - Errores comunes y correcciones|Errores comunes]]

