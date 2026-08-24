# ADR 2026-08-23-03: C# .NET 10 para el backend académico

- **Estado:** aceptada
- **Fecha:** 2026-08-23

## Contexto

El sistema usa tres lenguajes: TypeScript en el frontend, Python en el servicio
de agente, y un tercero para la API académica que administra usuarios, cursos,
matrículas y permisos.

Dato que orienta toda la decisión: **el curso evalúa el dominio de herramientas
—Git, CI/CD, contenedores, nube—, no el lenguaje.** Elegir mal no baja la nota
por el lenguaje en sí, pero un lenguaje que frene al equipo sí compromete las
entregas.

Restricción de hardware: la máquina del líder técnico tiene 16 GB de RAM y el
stack completo son siete contenedores.

## Opciones consideradas

**Java 21 + Spring Boot.** Ya instalado en la máquina del líder. El ecosistema
más grande, más material en español, más probable que el docente pueda ayudar.
Contra: cada servicio consume ~500 MB de RAM.

**C# .NET 10 + ASP.NET Core.** Tecnología nueva para el equipo. Consume ~150 MB
por servicio, unas tres veces menos que Java. Excelente tooling. Contra: nadie lo
maneja, hay que instalar el SDK y aprender ASP.NET Core y Entity Framework.

**Node.js + NestJS.** Un solo lenguaje para todo el equipo y la curva más corta
viniendo del frontend. Contra: se pierde el argumento de stack políglota, que es
parte de lo que justifica microservicios en este proyecto.

## Decisión

**C# .NET 10 con ASP.NET Core**, y **Entity Framework Core** con Npgsql sobre
PostgreSQL.

El factor decisivo fue el consumo de memoria: con 16 GB y siete contenedores,
la diferencia entre 500 MB y 150 MB por servicio es la diferencia entre poder
levantar el entorno completo y no poder.

Como el curso no evalúa el lenguaje, el costo de aprender uno nuevo se paga con
la holgura de RAM y con el valor que aporta al perfil profesional del equipo.

## Consecuencias

**Positivas**

- El entorno completo cabe cómodo en la máquina del líder.
- Entity Framework Core trae *Global Query Filters*, que hacen barato activar el
  aislamiento multi-tenant más adelante (ver ADR 2026-08-23-07).
- El equipo suma una tecnología muy demandada en el mercado.

**Negativas**

- Curva de aprendizaje de unas dos semanas para quien tome el backend.
- Hay que instalar el SDK de .NET en cada máquina (~30 minutos, ~200 MB).
- Menos material en español que Java, y menos probable que el docente pueda
  desatascar al equipo.
- Riesgo de secretos: .NET pone cadenas de conexión en `appsettings.json` por
  defecto. Se mitiga usando `dotnet user-secrets` en desarrollo y excluyendo
  `appsettings.Development.json` del control de versiones.
