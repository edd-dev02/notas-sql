---
tags:
  - postgresql
  - migracion-de-datos
  - normalizacion
  - curso/seccion-6
aliases:
  - Sección 6
  - Migración y normalización
---

# Sección 6 — Migración y normalización de datos

## Objetivo

Aprender a transformar datos existentes hacia estructuras más consistentes sin perder su significado: crear tablas catálogo, poblarlas desde valores actuales, incorporar claves foráneas, cambiar tipos y retirar columnas antiguas de forma controlada.

## Mapa de la sección

1. [[01 - Normalización y tablas catálogo|Normalización y tablas catálogo]]
2. [[02 - Ciclo de una migración segura|Ciclo de una migración segura]]
3. [[03 - Perfilado y limpieza previa|Perfilado y limpieza previa]]
4. [[04 - Respaldos y estrategia de recuperación|Respaldos y estrategia de recuperación]]
5. [[05 - INSERT SELECT y carga de catálogos|INSERT SELECT y carga de catálogos]]
6. [[06 - Backfill y actualizaciones masivas|Backfill y actualizaciones masivas]]
7. [[07 - ALTER COLUMN TYPE y USING|ALTER COLUMN TYPE y USING]]
8. [[08 - Secuencias SERIAL e IDENTITY|Secuencias, SERIAL e IDENTITY]]
9. [[09 - NOT NULL y constraints durante migraciones|NOT NULL y constraints durante migraciones]]
10. [[10 - Validación y reconciliación|Validación y reconciliación]]
11. [[11 - Laboratorio country y language|Laboratorio `country` y `language`]]
12. [[12 - Laboratorio employees_s6|Laboratorio `employees_s6`]]
13. [[13 - Migraciones de gran volumen|Migraciones de gran volumen]]
14. [[14 - Errores comunes y correcciones|Errores comunes y correcciones]]
15. [[15 - Retroalimentación del laboratorio|Retroalimentación del laboratorio]]
16. [[16 - Práctica y chuleta rápida|Práctica y chuleta rápida]]

## Transformación estudiada

```text
ANTES
country.continent = 'Europe'
country.continent = 'Asia'

DESPUÉS
continent
id | name
 1 | Asia
 2 | Europe
      ▲
      │ FK
country.continent_id
```

La misma idea se aplicó a idiomas, departamentos y países.

## Principio de seguridad

```text
Analizar
   ↓
Limpiar y definir equivalencias
   ↓
Crear nueva estructura
   ↓
Copiar o transformar sin destruir el origen
   ↓
Validar y reconciliar
   ↓
Agregar constraints
   ↓
Cambiar consumidores
   ↓
Retirar la estructura antigua
```

> [!important]
> Conserva simultáneamente la representación antigua y la nueva hasta demostrar que cada fila se transformó correctamente.

## Navegación

- [[SQL de cero - Tu guía práctica con PostgreSQL|Volver al índice general]]
- [[Notes/Sección 5/00 - Sección 5 - Relaciones llaves y constraints|Sección anterior]]

