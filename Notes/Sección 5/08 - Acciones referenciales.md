---
tags:
  - postgresql
  - sql/constraints
  - integridad-referencial
  - curso/seccion-5
aliases:
  - ON DELETE
  - ON UPDATE
---

# Acciones referenciales

Las cláusulas `ON DELETE` y `ON UPDATE` definen qué ocurre en las filas hijas cuando cambia la clave referenciada del padre.

## Opciones principales

| Acción | Comportamiento |
|---|---|
| `NO ACTION` | Rechaza la operación si al comprobar el constraint quedan referencias inválidas; es el valor predeterminado |
| `RESTRICT` | Rechaza inmediatamente la operación referenciada |
| `CASCADE` | Propaga el borrado o la actualización a las filas hijas |
| `SET NULL` | Coloca en `NULL` las columnas de la FK |
| `SET DEFAULT` | Coloca el valor predeterminado de las columnas de la FK |

`SET NULL` requiere que la columna admita `NULL`; `SET DEFAULT` solo funciona si el valor resultante también satisface la FK.

## CASCADE

```sql
ALTER TABLE city
ADD CONSTRAINT city_countrycode_fkey
FOREIGN KEY (countrycode)
REFERENCES country(code)
ON DELETE CASCADE
ON UPDATE CASCADE;
```

- Borrar un país elimina automáticamente sus ciudades.
- Cambiar su código actualiza las referencias de sus ciudades.

## SET NULL

```sql
FOREIGN KEY (manager_id)
REFERENCES employees(id)
ON DELETE SET NULL
```

Puede ser adecuado si la fila hija debe sobrevivir pero la relación se vuelve opcional.

## NO ACTION o RESTRICT

Son opciones conservadoras cuando borrar el padre con dependencias probablemente representa un error de negocio.

## Elegir por significado

- Pedido → detalles: `ON DELETE CASCADE` suele tener sentido; el detalle no existe sin el pedido.
- Producto → detalles históricos: probablemente convenga impedir el borrado o usar un borrado lógico, para conservar el historial.
- Empleado → supervisor: `SET NULL` puede reflejar que el empleado continúa sin supervisor asignado.

> [!danger]
> No agregues `CASCADE` mecánicamente. Una sola sentencia puede recorrer varias tablas y borrar muchas filas. La acción debe representar el ciclo de vida real de las entidades.

## Relacionado

- [[07 - FOREIGN KEY e integridad referencial|FOREIGN KEY]]
- [[09 - Cadenas de dependencias y CASCADE|Cadenas de dependencias]]

