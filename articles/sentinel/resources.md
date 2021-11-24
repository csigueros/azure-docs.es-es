---
title: Recursos útiles al trabajar con Microsoft Sentinel
description: En este documento encontrará una lista de recursos de gran utilidad al trabajar con Microsoft Sentinel.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7fbd367ce4d78c6cf71e95e091eb0580c2274f2e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712231"
---
# <a name="useful-resources-for-working-with-microsoft-sentinel"></a>Recursos útiles al trabajar con Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En este artículo se enumeran recursos que pueden serle de utilidad para obtener más información sobre cómo trabajar con Microsoft Sentinel.

## <a name="learn-more-about-creating-queries"></a>Más información sobre la creación de consultas

Microsoft Sentinel usa el lenguaje de consulta Kusto (KQL) de Azure Monitor Log Analytics para generar consultas. Para más información, consulte:

- [Conceptos de KQL](/azure/data-explorer/kusto/concepts/)
- [Consultas de KQL](/azure/data-explorer/kusto/query/)
- [Guía de referencia rápida de KQL](/azure/data-explorer/kql-quick-reference)
- [Introducción a las consultas de KQL](../azure-monitor/logs/get-started-queries.md)

## <a name="learn-more-about-creating-automation"></a>Más información sobre la creación de automatización

Cree automatización en Microsoft Sentinel mediante Azure Logic Apps, con una creciente galería de cuadernos de estrategias integrados. 

Para más información, consulte [Conectores de Azure Logic Apps](/connectors/).

## <a name="compare-playbooks-workbooks-and-notebooks"></a>Comparación de cuadernos de estrategias, libros y cuadernos

En la siguiente tabla se describen las diferencias entre los cuadernos de estrategias, los libros y los cuadernos en Microsoft Sentinel:

| Category |Playbooks  |Workbooks  |Cuaderno  |
|---------|---------|---------|---------|
|**Personas**     |   <ul><li>Ingenieros de SOC</li><li>Analistas de todos los niveles</li></ul>      | <ul><li> Ingenieros de SOC</li><li>Analistas de todos los niveles</li></ul>       | <ul><li>Buscadores de amenazas y analistas de los niveles 2 y 3</li><li>Investigadores de incidentes</li><li>Científicos de datos</li><li>Investigadores de seguridad</li></ul>       |
|**Usos**     | Automatización de tareas sencillas y repetibles:<ul><li>Ingesta de datos externos </li><li>Enriquecimiento de datos con TI, búsquedas de GeoIP, etc. </li><li> Investigación </li><li>Corrección </li></ul>       | <ul><li>Visualización</li></ul>        |   <ul><li>Consulta de datos y datos externos de Microsoft Sentinel </li><li>Enriquecimiento de datos con TI, búsquedas de GeoIP y búsquedas de WhoIs, etc. </li><li> Investigación </li><li> Visualización </li><li> Búsqueda </li><li>Aprendizaje automático y análisis de macrodatos </li></ul>      |
|**Ventajas**     |<ul><li> Mejor para tareas aisladas y repetibles </li><li>No se necesitan conocimientos de programación  </li></ul>      |<ul><li>Opción idónea para obtener una vista de alto nivel de los datos de Microsoft Sentinel </li><li>No se necesitan conocimientos de programación</li></ul>       | <ul><li>Mejor para cadenas complejas de tareas repetibles </li><li>Control ad hoc, más procedimental</li><li>Más fácil de dinamizar con la funcionalidad interactiva </li><li>Bibliotecas de Python completas para la manipulación y la visualización de datos </li><li>Aprendizaje automático y análisis personalizado </li><li>Facilidad para documentar y compartir evidencias de análisis </li></ul>       |
|**Desafíos**     | <ul><li>No adecuado para cadenas de tareas ad hoc y complejas </li><li>No adecuado para documentar y compartir evidencias</li></ul>        |   <ul><li>No se puede integrar con datos externos </li></ul>     |    <ul><li> Curva de aprendizaje alta y requiere conocimientos de programación </li></ul>   |
|  **Más información**   | [Automatización de la respuesta a amenazas con cuadernos de estrategias en Microsoft Sentinel](automate-responses-with-playbooks.md)        | [Visualización de los datos recopilados](get-visibility.md)        | [Uso de cuadernos de Jupyter para buscar amenazas de seguridad](notebooks.md)        |
|     |         |         |         |

## <a name="comment-on-our-blogs-and-forums"></a>Realice comentarios en nuestros blogs y foros

Nos encanta escuchar a nuestros usuarios.

En el espacio de TechCommunity para Microsoft Sentinel, haga lo siguiente:

- [Consulte y realice comentarios en las entradas de blog recientes](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog)
- [Publique sus propias preguntas sobre Microsoft Sentinel.](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)

También puede enviar sugerencias para obtener mejoras a través de nuestro programa [Voz del usuario](https://feedback.azure.com/d365community/forum/37638d17-0625-ec11-b6e6-000d3a4f07b8).

## <a name="join-the-microsoft-sentinel-github-community"></a>Únase a la comunidad de GitHub de Microsoft Sentinel

El [repositorio de GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel) es un recurso muy eficaz para la detección de amenazas y la automatización. 

Nuestros analistas de seguridad de Microsoft crean y agregan constantemente nuevos libros, cuadernos de estrategias, consultas de búsqueda, etc. y los publican en la comunidad para que los pueda usar en su entorno. 

Puede descargar contenido de ejemplo del repositorio de GitHub privado de la comunidad con el fin de crear libros, consultas de búsqueda, cuadernos y cuadernos de estrategias personalizados para Microsoft Sentinel.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtener la certificación](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Leer historias de casos de uso de clientes](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)
