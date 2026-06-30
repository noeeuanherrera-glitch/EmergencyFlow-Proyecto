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

    Views --> Controllers
    Controllers --> InterfacesB
    Services --> InterfacesB
    Services --> InterfacesD
    Repositories --> InterfacesD
    Repositories --> Entities
sequenceDiagram
    autonumber
    actor Usuario
    participant Vista as UI Razor View
    participant Ctrl as IncidenteController
    participant Serv as IncidenteService
    participant Repo as IncidenteRepository
    
    Usuario->>Vista: Rellena formulario de incidente y da clic en Enviar
    Vista->>Ctrl: Envia peticion POST con los datos del reporte
    Ctrl->>Serv: Llama a RegistrarIncidente
    Note over Serv: Se ejecutan las validaciones de negocio
    alt Datos Invalidos
        Serv-->>Ctrl: Retorna false y Mensaje de error
        Ctrl-->>Vista: Recarga la vista con alertas visuales
    else Datos Validos
        Serv->>Repo: Llama a Agregar incidente
        Note over Repo: Se genera ID unico y se guarda
        Repo-->>Serv: Confirmacion de guardado
        Serv-->>Ctrl: Retorna true de Exito
        Ctrl-->>Vista: Redirecciona al listado de incidentes
    end
