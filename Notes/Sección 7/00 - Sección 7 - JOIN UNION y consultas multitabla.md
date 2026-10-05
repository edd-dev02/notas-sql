---
tags:
  - postgresql
  - sql/joins
  - sql/set-operations
  - curso/seccion-7
aliases:
  - Sección 7
  - JOIN y UNION
---

# Sección 7 — JOIN, UNION y consultas multitabla

## Objetivo

Combinar información distribuida entre tablas, preservar filas sin coincidencia cuando el requerimiento lo exige y producir reportes cuya agrupación y métrica respondan exactamente a la pregunta de negocio.

## Mapa de la sección

1. [[01 - Relaciones muchos a muchos y tablas puente|Relaciones muchos a muchos y tablas puente]]
2. [[02 - UNION y UNION ALL|UNION y UNION ALL]]
3. [[03 - Sintaxis JOIN y condición ON|Sintaxis JOIN y condición ON]]
4. [[04 - INNER JOIN|INNER JOIN]]
5. [[05 - LEFT RIGHT y FULL OUTER JOIN|LEFT, RIGHT y FULL OUTER JOIN]]
6. [[06 - Anti-joins y registros sin relación|Anti-joins y registros sin relación]]
7. [[07 - Granularidad y agregaciones con JOIN|Granularidad y agregaciones con JOIN]]
8. [[08 - COUNT DISTINCT en relaciones|COUNT DISTINCT en relaciones]]
9. [[09 - Construir consultas complejas paso a paso|Construir consultas complejas paso a paso]]
10. [[10 - Rankings SUM MAX y LIMIT|Rankings, SUM, MAX y LIMIT]]
11. [[11 - Laboratorio world e idiomas oficiales|Laboratorio `world` e idiomas oficiales]]
12. [[12 - Laboratorio projects_s7|Laboratorio `projects_s7`]]
13. [[13 - Rendimiento de JOINs e índices|Rendimiento de JOINs e índices]]
14. [[14 - Errores comunes y correcciones|Errores comunes y correcciones]]
15. [[15 - Retroalimentación del laboratorio|Retroalimentación del laboratorio]]
16. [[16 - Práctica y chuleta rápida|Práctica y chuleta rápida]]

## Dos formas de combinar información

```text
JOIN
Combina columnas de tablas relacionadas.

customers + orders → customer | order | total

UNION
Apila filas de resultados compatibles.

employees
   +
projects
   ↓
name | type
```

## Preguntas previas a una consulta compleja

1. ¿Qué representa una fila del resultado?
2. ¿Qué quiero medir?
3. ¿Qué tablas contienen la información?
4. ¿Cómo se relacionan?
5. ¿Debo conservar filas sin relación?
6. ¿Filtro filas con `WHERE` o grupos con `HAVING`?
7. ¿Necesito contar registros o entidades distintas?

> [!important]
> Un `JOIN` sintácticamente correcto todavía puede responder otra pregunta si la granularidad o la agregación elegidas no coinciden con el requerimiento.

## Navegación

- [[SQL de cero - Tu guía práctica con PostgreSQL|Volver al índice general]]
- [[Notes/Sección 6/00 - Sección 6 - Migración y normalización de datos|Sección anterior]]

