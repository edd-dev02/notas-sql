---
tags:
  - postgresql
  - data-engineering
  - modelado-de-datos
  - performance
  - curso/seccion-5
---

# Criterios de diseño para Data Engineering

En ingeniería de datos, los constraints y los índices no son detalles decorativos: determinan la calidad, confiabilidad y operabilidad de los datos.

## Pensar primero en la regla

Antes de elegir sintaxis, formula la necesidad:

```text
¿Identidad?              → PRIMARY KEY
¿No debe repetirse?      → UNIQUE
¿Debe cumplir un rango?  → CHECK
¿Debe existir el padre?  → FOREIGN KEY
¿Debe existir un valor?  → NOT NULL
¿La consulta es lenta?   → medir y evaluar INDEX
```

## Identidad frente a dato de negocio

Un identificador artificial suele aislar las relaciones de cambios externos:

```sql
id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
passport_number text UNIQUE
```

Si cambia el pasaporte, no es necesario propagar el cambio por todas las tablas relacionadas.

## Constraints como barrera de calidad

Los datos pueden llegar desde:

- una aplicación;
- una API;
- un archivo CSV;
- un proceso ETL;
- un script manual;
- otra base de datos.

Validar solamente en una aplicación deja rutas sin protección. El constraint actúa en el último punto común: la base de datos.

## La PK compuesta y la FK responden preguntas distintas

```text
PRIMARY KEY (order_id, product_id)
→ ¿Está repetida esta combinación?

FOREIGN KEY (order_id) REFERENCES orders(id)
→ ¿Existe la orden?

FOREIGN KEY (product_id) REFERENCES products(id)
→ ¿Existe el producto?
```

Un buen modelo puede necesitar las tres restricciones simultáneamente.

## Índices guiados por carga de trabajo

Para una tabla de 50 millones de órdenes con la consulta frecuente:

```sql
SELECT *
FROM orders_s5
WHERE customer_id = 15000;
```

investigaría primero un índice en `customer_id`, mediría el plan con `EXPLAIN` y consideraría `CREATE INDEX CONCURRENTLY` si la tabla recibe escrituras en producción.

## Antes de crear un índice

- ¿La consulta ocurre con frecuencia?
- ¿La tabla es suficientemente grande para que importe?
- ¿La columna filtra una parte útil de las filas?
- ¿Existe ya un índice cuyo prefijo cubra el patrón?
- ¿Cuánto cuestan las escrituras y el almacenamiento adicionales?
- ¿El plan de ejecución confirma el problema?

## Cascadas y retención histórica

Una cascada puede ser correcta para datos dependientes sin vida propia, como detalles de una orden. Puede ser peligrosa si se necesita conservar información por auditoría, análisis histórico o cumplimiento.

En esos casos pueden considerarse:

- impedir el borrado;
- borrado lógico mediante estado o fecha;
- anonimización;
- archivado;
- tablas históricas.

## Flujo de razonamiento

```text
Regla de negocio
      ↓
Modelo y cardinalidad
      ↓
PK / UNIQUE / CHECK / FK / NOT NULL
      ↓
Datos existentes válidos
      ↓
Patrones reales de consulta
      ↓
Índices y medición
      ↓
Estrategia de despliegue y rollback
```

## Relacionado

- [[02 - Tipos de llaves|Tipos de llaves]]
- [[06 - Índices y creación concurrente|Índices]]
- [[09 - Cadenas de dependencias y CASCADE|Cadenas de dependencias]]

