---
tags:
  - postgresql
  - sql/filtros
  - curso/seccion-4
---

# BETWEEN, IN y lógica de condiciones

Estos operadores reducen las filas antes de agruparlas.

## `BETWEEN`: rango inclusivo

```sql
SELECT first_name, last_name, followers
FROM users
WHERE followers BETWEEN 4600 AND 4700
ORDER BY followers DESC;
```

`BETWEEN` incluye ambos límites. Es equivalente a:

```sql
WHERE followers >= 4600
  AND followers <= 4700
```

Para excluirlos:

```sql
WHERE followers > 4600
  AND followers < 4700
```

> [!warning]
> `BETWEEN 4 AND 4999` significa cualquier valor dentro del rango, no solamente `4` o `4999`.

## `IN`: pertenencia a una lista

```sql
SELECT country, first_name, last_name
FROM users
WHERE country IN ('Mexico', 'Honduras', 'Costa Rica')
ORDER BY country, first_name, last_name;
```

Es una forma compacta de escribir varias comparaciones con `OR` sobre la misma expresión.

## Combinar `AND` y `OR`

`AND` tiene mayor precedencia que `OR`. Los paréntesis hacen explícita la intención:

```sql
SELECT first_name, last_name, country, last_connection
FROM users
WHERE country = 'Iceland'
  AND (
      last_connection LIKE '112.%'
      OR last_connection LIKE '28.%'
      OR last_connection LIKE '188.%'
  )
ORDER BY last_name, first_name;
```

Sin esos paréntesis, las direcciones que empiecen por `28.` o `188.` podrían entrar aunque el país no sea Iceland.

## Filtro compuesto de ventas

```sql
SELECT *
FROM sales_practice
WHERE category = 'Technology'
  AND (
      amount > 10000
      OR units >= 5
  );
```

Primero exige la categoría y luego acepta cualquiera de las dos condiciones internas.

## `LIKE` para prefijos y sufijos

```sql
WHERE last_connection LIKE '221.%' -- comienza con 221.
WHERE email LIKE '%@google.com'    -- termina en @google.com
```

Consulta [[Notes/Sección 3/04 - LIKE y búsqueda por patrones|LIKE y búsqueda por patrones]] para los comodines y consideraciones de rendimiento.

## Relacionado

- [[06 - WHERE frente a HAVING|WHERE frente a HAVING]]
- [[10 - Patrones para reportes|Patrones para reportes]]
