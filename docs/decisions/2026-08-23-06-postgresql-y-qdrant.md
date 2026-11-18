# ADR 2026-08-23-06: PostgreSQL y Qdrant como almacenes separados

- **Estado:** aceptada
- **Fecha:** 2026-08-23

## Contexto

El sistema guarda tres tipos de información con necesidades muy distintas:

1. **Datos relacionales** — usuarios, roles, cursos, matrículas, metadatos de
   documentos, historial de consultas.
2. **Vectores** — la representación numérica de cada fragmento de PDF, sobre la
   que el agente busca por *similitud semántica*, no por coincidencia exacta.
3. **Archivos** — los PDF originales.

Restricción de hardware: 16 GB de RAM en la máquina del líder, con siete
contenedores.

## Opciones consideradas

**SQL Server**, lo "natural" viniendo de .NET. Descartado por consumo: en Docker
pide ~2 GB de RAM contra los ~300 MB de PostgreSQL. Con siete contenedores, esa
diferencia decide.

**PostgreSQL con la extensión `pgvector`**, para tener un solo motor cubriendo
relacional y vectorial. Ahorraría un contenedor. Contra: menor rendimiento de
búsqueda y filtrado por metadatos que una base vectorial dedicada.

**PostgreSQL + Qdrant**, cada motor en lo suyo.

**Guardar los PDF en la base de datos.** Descartado: las bases de datos no son
para archivos binarios grandes — inflan los respaldos y degradan el rendimiento.

## Decisión

Tres almacenes, cada uno para un trabajo distinto:

| Almacén | Qué guarda | Consumo aprox. |
|---|---|---|
| **PostgreSQL** | Usuarios, cursos, matrículas, metadatos, historial | ~300 MB |
| **Qdrant** | Vectores de cada fragmento de PDF | ~250 MB |
| **MinIO** (local) / **Azure Blob** (nube) | Los archivos PDF originales | ~150 MB |

El acceso a PostgreSQL va con **Entity Framework Core** mediante el proveedor
**Npgsql**, y las migraciones con `dotnet ef`.

**Ningún servicio comparte tablas con otro.** La base de PostgreSQL pertenece a
`practimentor-api-core`; Qdrant pertenece a `practimentor-ai`. Si un servicio
necesita datos del otro, los pide por su API.

## Consecuencias

**Positivas**

- Cada motor hace bien lo suyo: Qdrant filtra por metadatos y busca por
  similitud mucho mejor que una extensión genérica.
- El entorno completo cabe en la RAM disponible.
- Tener una base vectorial dedicada se explica mejor en el informe y refleja
  cómo se construyen estos sistemas en la práctica.
- PostgreSQL es gratuito sin condiciones y EF Core lo soporta igual de bien que
  a SQL Server.

**Negativas**

- Tres almacenes que levantar, configurar y respaldar en vez de uno.
- Dos modelos de consulta distintos que el equipo debe aprender.
- No hay transacciones que abarquen PostgreSQL y Qdrant a la vez: si se indexa un
  documento y falla a mitad, hay que limpiar a mano. Se mitiga registrando el
  estado de indexación en PostgreSQL.

## Plan B

Si en el Sprint 3 la RAM aprieta, se reemplaza Qdrant por la extensión
`pgvector` dentro de PostgreSQL. Eso ahorra un contenedor a cambio de
rendimiento. El cambio se registraría en un ADR nuevo que supersede a este.
