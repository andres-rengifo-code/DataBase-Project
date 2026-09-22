# S7 — Taller: modelado E-R del proyecto (trabajo en equipo)

**Módulo 2, semana 4, sesión 7 · Taller sincrónico (videollamada, salas por grupo de proyecto)**

A partir de aquí, el trabajo deja de ser sobre casos genéricos: cada grupo modela **el dominio real de su proyecto**. Todos los grupos del curso trabajan sobre el mismo dominio — un sistema de gestión de biblioteca —, definido en [`../proyecto-final/definicion-proyecto-final.md`](../proyecto-final/definicion-proyecto-final.md). No hay elección libre de dominio: la decisión de fijar un dominio único para todo el curso busca dar retroalimentación consistente entre los ~20-26 grupos y asegurar un alcance que todos puedan terminar en el tiempo disponible. Lo que produce cada grupo — el modelo E-R — sí es propio; es insumo directo de S8 y del Hito 1 del proyecto integrador (`proyecto-final/definicion-proyecto-final.md`, secciones 7-8).

## Objetivo

Que cada grupo produzca un primer modelo E-R completo del sistema de biblioteca descrito en el documento de proyecto, aplicando todo lo visto en S5 (entidades, atributos, dominios) y S6 (relaciones, cardinalidad, participación, entidades débiles).

## Requisito previo

Cada grupo debe llegar a la sesión con el documento de definición del proyecto ya leído — en particular la sección 3 (alcance funcional y reglas de negocio) y la sección 4 (perfiles de usuario: lector, bibliotecario, administrador, auditor), que son el insumo directo de este taller.

## Mecánica (2 horas)

1. **(10 min)** Encuadre: recordatorio rápido del alcance funcional (sección 3 del documento de proyecto) y de los 4 perfiles de usuario (sección 4) — la tarea de este taller es traducir ese alcance a un modelo E-R completo, no inventar ni acotar un dominio. Como referencia, un modelo de este alcance suele quedar entre 5 y 8 conjuntos de entidades — ni tan simple que falte cubrir el alcance funcional, ni tan fragmentado que sea difícil de programar después.
2. **(20 min) Lluvia de entidades:** cada grupo, de forma individual primero (5 min, cada integrante propone su lista) y luego en conjunto (15 min), consolida la lista de conjuntos de entidades necesarios para cubrir el alcance funcional del documento de proyecto — libros, préstamos, los 4 perfiles de usuario, y lo que cada grupo considere necesario para las reglas de negocio y los reportes pedidos.
3. **(60 min) Modelado:** el grupo completa la **hoja de trabajo** (ver abajo) y dibuja el diagrama E-R correspondiente (papel, Excalidraw o draw.io — todavía no la herramienta CASE, eso es S8).
4. **(20 min) Autorrevisión cruzada:** cada grupo intercambia su diagrama con otro grupo (asignado por el profesor) y le da 2 comentarios breves usando la misma rúbrica de S6 (cardinalidad, participación, entidades débiles) — esto acelera la retroalimentación real de S8, porque cada grupo ya llega con una mirada externa.
5. **(10 min)** Cierre: dudas generales, se resuelven en el espacio de S8 si son puntuales de un grupo.

## Hoja de trabajo (entregable)

Una tabla como esta, completa, además del diagrama:

| Entidad | Atributos (marca el/los clave) | Relacionado con | Cardinalidad | Participación | ¿Entidad débil? |
|---|---|---|---|---|---|
| *(ej. Lector)* | *(id\*, nombre, correo, telefono)* | *(Préstamo)* | *(1:N)* | *(parcial — un lector puede no tener préstamos aún)* | *(no)* |

Esta hoja **es exactamente lo que se traduce a la sintaxis de dbdiagram.io en S8** — completarla bien aquí ahorra tiempo en el laboratorio siguiente.

## Entregable

**Un documento por grupo** con: (1) el diagrama E-R del sistema de biblioteca, (2) la hoja de trabajo completa, (3) los 2 comentarios recibidos del grupo que les revisó.

## Rúbrica (checklist, calificación por grupo)

| Criterio | 0 | 1 | 2 |
|---|---|---|---|
| **Cobertura y alcance del modelo** | Modelo incompleto (faltan entidades necesarias para el alcance funcional del documento de proyecto) o sobre-fragmentado (más de 10 entidades sin necesidad clara) | Cobertura aceptable pero con ajustes necesarios | Entre 5 y 8 conjuntos de entidades, cubre el alcance funcional del documento de proyecto |
| **Cardinalidad y participación aplicadas correctamente** | Errores frecuentes o ausentes | Aciertos parciales | Correctas en la gran mayoría de relaciones |
| **Hoja de trabajo completa** | Faltan varias columnas o entidades | Completa pero con vacíos menores | Completa para todas las entidades y relaciones |
| **Uso de la retroalimentación cruzada** | No se registran los comentarios recibidos | Se registran pero no se nota si aportaron | Comentarios registrados y con evidencia de que se consideraron |

Máximo 8 puntos por grupo. 