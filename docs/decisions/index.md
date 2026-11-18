# Índice de decisiones de arquitectura

Cada ADR (*Architecture Decision Record*) registra **una** decisión importante:
qué problema había, qué opciones se evaluaron, qué se eligió y qué cuesta.

## Cómo funciona

**Los ADRs se agregan, nunca se reescriben.** Si una decisión cambia, se escribe
uno nuevo que dice a cuál supersede, y al viejo se le pone arriba una nota de
estado con el enlace al nuevo. El valor de un ADR está en ser registro
histórico: si se edita, deja de ser historia y pasa a ser solo documentación.

**Nomenclatura:** `YYYY-MM-DD-NN-slug.md`, donde `NN` es el orden dentro del
mismo día. Se citan en prosa como *ADR 2026-08-23-02*.

Esta tabla se actualiza a mano cada vez que se agrega un ADR.

## Decisiones

| ADR | Decisión | Estado |
|---|---|---|
| [2026-08-23-01](2026-08-23-01-polyrepo-bajo-organizacion.md) | Un repositorio por servicio, bajo la organización `practimentor` | Aceptada |
| [2026-08-23-02](2026-08-23-02-github-flow-ramas-personales.md) | GitHub Flow con ramas personales `dev/<nombre>` | Reemplazada por 2026-11-18-02 |
| [2026-08-23-03](2026-08-23-03-csharp-en-vez-de-java.md) | C# .NET 10 para el backend académico | Aceptada |
| [2026-08-23-04](2026-08-23-04-proveedor-ia-detras-de-interfaz.md) | Proveedor de IA detrás de una interfaz propia, Gemini por defecto | Aceptada |
| [2026-08-23-05](2026-08-23-05-azure-en-vez-de-aws.md) | Azure para el despliegue en nube | Aceptada |
| [2026-08-23-06](2026-08-23-06-postgresql-y-qdrant.md) | PostgreSQL y Qdrant como almacenes separados | Aceptada |
| [2026-08-23-07](2026-08-23-07-multi-tenant-diferido.md) | Multi-tenant diseñado ahora, aplicado en el Sprint 3 | Aceptada |
| [2026-11-18-01](2026-11-18-01-cambio-de-producto-a-practimentor.md) | El producto pasa a ser PractiMentor; se renombran la organización y los repositorios | Aceptada |
| [2026-11-18-02](2026-11-18-02-github-flow-ramas-cortas.md) | GitHub Flow con ramas cortas por tarea | Aceptada |

## Cómo escribir uno nuevo

1. Copia la estructura de cualquiera de los existentes.
2. Nómbralo con la fecha de hoy y el siguiente `NN` de ese día.
3. Agrega su fila a la tabla de arriba.
4. Si supersede a otro, dilo en el nuevo **y** anota el estado en el viejo.

Secciones que debe tener: **Contexto** (el problema y sus restricciones),
**Opciones consideradas** (las alternativas reales con su costo), **Decisión**
(qué se eligió) y **Consecuencias** (lo bueno y lo malo, sin maquillar).

Un ADR sin la sección de consecuencias negativas casi siempre está incompleto:
toda decisión de arquitectura cuesta algo.
