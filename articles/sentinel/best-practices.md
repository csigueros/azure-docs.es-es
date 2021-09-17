---
title: Procedimientos recomendados para Azure Sentinel
description: Obtenga información sobre los procedimientos recomendados que se deben emplear al administrar el área de trabajo de Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 4a47a13405402b381c1a1b3682f5d42038218959
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689154"
---
# <a name="best-practices-for-azure-sentinel"></a>Procedimientos recomendados para Azure Sentinel

Esta colección de procedimientos recomendados proporciona instrucciones para implementar, administrar y usar Azure Sentinel, e incluye vínculos a otros artículos para obtener más información.

> [!IMPORTANT]
> Antes de implementar Azure Sentinel, revise y complete las [actividades previas a la implementación y los requisitos previos](prerequisites.md).
>


## <a name="regular-soc-activities-to-perform"></a>Actividades normales de SOC

Programe con regularidad las siguientes actividades de Azure Sentinel para garantizar los procedimientos recomendados de seguridad continuos:

### <a name="daily-tasks"></a>Tareas diarias

- **Evaluar errores y buscar incidentes**.  Revise la página **Incidentes** de Azure Sentinel para comprobar si hay nuevos incidentes generados por las reglas analíticas configuradas actualmente y empezar a buscar incidentes nuevos. Para más información, consulte el [Tutorial: Investigación de incidentes con Azure Sentinel](investigate-cases.md).

- **Explorar las consultas de búsqueda y los marcadores**. Explore los resultados de todas las consultas integradas y actualice los marcadores y las consultas de búsqueda existentes. Genere manualmente nuevos incidentes o actualice los incidentes antiguos si procede.  Para más información, consulte:

    - [Creación automática de incidentes a partir de alertas de seguridad de Microsoft](create-incidents-from-alerts.md)
    - [Búsqueda de amenazas con Azure Sentinel](hunting.md)
    - [Seguimiento de los datos durante una búsqueda con Azure Sentinel](bookmarks.md)

- **Reglas analíticas**.  Revise y habilite las nuevas reglas analíticas según corresponda, incluidas las reglas recién publicadas o recientemente disponibles de los conectores de datos conectados recientemente.

- **Conectores de datos**. Revise el estado, la fecha y la hora del último registro recibido de cada conector de datos para asegurarse de que los datos fluyen. Compruebe si hay nuevos conectores y revise la ingesta de datos para asegurarse de que no se han superado los límites establecidos. Para obtener más información, consulte [Procedimientos recomendados para la recopilación de datos](best-practices-data.md) y [Conexión con orígenes de datos](connect-data-sources.md).

- **Agente de Log Analytics**. Compruebe que los servidores y estaciones de trabajo están conectados activamente al área de trabajo y corrija las conexiones fallidas.   Para obtener más información, consulte [Introducción al agente de Log Analytics](../azure-monitor/agents/log-analytics-agent.md).

- **Errores del cuaderno de estrategias**. Compruebe los estados de ejecución del cuaderno de estrategias y solucione los errores.   Para obtener más información, consulte el [Tutorial: Uso de cuadernos de estrategias con reglas de automatización en Azure Sentinel](tutorial-respond-threats-playbook.md).


### <a name="weekly-tasks"></a>Tareas semanales

- **Actualizaciones del cuaderno de estrategias**. Compruebe si los cuadernos de estrategias tienen actualizaciones que deban instalarse. Para obtener más información, consulte [Libros de Azure Sentinel que se usan comúnmente](top-workbooks.md).

- **Revisión del repositorio de GitHub de Azure Sentinel**. Revise el repositorio de [GitHub de Azure Sentinel](https://github.com/Azure/Azure-Sentinel) para explorar si hay recursos de valor nuevos o actualizados para su entorno, como reglas analíticas, libros, consultas de búsqueda o cuadernos de estrategias.

- **Auditoría de Azure Sentinel**. Revise la actividad de Azure Sentinel para ver quién ha actualizado o eliminado recursos, como reglas analíticas y marcadores, entre otros. Para más información, consulte [Auditoría de consultas y actividades de Azure Sentinel](audit-sentinel-data.md).

### <a name="monthly-tasks"></a>Tareas mensuales

- **Revisar el acceso de usuario**. Revise los permisos de los usuarios y compruebe si hay usuarios inactivos. Para obtener más información, vea [Permisos de Azure Sentinel](roles.md).

- **Revisar el área de trabajo de Log Analytics**. Revise que la directiva de retención de datos del área de trabajo de Log Analytics se sigue ajustando a la directiva de su organización.  Para más información, consulte la [Directiva de retención de datos](/workplace-analytics/privacy/license-expiration) y la sección [Integración con Azure Data Explorer para conservar registros a largo plazo](store-logs-in-azure-data-explorer.md).


## <a name="integrate-with-microsoft-security-services"></a>Integración con servicios de seguridad de Microsoft

Azure Sentinel está potenciado por los componentes que envían datos al área de trabajo y se fortalece mediante integraciones con otras servicios Microsoft. Los registros ingeridos en productos como Microsoft Cloud App Security, Microsoft Defender para punto de conexión y Microsoft Defender for Identity permiten a estos servicios crear detecciones y, a su vez, proporcionar esas detecciones a Azure Sentinel. Los registros también se pueden ingerir directamente en Azure Sentinel para proporcionar una imagen más completa de eventos e incidentes.

Por ejemplo, en la imagen siguiente se muestra cómo Azure Sentinel ingiere datos de otros servicios de Microsoft y varias nubes y plataformas asociadas para proporcionar cobertura para su entorno:

:::image type="content" source="media/best-practices/azure-sentinel-and-other-services.png" alt-text="Integración de Azure Sentinel con otros servicios de Microsoft y asociados":::

Además de ingerir alertas y registros de otros orígenes, Azure Sentinel también:

- **Usa la información que ingiere con [aprendizaje automático](bring-your-own-ml.md)** que permite una mejor correlación de eventos, agregación de alertas, detección de anomalías, etc.
- **Compila y presenta objetos visuales interactivos a través de [libros](get-visibility.md)** que muestran tendencias, información relacionada y datos clave usados para tareas de administración e investigaciones.
- **Ejecuta [cuadernos de estrategias](tutorial-respond-threats-playbook.md) para actuar sobre las alertas** recopilando información, realizando acciones sobre los elementos y enviando notificaciones a distintas plataformas.
- **Se integra con plataformas de asociados**, como ServiceNow y Jira, para proporcionar servicios esenciales a los equipos de SOC.
- **Ingiere y recupera datos de fuentes de enriquecimiento** de las [plataformas de inteligencia sobre amenazas](threat-intelligence-integration.md) para aportar datos valiosos de investigación.

##  <a name="manage-and-respond-to-incidents"></a>Administración de incidentes y respuesta a los mismos

En la imagen siguiente se muestran los pasos recomendados en un proceso de administración de incidentes y respuesta a los mismos.

:::image type="content" source="media/best-practices/incident-handling.png" alt-text="Proceso de administración de incidentes: Evaluación de errores. Preparación. Corrección. Erradicación. Actividades posteriores al incidente.":::

En las secciones siguientes se proporcionan descripciones detalladas sobre cómo usar las funciones de Azure Sentinel para administrar y dar respuesta a incidentes a lo largo del proceso. Para más información, consulte el [Tutorial: Investigación de incidentes con Azure Sentinel](investigate-cases.md).

### <a name="use-the-incidents-page-and-the-investigation-graph"></a>Uso de la página Incidentes y del gráfico Investigación

Inicie cualquier proceso de evaluación de nuevos incidentes en la página **Incidentes** de Azure Sentinel y el **gráfico de investigación**. 

Detecte entidades clave, como cuentas, direcciones URL, direcciones IP, nombres de host, actividades, escala de tiempo, etc. Use estos datos para saber si hay un [falso positivo](false-positives.md), en cuyo caso puede cerrar el incidente directamente.

Los incidentes generados se muestran en la página **Incidentes**, que actúa como ubicación central para la evaluación de prioridades e investigación temprana. En la página **Incidentes** se enumera el título, la gravedad y las alertas relacionadas, los registros y cualquier entidad de interés. Los incidentes también proporcionan un salto rápido a los registros recopilados y a cualquier herramienta relacionada con el incidente.

La página **Incidentes** funciona junto con el **Gráfico de investigación**, una herramienta interactiva que permite a los usuarios explorar y profundizar en una alerta para mostrar el alcance completo de un ataque. A continuación, los usuarios pueden construir una escala de tiempo de eventos y detectar la extensión de una cadena de amenazas.

Si descubre que el incidente es un verdadero positivo, actúe directamente desde la página **Incidentes** para investigar los registros y las entidades y explorar la cadena de amenazas. Después de identificar la amenaza y crear un plan de acción, use otras herramientas de Azure Sentinel y [otros servicios de seguridad de Microsoft](best-practices.md#integrate-with-microsoft-security-services) para continuar investigando.


### <a name="handle-incidents-with-workbooks"></a>Control de incidentes con libros

Además de [visualizar y mostrar información y tendencias](get-visibility.md), los libros de Azure Sentinel son valiosas herramientas de investigación.

Por ejemplo, use el libro [Investigation Insights](top-workbooks.md#investigation-insights) (Ideas de investigación) para investigar incidentes específicos junto con las entidades y alertas asociadas. Este libro le permite profundizar más en las entidades mediante la presentación de registros, acciones y alertas relacionados.

### <a name="handle-incidents-with-threat-hunting"></a>Control de incidentes con la búsqueda de amenazas

Al investigar y buscar las causas principales, ejecute consultas de búsqueda de amenazas integradas y compruebe los resultados de los indicadores de riesgo.

Durante una investigación, o después de haber tomado medidas para corregir y eliminar la amenaza, use [Live Stream](livestream.md) para supervisar, en tiempo real, si hay eventos malintencionados persistentes o si los eventos malintencionados continúan.

### <a name="handle-incidents-with-entity-behavior"></a>Control de incidentes con comportamiento de entidad

El comportamiento de la entidad en Azure Sentinel permite a los usuarios revisar e investigar acciones y alertas para entidades específicas, como investigar en cuentas y nombres de host. Para más información, consulte:

- [Habilitación del análisis de comportamiento de usuarios y entidades (UEBA) en Azure Sentinel](enable-entity-behavior-analytics.md)
- [Investigación de incidentes con datos de UEBA](investigate-with-ueba.md)
- [Referencia de características enriquecidas de UEBA de Azure Sentinel](ueba-enrichments.md)

### <a name="handle-incidents-with-watchlists-and-threat-intelligence"></a>Control de incidentes con listas de reproducción e inteligencia sobre amenazas

Para maximizar las detecciones basadas en inteligencia sobre amenazas, asegúrese de usar [conectores de datos de inteligencia sobre amenazas](connect-threat-intelligence-tip.md) para ingerir indicadores de riesgo:

- Conecte los orígenes de datos requeridos por la [Fusión](fusion.md) y las [alertas de TI Map](./understand-threat-intelligence.md)
- Ingesta de indicadores desde las [plataformas TAXII y TIP](./connect-threat-intelligence-tip.md)

Use indicadores de riesgo en las reglas analíticas para buscar amenazas, investigar registros o generar más incidentes.

Use una lista de reproducción que combine datos de los datos ingeridos y los orígenes externos, por ejemplo, datos de enriquecimiento. Por ejemplo, cree listas de intervalos de direcciones IP usados por su organización o de empleados despedidos recientemente. Use listas de reproducción con cuadernos de estrategias para recopilar datos de enriquecimiento, como agregar direcciones IP malintencionadas a listas de reproducción para usarlas durante la detección, la búsqueda de amenazas y las investigaciones.

Durante un incidente, use las listas de reproducción para contener los datos de investigación y, a continuación, elimínelos cuando se realice la investigación para asegurarse de que los datos confidenciales permanezcan ocultos.


## <a name="additional-best-practice-references"></a>Referencias adicionales de procedimientos recomendados

La documentación de Azure Sentinel tiene más instrucciones de procedimientos recomendados distribuidas en nuestros artículos. Por ejemplo, vea los siguientes artículos para obtener más información:

- **Usuarios administradores**:

    - [Pre-deployment activities and prerequisites for deploying Azure Sentinel](prerequisites.md) (Actividades previas a la implementación y requisitos previos para implementar Azure Sentinel)
    - [Procedimientos recomendados de arquitectura de áreas de trabajo de Azure Sentinel](best-practices-workspace-architecture.md)
    - [Diseño de una arquitectura de áreas de trabajo de Azure Sentinel](design-your-workspace-architecture.md)
    - [Diseños de ejemplo de áreas de trabajo de Azure Sentinel](sample-workspace-designs.md)
    - [Procedimientos recomendados para la recopilación de datos](best-practices-data.md)
    - [Costos y facturación de Azure Sentinel](azure-sentinel-billing.md)
    - [Permisos de Azure Sentinel](roles.md)
    - [Protección de la propiedad intelectual del MSSP en Azure Sentinel](mssp-protect-intellectual-property.md)
    - [Integración de inteligencia sobre amenazas en Azure Sentinel](threat-intelligence-integration.md)
    - [Auditoría de consultas y actividades de Azure Sentinel](audit-sentinel-data.md)

- **Analistas**:

    - [Cuadernos de estrategia recomendados](automate-responses-with-playbooks.md#recommended-playbooks)
    - [Control de falsos positivos en Azure Sentinel](false-positives.md)
    - [Búsqueda de amenazas con Azure Sentinel](hunting.md)
    - [Libros de Azure Sentinel que se usan comúnmente](top-workbooks.md)
    - [Detección de amenazas integrada](detect-threats-built-in.md)
    - [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md)
    - [Uso de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md)

Para obtener más información, vea también nuestro vídeo: [Architecting SecOps for Success: Best Practices for Deploying Azure Sentinel](https://youtu.be/DyL9MEMhqmI) (Arquitectura de SecOps para el éxito: procedimientos recomendados de implementación de Azure Sentinel)

## <a name="next-steps"></a>Pasos siguientes

Para empezar a utilizar Azure Sentinel, consulte:

- [Incorporación de Azure Sentinel](quickstart-onboard.md)
- [Obtención de visibilidad sobre las alertas](get-visibility.md)