---
tags:
  - postgresql
  - data-engineering
  - migracion-de-datos
  - performance
  - curso/seccion-6
aliases:
  - Migraciones en producción
---

# Migraciones de gran volumen

La lógica SQL de actualizar ocho filas y ochenta millones puede ser similar; la ingeniería operativa no lo es.

## Riesgos principales

- Bloqueos prolongados.
- Crecimiento del WAL.
- Retraso de réplicas.
- Mayor consumo de disco y E/S.
- Filas muertas y trabajo posterior de vacuum.
- Transacciones largas y recuperación costosa.
- Contención con tráfico de la aplicación.
- Tiempo de despliegue y rollback.

## Estrategia de alto nivel

### 1. Perfilar y limpiar

Analiza variantes, nulos, cardinalidad, distribución y volumen antes de crear el catálogo.

### 2. Definir recuperación

Coordina con el DBA respaldos, restauración, monitoreo, ventana de mantenimiento y criterios para detener el proceso.

### 3. Expandir el esquema

Agrega `country_id` inicialmente nullable sin retirar `customer_country`.

### 4. Crear y poblar el catálogo

Usa valores canónicos y una restricción `UNIQUE`.

### 5. Mantener ambas representaciones

Mientras dura la transición, nuevas escrituras deben mantener consistencia. Puede requerir cambios de aplicación, doble escritura temporal o un mecanismo controlado por el equipo.

### 6. Backfill por lotes

En lugar de una única transacción gigantesca, puede evaluarse actualizar rangos de PK o grupos limitados, confirmando y monitoreando entre lotes.

La forma exacta depende del sistema; no se debe improvisar un batching sin considerar concurrencia y filas que cambian durante el proceso.

### 7. Crear índices con estrategia operativa

```sql
CREATE INDEX CONCURRENTLY transactions_country_id_idx
ON transactions (country_id);
```

`CONCURRENTLY` reduce el bloqueo de escrituras, pero tarda más, hace trabajo adicional, no puede ejecutarse dentro de una transacción explícita y puede dejar un índice inválido si falla.

### 8. Validar y agregar la FK

```sql
ALTER TABLE transactions
ADD CONSTRAINT transactions_country_id_fkey
FOREIGN KEY (country_id)
REFERENCES countries(id)
NOT VALID;

ALTER TABLE transactions
VALIDATE CONSTRAINT transactions_country_id_fkey;
```

La posibilidad de separar creación y validación ayuda a planificar el impacto, pero no vuelve la operación gratuita.

### 9. Cambiar lecturas y escrituras

Migra aplicaciones, ETL, vistas y reportes hacia `country_id`. Monitorea errores y discrepancias.

### 10. Contraer

Cuando la nueva ruta esté estable, establece `NOT NULL` si corresponde y retira la columna textual en otro despliegue.

## Métricas a observar

- filas pendientes y filas actualizadas;
- tasa de errores o valores sin correspondencia;
- duración por lote;
- locks y consultas bloqueadas;
- WAL y retraso de réplica;
- espacio libre;
- latencia de lecturas y escrituras;
- avance de creación o validación de índices y constraints.

## Regla central

> [!important]
> El volumen convierte una transformación de datos en un problema de disponibilidad, observabilidad y recuperación, no solo de sintaxis SQL.

## Relacionado

- [[04 - Respaldos y estrategia de recuperación|Recuperación]]
- [[06 - Backfill y actualizaciones masivas|Backfill]]
- [[Notes/Sección 5/06 - Índices y creación concurrente|Índices concurrentes]]

