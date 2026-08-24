# ADR 2026-08-23-07: Multi-tenant diseñado ahora, aplicado en el Sprint 3

- **Estado:** aceptada
- **Fecha:** 2026-08-23

## Contexto

*Multi-tenancy* significa que un mismo sistema atiende a **varias instituciones
a la vez** sin que una vea los datos de la otra. Google Classroom es
multi-tenant: miles de colegios sobre los mismos servidores, cada uno viendo solo
lo suyo.

Es un **patrón de arquitectura**, no una tecnología: no hay nada que instalar.
Se decide en cómo se modelan los datos y cómo se verifican los permisos. **No
tiene ningún costo adicional de infraestructura** — son los mismos servidores y
los mismos contenedores.

Lo que sí cuesta es trabajo: cada tabla necesita su columna de institución, cada
consulta debe filtrar por ella, y cada prueba debe verificar que no se filtren
datos entre instituciones.

Restricción del proyecto: cuatro personas que además llevan otros cursos, y una
entrega final en la semana 18.

## Opciones consideradas

**No incluirlo.** Una sola institución. Modelo de datos más simple y máxima
velocidad. Contra: se pierde el argumento de "SaaS real", que tiene valor tanto
en el informe como en el portafolio profesional del equipo.

**Implementarlo completo desde el Sprint 1.** Aislamiento real y probado en cada
endpoint. El argumento más fuerte, pero cada endpoint y cada prueba cargan con
él desde el inicio. Riesgo real de no llegar a la semana 18.

**Diseñarlo ahora, aplicarlo después.**

## Decisión

**Toda entidad de negocio lleva una columna `TenantId` desde el primer día**,
pero el aislamiento estricto se activa en el **Sprint 3**.

La clave que hace viable esta decisión es técnica: el refactor caro es agregar
`TenantId` al esquema *tarde* —obliga a migraciones, a rellenar datos existentes
y a tocar cada consulta—. Si la columna está desde el inicio, activar el
aislamiento después son unas **20 líneas en un solo archivo**, gracias a los
*Global Query Filters* de Entity Framework Core:

```csharp
modelBuilder.Entity<Curso>()
    .HasQueryFilter(c => c.TenantId == _tenantProvider.Actual);
```

EF Core agrega automáticamente el `WHERE TenantId = ...` a **todas** las
consultas de esa entidad. No hay que tocar endpoint por endpoint.

Esto además encaja con el calendario de entregas: la APF1 es puro Git, la APF2 es
gestión y primeros módulos, la APF3 es CI/CD. El código de producto recién pesa
al final, así que activar el aislamiento en el Sprint 3 llega cómodo.

## Consecuencias

**Positivas**

- El equipo avanza rápido en los Sprints 1 y 2 sin cargar con el aislamiento.
- Se puede afirmar con honestidad que la base de datos está diseñada para
  multi-tenancy, lo cual es cierto.
- Si el ritmo no alcanza, el proyecto sigue siendo coherente sin activarlo: se
  documenta como trabajo futuro.

**Negativas**

- Durante los Sprints 1 y 2 existe una columna que no se usa. **No quitarla**
  aunque parezca inútil — quitarla es exactamente el error que vuelve caro el
  refactor. Queda anotado en el `AGENTS.md` del backend.
- Las pruebas de aislamiento se concentran todas en el Sprint 3.
- El servicio de agente debe filtrar la búsqueda vectorial por `tenant_id` y
  `course_id` desde el inicio, aunque el backend todavía no lo exija. Sin eso,
  activar el aislamiento después dejaría un hueco por donde se cruzarían
  documentos entre instituciones.
