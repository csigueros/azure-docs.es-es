---
title: Supervisión de referencia de datos de App Service
description: Material de referencia importante necesario al supervisar App Service
author: msangapu-msft
ms.topic: reference
ms.author: msangapu
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: 330629c958e036db382bed63b9ae0b9642e14bad
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114227885"
---
# <a name="monitoring-app-service-data-reference"></a>Supervisión de referencia de datos de App Service

Esta referencia se aplica al uso de Azure Monitor para supervisar App Service. Consulte [Supervisión de App Service](monitor-app-service.md) para más información sobre la recopilación y el análisis de datos de supervisión de App Service.

## <a name="metrics"></a>Métricas

En esta sección se indican todas las métricas de la plataforma recopiladas automáticamente para App Service.  

|Tipo de métrica | Espacio de nombres de proveedor de recursos/tipo<br/> y vínculo a métricas individuales |
|-------|-----|
| Planes de App Service | [Microsoft.Web/serverfarms](../azure-monitor/essentials/metrics-supported.md#microsoftwebserverfarms)
| Aplicaciones web | [Microsoft.Web/sites](../azure-monitor/essentials/metrics-supported.md#microsoftwebsites) |
| Espacios de ensayo | [Microsoft.Web/sites/slots](../azure-monitor/essentials/metrics-supported.md#microsoftwebsitesslots) 
| Entorno de App Service | [Microsoft.Web/hostingEnvironments](../azure-monitor/essentials/metrics-supported.md#microsoftwebhostingenvironments)
| Front-end de App Service Environment | [Microsoft.Web/hostingEnvironments/multiRolePools](../azure-monitor/essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)


Para obtener más información, vea una lista de [todas las métricas de la plataforma que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).


## <a name="metric-dimensions"></a>Dimensiones de métricas

Para obtener más información sobre las dimensiones de métricas, consulte [Métricas multidimensionales](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

App Service no tiene ninguna métrica que contenga dimensiones.

## <a name="resource-logs"></a>Registros del recurso

> [!NOTE]
> La integración de Azure Monitor con App Service se encuentra en [versión preliminar](https://aka.ms/appsvcblog-azmon).
>

En esta sección se enumeran los tipos de registros de recursos que se pueden recopilar para Azure Service Bus. 

| Tipo de registro | Windows | Contenedor de Windows | Linux | Contenedor Linux | Descripción |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE y Tomcat | Sí | Sí | Sí | Salida estándar y error estándar |
| AppServiceHTTPLogs | Sí | Sí | Sí | Sí | Registros de servidor web |
| AppServiceEnvironmentPlatformLogs | Sí | N/D | Sí | Sí | App Service Environment: escalado, cambios de configuración y registros de estado|
| AppServiceAuditLogs | Sí | Sí | Sí | Sí | Actividad de inicio de sesión a través de FTP y KUDU |
| AppServiceFileAuditLogs | Sí | Sí | TBA | TBA | Cambios de archivo realizados en el contenido del sitio; **solo disponible para el nivel Premium y versiones posteriores** |
| AppServiceAppLogs | ASP.NET | ASP.NET | Imágenes Java SE y Tomcat <sup>1</sup> | Imágenes preparadas de Java SE y Tomcat <sup>1</sup> | Registros de aplicación |
| AppServiceIPSecAuditLogs  | Sí | Sí | Sí | Sí | Solicitudes de reglas IP |
| AppServicePlatformLogs  | TBA | Sí | Sí | Sí | Registros de operación de contenedor |
| AppServiceAntivirusScanAuditLogs | Sí | Sí | Sí | Sí | [Registros de examen antivirus](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) con Microsoft Defender; **solo están disponibles para el nivel prémium** | 

<sup>1</sup> Para las aplicaciones Java SE, agregue "$WEBSITE _AZMON_PREVIEW_ENABLED" a la configuración de la aplicación y establézcala en 1 o en true.

Como referencia, vea una lista de [todos los tipos de categorías de registros de recursos admitidos en Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md).

## <a name="azure-monitor-logs-tables"></a>Tablas de registros de Azure Monitor

Azure App Service usa tablas de Kusto de registros de Azure Monitor. Puede consultar estas tablas con Log Analytics. Para obtener una lista de tablas de App Service usadas por Kusto, consulte la [referencia de tablas de registros de Azure Monitor: tablas de App Service](/azure/azure-monitor/reference/tables/tables-resourcetype#app-services). 

## <a name="activity-log"></a>Registro de actividades

En la tabla siguiente, se enumeran las operaciones comunes relacionadas con App Service que se pueden crear en el registro de actividad. Esta no es una lista exhaustiva.

| Operación | Descripción |
|:---|:---|
|Crear una aplicación web| Se creó o actualizó una aplicación|
|Eliminar aplicación web| Se eliminó la aplicación. |
|Crear una copia de seguridad de aplicación web| Copia de seguridad de una aplicación.|
|Obtener el perfil de publicación de aplicaciones web| Descargue un perfil de publicación. |
|Publicar aplicación web| Aplicación implementada. |
|Reinicio de una aplicación web| Aplicación reiniciada.|
|Iniciar aplicación web| Aplicación iniciada. |
|Detener aplicación web| Aplicación detenida.|
|Intercambiar ranuras de aplicación web| Se intercambiaron las ranuras.|
|Obtener diferencias de ranuras de aplicación web| Diferencias de ranura.|
|Aplicar configuración de la aplicación web| Cambios de configuración aplicados.|
|Restablecer la configuración de la aplicación web| Restablecimiento de los cambios de la configuración.|
|Aprueba las conexiones de punto de conexión privado.| Conexiones de punto de conexión privado aprobadas.|
|Realizar seguimiento de red de aplicaciones web| Seguimiento de red iniciado.|
|Generar una nueva contraseña para las instancias de Web Apps| Nueva contraseña creada. |
|Obtener registros de contenedor comprimidos para la aplicación web| Obtención de registros de contenedor |
|Restaurar Web Apps desde el blob de copia de seguridad| Aplicación restaurada a partir de la copia de seguridad.|

Para más información sobre el esquema de las entradas del registro de actividad, consulte [Esquema del registro de actividad](../azure-monitor/essentials/activity-log-schema.md). 

## <a name="see-also"></a>Consulte también

- Consulte [Supervisión de Azure App Service](monitor-app-service.md) para ver una descripción de este servicio.
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).