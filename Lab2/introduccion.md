# S8 · Laboratorio: retroalimentación + herramienta CASE (dbdiagram.io) + generación de DDL

**Módulo 2, semana 4, sesión 8 · Laboratorio sincrónico (videollamada, salas por grupo de proyecto)**

**Checkpoint del proyecto (hito 1):** al final de esta sesión cada grupo debe tener un script DDL de PostgreSQL, generado a partir de su modelo E-R, como punto de partida para M3 (fundamentos del modelo relacional) y M4 (creación del esquema real en S14).

## Objetivo

Formalizar el modelo E-R de S7 en una herramienta CASE liviana (dbdiagram.io) y generar el DDL correspondiente, cerrando el ciclo modelo conceptual → esquema ejecutable.

## Herramienta

**[dbdiagram.io](https://dbdiagram.io/)**: web, sin instalación, la cuenta gratuita es suficiente. Se modela con un lenguaje simple (DBML) y se exporta el DDL para PostgreSQL con un clic (`Export` → `PostgreSQL`).

*(Alternativa si prefieres una herramienta instalable con motor propio: [pgModeler](https://pgmodeler.io/), open source y específico para PostgreSQL; ver `plan-curso.md`, sección 9.)*

## Antes de escribir DBML: tres reglas mínimas de traducción

DBML **no** es un lenguaje E-R: en DBML se escriben directamente **tablas**. No hay rombos para las relaciones ni una forma de marcar una entidad débil. Por eso, al pasar el diagrama de S7 a DBML ya se está traduciendo el modelo conceptual a tablas. La traducción completa y rigurosa es el tema de M3 (S11); para este laboratorio bastan tres reglas:

1. **Relación 1:N → la clave foránea va en la tabla del lado "muchos".** Si un programa tiene muchos estudiantes, `estudiantes` lleva una columna `programa_id` que apunta a `programas`. Nunca al revés: `programas` no puede guardar "la lista" de sus estudiantes en una columna.
2. **Relación N:M → una tabla intermedia, escrita a mano.** Si un pedido incluye varios productos y un producto aparece en varios pedidos, se crea una tabla `detalle_pedido` con una columna que apunta a cada lado. La clave primaria de esa tabla es la **pareja** de columnas. Los atributos de la relación (la `cantidad` de cada producto en el pedido) son columnas de esta tabla, y es la única forma de guardarlos.
3. **Entidad débil → clave primaria compuesta.** La tabla de la entidad débil lleva la clave de su entidad fuerte (como clave foránea) **más** su clave parcial, y la clave primaria es la combinación de ambas. `Aula` (débil de `Edificio`) se identifica con (`codigo_edificio`, `numero`).

**Sobre el atajo `<>`:** DBML permite escribir `Ref: pedidos.id <> productos.id` y dbdiagram.io genera sola la tabla intermedia al exportar. Esa tabla automática **no admite atributos propios**, así que en este curso se pide escribir siempre la tabla intermedia a mano (regla 2). Así queda visible en el diagrama y se le pueden agregar columnas, como se necesitará en el proyecto (un préstamo tiene fechas).

### Sintaxis mínima de DBML (lo que se necesita para este laboratorio)

El ejemplo usa las tres reglas: un cliente hace muchos pedidos (1:N), un pedido incluye muchos productos y viceversa, con la cantidad como atributo de la relación (N:M), y las aulas son entidad débil de los edificios (el ejemplo de S6).

```dbml
// Regla 1: relación 1:N, la FK va en el lado "muchos" (pedidos)
Table clientes {
  cedula varchar [primary key]
  nombre varchar [not null]
  correo varchar [unique]
}

Table pedidos {
  id integer [primary key]
  fecha date [not null]
  cedula_cliente varchar [not null]   // FK hacia clientes
}

Ref: pedidos.cedula_cliente > clientes.cedula

// Regla 2: relación N:M con atributo, tabla intermedia escrita a mano
Table productos {
  id integer [primary key]
  nombre varchar [not null]
  precio integer [not null]
}

Table detalle_pedido {
  pedido_id integer
  producto_id integer
  cantidad integer [not null]         // atributo de la relación
  indexes {
    (pedido_id, producto_id) [pk]     // la PK es la pareja
  }
}

Ref: detalle_pedido.pedido_id > pedidos.id
Ref: detalle_pedido.producto_id > productos.id

// Regla 3: entidad débil, PK compuesta (clave de la fuerte + clave parcial)
Table edificios {
  codigo varchar [primary key]
  nombre varchar [not null]
}

Table aulas {
  codigo_edificio varchar
  numero varchar                      // clave parcial
  capacidad integer [not null]
  indexes {
    (codigo_edificio, numero) [pk]
  }
}

Ref: aulas.codigo_edificio > edificios.codigo [delete: cascade]
```

- `Table nombre { ... }` define una tabla con sus columnas.
- `[primary key]` (o `[pk]`) marca la clave primaria de una sola columna; `indexes { (a, b) [pk] }` marca una clave primaria compuesta.
- `[not null]` y `[unique]` se exportan tal cual como restricciones de PostgreSQL. Úsenlos para los atributos obligatorios y para los identificadores alternativos (un correo, un ISBN).
- `Ref: A.campo > B.campo` se lee "muchos `A` apuntan a un `B`": `A.campo` es la clave foránea. `[delete: cascade]` hace que, al borrar la fila de `B`, se borren sus filas en `A` (útil en la entidad débil: si desaparece el edificio, desaparecen sus aulas).

No se espera que el DDL de hoy quede perfecto: en S11 cada grupo lo revisa contra las reglas completas de traducción y le agrega las restricciones que falten.

## Mecánica (2 horas)

1. **(15 min) Retroalimentación rápida:** cada grupo revisa los 2 comentarios que recibió en S7 y ajusta su hoja de trabajo si hace falta, **antes** de empezar a construir en la herramienta (evita rehacer trabajo dentro de la herramienta).
2. **(25 min)** Demo en vivo del profesor, en dos partes:
   - **(10 min)** Las tres reglas mínimas de traducción (sección anterior), dibujando sobre el diagrama E-R del ejemplo cómo queda cada relación como tabla.
   - **(15 min)** El mismo ejemplo en dbdiagram.io de principio a fin, exportando el DDL y mostrando en el `.sql` dónde quedó cada regla: la FK de `pedidos`, la tabla `detalle_pedido` con su PK compuesta, la PK compuesta de `aulas`. Vale la pena mostrar también qué genera `<>` y por qué pierde la `cantidad`.
3. **(20 min) Marcado de la hoja de trabajo:** antes de abrir la herramienta, cada grupo marca en su hoja de trabajo de S7 qué regla le corresponde a cada relación: en cuál tabla va la FK de cada 1:N, qué tabla intermedia (con qué atributos) genera cada N:M, y cuál es la PK compuesta de cada entidad débil. Así la traducción se decide en el grupo y no a medias dentro del editor.
4. **(45 min) Construcción:** cada grupo escribe su modelo en DBML en dbdiagram.io, siguiendo la hoja marcada. El profesor/monitor circula por las salas resolviendo dudas de sintaxis y revisando que las N:M estén como tabla intermedia.
5. **(5 min)** Exportar: cada grupo exporta el DDL de PostgreSQL y el diagrama como imagen/PDF.
6. **(10 min)** Cierre: 1-2 grupos comparten pantalla mostrando su diagrama terminado; el profesor recuerda que este script es la base de M3 (en S11 se revisa y completa con todas las reglas de traducción y restricciones) y de S14 (creación del esquema real).

## Entregable (checkpoint del proyecto)

**Por grupo:**

1. Enlace público (o exportado) al diagrama en dbdiagram.io.
2. Script DDL generado (`.sql`).
3. Una lista corta (3-5 líneas) de qué cambió respecto al diagrama de S7 a raíz de la retroalimentación recibida.

## Rúbrica (checklist, calificación por grupo)

| Criterio | 0 | 1 | 2 |
|---|---|---|---|
| **El diagrama refleja la hoja de trabajo de S7** | Faltan entidades o relaciones importantes | Coincide en su mayoría, con omisiones menores | Coincide completamente (incluyendo los ajustes de la retroalimentación) |
| **DDL generado sin errores** | No se logró exportar o el script tiene errores evidentes | Se exportó con advertencias menores | Script limpio, ejecutable tal cual |
| **Relaciones correctamente traducidas** | Relaciones ausentes, FK en el lado equivocado, o N:M sin tabla intermedia | La mayoría correctas; alguna N:M con `<>` que pierde atributos, o una entidad débil sin PK compuesta | Todas las 1:N con la FK en el lado "muchos", todas las N:M como tabla intermedia con sus atributos, y las entidades débiles (si hay) con PK compuesta |
