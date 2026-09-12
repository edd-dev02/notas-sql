---
tags:
  - postgresql
  - sql/funciones
  - curso/seccion-3
---

# Funciones y operadores de texto

Un `SELECT` no solo recupera columnas: también puede calcular expresiones y transformar valores sin modificar los datos guardados.

## Ejemplo general

```sql
SELECT
    id,
    UPPER(name) AS upper_name,
    LENGTH(name) AS length_name,
    20 * 2 AS constante,
    CONCAT(id, ' ', name) AS concat_func,
    '-' || id || ' ' || name || '-' AS concat_pipe,
    name
FROM users;
```

| Expresión | Resultado |
|---|---|
| `UPPER(name)` | Convierte el texto a mayúsculas |
| `LENGTH(name)` | Cuenta caracteres |
| `20 * 2` | Calcula un valor constante por cada fila |
| `CONCAT(...)` | Une varios valores |
| `valor1 \|\| valor2` | Operador de concatenación |
| `AS alias` | Asigna un nombre a la columna calculada |

## `CONCAT` frente a `||`

La diferencia más importante aparece con `NULL`:

- `CONCAT('A', NULL, 'B')` produce `AB`.
- `'A' || NULL || 'B'` produce `NULL`.

## Extraer fragmentos

```sql
SELECT
    name,
    SUBSTRING(name FROM 1 FOR 4) AS first_four,
    POSITION(' ' IN name) AS space_position,
    TRIM(name) AS clean_name
FROM users;
```

- `SUBSTRING` extrae una parte del texto.
- `POSITION` devuelve la ubicación de una subcadena; en PostgreSQL la primera posición es `1`. Devuelve `0` si no encuentra coincidencia.
- `TRIM` elimina espacios al inicio y al final.

> [!note] Sobre `SUBSTRING(name, 0, 5)`
> PostgreSQL permite esa sintaxis y devuelve cuatro caracteres porque las posiciones se cuentan desde `1`. `SUBSTRING(name FROM 1 FOR 4)` expresa la intención con mayor claridad.

## Relacionado

- [[07 - Separar nombre y apellido|Separar nombre y apellido]]
- [[03 - SELECT filtros y paginación|SELECT, filtros y paginación]]

