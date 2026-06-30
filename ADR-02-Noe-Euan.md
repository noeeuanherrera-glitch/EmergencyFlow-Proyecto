# ADR-02: Definición y Documentación de Vistas Arquitectónicas para EmergencyFlow

| Campo | Valor |
|-------|-------|
| Autor | Noé Euan Herrera |
| Fecha | 05/06/2026 |
| Estado | `Propuesto` |

---

## Contexto
A medida que el backend y la estructura de "Emergency Flow" avanzan, un diagrama de contexto general resulta insuficiente para describir el comportamiento y la distribución del sistema. Se requiere una documentación técnica que desglose el proyecto desde diferentes perspectivas concurrentes. Esto permitirá mapear los flujos lógicos, los procesos en tiempo real, la disposición de los archivos en el entorno de desarrollo y la infraestructura de servidores necesaria para su puesta en marcha.

## Decisión
Se ha decidido implementar el modelo de vistas arquitectónicas para documentar de forma el sistema. Para cumplir con el estándar técnico y facilitar el mantenimiento del proyecto, el modelado de los diagramas se realizará directamente en lenguaje Mermaid dentro del repositorio. 

Se documentarán las siguientes cuatro perspectivas específicas:
1. **Vista Lógica:** Estructuración interna del software mediante el patrón de desarrollo en capas.
2. **Vista de Procesos:** Comportamiento dinámico del sistema y comunicación entre componentes durante un evento.
3. **Vista Física:** Organización real de los subproyectos y directorios dentro del repositorio.
4. **Vista de Despliegue:** Topología de red y servidores requeridos para el entorno de producción.

Asimismo, se anexa la declaración formal sobre las herramientas de apoyo utilizadas durante el proceso de diseño.

## Consecuencias
* **Positivas:** Mayor claridad en el flujo de datos para futuras implementaciones, modularidad transparente y un respaldo técnico robusto para las evaluaciones del proyecto.
* **Negativas:** Requiere tiempo adicional para actualizar los diagramas si se realizan modificaciones estructurales en el código.

---

## Diagramas de las 4 Vistas Aplicadas

### 1. Vista Lógica
Describe la distribución de los componentes del software organizados bajo el esquema de N-Capas implementado en el proyecto.

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
    participant Vista as UI (Razor View)
    participant Ctrl as IncidenteController
    participant Serv as IncidenteService
    participant Repo as IncidenteRepository
    
    Usuario->>Vista: Rellena formulario de incidente y da clic en "Enviar"
    Vista->>Ctrl: Envía petición POST con los datos del reporte
    Ctrl->>Serv: Llama a RegistrarIncidente(incidente)
    Note over Serv: Se ejecutan validaciones:<br/>¿El título o descripción están vacíos?
    alt Datos Inválidos
        Serv-->>Ctrl: Retorna false + Mensaje de error
        Ctrl-->>Vista: Recarga la vista con alertas visuales
    else Datos Válidos
        Serv->>Repo: Llama a Agregar(incidente)
        Note over Repo: Se genera un ID único y<br/>se guarda en el repositorio
        Repo-->>Serv: Confirmación de guardado
        Serv-->>Ctrl: Retorna true (Éxito)
        Ctrl-->>Vista: Redirecciona al listado general de incidentes
    end
