---
tags:
  - postgresql
  - sql/select
  - curso/seccion-4
---

# Anatomía y orden de ejecución de SELECT

## Orden de escritura

```sql
SELECT DISTINCT
    columnas,
    expresiones AS alias,
    funciones
FROM tabla
JOIN otra_tabla ON condición_de_unión
WHERE condición_sobre_filas
GROUP BY columnas_o_expresiones
HAVING condición_sobre_grupos
ORDER BY expresión ASC
LIMIT cantidad
OFFSET inicio;
```

No todas las cláusulas son obligatorias. Deben aparecer en ese orden cuando se utilizan.

## Orden lógico de procesamiento

El motor no razona en el mismo orden en el que escribimos la consulta:

| Paso | Cláusula | Trabajo principal |
|---:|---|---|
| 1 | `FROM` / `JOIN` | Obtiene y combina las filas de origen |
| 2 | `WHERE` | Descarta filas |
| 3 | `GROUP BY` | Forma grupos |
| 4 | `HAVING` | Descarta grupos |
| 5 | `SELECT` | Calcula las columnas de salida |
| 6 | `DISTINCT` | Elimina filas duplicadas del resultado |
| 7 | `ORDER BY` | Ordena |
| 8 | `LIMIT` / `OFFSET` | Recorta el resultado |

Este orden explica por qué un alias creado en `SELECT` suele estar disponible en `ORDER BY`, pero no en `WHERE`.

## Ejemplo completo

```sql
SELECT
    country,
    COUNT(*) AS total_users
FROM users
WHERE email LIKE '%@google.com'
GROUP BY country
HAVING COUNT(*) > 1
ORDER BY total_users DESC, country ASC
LIMIT 10 OFFSET 0;
```

## Alias

```sql
SELECT
    country,
    COUNT(*) AS total_users
FROM users
GROUP BY country
ORDER BY total_users DESC;
```

Los alias hacen más legible el reporte. En PostgreSQL pueden utilizarse en `ORDER BY` y, con ciertas reglas, en `GROUP BY`; para evitar ambigüedades, es frecuente agrupar por la columna o repetir la expresión.

## `ASC`, `DESC` y estabilidad

- `ASC`: orden ascendente; es el valor predeterminado.
- `DESC`: orden descendente.
- Puede haber varios criterios; el segundo resuelve empates del primero.

```sql
ORDER BY country ASC, first_name ASC, last_name ASC;
```

> [!tip]
> Para paginar de forma estable, agrega al final una columna única como criterio de desempate.

## Relacionado

- [[06 - WHERE frente a HAVING|WHERE frente a HAVING]]
- [[10 - Patrones para reportes|Patrones para reportes]]

