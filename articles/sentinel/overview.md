---
title: ¿Qué es Azure Sentinel? | Microsoft Docs
description: Obtenga información sobre Microsoft Azure Sentinel, una solución de administración de eventos de información de seguridad (SIEM) y respuesta automatizada de orquestación de seguridad (SOAR) que es escalable y nativa de la nube.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2021
ms.author: yelevin
ms.openlocfilehash: 0c782c7cb301a88e54213f18d702ed1f81baabf2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563103"
---
# <a name="what-is-azure-sentinel"></a>¿Qué es Azure Sentinel?

Microsoft Azure Sentinel es una solución de **administración de eventos de información de seguridad (SIEM)** y **respuesta automatizada de orquestación de seguridad (SOAR)** que es escalable y nativa de la nube. Azure Sentinel ofrece análisis de seguridad inteligente e inteligencia frente a amenazas en toda la empresa, de forma que proporciona una única solución para la detección de alertas, la visibilidad de amenazas, la búsqueda proactiva y la respuesta a amenazas.

Azure Sentinel permite obtener una vista general de toda la empresa, lo que suaviza la tensión de ataques cada vez más sofisticados, volúmenes de alertas cada vez mayores y plazos de resolución largos.

- **Recopile datos a escala de nube** de todos los usuarios, dispositivos, aplicaciones y de toda la infraestructura, tanto en el entorno local como en diversas nubes.

- **Detecte amenazas que antes no se detectaban** y [minimice los falsos positivos](false-positives.md) mediante el análisis y la inteligencia sobre amenazas sin precedentes de Microsoft.

- **Investigue amenazas con inteligencia artificial** y busque actividades sospechosas a escala, aprovechando el trabajo de ciberseguridad que ha llevado a cabo Microsoft durante décadas.

- **Responda a los incidentes con rapidez** con la orquestación y la automatización de tareas comunes integradas.

![Funcionalidades principales de Azure Sentinel](./media/overview/core-capabilities.png)

Creado sobre la gama completa de servicios de Azure existentes, Azure Sentinel incorpora de forma nativa bases contrastadas, como Log Analytics y Logic Apps. Azure Sentinel enriquece la investigación y la detección con AI al proporcionar el flujo de inteligencia de amenazas de Microsoft y permitirle aportar su propia inteligencia de amenazas.

## <a name="connect-to-all-your-data"></a>Conexión a todos sus datos

Para incorporar Azure Sentinel, primero debe [conectarse a sus orígenes de seguridad](connect-data-sources.md). 

Azure Sentinel incluye varios conectores para soluciones de Microsoft, que están disponibles inmediatamente y proporcionan integración en tiempo real, entre las que se incluyen las soluciones de Microsoft 365 Defender (anteriormente, Protección contra amenazas de Microsoft) y orígenes de Microsoft 365, incluido Office 365, Azure AD, Microsoft Defender for Identity (anteriormente, Azure ATP) y Microsoft Cloud App Security, entre otros. Además, hay conectores integrados al amplio ecosistema de seguridad para soluciones que no son de Microsoft. También puede usar el formato de evento común, Syslog o las API de REST para conectar los orígenes de datos con Azure Sentinel. 

Para más información, consulte el artículo sobre la [búsqueda de un conector de datos](data-connectors-reference.md).

![Recopiladores de datos](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Workbooks

Después de que haya[conectado los orígenes de datos](quickstart-onboard.md) a Azure Sentinel, puede supervisar los datos mediante la integración de Azure Sentinel con los libros de Azure Monitor, lo que proporciona versatilidad al crear libros personalizados.

Aunque los libros se muestran de manera diferente en Azure Sentinel, puede que le resulte útil ver cómo [crear informes interactivos con los libros de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md). Azure Sentinel permite crear libros personalizados en los datos y también incluye plantillas de libro integradas que permiten obtener información rápidamente en los datos en cuanto se conecta con un origen de datos.

![Paneles](./media/tutorial-monitor-data/access-workbooks.png)

- Los libros están diseñados para que los ingenieros y analistas de SOC de todos los niveles visualicen los datos.

- Aunque los libros son más adecuados para vistas generales de los datos de Azure Sentinel y no requieren ningún conocimiento de codificación, no se pueden integrar libros con datos externos.

## <a name="analytics"></a>Análisis

Para reducir el ruido y minimizar el número de alertas que tiene que revisar e investigar, Azure Sentinel usa [análisis para correlacionar las alertas con los incidentes](detect-threats-built-in.md). Los **incidentes** son grupos de alertas relacionadas que, juntas, crean una posible amenaza procesable que se puede investigar y resolver. Use las reglas de correlación integrada tal cual, o úselas como punto de partida para crear las suyas propias. Azure Sentinel también proporciona reglas de aprendizaje automático para asignar el comportamiento de red y buscar luego anomalías en los recursos. Estos análisis conectan los puntos, al combinar alertas de baja fidelidad sobre distintas entidades en posibles incidentes de seguridad de alta fidelidad.

![Incidentes](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Automatización y orquestación de la seguridad

Automatice las tareas comunes y [simplifique la orquestación de la seguridad con cuadernos de estrategias](tutorial-respond-threats-playbook.md) que se integran con los servicios de Azure y las herramientas existentes.

Construida sobre la base de Azure Logic Apps, la solución de automatización y orquestación de Azure Sentinel proporciona una arquitectura muy extensible que permite una automatización escalable a medida que emergen nuevas tecnologías y amenazas. Para crear cuadernos de estrategias con Azure Logic Apps, puede elegir de una galería creciente de cuadernos de estrategias integrados. Estos incluyen [más de 200 conectores](../connectors/apis-list.md) para servicios, como Azure Functions. Los conectores permiten aplicar cualquier lógica personalizada en el código, ServiceNow, Jira, Zendesk, solicitudes HTTP, Microsoft Teams, Slack, Windows Defender ATP y Cloud App Security.

Por ejemplo, si usa el sistema de vales de ServiceNow, puede usar las herramientas proporcionadas para usar Azure Logic Apps para automatizar los flujos de trabajo y abrir un vale en ServiceNow cada vez que se detecta un evento determinado.

![Playbooks](./media/tutorial-respond-threats-playbook/logic-app.png)

- Los cuadernos de procedimientos están diseñados para ingenieros y analistas de SOC de todos los niveles, con el fin de automatizar y simplificar las tareas, incluida la ingesta de datos, el enriquecimiento, la investigación y la corrección.

- Los cuadernos de estrategias son más adecuados para tareas únicas y repetibles, y no requieren ningún conocimiento de codificación. Los cuadernos de reproducción no son adecuados para cadenas de tareas ad hoc o complejas, ni para documentar y compartir evidencias.


## <a name="investigation"></a>Investigación

Las herramientas de [investigación profunda](investigate-cases.md) de Azure Sentinel están actualmente en versión preliminar y le ayudan a conocer el ámbito y a encontrar la causa principal de una posible amenaza de seguridad. Puede elegir una entidad en el gráfico interactivo para hacer preguntas interesantes sobre ella y explorar en profundidad esa entidad y sus conexiones para llegar a la causa principal de la amenaza. 

![Investigación](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Búsqueda

Use las [eficaces herramientas de búsqueda y consulta](hunting.md) de Azure Sentinel, basadas en el marco MITRE, que le permiten buscar de forma proactiva amenazas de seguridad en todos los orígenes de datos de la organización, antes de que se desencadene una alerta. Una vez que ha descubierto qué consulta de búsqueda proporciona las conclusiones más valiosas sobre posibles ataques, también puede crear reglas de detección personalizadas basadas en la consulta y exponer esas conclusiones como alertas para los respondedores a los incidentes de seguridad. Durante la búsqueda puede crear marcadores de los eventos interesantes, para así poder volver a ellos más tarde, compartirlos con otros usuarios y agruparlos con otros eventos correlacionados para crear un incidente de investigación convincente.

![Información general sobre la característica de búsqueda](./media/overview/hunting.png)

## <a name="notebooks"></a>Cuaderno

Azure Sentinel admite cuadernos de Jupyter en áreas de trabajo de Azure Machine Learning, incluidas las bibliotecas completa para el aprendizaje automático, la visualización y el análisis de datos.

[Use cuadernos en Azure Sentinel](notebooks.md) para ampliar el ámbito de lo que puede hacer con los datos de Azure Sentinel. Por ejemplo, realizar análisis que no están integrados en Azure Sentinel, como algunas características de aprendizaje automático de Python, crear visualizaciones de datos que no están integradas en Azure Sentinel, como escalas de tiempo personalizadas y árboles de proceso, o integrar orígenes de datos fuera de Azure Sentinel, como un conjunto de datos local.

:::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="Captura de pantalla de un cuaderno de Sentinel en un área de trabajo de AML.":::

- Los cuadernos de Azure Sentinel están destinados a buscadores de amenazas o analistas de nivel 2 y 3, investigadores de incidentes, científicos de datos e investigadores de seguridad.

- Los cuadernos proporcionan consultas tanto a datos de Azure Sentinel como externos, características para el enriquecimiento de datos, la investigación, la visualización, la búsqueda, el aprendizaje automático y el análisis de macrodatos.

- Los cuadernos son adecuados para cadenas más complejas de tareas repetibles, controles de procedimientos ad hoc, aprendizaje automático y análisis personalizado, admiten bibliotecas de Python enriquecidas para manipular y visualizar datos, y son útiles para documentar y compartir evidencias de análisis.

- Los cuadernos requieren una curva de aprendizaje más alta y conocimientos de codificación, y tienen compatibilidad limitada con la automatización.


## <a name="community"></a>Comunidad

La comunidad Azure Sentinel es un recurso muy eficaz para la detección y la automatización de amenazas. Nuestros analistas de seguridad de Microsoft crean y agregan constantemente nuevos libros, cuadernos de estrategias, consultas de búsqueda, etc. y los publican en la comunidad para que los pueda usar en su entorno. Puede descargar contenido de ejemplo del [repositorio](https://aka.ms/asicommunity) de GitHub privado de la comunidad para crear libros personalizados, consultas de búsqueda, cuadernos y cuadernos de estrategias Azure Sentinel.

![Explorar la comunidad de usuarios](./media/overview/community.png)

## <a name="next-steps"></a>Pasos siguientes

- Para empezar a trabajar con Azure Sentinel, necesita una suscripción a Microsoft Azure. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/free/).
- Aprenda a [incorporar los datos en Azure Sentinel](quickstart-onboard.md), [obtenga visibilidad sobre ellos y aprenda a defenderse de posibles amenazas](get-visibility.md).
