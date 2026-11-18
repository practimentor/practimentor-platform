# ADR 2026-08-23-02: GitHub Flow con ramas personales

- **Estado:** reemplazada por [2026-11-18-02](2026-11-18-02-github-flow-ramas-cortas.md)
- **Fecha:** 2026-08-23
- **Revisión prevista:** al cerrar el Sprint 1

## Contexto

El sílabo evalúa explícitamente "Estrategias de ramificación y fusión"
(Unidad 1, semana 4) y "Solicitudes Pull. Flujos de Trabajo: Merge Conflict y
Release" (semana 8). La estrategia no es solo una decisión operativa: es materia
calificada y debe poder justificarse frente a las alternativas.

Condiciones del equipo:

- Cuatro personas tocan código; tres parten sin experiencia previa en Git.
- Las tareas asignadas duran **días**, no horas.
- Cuatro entregas versionadas a lo largo del ciclo (`v0.1.0` … `v1.0.0`).
- No hay producción real ni usuarios: no se mantienen versiones en paralelo.

## Opciones consideradas

### Git Flow (Driessen, 2010)

Dos ramas permanentes, `main` y `develop`, más `feature/*`, `release/*` y
`hotfix/*`. Las de soporte se fusionan hacia ambas ramas largas.

Su costo real no está en la cantidad de ramas sino en los **puntos de
sincronización** `develop` ↔ `main`, que se multiplican. Para un equipo que
recién aprende Git, cada uno es una ocasión de conflicto y de confusión sobre de
dónde nace cada rama. Encaja cuando hay releases versionados y espaciados
—escritorio, firmware, librerías, on-premise— que no es este caso.

### Trunk-Based Development

Una sola rama, `main`, con ramas que viven **horas** e integran varias veces al
día. El trabajo incompleto se oculta tras *feature flags*.

El reporte DORA lo correlaciona con equipos de alto desempeño. Pero lo que lo
define no es "una rama por tarea" —eso lo hace casi cualquier modelo— sino la
**cadencia**, y esa exige CI maduro y feature flags. Con tareas de días y tres
personas aprendiendo, imponerlo agregaría una barrera sin mejorar el resultado.

### GitHub Flow

Una sola rama permanente, `main`, siempre desplegable. Cada cambio entra por
Pull Request revisado.

Un solo punto de integración, sin `develop`. Ramas de días, sin feature flags.
Los hitos se marcan con tags sobre `main`, que es justo lo que piden las cuatro
entregas. No sirve para sostener versiones antiguas en paralelo — algo que este
proyecto no necesita.

## Decisión

Se adopta **GitHub Flow**, con **ramas personales** en esta primera etapa.

```text
main ───●──────────●──────────●──────────●────►   siempre desplegable
       ↗ ↘        ↗ ↘        ↗ ↘
   dev/mafer   dev/sergio  dev/nicolas   dev/andre
```

Reglas:

1. `main` es la única rama permanente **compartida** y siempre queda ejecutable.
2. **No existe `develop`** ni ramas `release/*`.
3. Cada integrante tiene **una rama propia y permanente**: `dev/<nombre>`.
   El prefijo `dev/` las agrupa en la lista de ramas de GitHub.
4. Toda integración a `main` entra por Pull Request con **una aprobación**.
5. La rama personal **no se borra** al fusionar: se sigue usando.
6. Entregas marcadas con tags anotados sobre `main`: `v0.1.0` (APF1),
   `v0.2.0` (APF2), `v0.3.0` (APF3), `v1.0.0` (PROY).

### Por qué ramas personales y no una por tarea

Una rama por tarea es la forma canónica de GitHub Flow y probablemente sea a
donde migremos. Se eligió ramas personales **para esta etapa** porque hay tres
integrantes aprendiendo Git y una sola rama por persona es un modelo mental más
simple: no hay que decidir cuándo crear ni cuándo borrar nada.

Se reevalúa al cerrar el Sprint 1. Migrar después no cuesta: se crean ramas por
tarea y las personales se abandonan.

### Prácticas obligatorias que compensan el riesgo

Como la rama personal **no muere al fusionar**, sin estas tres prácticas el
modelo se degrada:

1. **Fusionar a `main` al terminar cada tarea**, no acumulando semanas. Un PR con
   una tarea se revisa en minutos; uno con seis mezcladas no lo revisa nadie.
2. **Traer `main` antes de empezar cada tarea nueva** (`git merge main` desde la
   rama propia), para que la divergencia no se acumule.
3. **Un Pull Request por tarea**, con `Closes #NN` en la descripción, aunque
   todos salgan de la misma rama. Es la evidencia que califica la APF2.

## Consecuencias

**Positivas**

- Un solo modelo mental: tu rama, PR, merge. Barrera de entrada mínima.
- Menos puntos de conflicto que Git Flow al no sincronizar dos ramas largas.
- Encaja directo con el pipeline de CI/CD.
- Da material propio para el informe: la comparación entre los tres modelos es
  exactamente lo que evalúa la Unidad 1.

**Negativas**

- Las ramas personales tienden a acumular cambios sin relación si no se aplican
  las tres prácticas de arriba. Es el riesgo principal de esta decisión.
- No permite mantener versiones antiguas en paralelo. Si hiciera falta, se
  agregaría una rama `release/*` puntual y se registraría en un ADR nuevo.
- En organización Free con repos privados, GitHub **no aplica** el bloqueo de
  `main`. La regla se sostiene por acuerdo del equipo más un workflow que detecta
  commits que no vinieron de un Pull Request.

## Nota sobre el conflicto de merge de la APF1

La entrega de la semana 6 exige demostrar resolución de conflictos. Se provoca
fácil: dos ramas personales modifican la misma línea del mismo archivo, la
primera se fusiona, la segunda entra en conflicto y se resuelve. Se documenta con
capturas del antes, del conflicto y de la resolución.
