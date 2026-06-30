# ADR-02: Definición y Documentación de Vistas Arquitectónicas para EmergencyFlow

| Campo | Valor |
|-------|-------|
| Autor | Noé Euan Herrera |
| Fecha | 05/06/2026 |
| Estado | `Propuesto` |

---

## Contexto
A medida que el backend y la estructura de "Emergency Flow" avanzan, un diagrama de contexto general resulta insuficiente para describir el comportamiento y la distribución del sistema. Se requiere una documentación técnica que desglose el proyecto desde diferentes perspectivas concurrentes para asegurar la comprensión de los flujos de proceso, la organización del código y la infraestructura de servidores.

## Decisión
Se ha decidido implementar el modelo de vistas arquitectónicas para documentar el sistema. El modelado se realizará directamente en lenguaje Mermaid dentro del repositorio para facilitar el mantenimiento.

Se documentan las siguientes cuatro perspectivas:
1. **Vista Lógica:** Estructura interna mediante el patrón de desarrollo en capas.
2. **Vista de Procesos:** Flujo dinámico de la comunicación entre componentes durante un evento.
3. **Vista Física:** Organización real de los directorios dentro del repositorio.
4. **Vista de Despliegue:** Topología de red y servidores para producción.

## Consecuencias
* **Positivas:** Mayor claridad en el flujo de datos y un respaldo técnico robusto para las evaluaciones del proyecto.
* **Negativas:** Requiere actualización manual de los diagramas ante cambios estructurales en el código fuente.

---

## Diagramas de las 4 Vistas Aplicadas

### 1. Vista Lógica
Muestra los componentes lógicos organizados bajo el esquema de N-Capas de .NET 8.

```mermaid
graph TD
    subgraph Capa_Presentacion["Capa Presentación"]
        Controllers["Controladores (IncidenteController)"]
        Views["Vistas (Razor HTML / CSS)"]
    end
    
    subgraph Capa_Negocio["Capa Negocio"]
        Services["Servicios (IncidenteService)"]
        InterfacesB["Interfaces de Servicio (IIncidenteService)"]
    end
    
    subgraph Capa_Datos["Capa Datos"]
        Repositories["Repositorios (IncidenteRepository)"]
        InterfacesD["Interfaces de Repositorio (IIncidenteRepository)"]
        Entities["Entidades (Incidente.cs)"]
    end

### 2. Vista de Procesos
Representa el flujo dinámico y el comportamiento del sistema cuando un usuario realiza la acción de reportar un incidente.

```mermaid
sequenceDiagram
    autonumber
    actor Usuario
    participant Vista as UI (Razor View)
    participant Ctrl as IncidenteController
    participant Serv as IncidenteService
    participant Repo as IncidenteRepository
    
    Usuario->>Vista: Rellena formulario de incidente y da clic en "Enviar"
    Vista->>Ctrl: Envía petición POST con los datos del reporte
    Ctrl->>Serv: Llama a RegistrarIncidente(incidente)
    Note over Serv: Se ejecutan las validaciones de negocio
    alt Datos Inválidos
        Serv-->>Ctrl: Retorna false + Mensaje de error
        Ctrl-->>Vista: Recarga la vista con alertas visuales
    else Datos Válidos
        Serv->>Repo: Llama a Agregar(incidente)
        Note over Repo: Se genera ID único y se guarda en el repositorio
        Repo-->>Serv: Confirmación de guardado
        Serv-->>Ctrl: Retorna true (Éxito)
        Ctrl-->>Vista: Redirecciona al listado general de incidentes
    end
    Views --> Controllers
    Controllers --> InterfacesB
    Services --> InterfacesB
    Services --> InterfacesD
    Repositories --> InterfacesD
    Repositories --> Entities

EmergencyFlow/
├── Flujo de emergencia.sln (Archivo de Solución Global)
├── Presentación de EmergencyFlow/ (Capa de Interfaz de Usuario y Controladores)
├── EmergencyFlow.Negocios/ (Capa de Lógica de Negocio y Validaciones)
└── Datos de EmergencyFlow/ (Capa de Persistencia y Repositorios)

graph LR
    subgraph Dispositivo_Cliente["Dispositivo Cliente"]
        Browser["Navegador Web<br>(Chrome / Edge / Safari)"]
    end
    
    subgraph Servidor_Cloud["Servidor Cloud (Azure / AWS)"]
        IIS["Servidor Kestrel / IIS<br>(Aloja Presentación de EmergencyFlow)"]
        Runtime[".NET 8.0 Runtime"]
    end
    
    subgraph Servidor_Base_Datos["Servidor Base de Datos"]
        DB[("Motor SQL Server<br>(Base de Datos Relacional)")]
    end

    Browser -->|HTTPS / Puerto 443| IIS
    IIS <--> Runtime
    Runtime -->|Cadenas de Conexión / TCP IP| DB
