# Onboarding — PractiMentor

Guía de entrada al proyecto. Sigue los pasos en orden y no avances hasta que el anterior te haya funcionado.

## 1. Herramientas comunes

En **PowerShell**, uno por uno (si ya está instalado, winget lo indica):

```powershell
winget install Git.Git
winget install GitHub.cli
winget install Docker.DockerDesktop
```

Cierra la terminal, abre otra y comprueba:

```powershell
git --version; gh --version; docker --version
```

## 2. Identifícate en Git

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu-correo@utp.edu.pe"
gh auth login
```

## 3. Entra a la organización

Acepta la invitación que llega a tu correo o entra a `github.com/practimentor`. Sin aceptarla no podrás ver los repositorios que sean privados ni asignarte issues.

## 4. Repositorios

| Repositorio | Qué contiene | Tecnología |
|---|---|---|
| `practimentor-web` | Interfaz del estudiante | Next.js + TypeScript + Tailwind |
| `practimentor-api-core` | API principal y autoridad del sistema | C# .NET 10 + ASP.NET Core |
| `practimentor-ai` | Servicio de IA: CV, recomendaciones, consulta | Python + FastAPI |
| `practimentor-gateway` | Punto de entrada (opcional) | YARP |
| `practimentor-platform` | Documentación, ADRs e infraestructura | — |

## 5. Flujo de trabajo

Usamos **GitHub Flow** (ver `docs/decisions/2026-11-18-02-github-flow-ramas-cortas.md`):

1. Toma un issue del tablero **PractiMentor - Tablero de Control** y asígnatelo.
2. Clona el repo y crea una rama corta desde `main`:

```bash
git clone https://github.com/practimentor/practimentor-web.git
cd practimentor-web
git checkout -b feature/estructura-inicial
```

3. Haz commits pequeños con el formato `tipo(ámbito): resumen` y sube la rama:

```bash
git add .
git commit -m "feat(web): crear la estructura inicial"
git push -u origin feature/estructura-inicial
```

4. Abre un Pull Request hacia `main` con `Closes #N` en la descripción.
5. Espera a que el CI pase y a la revisión. Se integra con *merge commit*.

**Nunca** trabajes directo sobre `main`.

## 6. Seguridad

No subas contraseñas, llaves, tokens ni cadenas de conexión. Usa variables de entorno y un `.env.example` sin valores reales.

## 7. Si algo falla

Copia el mensaje de error completo, busca el issue relacionado y comenta ahí con lo que intentaste.
