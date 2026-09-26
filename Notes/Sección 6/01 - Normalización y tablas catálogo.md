---
tags:
  - postgresql
  - normalizacion
  - modelado-de-datos
  - curso/seccion-6
aliases:
  - Tablas catálogo
---

# Normalización y tablas catálogo

Una tabla catálogo concentra un conjunto controlado de valores y permite referenciarlos mediante un identificador.

## Problema inicial

```text
employees
id | name     | department | country
1  | Eduardo  | Technology | Mexico
2  | Andrea   | Finance    | Mexico
3  | Valeria  | Technology | Colombia
```

Los mismos textos se repiten y pueden adquirir variantes como `Mexico`, `México` o ` MEXICO`.

## Modelo normalizado

```text
departments                employees
id | name                  id | name    | department_id
1  | Finance               1  | Eduardo | 2
2  | Technology            2  | Andrea  | 1
      ▲                                      │
      └────────────── FOREIGN KEY ───────────┘
```

## Beneficios

- Evita repetir descripciones extensas.
- Centraliza la identidad del valor.
- Reduce variantes y errores tipográficos.
- Permite agregar atributos al catálogo.
- Protege las relaciones mediante FKs.
- Facilita renombrar una descripción sin actualizar millones de filas.

## Costos y decisiones

- Las consultas legibles necesitan `JOIN`.
- La migración requiere limpieza y validación.
- No todo texto repetido merece una tabla nueva.
- El identificador no corrige por sí solo significados duplicados o inconsistentes.

## Diseño recomendado

```sql
CREATE TABLE departments_s6 (
    id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name varchar(40) NOT NULL UNIQUE
);
```

`UNIQUE (name)` evita duplicar el mismo departamento en el catálogo. La PK artificial ofrece una identidad estable independiente del nombre.

## Recuperar la vista legible

```sql
SELECT
    e.name AS employee,
    d.name AS department
FROM employees_s6 AS e
JOIN departments_s6 AS d
    ON d.id = e.department_id;
```

La tabla operacional guarda IDs; el `JOIN` recupera el significado.

## Relacionado

- [[Notes/Sección 5/02 - Tipos de llaves|Tipos de llaves]]
- [[05 - INSERT SELECT y carga de catálogos|Cargar un catálogo]]
- [[10 - Validación y reconciliación|Validar la migración]]

