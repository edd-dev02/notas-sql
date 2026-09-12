---
tags:
  - postgresql
  - sql/ddl
  - sql/dml
  - curso/seccion-3
---

# DELETE, TRUNCATE y DROP

Las tres sentencias eliminan algo, pero actúan en niveles distintos.

| Sentencia | Qué elimina | Conserva la tabla | Acepta `WHERE` |
|---|---|---:|---:|
| `DELETE` | Filas seleccionadas o todas | Sí | Sí |
| `TRUNCATE` | Todas las filas | Sí | No |
| `DROP TABLE` | La tabla completa y sus datos | No | No |

## DELETE

```sql
DELETE FROM users
WHERE name = 'Jorge'
RETURNING *;
```

Sin `WHERE`, elimina todas las filas:

```sql
DELETE FROM users;
```

## TRUNCATE

```sql
TRUNCATE TABLE users;
```

Vacía la tabla de forma directa. Si se desea reiniciar también las secuencias asociadas a columnas identidad o `SERIAL`:

```sql
TRUNCATE TABLE users RESTART IDENTITY;
```

## DROP TABLE

```sql
DROP TABLE users;
```

Elimina tanto los registros como la definición de la tabla. Después de ejecutarlo, `users` deja de existir.

## Regla para elegir

- Eliminar algunas filas: `DELETE ... WHERE ...`.
- Vaciar toda la tabla pero conservarla: `TRUNCATE`.
- Eliminar por completo la tabla: `DROP TABLE`.

> [!danger]
> Comprueba la tabla y la condición antes de ejecutar cualquiera de estas sentencias. Durante la práctica, una transacción permite revisar el resultado antes de confirmar con `COMMIT`.

## Relacionado

- [[05 - UPDATE y cambios seguros|UPDATE y cambios seguros]]
- [[01 - Crear tablas y restricciones|Crear tablas y restricciones]]

