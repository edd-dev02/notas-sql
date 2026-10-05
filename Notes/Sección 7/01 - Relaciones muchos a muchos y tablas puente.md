---
tags:
  - postgresql
  - modelado-de-datos
  - sql/joins
  - curso/seccion-7
aliases:
  - Tablas puente
  - Junction tables
---

# Relaciones muchos a muchos y tablas puente

Un empleado puede participar en muchos proyectos y un proyecto puede incluir muchos empleados. Una tabla puente representa esa relación.

## Modelo

```text
employees_s6  1 ─── N  employee_projects_s7  N ─── 1  projects_s7
                           employee_id
                           project_id
                           hours
```

## Definición

```sql
CREATE TABLE employee_projects_s7 (
    employee_id integer NOT NULL,
    project_id integer NOT NULL,
    hours integer NOT NULL CHECK (hours >= 0),
    CONSTRAINT employee_projects_s7_pkey
        PRIMARY KEY (employee_id, project_id),
    CONSTRAINT employee_projects_s7_employee_id_fkey
        FOREIGN KEY (employee_id)
        REFERENCES employees_s6(id),
    CONSTRAINT employee_projects_s7_project_id_fkey
        FOREIGN KEY (project_id)
        REFERENCES projects_s7(id)
);
```

## Granularidad de la tabla

Cada fila representa:

```text
un empleado asignado a un proyecto
```

La PK compuesta impide repetir la misma pareja empleado–proyecto.

## Atributos de la relación

`hours` no pertenece únicamente al empleado ni al proyecto: describe la participación de ese empleado en ese proyecto. Por eso vive en la tabla puente.

## Recuperar nombres

```sql
SELECT
    e.name AS employee,
    p.name AS project,
    ep.hours
FROM employee_projects_s7 AS ep
JOIN employees_s6 AS e
    ON e.id = ep.employee_id
JOIN projects_s7 AS p
    ON p.id = ep.project_id;
```

## Riesgo de duplicar métricas

Al unir proyectos con asignaciones, cada proyecto aparece una vez por empleado. Sumar `p.budget` después de ese JOIN contaría el presupuesto varias veces. La granularidad del JOIN debe revisarse antes de agregar.

## Relacionado

- [[Notes/Sección 5/03 - PRIMARY KEY y claves compuestas|Claves compuestas]]
- [[07 - Granularidad y agregaciones con JOIN|Granularidad]]

