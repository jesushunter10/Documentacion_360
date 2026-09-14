**Descripción de campos de `cat_puestos`:**

- **`puesto_id`**: Identificador único secuencial del puesto.
    
- **`puesto_padre_id`**: Clave foránea autorreferencial (autodependencia) que apunta al `puesto_id` del jefe inmediato/puesto superior. Si es `NULL`, indica que es la cabeza de la jerarquía (ej. CEO).
    
- **`empresa_id`**: Clave foránea que asigna el puesto a una empresa específica (`dm_empresas`).
    
- **`area_id`**: Clave foránea que ubica el puesto dentro de un área funcional (`cat_areas`).
    
- **`rol_id`**: Clave foránea que define el rol y nivel de permisos predeterminado del puesto (`seg_roles`).
    
- **`nombre_puesto`**: Título del puesto (ej. Gerente de Reclutamiento, Coordinador RH, Reclutador Junior).
    
- **`descripcion`**: Detalle de las responsabilidades principales del puesto.
    
- **`estatus`**: Estado de activación del puesto (`TRUE` = Activo, `FALSE` = Inactivo).
    
- **`creado_en`**: Fecha y hora de creación del registro.
    

#### Tabla: `seg_roles` (Actualizada)

Catálogo maestro de roles del sistema, incluyendo el nivel jerárquico asociado dentro de la organización.

|**Nombre del campo**|**Tipo de dato**|**Clave (PK / FK)**|
|---|---|---|
|`rol_id`|`SERIAL` / `INT`|**PK**|
|`nombre_rol`|`VARCHAR(100)`|No|
|`nivel_jerarquico`|`VARCHAR(50)`|No|
|`descripcion`|`TEXT`|No|

**Descripción de campos de `seg_roles`:**

- **`rol_id`**: Identificador único del rol.
    
- **`nombre_rol`**: Nombre identificador del perfil (ej. Administrador, Director, Gerente, Jefe, Operativo).
    
- **`nivel_jerarquico`**: Clasificación del nivel de autoridad (ej. `CEO`, `DIRECTOR`, `GERENTE`, `SUPERVISOR`, `OPERATIVO`).
    
- **`descripcion`**: Detalle del alcance global que posee el rol.
```mermaid
erDiagram
    dm_empresas ||--o{ cat_puestos : "contiene"
    cat_areas ||--o{ cat_puestos : "agrupa"
    seg_roles ||--o{ cat_puestos : "asigna nivel a"

    cat_puestos ||--o{ cat_puestos : "reporta a (autodependencia)"
    cat_puestos ||--o{ seg_usuarios : "desempeñado por"

    dm_empresas {
        int empresa_id PK
        string nombre_razon_social
    }

    cat_areas {
        int area_id PK
        string nombre_area
    }

    seg_roles {
        int rol_id PK
        string nombre_rol
        string nivel_jerarquico
    }

    cat_puestos {
        int puesto_id PK
        int puesto_padre_id FK
        int empresa_id FK
        int area_id FK
        int rol_id FK
        string nombre_puesto
    }

    seg_usuarios {
        int usuario_id PK
        int puesto_id FK
        string nombre
    }
    
```


