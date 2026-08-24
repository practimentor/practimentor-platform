# Onboarding — utp-agent

Guía de entrada al proyecto. Busca tu sección, sigue los pasos en orden y no
avances hasta que el anterior te haya funcionado.

Si usas un agente (Antigravity, Claude Code), preséntate — *"hola, soy Mafer"* —
y él te guiará por esta guía paso a paso.

---

## Para todos, antes que nada

### 1. Herramientas comunes

Abre **PowerShell** y ejecuta uno por uno. Si alguno ya está instalado, winget te
lo dirá y no pasa nada.

```powershell
winget install Git.Git
```

```powershell
winget install GitHub.cli
```

```powershell
winget install Docker.DockerDesktop
```

Cierra la terminal, abre una nueva y comprueba que todo responde:

```powershell
git --version; gh --version; docker --version
```

Si alguno dice *"no se reconoce"*, no está instalado o no reiniciaste la
terminal.

### 2. Identifícate en Git

Reemplaza con tus datos reales. El correo debe ser el mismo de tu cuenta de
GitHub, o tus commits no se asociarán a tu perfil — y eso importa, porque el
docente revisa quién contribuyó.

```powershell
git config --global user.name "Tu Nombre"
```

```powershell
git config --global user.email "tu-correo@utp.edu.pe"
```

### 3. Conecta GitHub

```powershell
gh auth login
```

Elige: `GitHub.com` → `HTTPS` → `Y` (autenticar Git) → `Login with a web browser`.

### 4. Acepta la invitación

Revisa tu correo o entra a `github.com/utp-agent`. Sin aceptar la invitación no
puedes clonar los repositorios: son privados.

### 5. Crea tu rama personal

**Haz esto una sola vez**, después de clonar tu repositorio y antes de empezar a
trabajar. De ahí en adelante vives en esa rama.

Cada uno tiene la suya:

| Persona | Rama |
|---|---|
| André | `dev/andre` |
| Mafer | `dev/mafer` |
| Sergio | `dev/sergio` |
| Nicolás | `dev/nicolas` |

Desde la carpeta de tu repositorio:

```powershell
git checkout main
```

```powershell
git pull origin main
```

```powershell
git checkout -b dev/mafer
```

```powershell
git push -u origin dev/mafer
```

Ese `-u` vincula tu rama local con la del servidor. Gracias a eso, de ahora en
adelante te basta con escribir `git push` a secas.

Comprueba que estás donde debes:

```powershell
git branch --show-current
```

Debe responder con tu rama, no con `main`.

---

## Mafer — Frontend

Repositorio: **`utp-agent-web`** · Tecnología: Next.js + TypeScript

### Instala

```powershell
winget install OpenJS.NodeJS.LTS
```

Verifica (debe decir v20 o superior):

```powershell
node --version
```

### Clona

```powershell
cd F:\
```

```powershell
git clone https://github.com/utp-agent/utp-agent-web.git
```

### Qué te toca

La interfaz completa: inicio de sesión, listado de cursos, vista de materiales y
la ventana de conversación con el agente.

**Lo que nunca va en tu parte:** claves de API, reglas de permisos ni conexiones
directas a la base de datos. Todo eso vive en el backend. El navegador puede leer
cualquier cosa que escribas aquí.

---

## Sergio — Backend

Repositorio: **`utp-agent-api-academic`** · Tecnología: C# .NET 10 + ASP.NET Core

### Instala

```powershell
winget install Microsoft.DotNet.SDK.10
```

Verifica. **Tiene que aparecer una versión 10.x en la lista:**

```powershell
dotnet --list-sdks
```

Si la lista sale vacía, tienes runtimes pero no SDK — no vas a poder compilar.
Reinstala.

Y la herramienta de migraciones de base de datos:

```powershell
dotnet tool install --global dotnet-ef
```

### Clona

```powershell
git clone https://github.com/utp-agent/utp-agent-api-academic.git
```

### Qué te toca

La API que manda en el sistema: usuarios, roles, cursos, matrículas y permisos.
Cualquier otro servicio confía en lo que tu API autoriza.

**Regla que no se negocia:** las contraseñas y cadenas de conexión **nunca** van
en `appsettings.json`. En desarrollo se usa `dotnet user-secrets`, que las guarda
fuera del repositorio.

---

## Nicolás — Servicio de agente

Repositorio: **`utp-agent-api-rag`** · Tecnología: Python + FastAPI

### Instala

```powershell
winget install Python.Python.3.12
```

```powershell
winget install astral-sh.uv
```

Verifica:

```powershell
python --version; uv --version
```

### Clona

```powershell
git clone https://github.com/utp-agent/utp-agent-api-rag.git
```

### Qué te toca

El agente: recibe una pregunta, busca evidencia en los PDFs del curso y responde
**citando el documento y la página**.

**Regla que no se negocia:** si no encuentra evidencia, debe decirlo. Nunca
inventar. Hay una verificación en el código que rechaza respuestas que no se
apoyaron en una búsqueda real.

---

## André — Líder técnico

Repositorios: **todos**, más `utp-agent-platform`

Infraestructura, Docker, Kubernetes, CI/CD, configuración de Keycloak, revisión
de todos los Pull Requests y diseño del agente.

---

## Cómo se trabaja, todos los días

El modelo se llama **GitHub Flow** y cabe en una frase: *`main` siempre funciona,
y cada quien trabaja en su propia rama*. No hay rama `develop` ni ramas de
release — si viste eso en algún tutorial, es **Git Flow**, un modelo distinto que
aquí no usamos. El porqué está en
`docs/decisions/2026-08-23-02-github-flow-ramas-personales.md`.

```text
main ───●──────────●──────────●──────────●────►   siempre desplegable
       ↗ ↘        ↗ ↘        ↗ ↘
   dev/mafer   dev/sergio  dev/nicolas   dev/andre
```

### 1. Toma un issue

Ve al tablero de la organización, elige un issue asignado a ti y muévelo a
*En progreso*.

### 2. Actualiza tu rama antes de empezar

**No te saltes este paso.** Trae a tu rama lo que otros ya fusionaron a `main`.
Si no lo haces, tu rama se va alejando y los conflictos se acumulan hasta
volverse un problema serio.

```powershell
git checkout dev/mafer
```

```powershell
git fetch origin
```

```powershell
git merge origin/main
```

Si aparece un conflicto aquí, resuélvelo ahora — es mucho más fácil que
resolverlo después junto con todo lo demás.

### 3. Trabaja

Solo en los archivos que el issue autoriza. Si crees que necesitas tocar otro,
**detente y coméntalo en el issue**.

### 4. Guarda tu avance

```powershell
git add <los-archivos-que-cambiaste>
```

```powershell
git commit -m "feat(web): agregar tarjeta de curso al listado"
```

El mensaje sigue el formato `tipo(ambito): descripcion`. Tipos: `feat` para algo
nuevo, `fix` para un arreglo, `docs` para documentación, `test` para pruebas.

### 5. Súbelo y abre el Pull Request

```powershell
git push
```

Como al crear tu rama usaste `-u`, `git push` a secas ya sabe a dónde va.

Luego entra a GitHub, abre el Pull Request **de tu rama hacia `main`** y
asígnaselo a André para revisión. En la descripción escribe `Closes #12` para que
el issue se cierre solo al fusionar.

**Un Pull Request por tarea, no uno por semana.** Aunque siempre salgan de la
misma rama, cada PR debe resolver un solo issue. Un PR con seis tareas mezcladas
no lo revisa nadie, y si una está mal se bloquean las otras cinco.

### 6. Espera la revisión

Si André pide cambios, los haces en la misma rama y vuelves a hacer commit y
push. El Pull Request se actualiza solo.

---

## Reglas que aplican a todos

- **Nunca subas** archivos `.env`, contraseñas, claves de API ni cadenas de
  conexión. Si dudas, pregunta antes de hacer commit.
- **Nunca trabajes directo sobre `main`.** Tu sitio es `dev/<tu-nombre>`.
- **Un issue, un Pull Request.** La rama es siempre la misma; lo que cambia es
  que cada PR resuelve una sola cosa.
- **Trae `main` a tu rama antes de cada tarea nueva.** Es el paso que evita que
  los conflictos se conviertan en un problema grande.
- Si algo no está claro, **coméntalo en el issue** en vez de improvisar. Preguntar
  cuesta cinco minutos; deshacer un cambio equivocado cuesta una tarde.

## Si algo se rompe

1. `git status` te dice en qué estado estás.
2. Copia el mensaje de error completo y pégaselo a tu agente.
3. Si sigue sin salir, comenta en el issue con el error y etiqueta a André.

No borres carpetas ni vuelvas a clonar para "empezar de cero" — casi siempre hay
una forma de arreglarlo sin perder tu trabajo.
