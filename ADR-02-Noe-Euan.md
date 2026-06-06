# ADR 02: Adopción de Vistas Arquitectónicas Multidimensionales para EmergencyFlow

## Status
Proposed

## Context
Con el crecimiento del sistema *EmergencyFlow* y la necesidad de comunicar de manera clara la estructura de software a diferentes interesados, se vuelve insuficiente contar únicamente con diagramas de contexto. Es necesario describir el sistema desde múltiples perspectivas concurrentes para asegurar la comprensión de los flujos de proceso, la organización del código, la infraestructura física y la topología de despliegue.

## Decision
Hemos decidido adoptar un enfoque de diseño basado en **Vistas Arquitectónicas** para modelar de manera integral el sistema *EmergencyFlow*. Específicamente, documentaremos e implementaremos 4 vistas fundamentales utilizando la herramienta estándar Mermaid:

1. **Vista Lógica:** Describe la organización del código en capas.
2. **Vista de Procesos:** Mapea el flujo dinámico de un reporte desde que se genera hasta su persistencia.
3. **Vista Física:** Muestra la organización del código fuente en el repositorio.
4. **Vista de Despliegue:** Modela la topología de red y servidores para producción.

Asimismo, se incluye formalmente la **Declaración de Uso de IA** en cumplimiento con los lineamientos académicos vigentes.

## Consequences
* **Positivas:** Mayor claridad para el desarrollo, facilidad para auditar el flujo de los reportes de emergencia y un sustento de diseño impecable para la evaluación académica.
* **Negativas:** Incremento en el tiempo de mantenimiento de la documentación técnica ante futuros cambios de diseño.

---

## Diagramas de las 4 Vistas Aplicadas
