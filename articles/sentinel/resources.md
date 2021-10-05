---
title: Recursos útiles al trabajar con Azure Sentinel | Microsoft Docs
description: En este documento encontrará una lista de recursos de gran utilidad al trabajar con Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: yelevin
ms.openlocfilehash: 247b025524a029fcb68fbe0682b365e9b7f240b5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128565180"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Recursos útiles al trabajar con Azure Sentinel

En este artículo se enumeran los recursos que sirven para obtener más información sobre cómo trabajar con Azure Sentinel.

## <a name="learn-more-about-creating-queries"></a>Más información sobre la creación de consultas

Azure Sentinel usa el lenguaje de consulta Kusto (KQL) de Azure Monitor Log Analytics para generar consultas. Para más información, consulte:

- [Conceptos de KQL](/azure/data-explorer/kusto/concepts/)
- [Consultas de KQL](/azure/data-explorer/kusto/query/)
- [Guía de referencia rápida de KQL](/azure/data-explorer/kql-quick-reference)
- [Introducción a las consultas de KQL](../azure-monitor/logs/get-started-queries.md)

## <a name="learn-more-about-creating-automation"></a>Más información sobre la creación de automatización

Cree automatización en Azure Sentinel mediante Azure Logic Apps, con una galería en aumento de cuadernos de estrategias integradas. 

Para más información, consulte [Conectores de Azure Logic Apps](/connectors/).

## <a name="compare-playbooks-workbooks-and-notebooks"></a>Comparación de cuadernos de estrategias, libros y cuadernos

En la tabla siguiente se describen las diferencias entre cuadernos de estrategias, libros y cuadernos en Azure Sentinel:

|  |Playbooks  |Workbooks  |Cuaderno  |
|---------|---------|---------|---------|
|**Personas**     |   <ul><li>Ingenieros de SOC</li><li>Analistas de todos los niveles</li></ul>      | <ul><li> Ingenieros de SOC</li><li>Analistas de todos los niveles</li></ul>       | <ul><li>Buscadores de amenazas y analistas de los niveles 2 y 3</li><li>Investigadores de incidentes</li><li>Científicos de datos</li><li>Investigadores de seguridad</li></ul>       |
|**Usos**     | Automatización de tareas sencillas y repetibles:<ul><li>Ingesta de datos externos </li><li>Enriquecimiento de datos con TI, búsquedas de GeoIP, etc. </li><li> Investigación </li><li>Corrección </li></ul>       | <ul><li>Visualización</li></ul>        |   <ul><li>Consulta de datos de Azure Sentinel y datos externos </li><li>Enriquecimiento de datos con TI, búsquedas de GeoIP y búsquedas de WhoIs, etc. </li><li> Investigación </li><li> Visualización </li><li> Búsqueda </li><li>Aprendizaje automático y análisis de macrodatos </li></ul>      |
|**Ventajas**     |<ul><li> Mejor para tareas aisladas y repetibles </li><li>No se necesitan conocimientos de programación  </li></ul>      |<ul><li>Mejor para obtener una vista de alto nivel de los datos de Azure Sentinel </li><li>No se necesitan conocimientos de programación</li></ul>       | <ul><li>Mejor para cadenas complejas de tareas repetibles </li><li>Control ad hoc, más procedimental</li><li>Más fácil de dinamizar con la funcionalidad interactiva </li><li>Bibliotecas de Python completas para la manipulación y la visualización de datos </li><li>Aprendizaje automático y análisis personalizado </li><li>Facilidad para documentar y compartir evidencias de análisis </li></ul>       |
|**Desafíos**     | <ul><li>No adecuado para cadenas de tareas ad hoc y complejas </li><li>No adecuado para documentar y compartir evidencias</li></ul>        |   <ul><li>No se puede integrar con datos externos </li></ul>     |    <ul><li> Curva de aprendizaje alta y requiere conocimientos de programación </li></ul>   |
|  **Más información**   | [Automatización de la respuesta a amenazas con cuadernos de estrategias en Azure Sentinel](automate-responses-with-playbooks.md)        | [Visualización de los datos recopilados](get-visibility.md)        | [Uso de cuadernos de Jupyter para buscar amenazas de seguridad](notebooks.md)        |
|     |         |         |         |

## <a name="comment-on-our-blogs-and-forums"></a>Realice comentarios en nuestros blogs y foros

Nos encanta escuchar a nuestros usuarios.

En el espacio de TechCommunity para Azure Sentinel:

- [Consulte y realice comentarios en las entradas de blog recientes](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog)
- [Publique sus propias preguntas sobre Azure Sentinel](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)

También puede enviar sugerencias para obtener mejoras a través de nuestro programa [Voz del usuario](https://feedback.azure.com/forums/920458-azure-sentinel).

## <a name="join-the-azure-sentinel-github-community"></a>Únase a la comunidad de GitHub de Azure Sentinel

El [repositorio de GitHub de Azure Sentinel](https://github.com/Azure/Azure-Sentinel) es un recurso muy eficaz para la detección y la automatización de amenazas. 

Nuestros analistas de seguridad de Microsoft crean y agregan constantemente nuevos libros, cuadernos de estrategias, consultas de búsqueda, etc. y los publican en la comunidad para que los pueda usar en su entorno. 

Descargue contenido de ejemplo del repositorio de GitHub privado de la comunidad para crear libros personalizados, consultas de búsqueda, cuadernos y cuadernos de estrategias de Azure Sentinel.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtener la certificación](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Leer historias de casos de uso de clientes](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)