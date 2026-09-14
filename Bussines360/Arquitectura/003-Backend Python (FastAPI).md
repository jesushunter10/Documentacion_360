
## Enfoque Arquitectónico (Monolito Modular)

Al igual que en el frontend, el backend implementa una estructura de **Monolito Modular con arquitectura en capas (Domain-Driven / Clean Architecture)**.

### ¿Por qué FastAPI?

- **Rendimiento Asíncrono (`async/await`):** Alta capacidad para procesar solicitudes concurrentes (fundamental para integraciones de IA, webhooks de WhatsApp y carga masiva de archivos).
    
- **Validación de Datos con Pydantic:** Garantiza tipado estricto en la entrada y salida de las API REST, previniendo fallos en tiempo de ejecución.
    
- **Documentación Automática (OpenAPI / Swagger):** Genera la documentación interactiva de los endpoints en tiempo real (`/docs`).
    

## Estructura y Manejo de Carpetas Backend

El proyecto se divide en una capa global (`core/`, `db/`) y subcarpetas desacopladas por cada dominio de negocio en `modules/`.

Plaintext

```
backend/
├── app/
│   ├── core/                   # Configuración global del sistema
│   │   ├── config.py           # Variables de entorno (.env, JWT secret, DB URLs)
│   │   ├── security.py         # Funciones de hashing (Bcrypt) y                 │   |   |                             generación/verificación de Tokens JWT
│   │   └── deps.py             # Dependencias compartidas de FastAPI (get_db,     │   │                                  get_current_user)
│   │
│   ├── db/                     # Capa de Persistencia y Base de Datos
│   │   ├── session.py          # Conexión y sesión de SQLAlchemy con PostgreSQL
│   │   └── base_class.py       # Modelo base declarativo de SQLAlchemy
│   │
│   ├── shared/                 # Utilidades compartidas (Middlewares, Helpers,    |   |                                  Handlers de Errores)
│   │   ├── exceptions.py       # Excepciones HTTP personalizadas
│   │   └── utils.py            # Formateadores, manipuladores de archivos, etc.
│   │
│   ├── modules/                # MÓDULOS DE NEGOCIO (Desacoplados)
│   │   │
│   │   ├── [nombre_modulo_1]/  # Ejemplo: auth
│   │   │   ├── router.py       # Endpoints de la API (/api/v1/auth/login, etc.)
│   │   │   ├── service.py      # Lógica de negocio (casos de uso)
│   │   │   ├── schemas.py      # Modelos de validación Pydantic (Request/Response)
│   │   │   └── models.py       # Modelos ORM de SQLAlchemy para PostgreSQL
│   │   │
│   │   ├── [nombre_modulo_2]/  # Ejemplo: ats, nomina, etc.
│   │   │   ├── router.py
│   │   │   ├── service.py
│   │   │   ├── schemas.py
│   │   │   └── models.py
│   │   │
│   │   └── [nuevo_modulo]/     # Plantilla para incorporar un nuevo módulo       |   |       |                                        funcional
│   │       ├── router.py
│   │       ├── service.py
│   │       ├── schemas.py
│   │       └── models.py
│   │
│   └── main.py                 # Punto de entrada de la aplicación FastAPI        |                                   (Routing Global y Middleware CORS)
│
├── alembic/                    # Gestión de Migraciones de Base de Datos
├── .env                        # Variables de entorno locales
├── Dockerfile                  # Contenedor para producción / Cloud Run
└── requirements.txt            # Dependencias del proyecto Python
```

## Flujo de Responsabilidades por Capa (Mapeo de Capas)

Para mantener una clara separación de responsabilidades, cada módulo funcional debe cumplir la siguiente división:

Plaintext

```
HTTP Request ──► [ router.py ] ──► [ service.py ] ──► [ models.py (SQLAlchemy) ] ──► PostgreSQL
                      │                  │
               Validación con      Reglas de
             [ schemas.py ]      Negocio Pure
```

|**Capa / Archivo**|**Responsabilidad Principal**|**Reglas de Uso**|
|---|---|---|
|**`router.py`**|Define los endpoints (API REST), métodos HTTP (`GET`, `POST`, `PUT`, `DELETE`) y códigos de estado.|Solo recibe la petición, aplica dependencias (`deps.py`) y llama a la capa de servicios. No contiene lógica de negocio.|
|**`schemas.py`**|Esquemas de validación entrada/salida mediante Pydantic.|Garantiza la integridad de los datos que envía el cliente y sanitiza las respuestas antes de devolverlas.|
|**`service.py`**|Implementa la lógica de negocio pura y consultas a la base de datos.|Contiene las operaciones CRUD y reglas del ERP.|
|**`models.py`**|Define la estructura de las tablas en PostgreSQL mapeadas como objetos Python mediante SQLAlchemy.|Refleja el esquema relacional de la base de datos.|