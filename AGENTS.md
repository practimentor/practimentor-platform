# AGENTS.md — utp-agent-platform

Repositorio central del proyecto **utp-agent**: infraestructura, documentación y
manifiestos de Kubernetes. Aquí no vive código de aplicación.

## Si alguien se presenta por primera vez

Cuando el usuario diga algo como "hola, soy Mafer":

1. Lee `docs/onboarding.md` y ubica su sección.
2. Ejecuta los comandos de verificación para saber qué le falta instalar.
3. Guíalo instalando **una herramienta a la vez**, esperando que confirme antes
   de seguir. No le des todos los comandos de golpe.
4. Cuando el entorno esté listo, pídele el número de issue asignado y trabaja
   solo dentro de lo que ese issue autoriza.
5. Si algo no está en el issue, detente y dile que comente en el issue.

## Git lo ejecuta la persona, no tú

No ejecutes `git add`, `commit`, `push`, `pull`, `merge`, `rebase`, `reset` ni
`checkout` por tu cuenta. Puedes leer con `status`, `log`, `diff` y `show`.

Cuando haga falta una operación de escritura, **no digas "haz commit"**. Escribe
el comando exacto y completo, listo para copiar, con el mensaje de commit ya
redactado en formato convencional, y explica en una línea qué hace:

```bash
git add docs/decisions/0007-elegir-broker.md
git commit -m "docs(adr): registrar eleccion de RabbitMQ como broker"
git push origin docs/7-adr-broker
```

Solo ejecútalos tú si la persona te lo pide explícitamente.

## Comandos

Todavía no hay infraestructura creada. Se irá poblando por sprint:

```bash
# Sprint 4 — levantar el entorno completo en local
docker compose --profile full up -d

# Sprint 4 — desplegar en el clúster local
kubectl apply -k infra/kubernetes/overlays/local

# Sprint 3 — desplegar en Azure
kubectl apply -k infra/kubernetes/overlays/azure
```

## Estructura

```text
docs/
├── plan-proyecto.md      Plan completo del proyecto
├── onboarding.md         Guía de entrada por integrante
├── arquitectura.md       Diagrama y descripción del sistema
├── riesgos.md            Riesgos técnicos y su mitigación
├── ideas-parqueadas.md   Ideas fuera del alcance actual
├── decisions/            ADRs, uno por decisión de arquitectura
├── actas/                Actas de planning y retrospectiva
└── entregas/             Informes y presentaciones de cada APF
infra/
├── compose.yaml          Docker Compose con perfiles
└── kubernetes/
    ├── base/             Manifiestos comunes
    └── overlays/         local (k3d), azure (AKS)
```

## Convenciones

**Estrategia de ramificación: GitHub Flow con ramas personales**
(ver `docs/decisions/2026-08-23-02-github-flow-ramas-personales.md`).

- `main` es la **única** rama permanente compartida y siempre debe quedar ejecutable
- **No existe rama `develop`** ni ramas `release/*`
- Cada integrante trabaja en su rama propia: `dev-qa/andre`, `dev/mafer`,
  `dev/sergio`, `dev/nicolas`. La rama **no se borra** al fusionar
- Antes de cada tarea nueva se trae `main` a la rama propia (`git merge origin/main`)
- Un Pull Request **por tarea**, con `Closes #NN`, aunque salgan de la misma rama
- Toda integración entra por Pull Request con una aprobación
- Entregas marcadas con tags anotados sobre `main`: `v0.1.0`, `v0.2.0`, `v0.3.0`, `v1.0.0`
- Commits en formato convencional: `feat:`, `fix:`, `docs:`, `ci:`, `chore:`
- Los ADRs se agregan, **nunca se reescriben**: si una decisión cambia, se escribe
  uno nuevo que dice a cuál supersede. Nomenclatura `YYYY-MM-DD-NN-slug.md`

## Prohibido sin confirmación explícita

- No modificar nada dentro de `infra/`.
- No modificar workflows de `.github/`.
- No agregar ni actualizar dependencias.
- No renombrar archivos ni carpetas existentes.
- No crear secretos, claves ni archivos `.env` con valores reales.
- Si crees que necesitas hacer algo de esta lista, detente y comenta en el issue.
