---
tags:
  - postgresql
  - sql
  - modelado-de-datos
  - curso/seccion-5
aliases:
  - Sección 5
  - Relaciones llaves y constraints
---

# Sección 5 — Relaciones, llaves y constraints

## Objetivo

Comprender cómo PostgreSQL identifica entidades, valida valores y protege las relaciones entre tablas. La sección también introduce índices como estructuras que pueden acelerar consultas y respaldar determinadas restricciones.

## Mapa de la sección

1. [[01 - Relaciones e integridad de datos|Relaciones e integridad de datos]]
2. [[02 - Tipos de llaves|Tipos de llaves]]
3. [[03 - PRIMARY KEY y claves compuestas|PRIMARY KEY y claves compuestas]]
4. [[04 - CHECK y validación de dominio|CHECK y validación de dominio]]
5. [[05 - UNIQUE y diferencia entre constraint e índice|UNIQUE y diferencia entre constraint e índice]]
6. [[06 - Índices y creación concurrente|Índices y creación concurrente]]
7. [[07 - FOREIGN KEY e integridad referencial|FOREIGN KEY e integridad referencial]]
8. [[08 - Acciones referenciales|Acciones referenciales]]
9. [[09 - Cadenas de dependencias y CASCADE|Cadenas de dependencias y CASCADE]]
10. [[10 - Inspeccionar y modificar constraints|Inspeccionar y modificar constraints]]
11. [[11 - Laboratorio world|Laboratorio `world`]]
12. [[12 - Laboratorio de comercio electrónico|Laboratorio de comercio electrónico]]
13. [[13 - Criterios de diseño para Data Engineering|Criterios de diseño para Data Engineering]]
14. [[14 - Errores comunes y correcciones|Errores comunes y correcciones]]
15. [[15 - Práctica y chuleta rápida|Práctica y chuleta rápida]]

## Modelo mental

| Necesidad | Herramienta principal |
|---|---|
| Identificar una fila | `PRIMARY KEY` |
| Evitar duplicados en un dato alternativo | `UNIQUE` |
| Validar el dominio de un valor | `CHECK` |
| Exigir que una relación apunte a una fila existente | `FOREIGN KEY` |
| Acelerar un patrón de consulta | `INDEX` |
| Decidir qué ocurre al cambiar o borrar el padre | Acción referencial |

```text
Reglas dentro de una fila       Relaciones entre tablas       Acceso eficiente
CHECK / NOT NULL / UNIQUE  →    PRIMARY KEY ← FOREIGN KEY  →  INDEX
```

> [!important]
> La base de datos debe proteger reglas que siempre sean verdaderas, incluso si los datos llegan desde aplicaciones, scripts, cargas masivas o procesos ETL diferentes.

## Navegación

- [[SQL de cero - Tu guía práctica con PostgreSQL|Volver al índice general]]
- [[Notes/Sección 4/00 - Sección 4 - Agregaciones agrupaciones y reportes|Sección anterior]]

