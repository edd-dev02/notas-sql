---
tags:
  - postgresql
  - sql/practica
  - curso/seccion-3
---

# Práctica y chuleta rápida

## Chuleta

```sql
-- Crear
CREATE TABLE users (
    id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name varchar(50) UNIQUE NOT NULL
);

-- Insertar
INSERT INTO users (name)
VALUES ('Jorge'), ('Valeria'), ('Michelle')
RETURNING *;

-- Consultar y filtrar
SELECT id, name
FROM users
WHERE name ILIKE '%a%'
ORDER BY id
LIMIT 5 OFFSET 0;

-- Actualizar
UPDATE users
SET name = 'Eduardo A'
WHERE name = 'Eduardo'
RETURNING *;

-- Eliminar filas específicas
DELETE FROM users
WHERE name = 'Jorge'
RETURNING *;

-- Vaciar la tabla
TRUNCATE TABLE users RESTART IDENTITY;

-- Eliminar la tabla
DROP TABLE users;
```

## Retos de práctica

- [ ] Crear `users` con `id`, `name`, `first_name` y `last_name`.
- [ ] Insertar cinco usuarios en una sola sentencia.
- [ ] Buscar nombres que comiencen con `M`, sin distinguir mayúsculas.
- [ ] Mostrar solo tres filas, ordenadas por `id`, omitiendo las dos primeras.
- [ ] Mostrar el nombre en mayúsculas y su longitud con alias claros.
- [ ] Actualizar una fila por su `id` y comprobarla con `RETURNING`.
- [ ] Separar nombre y apellido primero con `SUBSTRING` y luego con `SPLIT_PART`.
- [ ] Explicar con tus palabras cuándo usar `DELETE`, `TRUNCATE` y `DROP`.

## Preguntas de repaso

1. ¿Qué ocurre si ejecutas `UPDATE users SET name = 'Ana';` sin `WHERE`?
2. ¿Por qué `LIMIT` y `OFFSET` deberían acompañarse de `ORDER BY`?
3. ¿Qué diferencia hay entre los comodines `%` y `_`?
4. ¿`LIKE '%a%'` encuentra la palabra `Ana` en PostgreSQL? ¿Qué usarías para encontrarla?
5. ¿Por qué conviene probar una transformación de texto con `SELECT` antes de guardarla con `UPDATE`?

## Respuestas breves

1. Todas las filas intentan cambiar a `Ana`; una restricción `UNIQUE` también podría hacer fallar la sentencia.
2. Porque sin orden explícito no existe una secuencia estable de filas.
3. `%` representa cualquier cantidad de caracteres; `_`, exactamente uno.
4. No por la `A` mayúscula; usaría `ILIKE '%a%'`.
5. Permite revisar el resultado sin modificar los datos.

## Navegación

- [[00 - Sección 3 - Fundamentos de SQL|Volver al mapa de la sección]]
- [[SQL de cero - Tu guía práctica con PostgreSQL|Volver al índice general]]
