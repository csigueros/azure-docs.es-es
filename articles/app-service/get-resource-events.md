---
title: Obtención de eventos de recursos en Azure App Service
description: Aprenda a obtener eventos de recursos mediante registros de actividad y Event Grid en la aplicación de App Service.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 4416ef9c24b5dcaf3415b7c0cfc80da0dfde97be
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462374"
---
# <a name="get-resource-events-in-azure-app-service"></a>Obtención de eventos de recursos en Azure App Service

Azure App Service proporciona herramientas integradas para supervisar el estado y el mantenimiento de los recursos. Los eventos de recursos le ayudan a entender los cambios que se realizaron en los recursos subyacentes de la aplicación web y a tomar las medidas necesarias. Algunos ejemplos de eventos son el escalado de instancias, las actualizaciones de la configuración de la aplicación, el reinicio de la aplicación web y muchas más. En este artículo, aprenderá a ver los [registros de actividad de Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log) y a habilitar [Event Grid](../event-grid/index.yml) para supervisar eventos de recursos de App Service.

## <a name="view-azure-activity-logs"></a>Visualización de los registros de actividad de Azure
Los registros de actividad de Azure contienen eventos de recursos emitidos por las operaciones realizadas en los recursos de la suscripción. Tanto las acciones del usuario en Azure Portal como en las plantillas de Azure Resource Manager contribuyen a los eventos capturados por el registro de actividad. 

Los registros de actividad de Azure para App Service contienen detalles como:
- Qué operaciones se han realizado en los recursos (por ejemplo, planes de App Service)
- Quién inició la operación
- Cuándo tuvo lugar la operación
- Estado de la operación
- Valores de propiedad para ayudarle a investigar la operación

### <a name="what-can-you-do-with-azure-activity-logs"></a>¿Qué puede hacer con los registros de actividad de Azure?

Los registros de actividad de Azure se pueden consultar mediante Azure Portal, PowerShell, la API REST o la CLI. Puede enviar los registros a una cuenta de almacenamiento, a un centro de eventos y a Log Analytics. También puede analizarlos en Power BI o crear alertas para que permanezcan actualizados en los eventos de recursos.

[Visualización y recuperación de eventos del registro de actividad de Azure.](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>Envío de registros de actividad a Event Grid

Aunque los registros de actividad están basados en el usuario, hay una nueva integración de [Event Grid](../event-grid/index.yml) con App Service (versión preliminar) que registra las acciones del usuario y los eventos automatizados. Con Event Grid, puede configurar un controlador para que reaccione a los mencionados eventos. Por ejemplo, use Event Grid para desencadenar al instante una función sin servidor que ejecute un análisis de la imagen cada vez que se agrega una fotografía nueva a un contenedor de Blob Storage.

También puede usar Event Grid con Logic Apps para procesar datos en cualquier parte, sin necesidad de escribir código. Event Grid conecta orígenes de datos y controladores de eventos.

[Visualización de las propiedades y el esquema de los eventos de Azure App Service.](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> Pasos siguientes
* [Consulta de registros con Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Control de Supervisión de aplicaciones en Azure App Service](web-sites-monitor.md)
* [Troubleshooting Azure App Service in Visual Studio](troubleshoot-dotnet-visual-studio.md) (Solución de problemas de Azure App Service en Visual Studio)
* [Analyze app Logs in HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413) (Análisis de registros de aplicación en HDInsight)