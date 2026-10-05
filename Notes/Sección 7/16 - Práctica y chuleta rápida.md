---
tags:
  - postgresql
  - sql/practica
  - sql/joins
  - curso/seccion-7
---

# Práctica y chuleta rápida

## Elección rápida

| Necesidad | Patrón |
|---|---|
| Solo coincidencias | `INNER JOIN` |
| Todos los A, tengan B o no | `A LEFT JOIN B` |
| A sin B | `LEFT JOIN ... WHERE B.pk IS NULL` |
| Todo de ambos lados | `FULL OUTER JOIN` |
| Apilar y deduplicar | `UNION` |
| Apilar conservando duplicados | `UNION ALL` |
| Contar filas | `COUNT(*)` |
| Contar entidades diferentes | `COUNT(DISTINCT id)` |
| Filtrar una suma o conteo | `HAVING` |
| Acumulado | `SUM` |
| Mayor valor individual | `MAX` |

## Plantilla multitabla

```sql
SELECT
    dimension.id,
    dimension.name,
    SUM(fact.measure) AS total_measure
FROM fact_table AS fact
JOIN bridge_table AS bridge
    ON bridge.id = fact.bridge_id
JOIN dimension_table AS dimension
    ON dimension.id = bridge.dimension_id
WHERE row_condition
GROUP BY dimension.id, dimension.name
HAVING SUM(fact.measure) > 0
ORDER BY total_measure DESC;
```

## Retos

- [ ] Mostrar todos los departamentos, incluso sin proyectos, con presupuesto total cero.
- [ ] Encontrar empleados que trabajaron en proyectos de más de un departamento.
- [ ] Comparar asignaciones totales, empleados únicos y proyectos únicos por departamento.
- [ ] Encontrar proyectos cuyo total de horas supere el promedio de horas por proyecto.
- [ ] Mostrar todos los idiomas y continentes en un listado tipado mediante `UNION ALL`.
- [ ] Identificar los continentes sin idiomas oficiales registrados.
- [ ] Explicar cómo un JOIN puede duplicar un presupuesto.
- [ ] Diseñar el índice inicial para buscar asignaciones por proyecto y justificarlo.

## Ejercicio de razonamiento

Para cada requerimiento escribe primero:

```text
Una fila representa: __________________
La medida es:       __________________
Las tablas son:     __________________
El JOIN principal:  __________________
Filtro de filas:    __________________
Filtro de grupos:   __________________
```

Después escribe SQL.

## Preguntas de repaso

1. ¿Por qué `FULL OUTER JOIN` puede devolver más información de la solicitada?
2. ¿Qué diferencia existe entre `COUNT(*)` y `COUNT(DISTINCT employee_id)`?
3. ¿Por qué `MAX(hours)` no responde “horas totales”?
4. ¿Qué cambia al añadir `project` al `GROUP BY` de un reporte por departamento?
5. ¿Cuándo elegirías `UNION ALL`?
6. ¿Por qué la PK `(employee_id, project_id)` no sustituye necesariamente un índice por `project_id`?

## Respuestas breves

1. Porque conserva filas sin coincidencia de ambos lados, no solo de la entidad principal.
2. El primero cuenta asignaciones; el segundo empleados únicos.
3. Devuelve la mayor asignación individual; el total requiere `SUM`.
4. La granularidad cambia a una fila por proyecto y departamento.
5. Cuando los duplicados importan o no necesito pagar el costo de eliminarlos.
6. El índice de la PK comienza por `employee_id`; las búsquedas únicamente por `project_id` tienen otro patrón de acceso.

## Navegación

- [[00 - Sección 7 - JOIN UNION y consultas multitabla|Volver al mapa de la sección]]
- [[SQL de cero - Tu guía práctica con PostgreSQL|Volver al índice general]]

