# ADR 2026-11-18-01: El producto pasa a ser PractiMentor

- **Estado:** aceptada
- **Fecha:** 2026-11-18

## Contexto

Los ADRs de agosto de 2026 se escribieron para un asistente académico con búsqueda sobre documentos de cursos. Ese producto era una variante de plataformas académicas existentes y no justificaba por sí solo los recursos de contenedores y Kubernetes que pide el curso.

## Opciones consideradas

- **Mantener el asistente académico.** Menor cambio, pero producto derivado y de poco valor diferencial.
- **Cambiar a una plataforma de empleabilidad con agentes de IA (PractiMentor).** Problema real de los estudiantes de 5.° a 10.° ciclo, con servicios de formas de ejecución distintas (CRUD, procesamiento por lotes, conversación en tiempo real, tareas programadas), que justifican los microservicios.

## Decisión

El producto es **PractiMentor**. La organización de GitHub se llama `practimentor` y los repositorios se renombran: `practimentor-platform`, `practimentor-web`, `practimentor-api-core`, `practimentor-ai` y `practimentor-gateway`.

Los repositorios se renombran con el prefijo `practimentor-`. Se conservan las decisiones técnicas de los ADRs 2026-08-23-01, 03, 04, 05, 06 y 07 (polirepo, C# .NET 10, proveedor de IA detrás de una interfaz, Azure, PostgreSQL y Qdrant, multi-tenant). En ellos, donde dice «plataforma académica», léase PractiMentor.

## Consecuencias

- **Bueno:** un producto con problema real y una arquitectura que se justifica por las formas de ejecución de cada servicio.
- **Malo:** los ADRs anteriores conservan el nombre antiguo; no se reescriben para mantener el registro histórico.
- Los enlaces antiguos a los repositorios siguen funcionando por la redirección de GitHub.
