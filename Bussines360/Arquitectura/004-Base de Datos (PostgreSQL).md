## Principios de Diseño **Party / Person Centralized (Persona Única)**.

Para evitar la duplicación de registros en un ERP escalar, se utiliza el patrón de **Generalización y Extensión de Entidades**.

- **Registro Único Master:** Una entidad real (persona, empresa, activo) existe una sola vez en la base de datos, sin importar los roles o estados por los que transite.
    
- **Segregación de Atributos Específicos:** La información común obligatoria se almacena en la tabla máster, mientras que los atributos particulares de un perfil o función se extienden en tablas secundarias.
    

## Convención de Nomenclatura de Tablas

Para mantener consistencia en todo el ERP, las tablas deben escribirse en minúsculas y utilizar obligatoriamente los siguientes prefijos:

|**Prefijo**|**Definición**|**Descripción**|
|---|---|---|
|**`dm_`**|**Data Master**|Tablas maestras globales. Almacenan datos universales y obligatorios compartidos entre múltiples módulos.|
|**`cat_`**|**Catálogos**|Tablas de referencia estáticas o dinámicas utilizadas para listas desplegables y parametrización.|
|**`dt_`**|**Data Transaccional / Relacionada**|Tablas de extensión, expedientes específicos, transacciones o relaciones N:M.|

## Esquema Generalizado de Extensión


```
                           ┌─────────────────────────┐
                           │       dm_[entidad]      │ (Datos generales                                       |                         |   obligatorios)
                           └────────────┬────────────┘
                                        │
           ┌────────────────────────────┼────────────────────────────┐
           │ 1:1                        │ 1:1                        │ 1:N
           ▼                            ▼                            ▼
┌──────────────────────┐    ┌──────────────────────┐    ┌──────────────────────┐
│ dt_expediente_[perfil1]│  │ dt_expediente_[perfil2]│  │    dt_[transaccion]  │
│ (Datos específicos   │    │ (Datos específicos   │    │ (Historial / Eventos │
│  del Perfil 1)       │    │  del Perfil 2)       │    │  del sistema)        │
└──────────────────────┘    └──────────────────────┘    └──────────────────────┘
```

## Plantilla SQL Genérica (PostgreSQL)

SQL
# Esquema Genérico de Base de Datos - ERP

> [!INFO] **Convención de Nomenclatura**
> - **`cat_`**: Tablas de Catálogo (Listas de referencia estáticas/dinámicas).
> - **`dm_`**: Data Master (Entidades principales unificadas).
> - **`dt_`**: Data Transaccional / Relacionada (Expedientes derivados y movimientos 1:1 o 1:N).

---

