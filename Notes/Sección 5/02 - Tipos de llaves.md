---
tags:
  - postgresql
  - modelado-de-datos
  - sql/keys
  - curso/seccion-5
aliases:
  - Tipos de claves
---

# Tipos de llaves

![[tipos_llaves.png]]

No todos los nombres de esta clasificación corresponden a constraints independientes de PostgreSQL. Algunos son conceptos de modelado que se implementan mediante `PRIMARY KEY`, `UNIQUE` o `FOREIGN KEY`.

## Superkey o superclave

Cualquier conjunto de columnas que identifica una fila de forma única, aunque incluya columnas innecesarias.

Si `id` identifica un producto, tanto `{id}` como `{id, name}` son superclaves; la segunda tiene información sobrante.

## Candidate key o clave candidata

Superclave mínima: si se elimina una de sus columnas, deja de identificar la fila de forma única.

Ejemplo: `customers.id` y quizá `customers.email` pueden ser claves candidatas si ambos son únicos y obligatorios.

## Primary key o clave primaria

La clave candidata elegida como identificador principal de la entidad. Debe ser única y no nula. Solo puede haber una PK por tabla, aunque incluya varias columnas.

```sql
PRIMARY KEY (id)
```

## Alternate key o clave alternativa

Clave candidata que no fue elegida como PK. Normalmente se implementa con `UNIQUE` y, si debe ser obligatoria, `NOT NULL`.

```sql
email text UNIQUE NOT NULL
```

## Composite key o clave compuesta

Clave formada por dos o más columnas. Puede ser primaria, candidata o foránea.

```sql
PRIMARY KEY (countrycode, language)
```

La combinación es única; cada columna por separado puede repetirse.

## Foreign key o clave foránea

Columna o conjunto de columnas que referencia una PK o clave única de otra tabla.

```sql
FOREIGN KEY (countrycode)
REFERENCES country(code)
```

## Natural key o clave natural

Proviene del dominio del negocio: número de pasaporte, RFC, correo o código oficial. Puede cambiar por razones externas y a veces contiene información sensible.

## Surrogate o artificial key

Identificador sin significado de negocio, controlado por el sistema:

```sql
id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY
```

> [!tip] Identidad estable
> Para muchas entidades conviene una PK artificial y estable, mientras la clave natural se protege con `UNIQUE`. Un pasaporte puede renovarse, corregirse o cambiar de formato; no es ideal que todas las relaciones dependan de él.

## Resumen de implementación

| Concepto | Implementación habitual |
|---|---|
| Primary key | `PRIMARY KEY` |
| Alternate/candidate no elegida | `UNIQUE` + posiblemente `NOT NULL` |
| Foreign key | `FOREIGN KEY ... REFERENCES` |
| Composite key | Constraint sobre varias columnas |
| Superkey | Concepto lógico; no requiere un tipo especial de constraint |
| Artificial key | `IDENTITY`, UUID u otro valor generado |

## Relacionado

- [[03 - PRIMARY KEY y claves compuestas|PRIMARY KEY y claves compuestas]]
- [[05 - UNIQUE y diferencia entre constraint e índice|UNIQUE]]

