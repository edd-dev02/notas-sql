# SQL de cero: guía práctica con PostgreSQL

## Índice

- [[postgres-cheatsheet.pdf|Guía de atajos de PostgreSQL]]
- [[Notes/Sección 3/00 - Sección 3 - Fundamentos de SQL|Sección 3 — SELECT, INSERT, UPDATE, DELETE, funciones y operadores]]
- [[Notes/Sección 4/00 - Sección 4 - Agregaciones agrupaciones y reportes|Sección 4 — Funciones agregadas, agrupaciones y ordenamiento]]
- [[Notes/Sección 5/00 - Sección 5 - Relaciones llaves y constraints|Sección 5 — Relaciones, llaves, constraints e índices]]
- [[Notes/Sección 6/00 - Sección 6 - Migración y normalización de datos|Sección 6 — Migración, normalización y transformación segura de datos]]

## Sección 3: fundamentos de SQL

En esta sección se practica el ciclo básico de trabajo con una tabla: definir su estructura, insertar datos, consultarlos, modificarlos y eliminarlos. También se introducen restricciones y funciones para transformar texto.

> [!tip] Idea central
> Primero define la estructura y sus reglas; después modifica los datos con sentencias DML; finalmente consulta y transforma el resultado con `SELECT`.

### Temas

- [[Notes/Sección 3/01 - Crear tablas y restricciones|Crear tablas y restricciones]]
- [[Notes/Sección 3/02 - INSERT y carga de datos|INSERT y carga de datos]]
- [[Notes/Sección 3/03 - SELECT filtros y paginación|SELECT, filtros y paginación]]
- [[Notes/Sección 3/04 - LIKE y búsqueda por patrones|LIKE y búsqueda por patrones]]
- [[Notes/Sección 3/05 - UPDATE y cambios seguros|UPDATE y cambios seguros]]
- [[Notes/Sección 3/06 - Funciones y operadores de texto|Funciones y operadores de texto]]
- [[Notes/Sección 3/07 - Separar nombre y apellido|Separar nombre y apellido]]
- [[Notes/Sección 3/08 - DELETE TRUNCATE y DROP|DELETE, TRUNCATE y DROP]]
- [[Notes/Sección 3/09 - Práctica y chuleta rápida|Práctica y chuleta rápida]]

## Sección 4: funciones agregadas, agrupaciones y ordenamiento

El objetivo es transformar filas en información útil para reportes: totales, promedios, extremos, conteos por grupo y filtros sobre resultados agregados.

> [!tip] Idea central
> `WHERE` decide qué filas entran al análisis, `GROUP BY` forma los grupos y `HAVING` decide qué grupos aparecen en el resultado.

### Temas

- [[Notes/Sección 4/01 - Familias de sentencias SQL|DDL, DML, DQL y TCL]]
- [[Notes/Sección 4/02 - Anatomía y orden de ejecución de SELECT|Anatomía y orden de ejecución de SELECT]]
- [[Notes/Sección 4/03 - Funciones agregadas y ROUND|COUNT, SUM, MIN, MAX, AVG y ROUND]]
- [[Notes/Sección 4/04 - BETWEEN IN y lógica de condiciones|BETWEEN, IN y lógica de condiciones]]
- [[Notes/Sección 4/05 - GROUP BY|GROUP BY]]
- [[Notes/Sección 4/06 - WHERE frente a HAVING|WHERE frente a HAVING]]
- [[Notes/Sección 4/07 - DISTINCT y valores únicos|DISTINCT y valores únicos]]
- [[Notes/Sección 4/08 - Agrupar por partes de strings|Agrupar por partes de strings]]
- [[Notes/Sección 4/09 - Introducción a subqueries|Introducción a subqueries]]
- [[Notes/Sección 4/10 - Patrones para reportes|Patrones para reportes]]
- [[Notes/Sección 4/11 - Laboratorio sales_practice|Laboratorio sales_practice]]
- [[Notes/Sección 4/12 - Errores comunes y correcciones|Errores comunes y correcciones]]
- [[Notes/Sección 4/13 - Práctica y chuleta rápida|Práctica y chuleta rápida]]

## Sección 5: relaciones, llaves, constraints e índices

Esta sección pasa de consultar datos a diseñar cómo se identifican, validan y relacionan. Las restricciones protegen la integridad; los índices ayudan a localizar información eficientemente.

> [!tip] Idea central
> Una clave define identidad o relaciones; un constraint expresa una regla que siempre debe cumplirse; un índice es una estructura de acceso. Algunos constraints utilizan índices internamente, pero los conceptos no son intercambiables.

### Temas

- [[Notes/Sección 5/01 - Relaciones e integridad de datos|Relaciones e integridad de datos]]
- [[Notes/Sección 5/02 - Tipos de llaves|Tipos de llaves]]
- [[Notes/Sección 5/03 - PRIMARY KEY y claves compuestas|PRIMARY KEY y claves compuestas]]
- [[Notes/Sección 5/04 - CHECK y validación de dominio|CHECK y validación de dominio]]
- [[Notes/Sección 5/05 - UNIQUE y diferencia entre constraint e índice|UNIQUE: constraint frente a índice]]
- [[Notes/Sección 5/06 - Índices y creación concurrente|Índices y creación concurrente]]
- [[Notes/Sección 5/07 - FOREIGN KEY e integridad referencial|FOREIGN KEY e integridad referencial]]
- [[Notes/Sección 5/08 - Acciones referenciales|CASCADE, RESTRICT, SET NULL y otras acciones]]
- [[Notes/Sección 5/09 - Cadenas de dependencias y CASCADE|Cadenas de dependencias y CASCADE]]
- [[Notes/Sección 5/10 - Inspeccionar y modificar constraints|Inspeccionar y modificar constraints]]
- [[Notes/Sección 5/11 - Laboratorio world|Laboratorio con country, city y countrylanguage]]
- [[Notes/Sección 5/12 - Laboratorio de comercio electrónico|Laboratorio customers, orders, products y details]]
- [[Notes/Sección 5/13 - Criterios de diseño para Data Engineering|Criterios de diseño para Data Engineering]]
- [[Notes/Sección 5/14 - Errores comunes y correcciones|Errores comunes y correcciones]]
- [[Notes/Sección 5/15 - Práctica y chuleta rápida|Práctica y chuleta rápida]]

## Sección 6: migración y normalización de datos

Esta sección estudia cómo transformar tablas existentes hacia un modelo mejor diseñado: extraer catálogos, migrar valores de texto a identificadores, cambiar tipos, validar correspondencias y proteger el resultado con constraints.

> [!tip] Idea central
> Una migración no termina cuando el `UPDATE` responde “OK”; termina cuando se demuestra que los datos nuevos representan correctamente a los originales y existe un camino de recuperación.

### Temas

- [[Notes/Sección 6/01 - Normalización y tablas catálogo|Normalización y tablas catálogo]]
- [[Notes/Sección 6/02 - Ciclo de una migración segura|Ciclo de una migración segura]]
- [[Notes/Sección 6/03 - Perfilado y limpieza previa|Perfilado y limpieza previa]]
- [[Notes/Sección 6/04 - Respaldos y estrategia de recuperación|Respaldos y estrategia de recuperación]]
- [[Notes/Sección 6/05 - INSERT SELECT y carga de catálogos|INSERT SELECT y carga de catálogos]]
- [[Notes/Sección 6/06 - Backfill y actualizaciones masivas|Backfill y actualizaciones masivas]]
- [[Notes/Sección 6/07 - ALTER COLUMN TYPE y USING|ALTER COLUMN TYPE y USING]]
- [[Notes/Sección 6/08 - Secuencias SERIAL e IDENTITY|Secuencias, SERIAL e IDENTITY]]
- [[Notes/Sección 6/09 - NOT NULL y constraints durante migraciones|NOT NULL y constraints durante migraciones]]
- [[Notes/Sección 6/10 - Validación y reconciliación|Validación y reconciliación]]
- [[Notes/Sección 6/11 - Laboratorio country y language|Laboratorio country y language]]
- [[Notes/Sección 6/12 - Laboratorio employees_s6|Laboratorio employees_s6]]
- [[Notes/Sección 6/13 - Migraciones de gran volumen|Migraciones de gran volumen]]
- [[Notes/Sección 6/14 - Errores comunes y correcciones|Errores comunes y correcciones]]
- [[Notes/Sección 6/15 - Retroalimentación del laboratorio|Retroalimentación del laboratorio]]
- [[Notes/Sección 6/16 - Práctica y chuleta rápida|Práctica y chuleta rápida]]
