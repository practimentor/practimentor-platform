# ADR 2026-08-23-01: Un repositorio por servicio, bajo una organización

- **Estado:** aceptada
- **Fecha:** 2026-08-23

## Contexto

El proyecto se construye como microservicios en tres lenguajes. Había que
decidir si el código vive en un solo repositorio o en varios.

Conviene aclarar que **son dos cosas independientes**: microservicios describe
cómo se *ejecuta* el sistema (procesos y contenedores separados); monorepo o
polyrepo describe cómo se *guarda* el código. Empresas como Google o Uber corren
miles de microservicios desde un solo repositorio.

Restricción particular del curso: el docente califica revisando la evidencia en
GitHub —commits, Pull Requests, issues, Actions, releases—, así que dispersarla
tiene un costo real.

## Opciones consideradas

**Monorepo.** Un repositorio con `frontend/`, `backend/`, `rag/`, `infra/`.
Docker Compose, manifiestos y un solo pipeline de CI/CD viven juntos. Toda la
evidencia en un solo lugar. Contra: se aleja del modelo profesional de
microservicios que el equipo quiere practicar.

**Polyrepo.** Un repositorio por servicio. Cada uno con su CI y su ciclo de vida
independiente. Contra: multiplica el trabajo de CI/CD y dispersa la evidencia.

## Decisión

**Polyrepo**, con cinco repositorios agrupados en la organización `utp-agent`:

```text
utp-agent-platform      infraestructura, documentación, manifiestos
utp-agent-web           frontend Next.js
utp-agent-api-academic  backend C#
utp-agent-api-rag       servicio de agente Python
utp-agent-gateway       API Gateway (opcional, Sprint 4)
```

El problema de la evidencia dispersa se resuelve con un **Project a nivel de
organización**, que agrupa los issues de los cinco repositorios en un solo
tablero Kanban. El docente entra a un lugar y ve todo.

El repositorio `utp-agent-platform` es la pieza que sostiene el modelo: sin un
lugar común para Docker Compose, los manifiestos y la documentación, el polyrepo
no se aguanta.

## Consecuencias

**Positivas**

- Cada servicio tiene su ciclo de vida y su pipeline independiente, como en un
  entorno profesional real.
- Los permisos se pueden dar por repositorio.
- El nombre de cada repositorio identifica el servicio sin ambigüedad.

**Negativas**

- Cinco pipelines de CI que mantener en vez de uno.
- Cada repositorio necesita su propio `AGENTS.md`, `.gitignore` y configuración.
- Levantar el entorno completo exige clonar cinco repositorios.
- Un cambio que toca dos servicios requiere dos Pull Requests coordinados.
