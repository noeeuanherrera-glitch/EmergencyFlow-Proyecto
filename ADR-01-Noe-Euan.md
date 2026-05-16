# ADR-01: Selección del Patrón de Arquitectura para el Sistema de Despacho de Emergencias

| Campo  | Valor |
|--------|-------|
| Autor  | Noé Euan Herrera |
| Fecha  | 16/05/2026 |
| Estado | `Propuesto` |

---

## Contexto

Estamos construyendo una plataforma web llamada "Emergency Flow" para la gestión y optimización de rutas de ambulancias conectadas a las salas de urgencias de los hospitales. El problema principal es que la asignación actual de unidades se hace de forma manual o empírica, lo que genera retrasos críticos en la atención y la saturación de los centros médicos más cercanos por falta de coordinación de datos. El sistema debe procesar datos constantes (ubicación de ambulancias, estatus de gravedad del paciente y disponibilidad de camas) y coordinar el despacho sin perder tiempo.

Como restricciones principales que influyen en el diseño, contamos únicamente con el tiempo del cuatrimestre para el desarrollo, el equipo de programación es pequeño y utilizaremos las tecnologías backend basadas en el ecosistema de .NET / C# y la arquitectura web MVC que hemos estado revisando en las materias de la carrera. El sistema requiere un flujo estable y no puede ser propenso a caídas lentas, ya que un retraso de software aquí afecta directamente la atención de un paciente.

---

## Decisión

Decidí implementar una **Arquitectura en Capas (N-Tier Architecture)** combinada con un **Patrón de Repositorio (Repository Pattern)** para el desarrollo del backend en ASP.NET Core, separando estrictamente la interfaz de usuario, la lógica de negocio (reglas de asignación de ambulancias) y el acceso a los datos de los hospitales en la base de datos SQL Server.

### ¿Por qué?

Elegí esta arquitectura porque la separación de responsabilidades nos permite aislar el algoritmo de optimización de rutas y el cálculo de disponibilidad hospitalaria de la persistencia de datos. Si más adelante necesitamos cambiar el proveedor de base de datos o modificar las reglas con las que se calcula qué ambulancia está más cerca, solo se requiere tocar la capa de negocio sin romper la pantalla web ni el almacenamiento. Además, al mapear las entidades (Ambulancia, Hospital, Incidente) de forma independiente, el código se vuelve modular, fácil de depurar y se adapta perfectamente al patrón arquitectónico que manejamos en clase.

### Alternativas consideradas

| Alternativa | Por qué la descarté |
|-------------|---------------------|
| **Arquitectura de Microservicios** | Aunque ofrece la mayor disponibilidad, su configuración, despliegue en contenedores (Docker) y la comunicación entre bases de datos independientes elevaría demasiado la complejidad técnica, consumiendo el tiempo del cuatrimestre en pura configuración de infraestructura en lugar de programar la lógica del proyecto. |
| **Arquitectura Monolítica Tradicional (Todo en un solo proyecto sin capas)** | Es rápida de implementar al inicio, pero mezclar las consultas de la base de datos directamente con las vistas web crearía un "código espagueti". Conforme el proyecto avance y agreguemos más funciones a lo largo del cuatrimestre, se volvería imposible de mantener, escalar o hacer pruebas unitarias. |
| **Arquitectura Orientada a Eventos (EDA) pura** | Requiere implementar herramientas de mensajería asíncrona complejas (como RabbitMQ o Kafka) para manejar los cambios de estatus de las ambulancias. Para el alcance escolar del proyecto, el manejo de peticiones síncronas bien optimizadas a través de la API en capas es suficiente y mitiga errores de sincronización de datos. |

---

## Consecuencias

**✅ Lo que gano:**

- Una consecuencia **técnica**: El sistema se vuelve altamente mantenible y escalable. Si el algoritmo de despacho se vuelve más complejo o se integran mapas externos más adelante, la estructura de capas permitirá modificar esa lógica sin alterar las consultas de datos existentes ni la interfaz del operador.
- Una consecuencia sobre el **proceso o el equipo**: Al estar el proyecto dividido en capas claras con el patrón de repositorio, el equipo puede trabajar en paralelo sin pisarse el código; un desarrollador puede diseñar las pantallas de asignación en el Front-End mientras otro programa los métodos de consulta de datos en el Backend.

**⚠️ Lo que sacrifico o asumo:**

- Una **limitación técnica**: Al usar una arquitectura síncrona en capas sobre una base de datos relacional, si el sistema llega a recibir cientos de solicitudes simultáneas de geolocalización de ambulancias al mismo tiempo, el rendimiento de la base de datos podría verse ralentizado debido al bloqueo de tablas.
- Una **deuda o riesgo**: Al aislar el acceso a datos mediante repositorios, se escribe más código inicial (interfaces y clases de implementación). Si el proyecto crece exponencialmente fuera del entorno escolar, tendremos el riesgo de sufrir un ligero retraso en el tiempo de respuesta general debido al paso de datos a través de múltiples capas intermedias, lo que nos obligaría a meter capas de caché (como Redis) más adelante.

## Diagrama

Un boceto de cómo se estructura tu sistema (draw.io, Mermaid o a mano escaneado)

![Diagrama del sistema](https://github.com/noeeuanherrera-glitch/EmergencyFlow-Proyecto.git)