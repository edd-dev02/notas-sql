---
tags:
  - postgresql
  - sql/reportes
  - curso/seccion-4
---

# Patrones para reportes

Estas consultas condensan los patrones principales de la sección usando la tabla `users`.

## Usuarios por país

```sql
SELECT
    country,
    COUNT(*) AS total_users
FROM users
GROUP BY country
ORDER BY total_users DESC, country;
```

## Países con más de cinco usuarios

```sql
SELECT
    country,
    COUNT(*) AS total_users
FROM users
GROUP BY country
HAVING COUNT(*) > 5
ORDER BY total_users DESC, country;
```

## Seguidores mínimos, máximos y promedio

```sql
SELECT
    MIN(followers) AS minimum_followers,
    MAX(followers) AS maximum_followers,
    ROUND(AVG(followers), 2) AS average_followers
FROM users;
```

El mínimo o máximo indica un valor, no qué usuario lo tiene. Varias personas pueden compartirlo.

## Contar cuentas de un dominio

```sql
SELECT COUNT(*) AS google_accounts
FROM users
WHERE email LIKE '%@google.com';
```

Si el correo puede mezclar mayúsculas, usa `ILIKE` o normaliza el dato.

## Países de las cuentas de un dominio

```sql
SELECT
    country,
    COUNT(*) AS google_accounts
FROM users
WHERE email ILIKE '%@google.com'
GROUP BY country
ORDER BY google_accounts DESC, country;
```

## Usuarios de Iceland con determinados prefijos de IP

```sql
SELECT
    first_name,
    last_name,
    country,
    last_connection
FROM users
WHERE country = 'Iceland'
  AND (
      last_connection LIKE '112.%'
      OR last_connection LIKE '28.%'
      OR last_connection LIKE '188.%'
  )
ORDER BY last_name, first_name;
```

## Contar usuarios de Iceland cuyo IP comienza por `112.`

```sql
SELECT COUNT(*) AS total_users
FROM users
WHERE country = 'Iceland'
  AND last_connection LIKE '112.%';
```

## Países concretos y su población de usuarios

```sql
SELECT
    country,
    COUNT(*) AS total_users
FROM users
WHERE country IN ('Mexico', 'Honduras', 'Costa Rica')
GROUP BY country
ORDER BY country;
```

## Relacionado

- [[04 - BETWEEN IN y lógica de condiciones|BETWEEN, IN y lógica de condiciones]]
- [[06 - WHERE frente a HAVING|WHERE frente a HAVING]]
- [[11 - Laboratorio sales_practice|Laboratorio sales_practice]]

