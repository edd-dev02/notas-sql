---
tags:
  - postgresql
  - sql/debugging
  - curso/seccion-4
aliases:
  - Errores frecuentes de agregación
---

# Errores comunes y correcciones

Estas correcciones nacen de los ejercicios de la sección. Son útiles porque muestran consultas válidas que no necesariamente responden la pregunta planteada.

## 1. Alias contradictorio

```sql
-- Confuso
SELECT MAX(followers) AS min_followers
FROM users;

-- Correcto
SELECT MAX(followers) AS max_followers
FROM users;
```

El alias no cambia el cálculo, pero puede hacer que el reporte se interprete al revés.

## 2. Un rango no equivale a dos valores concretos

```sql
-- Solamente 4 o 4999
WHERE followers IN (4, 4999)

-- Cualquier valor desde 4 hasta 4999, ambos incluidos
WHERE followers BETWEEN 4 AND 4999
```

## 3. Pedir un conteo y devolver el listado

Si la pregunta es “¿cuántos usuarios de Iceland tienen IP `112.*`?”, la consulta debe agregar:

```sql
SELECT COUNT(*) AS total_users
FROM users
WHERE country = 'Iceland'
  AND last_connection LIKE '112.%';
```

Seleccionar nombres e IP responde “¿quiénes son?”, no “¿cuántos son?”.

## 4. Precedencia incorrecta de `AND` y `OR`

```sql
-- Riesgo: country solo se aplica necesariamente al primer LIKE
WHERE country = 'Iceland'
  AND last_connection LIKE '112.%'
  OR last_connection LIKE '28.%'
  OR last_connection LIKE '188.%'
```

Versión correcta:

```sql
WHERE country = 'Iceland'
  AND (
      last_connection LIKE '112.%'
      OR last_connection LIKE '28.%'
      OR last_connection LIKE '188.%'
  )
```

## 5. Ordenar por una métrica diferente

```sql
SELECT country, SUM(amount) AS total_amount
FROM sales_practice
GROUP BY country
ORDER BY COUNT(*) DESC;
```

La consulta es válida, pero ordena por cantidad de ventas, no por dinero vendido. Si se quiere un ranking por monto:

```sql
ORDER BY total_amount DESC;
```

## 6. Promedio manual

```sql
SUM(followers) / COUNT(followers)
```

Es mejor usar:

```sql
AVG(followers)
```

Es más explícito y evita problemas de tipos o de tratamiento de `NULL`.

## 7. Confundir `DISTINCT` con conteo por grupo

```sql
-- Solo países únicos
SELECT DISTINCT country
FROM users
WHERE email ILIKE '%@google.com';

-- Países y cantidad de cuentas en cada uno
SELECT country, COUNT(*) AS total_accounts
FROM users
WHERE email ILIKE '%@google.com'
GROUP BY country;
```

Ambas consultas son correctas, pero responden preguntas distintas.

## 8. Agrupar sin validar el dato transformado

Extraer un dominio de valores nulos o sin `@` puede generar grupos engañosos. Antes de transformar:

```sql
WHERE email IS NOT NULL
  AND POSITION('@' IN email) > 0
```

## Lista de comprobación

- ¿La consulta responde “cuáles” o “cuántos”?
- ¿Los alias describen realmente la expresión?
- ¿El `ORDER BY` usa la métrica solicitada?
- ¿Los límites de `BETWEEN` deben incluirse?
- ¿Los paréntesis representan la lógica verbal?
- ¿La condición corresponde a filas (`WHERE`) o grupos (`HAVING`)?

## Relacionado

- [[02 - Anatomía y orden de ejecución de SELECT|Anatomía de SELECT]]
- [[04 - BETWEEN IN y lógica de condiciones|Lógica de condiciones]]
- [[11 - Laboratorio sales_practice|Laboratorio sales_practice]]

