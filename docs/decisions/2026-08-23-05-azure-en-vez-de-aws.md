# ADR 2026-08-23-05: Azure para el despliegue en nube

- **Estado:** aceptada
- **Fecha:** 2026-08-23

## Contexto

La Unidad 3 del sílabo cubre "Plataformas en la nube" y "Despliegue de
aplicaciones con contenedores". El sistema tiene que correr en Kubernetes en la
nube, no solo en local.

El equipo son estudiantes sin presupuesto. Había preocupación explícita por
recibir una factura inesperada.

Beneficios disponibles verificados:

- **AWS Student Rewards:** 12 meses de Skill Builder Premium, voucher de
  certificación y ~$30 en créditos promocionales. **Skill Builder es una
  plataforma de cursos, no una cuenta donde hospedar** — sus laboratorios son
  entornos temporales que se borran en horas.
- **AWS Free Tier (cuenta nueva):** $100 al registrarse, hasta $100 más
  completando cinco actividades. Vigencia de 6 meses.
- **Azure for Students:** $100 al año, renovable, **sin tarjeta de crédito**.

## Opciones consideradas

El factor decisivo resultó ser el costo del plano de control de Kubernetes:

| | AWS EKS | Azure AKS |
|---|---|---|
| Plano de control | **~$73/mes** | **Gratis** (tier Free) |
| Se paga | Control plane + nodos | Solo los nodos |
| Pausar sin destruir | No existe equivalente | `az aks stop` / `az aks start` |
| Tarjeta requerida | Sí | No |

Con AWS, EKS consumiría casi todo el crédito antes de correr una línea de código.
Se evaluó encenderlo solo para la demo (~$4/día, ~$28 la semana), que es viable,
pero tiene trampas conocidas: el asistente por defecto crea un NAT Gateway
($1,08/día extra) y borrar el clúster sin borrar antes los Services de tipo
LoadBalancer deja el balanceador vivo y cobrando.

## Decisión

**Azure**, con **AKS en tier Free**.

- El plano de control no cuesta nada; solo se paga el nodo. Una VM `B2s` sale
  ~$30/mes, y encendida solo las últimas tres semanas del ciclo son ~$25.
- `az aks stop` apaga los nodos y detiene el cobro **conservando el clúster
  configurado**; `az aks start` lo revive en ~5 minutos. EKS no tiene equivalente:
  su plano de control cobra mientras exista.
- Como la cuenta de estudiante **no acepta tarjeta**, al agotarse el crédito los
  servicios se detienen. Una factura sorpresa es imposible.
- Las imágenes van a **Azure Container Registry**, no a GHCR: los repositorios
  privados en plan Free solo tienen 500 MB de almacenamiento de paquetes.

**AWS no se descarta, se usa para lo que sirve:** Skill Builder Premium para que
el equipo aprenda y saque una certificación.

Los manifiestos se escriben con **Kustomize** (`base/` + `overlays/`), así que la
portabilidad queda intacta: si algún día conviene mover a AWS, es agregar un
overlay.

## Consecuencias

**Positivas**

- Kubernetes real en la nube por ~$25 en todo el ciclo.
- Imposible generar una factura.
- El clúster se puede apagar y encender sin perder configuración.
- Como objetivo opcional del Sprint 4, se puede desplegar además a AWS por ~3
  días (~$12) y escribir un capítulo de portabilidad multinube.

**Negativas**

- El equipo aprende Azure en vez de AWS, que tiene más presencia en el mercado.
- El tier Free de AKS no tiene acuerdo de nivel de servicio — irrelevante para un
  proyecto de curso.
- Hay que crear y verificar la cuenta de Azure for Students, que exige ser mayor
  de 18 y estudiante a tiempo completo de una institución acreditada.

## Control de gasto obligatorio

1. Configurar alertas de presupuesto al 50% y 80% el primer día.
2. No encender nada en la nube hasta el Sprint 3: los Sprints 0, 1 y 2 corren
   enteros en k3d local.
3. `az aks stop` fuera de las ventanas de trabajo.
