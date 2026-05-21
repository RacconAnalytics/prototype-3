# Artifact - Prototipo 3 - RacconAnalytics

## Tabla de Contenidos

- [1. Grupo 2F](#1-grupo-2f)
- [2. Software System](#2-software-system)
  - [2.1. Name](#21-name)
  - [2.2. Logo](#22-logo)
  - [2.3. Description](#23-description)
  - [2.4. Lenguajes de propósito general](#24-lenguajes-de-propósito-general)
- [3. Architectural Structures](#3-architectural-structures)
  - [3.1. C&C Structure](#31-component-and-connector-cc-structure)
    - [3.1.1. C&C View](#311-cc-view)
    - [3.1.2. Architectural Styles](#312-architectural-styles)
    - [3.1.3. Architectural Elements and Relations](#313-architectural-elements-and-relations)
    - [3.1.4. Architectural Pattern](#314-architectural-pattern)
  - [3.2. Layered Structure](#32-layered-structure)
    - [3.2.1. Tier 1 — Presentation](#321-tier-1--presentation)
    - [3.2.2. Tier 2 — Distribution](#322-tier-2--distribution)
    - [3.2.3. Tier 3 — Business Logic](#323-tier-3--business-logic)
    - [3.2.4. Tier 4 — Data](#324-tier-4--data)
    - [3.2.5. External APIs](#325-external-apis)
    - [3.2.6. Relations Summary](#326-relations-summary)
    - [3.2.7. Logic Layers](#327-logic-layers)
  - [3.3. Decomposition View](#33-decomposition-view)
  - [3.4. Deployment View](#34-deployment-view)
- [4. Atributos de Calidad — Seguridad](#4-atributos-de-calidad--seguridad)
  - [4.1. Patrón Network Segmentation](#41-patrón-network-segmentation-limitar-acceso--resistir-ataque)
    - [4.1.1. Táctica arquitectónica aplicada](#411-táctica-arquitectónica-aplicada)
    - [4.1.2. Patrón arquitectónico aplicado](#412-patrón-arquitectónico-aplicado)
    - [4.1.3. Escenarios de seguridad](#413-escenarios-de-seguridad)
    - [4.1.4. Implementación](#414-implementación)
    - [4.1.5. Pruebas](#415-pruebas)
- [5. Prototype](#5-prototype)
  - [5.1. Instructions](#51-instructions)

---

## 1. Grupo 2F

- Juan David Buitrago Salazar
- Juan David Serrano Ruiz
- Federico Hernández Montaño
- Johan Stiven Sarmiento Torres
- Daniela Ariadna Rueda Hernández
- Luis David Garzón Morales
- Miguel Angel Citarella Camargo
- David Felipe Chaparro Pérez
- Andrés Felipe León Sánchez

## 2. Software System

### 2.1. Name

Raccon Analytics

### 2.2. Logo

![](./images/logo.jfif)

### 2.3. Description

El proyecto consiste en el desarrollo de una aplicación web orientada al análisis de tendencias de contenido en plataformas digitales. El sistema permitirá a los usuarios realizar búsquedas sobre temas específicos y visualizar indicadores que reflejen el nivel de actividad, popularidad y relevancia del tema dentro de distintas plataformas sociales. En el primer prototipo del sistema, el análisis se enfocó principalmente en contenido proveniente de YouTube. Para este segundo prototipo, el sistema integra dos nuevos componentes lógicos: un componente de consulta de Google Trends y un componente de Procesamiento de lenguaje natural, para expandir semánticamente la búsqueda del usuario y mostrar tendencias de búsqueda, complementando así el análisis proveniente de Youtube.

El funcionamiento general de la aplicación se basa en que el usuario ingresa una consulta relacionada con un tema de interés. A partir de esta consulta, el sistema realizará solicitudes a las APIs disponibles de las plataformas objetivo y recopilará información sobre contenido relacionado con dicha búsqueda. Posteriormente, la aplicación procesará los resultados obtenidos para generar estadísticas básicas que permitan evaluar la relevancia del tema dentro de cada plataforma.

El propósito de la plataforma no es únicamente mostrar resultados de búsqueda, sino ofrecer una visión agregada del comportamiento del contenido asociado a un tema. Esto permitirá identificar tendencias, evaluar la popularidad de ciertos tópicos y detectar contenido relevante dentro de comunidades digitales.

El alcance de este segundo prototipo del sistema está limitado a la recopilación y análisis de métricas básicas disponibles a través de las APIs públicas de las plataformas seleccionadas: Youtube y Google trends, complementando la búsqueda del usuario con su expandimiento en búsuqedas relacionadas por el Procesamiento de lenguaje natural. Debido a las restricciones propias de estas APIs, tales como límites diarios de consultas o disponibilidad limitada de ciertos tipos de información, el sistema priorizará la obtención de datos esenciales que permitan generar indicadores representativos del comportamiento del contenido.

### 2.4. Lenguajes de propósito general

Para el desarrolllo del sistema se usaron los siguientes lenguajes de programación de propósito general:

- Python
- Typescript
- Java
- Go
- C#

## 3. Architectural Structures

### 3.1. Component-and Connector (C&C) Structure

#### 3.1.1. C&C View

![](./images/Vista-C&C.png)

#### 3.1.2. Architectural Styles

La aplicación emplea un estilo arquitectónico de Microservicios, caracterizado por su naturaleza distribuida y el alto grado de autonomía de sus componentes. La comunicación externa se gestiona a través de dos proxies inversos que constituyen los únicos puntos de entrada públicos al sistema: uno orientado a clientes web, con terminación TLS (HTTPS en el puerto 8443), y otro para clientes de escritorio (puerto 8081). El tráfico entrante es enrutado hacia el frontend web interno o directamente al API Gateway, que actúa como orquestador interno desacoplando la lógica de negocio distribuida de los consumidores externos.

Este diseño permite la orquestación y el enrutamiento hacia servicios especializados que operan de manera independiente y poseen su propia persistencia de datos:

- _Servicio de Adquisición de Datos de YouTube:_ Se encarga de capturar información en tiempo real (tendencias, videos registrados y mátricas de análisis de red social) mediante la integración con la API externa de YouTube V3.

- _Servicio de Gestión de Usuarios:_ Administra el ciclo de vida de las cuentas, permitiendo el registro y la autenticación de usuarios de forma aislada, garantizando que la lógica de identidad no interfiera con las funciones de búsqueda.

- _Servicio de Adquisión de Datos de Google Trends_: Captura información sobre la tendencia de la búsqueda del usuario, como el volumen de consulta de la temática a través del tiempo.

- _Procesamiento de Lenguaje Natural_: Este componente se apoya sobre un servicio de API externo de Nvidia para realizar Procesamiento de Lenguaje Natural (NLP) sobre la búsqueda del usuario y expandir semánticamente la consulta hallando posibles búsquedas o entradas de usuario relacionadas.

Los componentes son reutilizables, escalables independientemente y se comunican principalmente a través de protocolos ligeros (HTTP: REST y Streaming), lo que refuerza la agilidad y el bajo acoplamiento del sistema.

#### 3.1.3. Architectural Elements and Relations

Nuestro sistema cuenta con:

- **2 Componentes de presentación: Presenta a través de KPIs, gráficos analíticos y métricas importantes, los datos retornados por los servicios lógicos.**
  - **Frontend web:** Desarrollado en Typescript usando el framework Next JS para implementar Server Side Rendering. Se limita a la interfaz web, cuya responsabilidad es renderizar la información suministrada por los servicios lógicos a través del API Gateway
  - **Frontend de escritorio:** Desarrollado en C#. Permite al usuario interactuar desde la app con interfaz de escritorio mostrando la infmración suministrada por los servicios lógicos a través del API-Gateway
- **5 Componentes lógicos:**
  - **Orquestador (API Gateway):** Componente interno de orquestación y enrutamiento. Recibe las solicitudes provenientes de los proxies inversos, valida la autenticación mediante tokens JWT y enruta las peticiones al microservicio correspondiente. Provee una interfaz unificada para el frontend, ocultando la complejidad de la arquitectura distribuida. No está expuesto públicamente. Desarrollado en el lenguaje de propósito general Go.
  - **Users Management Service**: Gestiona el ciclo de vida de los usuarios (registro e inicio de sesión), exponiendo recursos de autenticación. Desarrollado en TypeScript.
  - **Youtube Data Acquisition Service**: Orquesta la extracción de datos externos. Procesa la query del usuario, consulta la API de YouTube, cálcula métricas y estandarizada los datos para ser presentados en el front. Desarrollado en el lenguaje Python.
  - **Google Trends Data Acquisition Service**: Maneja la extracción de datos de tendecias de búsquedas, consultando la API externa de Google Trends, enriqueciendo la información retornada por el componente de Youtube. Desarrollado en lenguaje de propósito general Python.
  - **Natural Language Processing Service**: Implementa la lógica de IA. Diseña prompts específicos para expandir la búsqueda del usuario a partir de hallazgo de posibles bbúsquedas relacionadas. Desarrollado en el lenguaje Java.
- **4 Componentes de Datos:**
  - _Base de datos relacional Users_: Repositorio centralizado para la información básica y credenciales de acceso de los usuarios.
  - 2 Bases de datos No SQL:
    - _Youtube Historical Search Keywords:_ Almacenamiento persistente de los datos estandarizados y retornados por la API externa de Youtube.
    - _Google Trends Historical Search Keywords:_ Almacena los datos retornador por la API externa de Google Trends
  - _Almacenamiento de datos Caché:_ Componente de almacenamiento volátil que almacena los datos retornados por la API externa de Youtube para mejorar el rendimiento del componente lógico y el uso limitado de la API externa.
- **5 Componentes externos**
  - Web browser: Consume el contenido web para renderizar la interfaz web.
  - OAuth Google API
  - External Youtube API
  - External Google Trends API
  - Nvidia NIM API

#### 3.1.4. Architectural Pattern

Se implementó un patrón arquitectónico con un componente orquestador (API Gateway), evitando que los componentes de presentación adquieran una responsabilidad de sincronización de lógica de negocio que no es responsabilidad natural en la capa de presentación. Las solicitudes externas llegan primero a los proxies inversos, que terminan el canal seguro (TLS/HTTPS) y reenvían el tráfico hacia la red interna. El API Gateway, como orquestador interno, valida la autenticación mediante tokens JWT (*Bearer tokens*), enruta las peticiones al microservicio correspondiente y gestiona el control de acceso. De esta manera, se desacopla al cliente de la arquitectura interna basada en microservicios y ningún servicio interno queda expuesto directamente al exterior.

---

### 3.2. Layered Structure

![](./images/Layered-view.png)

#### 3.2.1. Tier 1 — Presentation

##### 3.2.1.1. Web Frontend

- **Stack:** Next.js 14, React 18, TailwindCSS 4, DaisyUI 5, Recharts, Framer Motion
- **Puerto:** 3000 (privado, interno). Acceso publico via reverse-proxy-web en 8443.
- **Responsabilidades:**
  1. Renderizar UI de autenticación (login, registro, recuperación de contraseña, OAuth callback)
  2. Dashboard con gráficas interactivas de tendencias y análisis
  3. Comunicación con el API Gateway vía HTTP REST desde SSR
- **Endpoints que consume:** `GET/POST /api/users/*`, `GET /api/youtube/*`, `GET /api/trends/*`
- **Dependencias:** api-gateway (HTTP REST) via route handler interno

**Flujo de acceso (Web SSR):**

Navegador -> reverse-proxy-web (HTTPS 8443) -> web-page (SSR, privado) -> api-gateway (privado)

El navegador solo ve el reverse proxy. Las llamadas `/api/*` se resuelven en el servidor de Next.js y se reenvian por red interna al API Gateway.

##### 3.2.1.2. Desktop Frontend

- **Stack:** WPF, C# (.NET), XAML
- **Puerto de acceso público:** 8081 (vía reverse-proxy)
- **Responsabilidades:**
  1. UI de escritorio para autenticación (SignIn, CreateAccount)
  2. Dashboard desktop con HomePage
  3. Comunicación con el API Gateway a través del reverse-proxy en el puerto 8081
- **Endpoints que consume:** Mismos que Web Frontend
- **Dependencias:** reverse-proxy (8081) → api-gateway (HTTP REST)

**Flujo de acceso (Desktop):**

Cliente Desktop -> reverse-proxy (8081) -> api-gateway (privado)

---

#### 3.2.2. Tier 2 — Distribution

##### 3.2.2.1. API Gateway

- **Stack:** Go, net/http, golang-jwt, godotenv
- **Puerto:** 8080 (interno, no expuesto al host)
- **Responsabilidades:**
  1. JWT Authentication — valida Bearer tokens en rutas protegidas; inyecta X-User-Id y X-User-Email en headers downstream
  2. Reverse Proxy — reescribe rutas públicas (`/api/users/*` → users-service, `/api/youtube/*` → youtube-service)
  3. CORS + Logging middleware — maneja preflight OPTIONS, loguea método/ruta/status/latencia/IP
- **Endpoints públicos:** `/health`, `/health/dependencies`, `/api/users/auth/*`
- **Dependencias:** users-service (HTTP REST), youtube-service (HTTP REST), nlp-service (HTTP REST)

---

#### 3.2.3. Tier 3 — Business Logic

##### 3.2.3.1. YouTube Acquisition Service

- **Stack:** Python 3, FastAPI, Motor (MongoDB async), aio-pika (RabbitMQ), redis-py, google-api-python-client
- **Puerto:** 8000 (interno, no expuesto al host)
- **Responsabilidades:**
  1. Scraping de YouTube Data API v3 — búsqueda y recolección de datos de videos/canales
  2. Gestión de cuota de API — tracking y rate limiting del YouTube API quota
  3. Orquestación asíncrona de análisis via RabbitMQ (producer/consumer de `analyses_queue` y `results_queue`)
- **Endpoints:** `GET/POST /api/analyze`, `GET /api/health`
- **DBs:** MongoDB (cache de análisis), Redis (cache de queries con TTL 5min), RabbitMQ (mensajería asíncrona)
- **Dependencias:** MongoDB, Redis, RabbitMQ, nlp-service (allowed-to-use-below), YouTube Data API (externa), users-service (validación)

##### 3.2.3.2. Google Trends Acquisition Service

- **Stack:** Python 3, FastAPI, Motor (MongoDB async), pytrends
- **Puerto:** 8001 (interno, no expuesto al host)
- **Responsabilidades:**
  1. Retrieval de datos de Google Trends — volumen de búsqueda histórica, queries relacionadas
  2. Cache en MongoDB con TTL 24h — minimiza llamadas a la API de Google Trends
  3. Delegación de NLP al servicio NLP para expansión de keywords
- **Endpoints:** `GET /api/v1/trends/*`, `GET /health`
- **DBs:** MongoDB (cache de tendencias)
- **Dependencias:** MongoDB, nlp-service (allowed-to-use-below), Google Trends API (externa), users-service (validación)

##### 3.2.3.3. NLP Service

- **Stack:** Java 17, Spring Boot 3.2, Jackson
- **Puerto:** 8193 (interno, no expuesto al host)
- **Responsabilidades:**
  1. Expansión de Keywords — genera keywords adicionales a partir de una query original
  2. Enrichment de queries — genera expanded_queries para mejorar búsquedas
  3. Detección de idioma del input
- **Endpoints:** `POST /inference`, `GET /inference/health`
- **Dependencias:** Nvidia NIM API (externa, HTTP REST)

##### 3.2.3.4. Users Management Service

- **Stack:** NestJS 11, Prisma ORM, PostgreSQL, Redis (ioredis), Passport (Google OAuth2, GitHub OAuth2, JWT), bcrypt, nodemailer
- **Puerto:** 3001 (interno, no expuesto al host)
- **Responsabilidades:**
  1. Autenticación — Local (email/password con bcrypt), OAuth2 (Google, GitHub), JWT (access + refresh tokens)
  2. CRUD de Users — registro, actualización de perfil/settings, recovery de contraseña via email
  3. Rate Limiting de auth endpoints + audit de sesiones
- **Endpoints:** `POST /api/v1/auth/*`, `GET/PUT /api/v1/users/*`, `POST /api/v1/auth/recovery/*`
- **DBs:** PostgreSQL (users, sessions), Redis (rate limiting + session cache)
- **Dependencias:** PostgreSQL, Redis, OAuth Google API (externa)

---

#### 3.2.4. Tier 4 — Data

##### 3.2.4.1. PostgreSQL

- **Imagen:** postgres:15
- **Puerto:** 5432 (no publicado al host, solo accesible en red interna)
- **Responsabilidades:** Almacenamiento relacional de usuarios, configuraciones y sesiones
- **Usado por:** users-service (vía Prisma ORM)

##### 3.2.4.2. MongoDB

- **Imagen:** mongo:6
- **Puerto:** 27017 (no publicado al host, solo accesible en red interna)
- **Responsabilidades:** Almacenamiento documental de cache de análisis YouTube y tendencias Google
- **Usado por:** youtube-acquisition-service, google-trends-acquisition-service (vía Motor async)

##### 3.2.4.3. Redis

- **Imagen:** redis:7
- **Puerto:** 6379 (no publicado al host, solo accesible en red interna)
- **Responsabilidades:** Cache de queries (YouTube: TTL 5min), rate limiting de auth (Users), session cache
- **Usado por:** youtube-acquisition-service, users-service (vía ioredis)

##### 3.2.4.4. RabbitMQ

- **Imagen:** rabbitmq:3-management
- **Puertos:** 5672 (AMQP), 15672 (Management UI) — ninguno publicado al host, solo accesibles en red interna
- **Responsabilidades:** Mensajería asíncrona para orquestación de análisis (producer/consumer pattern)
- **Colas:** `analyses_queue`, `results_queue`
- **Usado por:** youtube-acquisition-service (vía aio-pika)

---

#### 3.2.5. External APIs

##### 3.2.5.1. YouTube Data API v3

- **Protocolo:** HTTP REST
- **Responsabilidades:** Provee datos de búsqueda, videos y canales de YouTube
- **Usado por:** youtube-acquisition-service

##### 3.2.5.2. Google Trends API (pytrends)

- **Protocolo:** HTTP (via pytrends library)
- **Responsabilidades:** Provee datos de volumen de búsqueda histórica y queries relacionadas
- **Usado por:** google-trends-acquisition-service

##### 3.2.5.3. Nvidia NIM API

- **Protocolo:** HTTP REST
- **Responsabilidades:** Inferencia LLM para expansión de keywords y enriquecimiento de queries
- **Usado por:** nlp-service-nvidia

##### 3.2.5.4. OAuth Google API

- **Protocolo:** OAuth 2.0 / HTTP REST
- **Responsabilidades:** Autenticación de usuarios via Google SSO
- **Usado por:** users-service (vía Passport Google OAuth2 strategy)

---

#### 3.2.6. Relations Summary

| Origen                    | Destino                   | Protocolo    | Tipo                 |
| ------------------------- | ------------------------- | ------------ | -------------------- |
| Web Frontend              | API Gateway               | HTTP REST    | allowed-to-use       |
| Desktop Frontend          | API Gateway               | HTTP REST    | allowed-to-use       |
| API Gateway               | YouTube Acquisition       | HTTP REST    | allowed-to-use       |
| API Gateway               | Google Trends Acquisition | HTTP REST    | allowed-to-use       |
| API Gateway               | Users Management          | HTTP REST    | allowed-to-use       |
| YouTube Acquisition       | MongoDB                   | DB_CONNECTOR | allowed-to-use       |
| YouTube Acquisition       | Redis                     | DB_CONNECTOR | allowed-to-use       |
| YouTube Acquisition       | RabbitMQ                  | AMQP         | allowed-to-use       |
| YouTube Acquisition       | NLP Service               | HTTP REST    | allowed-to-use-below |
| YouTube Acquisition       | YouTube Data API          | HTTP REST    | allowed-to-use       |
| Google Trends Acquisition | MongoDB                   | DB_CONNECTOR | allowed-to-use       |
| Google Trends Acquisition | NLP Service               | HTTP REST    | allowed-to-use-below |
| Google Trends Acquisition | Google Trends API         | HTTP REST    | allowed-to-use       |
| NLP Service               | Nvidia NIM API            | HTTP REST    | allowed-to-use       |
| Users Management          | PostgreSQL                | DB_CONNECTOR | allowed-to-use       |
| Users Management          | Redis                     | DB_CONNECTOR | allowed-to-use       |
| Users Management          | OAuth Google API          | HTTP REST    | allowed-to-use       |

---

#### 3.2.7. Logic Layers

Para complementar la vista por capas de todo el sistema, se establecieron de igual forma la estructura de capas lógicas o subarquitectura de los componentes lógicos a continuación:

![](./images/Layered-Architecture-view-Logic-layers.png)

---

##### 3.2.7.1. YouTube Acquisition Data Service

![](./images/youtube.png)

Este componente implementa la lógica de adquisición, procesamiento y almacenamiento de datos provenientes de la API de YouTube.

- _Controllers_ (/analyze, /healthy):
  Actúan como punto de entrada HTTP. Delegan la lógica al Orchestrator-service y al Cache-service.

- _Cache-service_:
  Gestiona la verificación de resultados previamente calculados.
  - Flechas verdes: puede ser invocado por los controllers y el orquestador para evitar llamadas redundantes.
  - Flechas rojas: persiste información auxiliar como Quota_log en el repositorio.

- _Orchestrator-service_:
  Coordina el flujo principal del análisis.
  - Flechas verdes: invoca servicios permitidos como Transformer-service.
  - Flechas rojas: invoca hacia abajo a Youtube_client-service para consumir la API externa.

- _Youtube_client-service_:
  Encapsula las llamadas a la API de YouTube. Solo es invocado por el orquestador (flecha roja).

- _Transformer-service_:
  Se encarga de mapear y normalizar la respuesta de la API hacia los modelos internos.
  - Flechas verdes: interactúa con Models / Schemas.

- _Repositories_ (Quota_log, Analysis_repository):
  Persisten datos en MongoDB.
  - Flechas rojas: indican escritura desde servicios superiores.
  - Flechas verdes: permiten acceso controlado a los modelos.

- _Models / Schemas_:
  Definen la estructura tipada de los datos del sistema. Son utilizados por el transformer y los repositorios.

---

##### 3.2.7.2. Google Trends Service

![](./images/Google_Trends.png)

Este servicio obtiene y procesa tendencias desde Google Trends.

- _Controllers_ (/interest_over_time, /bulk-interest, /related_queries):
  Exponen endpoints que delegan la lógica al Trends service.

- _Key build generator_:
  Genera claves normalizadas para consultas y almacenamiento.
  - Flechas verdes: puede ser invocado por controllers.
  - Flechas rojas: persiste información en Repositories.

- _Trends service_:
  Orquesta la lógica de negocio.
  - Flechas rojas: invoca a PyTrends client para consumir la API externa.

- _PyTrends client_:
  Cliente que encapsula la comunicación con Google Trends.
  - Flechas verdes: entrega datos hacia Models / Schemas.

- _Repositories_:
  Persisten resultados procesados.
  - Flechas verdes: interactúan con los modelos.

- _Models / Schemas_:
  Definen la estructura de datos utilizada en el servicio.

---

##### 3.2.7.3. NLP Service

![](./images/NLP_Service.png)

Este servicio se encarga del procesamiento de lenguaje natural para enriquecer las consultas.

- _Controllers_ (/inference, /health):
  Exponen endpoints para inferencia y monitoreo.

- _Prompt Builder Service_:
  Construye prompts estructurados para el modelo NLP.
  - Flechas verdes: interactúa con Models / Schemas.

- _Nvidia Nim Service_:
  Ejecuta la inferencia usando modelos de IA.
  - Flechas verdes: también utiliza los modelos definidos.

- _Models / Schemas_:
  Representan la estructura de entrada/salida del procesamiento NLP.

---

##### 3.2.7.4. Users Service

![](./images/users_service.png)

Gestiona autenticación, usuarios y servicios relacionados.

- _Controllers_:
  Contienen endpoints de autenticación, OAuth, recuperación de cuenta y perfil de usuario.

- _AuthService_:
  Núcleo de la lógica de autenticación.
  - Flechas verdes: interactúa con otros servicios como RateLimitService y UsersService.

- _AuthAuditService_:
  Registra eventos de autenticación.
  - Flechas rojas: persiste logs en Repositories.

- _RecoveryMailService_:
  Gestiona recuperación de cuentas vía correo.
  - Flechas rojas: escribe en repositorios.

- _RateLimitService_:
  Controla la tasa de solicitudes hacia el sistema.

- _UsersService_:
  Gestiona información de usuarios.
  - Flechas verdes: interactúa con Models / Schemas.

- _Repositories_ (PrismaService):
  Acceso a la base de datos relacional.
  - Flechas rojas: reciben escritura desde servicios.

- _Models / Schemas_:
  Definen estructuras de datos para usuarios y autenticación.

---

### 3.3. Decomposition View

![](./images/Vista_Descomposicion.png)

La aplicación fue dividida en dos modulos principales y 3 sub-modulos, con un total de 11 funcionalidades

-User Auth: Es el modulo que contiene todas las funciones que permiten al usuario ingresar, crear su cuenta, salir de la sesión, recuperar contraseña y autorizarse como usuario
-Search: Es el modulo que contiene todo lo relacionado a busqueda, en este caso 3 sub-modulos que nos indican como se dividen estas funcionalidades
-Sub-modulo Optimize Search: Es el que contiene tanto la generación de busquedas relacionadas por parte de nuestro modelo LLM como el guardado de las busquedas previas usando un caché
-Sub-modulo Youtube: Contiene tanto la funcionalidad de la busqueda en youtube como la funcionalidad de la generación de stats y gráficas para youtube
-Sub_modulo Google Trends: Al igual que el sub-modulo de Youtube contiene las funcionalidades de la generación de stats y graficas pero para google trends

---

### 3.4. Deployment View

![](./images/Vista_Despliegue.png)

#### 3.4.1. Deployment Architecture

El diagrama de despliegue ilustra la distribución física y lógica de los componentes del sistema, dividiendo la arquitectura en dos zonas de red principales: una red de área local (LAN) y una red externa (Internet). El sistema se distribuye a través de dos nodos físicos o virtuales que alojan múltiples entornos de ejecución y microservicios.

1. Zona LAN: Node 1 (Device Localhost)
   Este nodo actúa como el entorno principal de alojamiento local y contiene todos los microservicios, interfaces y bases de datos del sistema. El acceso externo al nodo está restringido a dos únicos puntos de entrada públicos (proxies inversos); todos los demás servicios operan en redes privadas internas sin exposición directa al host ni a internet.

   **Puntos de entrada públicos:**
   - **Reverse Proxy Web** (nginx): Termina el canal TLS (HTTPS) en el puerto 8443, redirige HTTP (puerto 8080) a HTTPS, y enruta el tráfico hacia el frontend web interno. Aplica rate limiting y cabeceras de seguridad (HSTS, X-Frame-Options).
   - **Reverse Proxy Desktop** (nginx): Recibe conexiones de clientes de escritorio en el puerto 8081 y las enruta al API Gateway interno. Aplica rate limiting.

   **Interfaces de usuario y orquestación (red privada de servicios):**
   - **Website Frontend**: Desplegado en Node.js (puerto 3000, privado). No expuesto directamente; accesible únicamente a través del Reverse Proxy Web vía HTTPS.
   - **Desktop Frontend**: Desplegado bajo el marco de trabajo .NET. Se comunica con el sistema a través del Reverse Proxy Desktop (puerto 8081).
   - **API Gateway**: Desplegado en Go (puerto 8080, privado). Orquesta y enruta las solicitudes internas; valida tokens JWT antes de reenviar peticiones a los microservicios.

   **Microservicios de procesamiento y adquisición (red privada de servicios):**
   - **YouTube Data Acquisition Microservice**: Desplegado en Python (puerto 8000, privado).
   - **Natural Language Processing Microservice**: Ejecutado sobre JVM (puerto 8193, privado).
   - **Google Trends Data Acquisition Microservice**: Desplegado en Python (puerto 8001, privado).
   - **Users Management Microservice**: Alojado en Node.js (puerto 3001, privado).

   **Almacenamiento en caché y bases de datos locales (red interna aislada, sin acceso externo):**
   - **Cache YouTube Historical Search Keywords**: Redis (puerto 6379, no publicado al host).
   - **Google Trends Historical Search Keywords**: MongoDB (puerto 27017, no publicado al host).
   - **Users**: PostgreSQL (puerto 5432, no publicado al host).

2. Zona Internet: Node 2 (Server)
   Este nodo representa un servidor remoto accesible a través de Internet, dedicado específicamente al almacenamiento persistente externo.
   Almacenamiento Remoto:
   - **YouTube Historical Search Keywords**: Base de datos desplegada en MongoDB (puerto 27017).

---

## 4. Atributos de Calidad — Seguridad

### 4.1. Patrón Network Segmentation (Limitar Acceso — Resistir Ataque)

#### 4.1.1. Táctica arquitectónica aplicada

La táctica aplicada es **Limitar acceso** (*Limit Access*), perteneciente a la categoría **Resistir ataque** (*Resist Attack*) del atributo de calidad de Seguridad. Esta táctica restringe los puntos de acceso a los recursos del sistema y el tipo de tráfico permitido, con el objetivo de reducir la superficie de ataque expuesta. Se implementa mediante la creación de zonas de red diferenciadas con distintos niveles de confianza y visibilidad, análogas a una zona desmilitarizada (DMZ) reforzada con cortafuegos.

#### 4.1.2. Patrón arquitectónico aplicado

El patrón aplicado es **Network Segmentation**, que consiste en dividir la red del sistema en segmentos aislados entre sí, limitando la propagación de ataques y el movimiento lateral entre componentes. El patrón distingue dos tipos de subred:

- **Subred pública (*Public Subnet*):** segmentos de red accesibles desde el exterior, que contienen únicamente los componentes de frontera que deben recibir tráfico externo (proxies inversos). En este sistema corresponde a las redes `raccon_public_web` y `raccon_public_desktop`.
- **Subred privada (*Private Subnet*):** segmentos de red con espacios de direcciones IP privadas, sin acceso desde el exterior. Contienen los microservicios internos, las bases de datos y la mensajería. Corresponde a las redes `raccon_private_services` y `raccon_private_internal`.

Un atacante que comprometa un componente de la subred de presentación (por ejemplo, mediante XSS avanzado en el frontend SSR) no puede alcanzar directamente las bases de datos ni los microservicios internos, ya que la segmentación de red actúa como barrera técnica independiente de la lógica de aplicación.

#### 4.1.3. Escenarios de seguridad

##### 4.1.3.1. Escenario 1 — Aislamiento de bases de datos frente a acceso externo

En el estado previo del sistema (Prototype 2), los contenedores de bases de datos publicaban sus puertos directamente al host mediante la directiva `ports` en `docker-compose.yml`. Esto permitía que cualquier proceso en la máquina anfitriona o en la red local se conectara directamente a MongoDB, PostgreSQL o Redis sin atravesar ninguna capa de seguridad intermedia. Este escenario valida que, tras aplicar el patrón de segmentación de red, las bases de datos quedan completamente inaccesibles desde el exterior: ningún puerto de base de datos está expuesto al host y la red interna en la que residen bloquea todo enrutamiento externo. La verificación se realiza tanto desde la máquina anfitriona (mediante `Test-NetConnection`) como desde un contenedor externo ajeno a la red interna del sistema.

| Campo | Descripción |
|---|---|
| **Fuente del estímulo** | Atacante externo (desde el host o desde internet) |
| **Estímulo** | Intento de conexión directa a MongoDB (27017), PostgreSQL (5432) o Redis (6379) mediante escaneo de puertos, cliente CLI o conexión manual |
| **Entorno** | Sistema en operación normal con todos los contenedores activos |
| **Artefacto** | Contenedores de bases de datos: `mongo`, `postgres`, `redis` |
| **Respuesta** | La conexión es rechazada inmediatamente. Los puertos de las bases de datos no están publicados al host. La red `raccon_private_internal` tiene `internal: true`, lo que bloquea todo tráfico externo hacia ella |
| **Medida de respuesta** | 0 puertos de bases de datos accesibles desde el host. Rechazo inmediato (*Connection refused*). La red interna no enruta tráfico hacia el exterior ni recibe tráfico desde el exterior |

**Contramedida implementada:** configuración de la red `raccon_private_internal` con `internal: true` en `docker-compose.yml`, sin publicación de puertos (`ports`) en los contenedores de base de datos.

![Diagrama del escenario 1](images/ns-sce1.jpg)

**Evidencia comparativa — Prototype 2 (antes) vs. Prototype 3 (después):**

*Acceso a MongoDB desde el host:*

| Antes | Después |
|---|---|
| ![MongoDB accesible desde el host en Prototype 2](images/sce1-pre-host-mongo.png) | ![MongoDB bloqueado desde el host en Prototype 3](images/sce1-pos-host-mongo.png) |

*Acceso a Redis desde el host:*

| Antes | Después |
|---|---|
| ![Redis accesible desde el host en Prototype 2](images/sce1-pre-host-redis.png) | ![Redis bloqueado desde el host en Prototype 3](images/sce1-pos-host-redis.png) |

*Acceso a MongoDB desde un contenedor externo:*

| Antes | Después |
|---|---|
| ![MongoDB accesible desde contenedor en Prototype 2](images/sce1-pre-cont-mongo.png) | ![MongoDB bloqueado desde contenedor en Prototype 3](images/sce1-pos-cont-mongo.png) |

*Acceso a Redis desde un contenedor externo:*

| Antes | Después |
|---|---|
| ![Redis accesible desde contenedor en Prototype 2](images/sce1-pre-cont-redis.png) | ![Redis bloqueado desde contenedor en Prototype 3](images/sce1-pos-cont-redis.png) |

##### 4.1.3.2. Escenario 2 — Frontend aislado de bases de datos

Este escenario aborda el riesgo de movimiento lateral desde el frontend hacia las bases de datos. En Prototype 2, el contenedor `web-page` compartía red con los contenedores de base de datos, por lo que un atacante con ejecución de código en el proceso SSR del frontend podía resolver y alcanzar directamente los servicios de datos mediante `ping` o conexiones TCP directas. La contramedida consiste en restringir el contenedor `web-page` a una red de servicios separada, sin membresía en la red interna donde residen las bases de datos. Como consecuencia, el DNS embebido de Docker no resuelve los nombres de host `mongo`, `redis` ni `postgres` desde el interior del contenedor del frontend, haciendo imposible cualquier conexión directa a datos sensibles incluso si el proceso del frontend es comprometido.

| Campo | Descripción |
|---|---|
| **Fuente del estímulo** | Atacante que ha comprometido el contenedor del frontend (SSR comprometido o XSS con ejecución en servidor) |
| **Estímulo** | Intento de conexión directa a `mongo` (27017), `redis` (6379) o `postgres` (5432) desde dentro del contenedor `web-page` |
| **Entorno** | Sistema en operación normal |
| **Artefacto** | Contenedor `web-page`, redes Docker `raccon_private_services` y `raccon_private_internal` |
| **Respuesta** | La conexión falla en resolución de nombre de host. El contenedor `web-page` pertenece únicamente a `raccon_private_services` y no tiene visibilidad de los contenedores en `raccon_private_internal`. La resolución DNS de Docker para `mongo`, `postgres` y `redis` no existe en la red de `web-page` |
| **Medida de respuesta** | Fallo inmediato con error *bad address* al intentar resolver los hostnames de bases de datos. El movimiento lateral desde el frontend hacia datos sensibles es imposible por diseño de red |

**Contramedida implementada:** asignación del contenedor `web-page` exclusivamente a la red `raccon_private_services`, sin membresía en `raccon_private_internal` donde residen las bases de datos.

![Diagrama del escenario 2](images/ns-sce2.jpg)

**Evidencia comparativa — Prototype 2 (antes) vs. Prototype 3 (después):**

*Resolución del hostname `mongo` desde el contenedor `web-page` (ping):*

| Antes | Después |
|---|---|
| ![ping mongo resuelve en Prototype 2](images/sce2.1-pre-host-mongo.png) | ![ping mongo falla en Prototype 3](images/sce2.1-pos-host-mongo.png) |

*Conexión TCP a MongoDB desde el contenedor `web-page` (netcat):*

| Antes | Después |
|---|---|
| ![nc -zv mongo accesible en Prototype 2](images/sce2.2-pre-host-mongo.png) | ![nc -zv mongo bloqueado en Prototype 3](images/sce2.2-pos-host-mongo.png) |

##### 4.1.3.3. Escenario 3 — Reverse Proxy como único punto de entrada

En Prototype 2, múltiples servicios internos publicaban puertos directamente al host: el API Gateway en el puerto 8080, el servicio de adquisición de YouTube en el puerto 8000 y el frontend web en el puerto 3000. Esto permitía a cualquier cliente saltarse el proxy inverso y acceder directamente a los servicios internos, eludiendo controles como la terminación TLS, la limitación de tasa y la inyección de cabeceras de autenticación. Este escenario valida que, en Prototype 3, ningún servicio interno tiene puertos publicados al host: el único acceso externo válido es a través de los proxies inversos, que actúan como única puerta de entrada controlada al sistema.

| Campo | Descripción |
|---|---|
| **Fuente del estímulo** | Atacante externo |
| **Estímulo** | Intento de acceso directo al API Gateway (8080), microservicios internos (8000, 8001, 8193, 3001) o bases de datos (27017, 5432, 6379) desde el host o internet |
| **Entorno** | Sistema en operación normal |
| **Artefacto** | Todos los contenedores internos: `api-gateway`, `users-service`, `youtube-service`, `google-trends-service`, `nlp-service`, `mongo`, `postgres`, `redis`, `rabbitmq`, `web-page` |
| **Respuesta** | Todos los intentos de conexión directa a puertos internos son rechazados. Únicamente los reverse proxies (`reverse-proxy` en puerto 8081 y `reverse-proxy-web` en puertos 8443/8080) tienen puertos publicados al host |
| **Medida de respuesta** | 10 de 10 puertos internos inaccesibles desde el host. Dos únicos puntos de entrada externamente accesibles. La topología interna no es visible ni deducible desde el exterior |

**Contramedida implementada:** ningún contenedor interno tiene la directiva `ports` con binding a `0.0.0.0` en `docker-compose.yml`. Únicamente `reverse-proxy` y `reverse-proxy-web` publican puertos.

![Diagrama del escenario 3](images/ns-sce3.jpg)

**Evidencia comparativa — Prototype 2 (antes) vs. Prototype 3 (después):**

*Acceso directo al API Gateway y servicio YouTube desde el host (antes):*

![API Gateway y YouTube accesibles directamente en Prototype 2](images/sce3-pre-apigateway-youtube.png)

*Acceso directo al frontend web desde el host (antes):*

![Frontend web accesible directamente en Prototype 2](images/sce3-pre-webpage.png)

*Todos los servicios internos inaccesibles desde el host (después):*

![API Gateway, YouTube y frontend bloqueados en Prototype 3](images/sce3-pos-apigateway-youtube-webpage.png)

#### 4.1.4. Implementación

La implementación se realiza mediante cuatro redes Docker definidas en el `docker-compose.yml` de la raíz del repositorio:

```yaml
networks:
  public_web:
    driver: bridge
    name: raccon_public_web

  public_desktop:
    driver: bridge
    name: raccon_public_desktop

  private_services:
    driver: bridge
    name: raccon_private_services

  private_internal:
    driver: bridge
    name: raccon_private_internal
    internal: true    # sin acceso a internet; solo comunicación intra-red
```

La directiva `internal: true` en `raccon_private_internal` es el mecanismo central del patrón: Docker bloquea cualquier enrutamiento de tráfico entre esa red y el exterior (host o internet), independientemente de las reglas de la aplicación.

**Asignación de contenedores a redes y puertos publicados al host:**

| Contenedor | Redes asignadas | Puertos publicados |
|---|---|---|
| `reverse-proxy-web` | `public_web`, `private_services` | 8443 (HTTPS), 8080 (HTTP → 301 HTTPS) |
| `reverse-proxy` | `public_desktop`, `private_services` | 8081 |
| `web-page` | `private_services` | ninguno |
| `api-gateway` | `private_services`, `private_internal` | ninguno |
| `users-service` | `private_services`, `private_internal` | ninguno |
| `youtube-service` | `private_services`, `private_internal` | ninguno |
| `google-trends-service` | `private_services`, `private_internal` | ninguno |
| `nlp-service` | `private_services` | ninguno |
| `postgres` | `private_internal` | ninguno |
| `mongo` | `private_internal` | ninguno |
| `redis` | `private_internal` | ninguno |
| `rabbitmq` | `private_internal` | ninguno |

**Flujo de acceso con segmentación aplicada:**

```
Browser (web)                         Cliente desktop
      |                                      |
  (HTTPS 8443)                           (HTTP 8081)
      |                                      |
reverse-proxy-web                      reverse-proxy
[public_web + private_services]        [public_desktop + private_services]
      |                                      |
  web-page                                   |
  [private_services]                         |
      |                                      |
      +-------------> api-gateway <----------+
                   [private_services + private_internal]
                              |
          +----------+--------+---------+----------+
          |          |                  |          |
    users-service  youtube-service  trends-service  nlp-service
    [priv_svc +    [priv_svc +      [priv_svc +    [priv_svc]
     priv_int]      priv_int]        priv_int]
          |               |               |
    [postgres,      [mongo, redis,     [mongo]
     redis]          rabbitmq]
                          ↑
        raccon_private_internal (internal=true — sin acceso desde fuera)
```

La red `raccon_private_services` permite que los microservicios realicen llamadas salientes a APIs externas (YouTube Data API, Google Trends API, Nvidia NIM API). La red `raccon_private_internal` está completamente aislada del exterior y contiene únicamente las bases de datos y los servicios que necesitan accederlas directamente.

#### 4.1.5. Pruebas

Las pruebas del patrón se encuentran en el directorio `reverse-proxy/`. Los grupos más relevantes para Network Segmentation son los **Grupos 1, 5 y 6** de `test_security_comparison.py`:

- **Grupo 1 — Aislamiento de servicios:** verifica que los puertos 8080, 3001, 8000, 8001, 8193, 5432, 27017 y 6379 son inaccesibles desde el host (connection refused).
- **Grupo 5 — Port bindings de Docker:** verifica mediante `docker compose ps` que solo `reverse-proxy` y `reverse-proxy-web` tienen puertos publicados (`0.0.0.0`) y que todos los demás contenedores son internos.
- **Grupo 6 — Aislamiento de red Docker:** verifica que `internal: true` está configurado en `docker-compose.yml` y que las redes públicas y privadas están correctamente separadas.

La prueba del Escenario 2 (frontend aislado de bases de datos) se ejecuta manualmente entrando al contenedor `web-page`:

```bash
docker exec -it arquisoft-web-page-1 sh
# Intentar alcanzar las bases de datos (deben fallar):
ping mongo          # → ping: bad address 'mongo'
nc -zv mongo 27017  # → nc: bad address 'mongo'
```

**Ejecución de la suite completa:**

```bash
# Desde la raíz del repositorio umbrella, con el sistema desplegado
python3 reverse-proxy/test_security_comparison.py
python3 reverse-proxy/test_confidenciality.py
bash   reverse-proxy/test_confidenciality.sh
```

**Resultados verificados con el sistema desplegado:**

```
SUMMARY: 32 passed, 0 failed, 28 protections confirmed

Protecciones confirmadas relevantes a Network Segmentation:
  ✓ Puerto 27017 (MongoDB)     — BLOQUEADO desde el host (connection refused)
  ✓ Puerto 5432  (PostgreSQL)  — BLOQUEADO desde el host (connection refused)
  ✓ Puerto 6379  (Redis)       — BLOQUEADO desde el host (connection refused)
  ✓ Puerto 8080  (API Gateway) — BLOQUEADO desde el host (connection refused)
  ✓ Puerto 3001  (Users Svc)   — BLOQUEADO desde el host (connection refused)
  ✓ Puerto 8000  (YouTube Svc) — BLOQUEADO desde el host (connection refused)
  ✓ Puerto 8001  (Trends Svc)  — BLOQUEADO desde el host (connection refused)
  ✓ Puerto 8193  (NLP Svc)     — BLOQUEADO desde el host (connection refused)
  ✓ web-page → mongo           — INALCANZABLE (bad address — sin DNS en private_internal)
  ✓ web-page → redis           — INALCANZABLE (bad address)
  ✓ web-page → postgres        — INALCANZABLE (bad address)
  ✓ internal=true configurado en raccon_private_internal
  ✓ Solo reverse-proxy (8081) y reverse-proxy-web (8443/8080) accesibles externamente
```

---

## 5. Prototype

### 5.1. Instructions

_Prerrequisitos:_

- El despliegue de este sistema está orientado a contenedores, usando Docker y docker compose.
  Por tanto, si se desea hacer el despliegue orientado a contenedores solo se necesita tener Docker en el equipo.

- Si se desea hacer el despliegue de forma local sin contenedroes, Se deben tener las siguientes aplicaciones instaladas en el sistema: - API Gateway (Go) - Go 1.22 (según api-gateway/go.mod) - Variables de entorno de servicios dependientes (URLs a users/youtube/etc.) en .env (api-gateway/README.md)

      - Google Trends Acquisition Service
          - Python 3.11+ y pip
          - FastAPI + Uvicorn + Pytrends + Motor/PyMongo (requirements en google-trends-acquisition-service/requirements.txt)
          - MongoDB (cache y TTL, ver app/db/cache_repository.py)
          - Variables de entorno .env (ver google-trends-acquisition-service/README.md)

      - YouTube Acquisition Service
          - Python 3.11+ y pip
          - FastAPI + Uvicorn + Motor/PyMongo + Redis client (ver youtube-acquisition-service/requirements.txt)
          - MongoDB (persistencia) y Redis (cache)

  YouTube Data API v3 key (configuración en .env)

      - NLP Service (NVIDIA NIM)
          - Java 17 (maven compiler 17) + Maven (ver nlp-service-nvidia/pom.xml)
          - Spring Boot
          - API key de NVIDIA NIM (nvidia.nim.api.key) en variables de entorno o properties (application.properties)

       - Users Service
          - Node.js + npm
          - NestJS + TypeScript (ver users-service/package.json)
          - PostgreSQL (usado por Prisma)
          - Redis (sesiones/cache)
          - Variables de entorno .env (ver users-service/README.md)

  Web Frontend (Next.js)

          - Node.js + npm/yarn/pnpm
          - Next.js + React (ver web-page/package.json)

      - Desktop Frontend (WPF)
          - Windows
          - .NET SDK 10 (target net10.0-windows) + WPF (ver desktop-frontend/desktop-frontend.csproj)

---

**Pasos para despliegue:**
El sistema se distribuye utilizando una estrategia de repositorio tipo umbrella, donde un repositorio principal actúa como orquestador y contiene referencias a los distintos microservicios mediante submódulos de Git.

Este enfoque permite mantener cada componente desacoplado, pero coordinado desde un único punto de entrada para facilitar el despliegue.

1. Clonar el repositorio principal: El repositorio prototype-2 actá como orquestador y punto de entrada para despliegue al tener punteros que referencian cada uno de los repositorios de cada componente del sistema. Se clonan de forma recursiva los repositorios apuntados desde prototype-2 con el comando a continuación.

```bash
git clone --recurse-submodules https://github.com/RacconAnalytics/prototype-2.git
```

_Notas:_

- _Si ya clonó antes el repositorio ejecute el siguiente comando para sincronizar todos los subrepositorios bajo prototype-2_

```bash
git submodule update --init --recursive
```

- _Para evitar problemas de despliegue por contenedores ya existentes en el equipo de despliegue que puedan tener el mismo nombre que los contenedores referenciacos aquí, o porqué ya hay contenedores corriendo en puertos a ser usados, se opta por ejecutar los siguiente comandos:_

```bash
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

2. Ejecutar el docker compose: Este comando permite levantar todos los contenedores por cada uno de los componentes del sistema a partir de un docker compose que actua como orquestador de despliegue desde el repositorio principal prototype-2.

```bash
docker compose up -d --build
```

Con todos los contenedores activos, teniendo los componentes en ejecución, ya es posible acceder y probar sistema desde la interfaz web, ingresando al puerto 3000 en localhost:
**http://localhost:3000**
