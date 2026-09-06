# 📄 Informe Técnico del Taller

## 🔖 Nombre del Taller
Taller 3 - Arquitectura Actual del Sistema con el Modelo C4

## 👥 Integrantes del equipo
- Julián David Aguilar
- Juan Esteban Ramirez

## 🧠 Descripción general del trabajo
El objetivo del taller es representar la arquitectura actual del sistema de programación y ejecución de encuestas de satisfacción de la Universidad de La Sabana mediante las vistas C1 (Contexto) y C2 (Contenedores) del modelo C4. Primero se trabajó el ejemplo guiado de RedExpress para interiorizar la metodología de 4 pasos, y luego se aplicó la misma metodología al caso real del cliente (Vicerrectoría de Desarrollo), partiendo de lo ya definido en la Ficha de Caracterización (Taller 0), el modelo BPMN (Taller 1) y el modelo de información / diagrama de contexto (Taller 2).

## 🔧 Proceso de desarrollo
Se partió del diagrama de contexto y el ERD ya construidos en el Taller 2, verificando que los actores y sistemas allí identificados (Coordinador de Encuestas, Profesor, Estudiante, y el sistema académico SIGA) fueran consistentes con los tres carriles del BPMN del Taller 1 (Coordinadora, SIGA, Profesor). A partir de esa base se construyó el C1 consolidando el "Notificador" del Taller 2 en un sistema externo más preciso: el servicio de correo institucional (Outlook / Exchange Online), dado que la Visión de Arquitectura (Taller 0) restringe la solución al ecosistema Microsoft (Power Automate, Office 365, Excel/SharePoint).

Para el C2 se descompuso el sistema en alcance en los contenedores que efectivamente lo implementan según esa misma Visión: tres flujos de Power Automate (cruce de horarios, notificación a profesores, distribución y registro de la encuesta), un panel de coordinación en Excel/Power BI y un repositorio de datos en SharePoint/Excel que persiste las entidades ya modeladas en el ERD del Taller 2 (Encuesta, Programación, Estudiante, Salón, Profesor, Notificación). Se usó draw.io para ambos diagramas, siguiendo la notación y los colores de la leyenda de la guía del taller.

## 🧩 Análisis del modelo propuesto

### Cómo se estructura el modelo entregado
El C1 muestra el "Sistema de Programación y Ejecución de Encuestas de Satisfacción" como una sola caja, rodeada de tres actores (Coordinador de Encuestas, Profesor, Estudiante) y dos sistemas externos (SIGA y el Servicio de Correo Institucional). El C2 abre esa caja en cinco contenedores: un panel de coordinación, tres flujos de Power Automate con responsabilidades separadas, y un repositorio de datos en SharePoint/Excel, manteniendo visibles los mismos actores y sistemas externos del C1.

### Cómo representa las necesidades del cliente
El modelo refleja la restricción más importante del cliente: no usar herramientas externas al ecosistema autorizado por la universidad (ver Visión, Taller 0), por lo que cada contenedor del C2 corresponde a una pieza real del stack de Microsoft 365 y no a un sistema genérico o hipotético. El desglose en tres flujos separados (cruce de horarios, notificación, distribución/registro) hace explícito el cuello de botella manual identificado en el BPMN del Taller 1 — la búsqueda de horarios compatibles y la espera de aceptación del profesor — y muestra dónde se automatiza cada parte del proceso.

### Qué supuestos se tomaron
- El "Notificador" del diagrama de contexto del Taller 2 se modela como el servicio de correo institucional (Outlook/Exchange Online) del ecosistema Microsoft 365, no como un servicio de notificaciones de terceros.
- Los tres flujos de Power Automate se separan en el C2 porque cada uno tiene un disparador (trigger) y una responsabilidad distinta, aunque en la implementación real pudieran convivir en un mismo entorno de Power Automate.
- El repositorio de datos (SharePoint Lists/Excel) se modela como infraestructura de soporte compartida por los tres flujos y el panel de coordinación, siguiendo el ERD del Taller 2.
- Se asume que la comunicación con SIGA se realiza mediante una consulta institucional (API o exportación de datos), dado que el cliente no especificó el mecanismo técnico exacto de integración.

## 📈 Diagrama final entregado
> Ver `c1-contexto-final.drawio` y `c2-contenedores-final.drawio` adjuntos en esta carpeta.

## 📋 Tabla de actores, entidades o componentes

| Nombre del elemento | Tipo | Descripción | Responsable |
|---|---|---|---|
| Coordinador de Encuestas | Actor | Define fecha, capacidad y criterios de muestra; recibe reportes | Cliente (Vicerrectoría de Desarrollo) |
| Profesor | Actor | Acepta o rechaza ceder el espacio de su clase para la encuesta | Docentes de la universidad |
| Estudiante | Actor | Recibe el enlace y responde la encuesta | Estudiantes PAT / muestra seleccionada |
| SIGA | Sistema externo | Sistema de Información Académica: horarios de clases y datos de estudiantes | Universidad de La Sabana |
| Servicio de Correo Institucional | Sistema externo | Outlook / Exchange Online, canal de envío de notificaciones y enlaces | Universidad de La Sabana (M365) |
| Panel de Coordinación | Contenedor | Interfaz en Excel/Power BI donde el coordinador define criterios y ve reportes | Coordinador de Encuestas |
| Flujo de Cruce de Horarios | Contenedor | Power Automate: consulta SIGA y cruza horarios de clases con disponibilidad PAT | Equipo de automatización |
| Flujo de Notificación a Profesores | Contenedor | Power Automate: envía la notificación y procesa la aceptación/rechazo del profesor | Equipo de automatización |
| Flujo de Distribución y Registro de Encuesta | Contenedor | Power Automate: envía el enlace al estudiante y registra sus respuestas | Equipo de automatización |
| Repositorio de Programación | Infraestructura | SharePoint Lists / Excel: almacena Encuesta, Programación, Estudiante, Salón, Profesor, Notificación | Equipo de automatización |

## 🔍 Investigación complementaria
### Tema investigado:
Arquitecturas de automatización sobre Microsoft Power Platform (Power Automate + SharePoint) en instituciones de educación superior, y su representación en el modelo C4.

### Resumen:
Se investigó cómo documentar arquitecturas basadas en Power Automate usando el modelo C4, dado que estos flujos no son aplicaciones tradicionales desplegadas en servidores propios sino servicios SaaS orquestados por triggers y conectores. La práctica recomendada es tratar cada Cloud Flow con un disparador y una responsabilidad claramente distinta como un contenedor independiente —tal como se hizo aquí con los tres flujos—, en lugar de modelar todo Power Automate como una única caja, porque eso ocultaría la distribución real de responsabilidades del proceso (el mismo error que la guía del taller señala para el caso RedExpress).

También se revisó cómo las universidades documentan integraciones entre sistemas de información académica (tipo SIGA) y herramientas de automatización de bajo código, encontrando que el patrón más común es tratar el sistema académico como un sistema externo de solo consulta (sin escritura), justamente el rol que cumple SIGA en este modelo: es consultado para obtener horarios y estudiantes, pero el sistema en alcance no modifica su información.

Esta investigación se relaciona directamente con el taller porque justificó dos decisiones de modelado: separar los tres flujos de Power Automate en contenedores distintos en el C2, y mantener a SIGA como un sistema externo de solo lectura en ambas vistas.

## 📚 Referencias
- [1] The C4 Model for Software Architecture. *C4 Model - Container diagram*. https://c4model.com/diagrams/container
- [2] Microsoft. *Power Automate documentation - Cloud flows overview*. https://learn.microsoft.com/power-automate/
- [3] Microsoft. *Microsoft Graph API - Send mail*. https://learn.microsoft.com/graph/api/user-sendmail

---

_Este documento hace parte de la entrega del taller 3 del curso AREM (Arquitectura Empresarial) - Universidad de La Sabana._