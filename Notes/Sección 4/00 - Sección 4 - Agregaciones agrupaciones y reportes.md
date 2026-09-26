---
tags:
  - postgresql
  - sql
  - curso/seccion-4
aliases:
  - Sección 4
  - Agregaciones y agrupaciones
---

# Sección 4 — Agregaciones, agrupaciones y reportes

## Objetivo

Convertir datos detallados en información resumida y digerible: contar registros, calcular totales y promedios, comparar grupos y construir consultas útiles para reportes.

## Mapa de la sección

1. [[01 - Familias de sentencias SQL|DDL, DML, DQL y TCL]]
2. [[02 - Anatomía y orden de ejecución de SELECT|Anatomía y orden de ejecución de SELECT]]
3. [[03 - Funciones agregadas y ROUND|Funciones agregadas y ROUND]]
4. [[04 - BETWEEN IN y lógica de condiciones|BETWEEN, IN y lógica de condiciones]]
5. [[05 - GROUP BY|GROUP BY]]
6. [[06 - WHERE frente a HAVING|WHERE frente a HAVING]]
7. [[07 - DISTINCT y valores únicos|DISTINCT y valores únicos]]
8. [[08 - Agrupar por partes de strings|Agrupar por partes de strings]]
9. [[09 - Introducción a subqueries|Introducción a subqueries]]
10. [[10 - Patrones para reportes|Patrones para reportes]]
11. [[11 - Laboratorio sales_practice|Laboratorio `sales_practice`]]
12. [[12 - Errores comunes y correcciones|Errores comunes y correcciones]]
13. [[13 - Práctica y chuleta rápida|Práctica y chuleta rápida]]

## Modelo mental

```text
Filas de la tabla
      ↓  WHERE
Filas relevantes
      ↓  GROUP BY
Grupos
      ↓  funciones agregadas
Estadísticas por grupo
      ↓  HAVING
Grupos relevantes
      ↓  ORDER BY / LIMIT
Reporte final
```

## Preguntas que ahora podemos responder

- ¿Cuántos usuarios hay en total o por país?
- ¿Cuál fue la venta mínima, máxima, promedio y total?
- ¿Qué países superan cierto monto de ventas?
- ¿Qué dominios de correo se repiten?
- ¿Qué vendedores cumplen simultáneamente condiciones sobre sus filas y sus totales?

## Regla de oro

> [!important]
> Si la condición habla de una fila, normalmente pertenece en `WHERE`. Si habla de un resultado agregado o de un grupo, pertenece en `HAVING`.

## Navegación

- [[SQL de cero - Tu guía práctica con PostgreSQL|Volver al índice general]]
- [[Notes/Sección 3/00 - Sección 3 - Fundamentos de SQL|Sección anterior]]
