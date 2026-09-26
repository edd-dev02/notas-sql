---
tags:
  - postgresql
  - sql/select
  - curso/seccion-4
---

# DISTINCT y valores únicos

`DISTINCT` elimina filas duplicadas del resultado de un `SELECT`.

## Una columna

```sql
SELECT DISTINCT country
FROM users
ORDER BY country;
```

Cada país aparece una sola vez.

## Varias columnas

```sql
SELECT DISTINCT country, city
FROM users;
```

La combinación completa debe ser única. Un país puede aparecer varias veces si está asociado con ciudades diferentes.

## Contar valores distintos

```sql
SELECT COUNT(DISTINCT country) AS country_count
FROM users;
```

## `DISTINCT` frente a `GROUP BY`

Si solo se necesitan valores únicos, `DISTINCT` comunica mejor la intención:

```sql
SELECT DISTINCT country FROM users;
```

Si además se necesita una estadística por valor, usa `GROUP BY`:

```sql
SELECT country, COUNT(*) AS total_users
FROM users
GROUP BY country;
```

## Países con cuentas de Google

Solo la lista de países:

```sql
SELECT DISTINCT country
FROM users
WHERE email LIKE '%@google.com'
ORDER BY country;
```

Países y cantidad de usuarios:

```sql
SELECT country, COUNT(*) AS total_users
FROM users
WHERE email LIKE '%@google.com'
GROUP BY country
ORDER BY total_users DESC;
```

Son preguntas parecidas, pero producen reportes diferentes.

## Relacionado

- [[05 - GROUP BY|GROUP BY]]
- [[08 - Agrupar por partes de strings|Agrupar por partes de strings]]

