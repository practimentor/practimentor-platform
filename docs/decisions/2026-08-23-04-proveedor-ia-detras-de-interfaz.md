# ADR 2026-08-23-04: Proveedor de IA detrás de una interfaz propia

- **Estado:** aceptada
- **Fecha:** 2026-08-23

## Contexto

El servicio de agente necesita dos capacidades de un proveedor de IA:
**embeddings** (convertir texto a vectores para la búsqueda) y **generación**
(redactar la respuesta).

El proyecto no tiene presupuesto. Y hay una restricción de hardware: la máquina
del líder tiene 16 GB de RAM y el stack ya consume ~10 GB.

Aclaración de una confusión frecuente: **el modelo de IA no corre en la laptop.**
Lo que corre localmente es el servicio FastAPI (~250 MB) y la base vectorial
Qdrant (~250 MB). El modelo se llama por API.

## Opciones consideradas

**Modelo local con Ollama.** Gemma 4 de 12B necesita ~8 GB de RAM en cuantización
de 4 bits. Con 10 GB ya ocupados, no entra; y sobre CPU sin GPU sería demasiado
lento para una demo en vivo. **Descartado.**

**Atarse a la nube elegida.** Usar AWS Bedrock o Azure Foundry. Contra: acopla el
código a un proveedor de infraestructura. Si cambia la nube, cambia el código.

**API directa, detrás de una interfaz propia.** El servicio define sus propias
abstracciones y las implementaciones son intercambiables por configuración.

## Decisión

Se definen dos interfaces en el servicio de agente:

```python
class ProveedorEmbeddings(Protocol):
    def generar(self, textos: list[str]) -> list[list[float]]: ...

class ProveedorLLM(Protocol):
    def responder(self, mensajes, herramientas) -> Respuesta: ...
```

**Implementación por defecto: la API de Gemini en su capa gratuita.**

| Uso | Límite gratuito |
|---|---|
| Embeddings | 100 peticiones/min, 1.000/día |
| Flash-Lite (generación) | 15/min, 1.000/día |
| Flash | 10/min, 250/día |

Los embeddings son el volumen alto — cada PDF se parte en cientos de fragmentos
y cada uno necesita su vector. Que sean gratuitos es el mayor ahorro disponible.

Queda configurada una implementación alternativa con la API de Anthropic para la
demo final, si se busca mayor calidad de respuesta en la sustentación. Cambiar
entre una y otra es una variable de entorno.

### Nota sobre los beneficios de estudiante

**Google AI Pro/Plus para estudiantes no incluye clave de API** — es una
suscripción de consumidor (app de Gemini, almacenamiento, límites más altos). La
capa gratuita de la API se obtiene aparte, en Google AI Studio, y no pide
tarjeta.

## Consecuencias

**Positivas**

- Costo del ciclo: prácticamente cero.
- El agente funciona igual con cualquier nube, porque no depende de ninguna.
- Cambiar de proveedor es configuración, no reescritura.
- La inversión de dependencias es material directo para el informe.

**Negativas**

- La capa gratuita tiene límites de tasa estrictos. **Indexar un PDF grande falla
  con error 429 si no hay control de ritmo.** Todo código que llame al proveedor
  debe pasar por un limitador y reintentar con espera exponencial — esto se
  escribe desde el primer día, no como parche después.
- La interfaz propia agrega una capa de indirección que hay que mantener.
- Los límites diarios pueden agotarse en una sesión intensa de pruebas.
