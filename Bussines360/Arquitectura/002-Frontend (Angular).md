
#### Carga Perezosa Condicional (Lazy Loading) y PWA Multiplataforma (Escritorio y Móvil)

En un sistema ERP escalar que engloba múltiples módulos (ATS, Nómina, Finanzas, Operaciones), empaquetar toda la aplicación en un solo archivo JavaScript (`main.js`) provoca que el usuario tenga que descargar todo el sistema al iniciar sesión. Para mantener la máxima velocidad y una experiencia de usuario optimizada en cualquier dispositivo, se utiliza **Carga Perezosa (Lazy Loading)** combinada con las capacidades de instalación de una **PWA (Progressive Web App)** adaptada tanto para **Escritorio** como para **Móviles (Android e iOS)**.

- **Rendimiento e Inicio Rápido (Time to Interactive - TTI):** Carga únicamente el bundle necesario para la pantalla actual (por ejemplo, el módulo de Auth/Login), reduciendo drásticamente el consumo de datos y el peso inicial de la aplicación, lo cual es crítico en redes móviles.
    
- **Eficiencia en Uso de Recursos:** Un reclutador del ATS no necesita descargar en memoria los módulos de Nómina o Finanzas a menos que navegue explícitamente hacia ellos.
    
- **Escalabilidad del Monolito Modular:** Permite que diferentes módulos funcionales se desarrollen e independicen en subcarpetas/módulos de Angular, garantizando que añadir un módulo nuevo en el futuro no afecte la velocidad del resto del sistema.
    
- **Carga Condicional (Basada en Roles/Guardias):** La carga perezosa se integra directamente con las guardias de seguridad (`CanMatch` / `CanActivate`). Si un usuario no tiene los permisos RBAC/ABAC para el módulo de Finanzas, el navegador nunca llegará a descargar el código fuente de ese módulo.
    
- **Experiencia de Aplicación Nativa Multiplataforma (Look and Feel Adaptativo):** La PWA ofrece una interfaz fluida tanto en computadoras como en teléfonos o tabletas:
    
    - **Acceso Directo e Ícono:** El ERP se instala con su propio icono corporativo en el Escritorio/Menú Inicio (Windows/macOS) y en la **Pantalla de Inicio del móvil (Android/iOS)**.
        
    - **Ventana e Interfaz Independiente (Standalone):** Al abrir la app desde el móvil o la PC, se ejecuta en su propio contenedor sin barra de direcciones URL, pestañas de navegación ni controles del navegador, ofreciendo una experiencia idéntica a una app nativa.
        
    - **Diseño Responsivo de Borde a Borde:** La interfaz de Angular ajusta automáticamente el _Layout_ (menú lateral replegable/Bottom Bar en móvil) garantizando la misma comodidad de uso en pantallas táctiles pequeñas o en monitores multiventana.
        

#### Estructura General y Manejo de Carpetas

Para mantener una arquitectura extensible y fácil de escalar en entornos web, desktop y mobile, la aplicación se organiza bajo la siguiente estructura modular con soporte PWA:

Plaintext

```
src/
│
├── app/
│   ├── core/                   # Módulo Singleton (Servicios globales, Guardias e Interceptores)
│   │   ├── guards/             # Guardias de protección de rutas (Auth, Roles, CanMatch)
│   │   ├── interceptors/      # Interceptores HTTP (Tokens JWT, Manejo de errores)
│   │   ├── models/             # Modelos e interfaces globales
│   │   └── services/           # Servicios globales (AuthService, HTTP Base)
│   │
│   ├── shared/                 # Módulo de Recursos Compartidos (Reutilizable en todo el proyecto)
│   │   ├── components/         # Componentes comunes (Botones, Tablas, Modales, Skeletons)
│   │   ├── directives/         # Directivas personalizadas
│   │   ├── pipes/              # Formateadores (Fechas, Monedas, Textos)
│   │   └── shared.module.ts
│   │
│   ├── modules/                # MÓDULOS DE NEGOCIO (Cargados perezosamente via Lazy Loading)
│   │   │
│   │   ├── [nombre-modulo-1]/  # Ejemplo: auth, dashboard, etc.
│   │   │   ├── pages/          # Vistas / Pantallas principales del módulo
│   │   │   ├── components/     # Sub-componentes exclusivos del módulo
│   │   │   ├── services/       # Servicios específicos de lógica de negocio del módulo
│   │   │   ├── models/         # Interfaces/Modelos propios del módulo
│   │   │   ├── [modulo-1]-routing.module.ts  # Rutas internas del módulo
│   │   │   └── [modulo-1].module.ts          # Definición del módulo
│   │   │
│   │   ├── [nombre-modulo-2]/  # Ejemplo: ats, nomina, inventarios, etc.
│   │   │   ├── pages/
│   │   │   ├── components/
│   │   │   ├── services/
│   │   │   ├── models/
│   │   │   ├── [modulo-2]-routing.module.ts
│   │   │   └── [modulo-2].module.ts
│   │   │
│   │   └── [nuevo-modulo]/     # Estructura a replicar para cada nuevo módulo funcional
│   │       ├── pages/
│   │       ├── components/
│   │       ├── services/
│   │       ├── models/
│   │       ├── [nuevo-modulo]-routing.module.ts
│   │       └── [nuevo-modulo].module.ts
│   │
│   ├── layout/                 # Estructura o Shell Visual Adaptativo (Desktop / Mobile)
│   │   ├── header/             # Encabezado superior
│   │   ├── sidebar/            # Menú lateral (Desktop) / Menú colapsable (Mobile)
│   │   └── main-layout/        # Layout principal que contiene el router-outlet
│   │
│   ├── app-routing.module.ts   # Enrutador Raíz (Configuración de Lazy Loading de cada módulo)
│   └── app.module.ts           # Módulo Raíz principal
│
├── assets/                     # Recursos Estáticos
│   └── icons/                  # Logotipos e íconos adaptados para SO y Móviles (192x192, 512x512, Apple Touch Icon)
│
└── manifest.webmanifest        # Definición del modo 'standalone', orientación e iconos 
```