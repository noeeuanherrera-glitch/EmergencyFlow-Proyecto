# ADR-03: Selección del Estilo Arquitectónico para EmergencyFlow

| Campo | Valor |
|-------|-------|
| Autor | Noé Euan Herrera |
| Fecha | 30/06/2026 |
| Estado | `Aprobado` |

---

## Contexto
El sistema *EmergencyFlow* requiere una estructura clara que permita procesar reportes de incidentes, validar reglas de negocio críticas (como la asignación de prioridades) y gestionar la persistencia de datos de forma eficiente. Para evitar un diseño acoplado y garantizar que el software sea mantenible, se debe definir un estilo arquitectónico formal.

## Estilo Arquitectónico Elegido
Se ha seleccionado el estilo de **Arquitectura en Capas (Layered Architecture / N-Tier)**. La solución de Visual Studio se ha dividido físicamente en tres proyectos con un flujo de dependencia unidireccional (de arriba hacia abajo):
1. **EmergencyFlow.Presentation:** Interfaz de usuario (Razor Views) y Controladores.
2. **EmergencyFlow.Business:** Centraliza las reglas de asignación y despacho de incidentes.
3. **EmergencyFlow.Data:** Maneja la abstracción del almacenamiento mediante el patrón repositorio.

## Justificación
Este estilo resuelve de manera óptima el problema por las siguientes razones:
* **Curva de desarrollo:** Permite un avance rápido y ordenado, ideal para los tiempos de entrega del cuatrimestre.
* **Separación de Responsabilidades:** Se puede modificar el diseño visual en la capa de presentación sin alterar las reglas críticas de negocio en el backend.
