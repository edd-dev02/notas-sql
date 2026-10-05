---
tags:
  - postgresql
  - sql/joins
  - performance
  - data-engineering
  - curso/seccion-7
aliases:
  - Optimización de JOINs
---

# Rendimiento de JOINs e índices

Con una tabla puente de 200 millones de filas, la forma lógica de la consulta sigue siendo importante, pero también lo son el acceso físico, estadísticas y volumen.

## Consulta frecuente

```sql
SELECT
    project_id,
    SUM(hours) AS total_hours
FROM employee_projects_s7
GROUP BY project_id;
```

Y relaciones frecuentes:

```text
employee_projects_s7.project_id → projects_s7.id
```

## Índices existentes por constraints

La PK:

```sql
PRIMARY KEY (employee_id, project_id)
```

crea un índice cuyo primer componente es `employee_id`. Puede ayudar a búsquedas por empleado, pero no es equivalente a un índice cuyo primer componente sea `project_id`.

PostgreSQL tampoco crea automáticamente un índice en cada columna FK de la tabla hija.

## Primer índice a evaluar

```sql
CREATE INDEX employee_projects_s7_project_id_idx
ON employee_projects_s7 (project_id);
```

Puede ayudar a buscar asignaciones por proyecto y a operaciones referenciales desde `projects_s7`.

Para patrones concretos podría investigarse un índice que también facilite acceder a `hours`, pero no debe crearse automáticamente por intuición.

## El error de ubicar la columna

`hours` pertenece a `employee_projects_s7`, no a `projects_s7`. Antes de diseñar un índice, confirma en qué tabla reside cada filtro, clave y medida.

## Más índices no significa más velocidad

Cada índice:

- ocupa almacenamiento;
- incrementa el costo de `INSERT`, `UPDATE` y `DELETE`;
- genera WAL;
- requiere mantenimiento;
- puede no ser elegido por el optimizador.

## Investigar antes de decidir

```sql
EXPLAIN
SELECT project_id, SUM(hours)
FROM employee_projects_s7
GROUP BY project_id;
```

Más adelante, `EXPLAIN ANALYZE` permitirá comparar estimaciones con ejecución real; recuerda que sí ejecuta la consulta.

También deben revisarse:

- consultas reales y su frecuencia;
- selectividad;
- estadísticas actualizadas;
- distribución y skew de proyectos;
- memoria y paralelismo;
- particionamiento, si el patrón y volumen lo justifican;
- tablas resumidas o procesos incrementales para reportes repetitivos.

> [!important]
> Un índice puede acelerar el acceso, pero calcular `SUM(hours)` para todos los proyectos sobre 200 millones de filas todavía puede requerir procesar gran parte de la tabla. El diseño del reporte y su frecuencia también importan.

## Relacionado

- [[Notes/Sección 5/06 - Índices y creación concurrente|Índices]]
- [[07 - Granularidad y agregaciones con JOIN|Granularidad]]

