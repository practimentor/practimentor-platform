# ADR 2026-11-18-02: GitHub Flow con ramas cortas por tarea

- **Estado:** aceptada — sustituye al ADR 2026-08-23-02
- **Fecha:** 2026-11-18

## Contexto

El ADR 2026-08-23-02 eligió GitHub Flow con ramas personales permanentes (`dev/<nombre>`). En la práctica, esas ramas acumularon cambios sin integrar y dificultaron seguir qué cambio resolvía qué tarea.

## Decisión

Se mantiene **GitHub Flow** con una sola rama permanente, `main`, pero las ramas son **cortas y por tarea**: `feature/…`, `fix/…` o `docs/…`, creadas desde `main`, integradas mediante Pull Request que cierra su issue (`Closes #N`) y borradas al fusionar. `main` se protege con un ruleset: sin borrado, sin reescritura de historia y cambios solo por Pull Request. Los hitos se marcan con tags semánticos (`v0.1.0`, `v0.2.0`, …).

## Consecuencias

- **Bueno:** trazabilidad directa issue → rama → PR → merge; `main` siempre estable.
- **Malo:** exige abrir un PR por cada tarea, también las pequeñas.
- Las ramas `dev/<nombre>` y `dev-qa/andre` quedan obsoletas.
