---
title: Notas de la versión para la extensión de Azure Web Apps - Application Insights
description: Notas de la versión para la extensión de Azure Web Apps para la instrumentación de entorno de ejecución con Application Insights.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 8b0ddf90f5e6ffbe685c4aea1239323beea6c106
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828809"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Notas de la versión para la extensión de Azure Web Apps para Application Insights

Este artículo contiene las notas de la versión para la extensión de Azure Web Apps para la instrumentación de entorno de ejecución con Application Insights. Esto solo se aplica a las extensiones preinstaladas.

Obtenga más información sobre la [extensión de Azure Web Apps para Application Insights](azure-web-apps.md).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

- ¿Cómo puedo encontrar la versión de la extensión en la que estoy actualmente?
    - Ir a `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`. Visite la guía paso a paso de solución de problemas para la supervisión de [ASP.NET Core](./azure-web-apps-net-core.md#troubleshooting), [ASP.NET](./azure-web-apps-net.md#troubleshooting), [Java](./azure-web-apps-java.md#troubleshooting) o [Node.js](./azure-web-apps-nodejs.md#troubleshooting) basada en agente o extensión para obtener más información.

- ¿Qué ocurre si uso extensiones privadas?
    - Desinstale las extensiones de sitio privado, puesto que ya no se admiten.

## <a name="release-notes"></a>Notas de la versión

### <a name="2838"></a>2.8.38

- Extensión JAVA: se ha actualizado a [Java Agent 3.0.2 (GA)](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.0.2) desde 2.5.1.
- Extensión Node.js: se ha actualizado el SDK de AI a [1.8.8](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/1.8.8) desde 1.8.7.
- .NET Core: se han quitado las versiones que no son compatibles (2.0, 2.2, 3.0). Las versiones compatibles son 2.1 y 3.1.

### <a name="2837"></a>2.8.37

- Extensión AppSvc de Windows: ha hecho que .NET Core funcione con cualquier versión de System.Diagnostics.DiagnosticSource.dll.

### <a name="2836"></a>2.8.36

- Extensión AppSvc de Windows: se ha habilitado la interoperabilidad con el SDK de AI en .NET Core.

### <a name="2835"></a>2.8.35

- Extensión AppSvc de Windows: se ha agregado compatibilidad con .NET Core 3.1.

### <a name="2833"></a>2.8.33

- Agentes de .NET, .NET Core, Java y Node.js, así como la extensión de Windows: compatibilidad con nubes soberanas. Se pueden usar las cadenas de conexiones para enviar datos a nubes soberanas.

### <a name="2831"></a>2.8.31

- Agente de ASP.NET Core: se ha corregido un problema relacionado con una de las referencias actualizadas del SDK de Application Insights (consulte los problemas conocidos de 2.8.26). Si el entorno de ejecución ya ha cargado la versión incorrecta de `System.Diagnostics.DiagnosticSource.dll`, la extensión sin código no bloqueará la aplicación y se interrumpirá. Se recomienda a los clientes afectados por ese problema que quiten el archivo `System.Diagnostics.DiagnosticSource.dll` de la carpeta bin o que usen la versión anterior de la extensión mediante el establecimiento de "ApplicationInsightsAgent_EXTENSIONVERSION=2.8.24"; de lo contrario, la supervisión de aplicaciones no se habilitará.

### <a name="2826"></a>2.8.26

- Agente de ASP.NET Core: se ha corregido un problema relacionado con el SDK actualizado de Application Insights. El agente no intentará cargar `AiHostingStartup` si el archivo ApplicationInsights.dll ya está presente en la carpeta bin. Esto resuelve los problemas relacionados con la reflexión a través de Assembly\<AiHostingStartup\>.GetTypes().
- Problemas conocidos: se podría producir la excepción `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` si se carga otra versión del archivo dll `DiagnosticSource`. Esto puede ocurrir, por ejemplo, si `System.Diagnostics.DiagnosticSource.dll` está presente en la carpeta de publicación. Para mitigarlo, use la versión anterior de la extensión mediante el establecimiento de la configuración de la aplicación en App Services: ApplicationInsightsAgent_EXTENSIONVERSION=2.8.24.

### <a name="2824"></a>2.8.24

- Versión reempaquetada de 2.8.21.

### <a name="2823"></a>2.8.23

- Se ha agregado compatibilidad con la supervisión sin código de ASP.NET Core 3.0.
- Se ha actualizado el SDK de ASP.NET Core a [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) para las versiones de entorno de ejecución 2.1, 2.2 y 3.0. Las aplicaciones que tienen como destino .NET Core 2.0 siguen usando la versión 2.1.1 del SDK.

### <a name="2814"></a>2.8.14

- Se ha actualizado la versión del SDK de ASP.NET Core de 2.3.0 a la más reciente (2.6.1) para las aplicaciones que tienen como destino .NET Core 2.1 y 2.2. Las aplicaciones que tienen como destino .NET Core 2.0 siguen usando la versión 2.1.1 del SDK.

### <a name="2812"></a>2.8.12

- Compatibilidad con aplicaciones de ASP.NET Core 2.2.
- Se ha corregido un error en la extensión de ASP.NET Core que producía la inserción del SDK incluso cuando la aplicación ya estaba instrumentada con el SDK. En el caso de las aplicaciones de la versión 2.1 y 2.2, la presencia de ApplicationInsights.dll en la carpeta de la aplicación ahora hace que la extensión se interrumpa. En el caso de las aplicaciones de la versión 2.0, la extensión solo se interrumpe si ApplicationInsights está habilitado con una llamada `UseApplicationInsights()`.

- Se ha corregido de forma permanente la respuesta HTML incompleta para aplicaciones de ASP.NET Core. Esta corrección ahora se ha ampliado para que funcione con aplicaciones de .NET Core 2.2.

- Se ha agregado compatibilidad para desactivar la inserción de JavaScript para aplicaciones de ASP.NET Core (`APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting`). En el caso de ASP.NET Core, la inserción de JavaScript está en modo deshabilitado de forma predeterminada, a menos que se desactive explícitamente. (La configuración predeterminada está destinada a conservar el comportamiento actual).

- Se ha corregido un error de extensión de ASP.NET Core que provocaba la inserción aunque ikey no estuviera presente.
- Se ha corregido un error en la lógica del prefijo de la versión del SDK que provocaba una versión incorrecta del SDK en la telemetría.

- Se ha agregado el prefijo de versión del SDK para las aplicaciones de ASP.NET Core con el fin de identificar cómo se recopiló la telemetría.
- Se ha corregido la página de ApplicationInsights de SCM para que muestre correctamente la versión de la extensión preinstalada.

### <a name="2810"></a>2.8.10

- Se ha corregido la respuesta HTML incompleta para aplicaciones de ASP.NET Core.

## <a name="next-steps"></a>Pasos siguientes

- Visite la [documentación de Supervisión de aplicaciones para Azure App Service](azure-web-apps.md) para obtener más información sobre cómo configurar la supervisión de Azure App Service. 
