---
tags:
  - postgresql
  - modelado-de-datos
  - sql/constraints
  - curso/seccion-5
---

# Relaciones e integridad de datos

Una base relacional distribuye la información entre tablas y conecta sus filas mediante claves.

## Ejemplo básico

```text
country                         city
------------------              -------------------
code  PRIMARY KEY  ◄──────────  countrycode  FK
name                            id           PK
                                name
```

La relación expresa que muchas ciudades pueden pertenecer a un país:

```text
country 1 ─────── N city
```

## Tres niveles de integridad

### Integridad de entidad

Cada fila debe poder identificarse de forma estable. La protege principalmente la [[03 - PRIMARY KEY y claves compuestas|PRIMARY KEY]].

### Integridad de dominio

Los valores deben pertenecer al conjunto permitido para la columna. Se protege con tipos de datos, `NOT NULL`, `CHECK` y, según el caso, `UNIQUE`.

```sql
CHECK (population >= 0)
CHECK (percentage BETWEEN 0 AND 100)
```

### Integridad referencial

Una fila hija no debe apuntar a una fila padre inexistente. La protege una [[07 - FOREIGN KEY e integridad referencial|FOREIGN KEY]].

```sql
FOREIGN KEY (countrycode)
REFERENCES country(code)
```

## JOIN frente a FOREIGN KEY

Ambos conceptos se relacionan, pero cumplen funciones distintas:

- Un `JOIN` combina información al consultar.
- Una `FOREIGN KEY` protege la relación al insertar, actualizar o eliminar.

```sql
SELECT c.name, ci.name
FROM country AS c
JOIN city AS ci
    ON ci.countrycode = c.code;
```

El `JOIN` puede escribirse aunque no exista una FK, pero sin la restricción podrían existir ciudades huérfanas.

## Cardinalidades frecuentes

- Uno a uno: una fila se relaciona como máximo con una fila de la otra tabla.
- Uno a muchos: un país tiene muchas ciudades.
- Muchos a muchos: se representa mediante una tabla intermedia, como `countrylanguage`.

```text
country 1 ── N countrylanguage N ── 1 language
```

## Relacionado

- [[02 - Tipos de llaves|Tipos de llaves]]
- [[07 - FOREIGN KEY e integridad referencial|FOREIGN KEY]]

