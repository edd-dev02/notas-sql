---
tags:
  - postgresql
  - sql/agrupaciones
  - sql/funciones
  - curso/seccion-4
aliases:
  - Agrupar dominios de correo
---

# Agrupar por partes de strings

No es necesario agrupar únicamente por columnas completas. También puede agruparse por una expresión calculada, como el dominio extraído de un correo.

## Extraer el dominio

```sql
SELECT
    email,
    SUBSTRING(email FROM POSITION('@' IN email) + 1) AS email_domain
FROM users;
```

Para `ana@example.com`:

1. `POSITION('@' IN email)` encuentra la posición de `@`.
2. Se suma `1` para comenzar después del símbolo.
3. `SUBSTRING` devuelve el resto del texto.

## Contar usuarios por dominio

```sql
SELECT
    SUBSTRING(email FROM POSITION('@' IN email) + 1) AS email_domain,
    COUNT(*) AS total_users
FROM users
GROUP BY SUBSTRING(email FROM POSITION('@' IN email) + 1)
ORDER BY total_users DESC;
```

En PostgreSQL también puede emplearse el alias en este `GROUP BY` sencillo:

```sql
SELECT
    SUBSTRING(email FROM POSITION('@' IN email) + 1) AS email_domain,
    COUNT(*) AS total_users
FROM users
GROUP BY email_domain;
```

Repetir la expresión es más portable entre motores y evita ambigüedades si existe una columna real con el mismo nombre.

## Mostrar solo dominios repetidos

```sql
SELECT
    SUBSTRING(email FROM POSITION('@' IN email) + 1) AS email_domain,
    COUNT(*) AS total_users
FROM users
WHERE email IS NOT NULL
  AND POSITION('@' IN email) > 0
GROUP BY SUBSTRING(email FROM POSITION('@' IN email) + 1)
HAVING COUNT(*) > 1
ORDER BY total_users DESC, email_domain;
```

Los filtros adicionales evitan interpretar como dominios los valores nulos o sin `@`.

## Alternativa con `SPLIT_PART`

```sql
SELECT
    SPLIT_PART(email, '@', 2) AS email_domain,
    COUNT(*) AS total_users
FROM users
WHERE email LIKE '%@%'
GROUP BY SPLIT_PART(email, '@', 2)
HAVING COUNT(*) > 1
ORDER BY total_users DESC;
```

`SPLIT_PART` resulta muy legible cuando el separador es conocido.

## Dominios únicos

```sql
SELECT DISTINCT
    SUBSTRING(customer_email FROM POSITION('@' IN customer_email) + 1) AS domain
FROM sales_practice
ORDER BY domain;
```

## Relacionado

- [[Notes/Sección 3/06 - Funciones y operadores de texto|Funciones y operadores de texto]]
- [[05 - GROUP BY|GROUP BY]]
- [[07 - DISTINCT y valores únicos|DISTINCT]]
