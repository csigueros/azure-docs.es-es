---
title: Generación de perfiles de aplicaciones activas de Azure App Service con Application Insights | Microsoft Docs
description: Cree perfiles de aplicaciones activas de Azure App Service con Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.openlocfilehash: e80ce3a4e96720bc3149dd9d8083a1f8f0943dbe
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815749"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Generación de perfiles de aplicaciones activas de Azure App Service con Application Insights

Puede ejecutar Profiler en aplicaciones ASP.NET y ASP.NET Core que se ejecutan en Azure App Service con el nivel de servicio Básico o superior. Actualmente, la habilitación de Profiler en Linux solo es posible a través de [este método](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Habilitación de Profiler en la aplicación
Para habilitar Profiler en una aplicación, siga las instrucciones que se indican a continuación. Si está ejecutando otro tipo de servicio de Azure, aquí encontrará instrucciones para habilitar Profiler en otras plataformas compatibles:
* [Cloud Services](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Aplicaciones de Service Fabric](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Máquinas virtuales](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler viene preinstalado en el entorno de ejecución de App Services. Los pasos siguientes le mostrarán cómo habilitarlo para App Service. Siga estos pasos incluso si ha incluido el SDK de App Insights en la aplicación en el momento de la compilación.

> [!NOTE]
> La instalación sin código de Application Insights Profiler sigue la directiva de compatibilidad de .NET Core.
> Para más información sobre los entornos de ejecución admitidos, consulte [Directiva de compatibilidad de .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

1. Vaya al panel de control de Azure y busque la instancia de App Service.
1. Habilite a la opción "Siempre disponible" para el servicio de aplicación. Puede encontrar esta opción en **Configuración**, página **Configuración** (consulte la captura de pantalla del paso siguiente) y seleccione la pestaña **Configuración general**.
1. Vaya a la página **Configuración > Application Insights**.

   ![Habilitación de App Insights en el portal de App Services](./media/profiler/AppInsights-AppServices.png)

1. Siga las instrucciones que aparecen en el panel para crear un nuevo recurso o seleccione un recurso de App Insights existente para supervisar la aplicación. También, asegúrese de que Profiler esté **activado**. Si el recurso de Application Insights está en otra suscripción de App Service, no puede usar esta página para configurar Application­ Insights. Sin embargo, puede hacerlo manualmente creando la configuración de la aplicación necesaria por usted mismo. [La siguiente sección contiene instrucciones para habilitar Profiler de forma manual.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Adición de la extensión de sitio de App Insights][Enablement UI]

1. Profiler está habilitado con una configuración de aplicación de App Services.

    ![Configuración de aplicación de Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Habilitación de Profiler manualmente o con Azure Resource Manager
Application Insights Profiler puede habilitarse mediante la creación de la configuración de la aplicación para Azure App Service. La página con las opciones mostradas anteriormente crea esta configuración de aplicación de forma automática. Sin embargo, es posible automatizar la creación de estas opciones mediante una plantilla u otros medios. Esta configuración también funciona si el recurso de Application Insights está en otra suscripción de Azure App Service.
Estos son los valores necesarios para habilitar el generador de perfiles:

|Configuración de aplicación    | Value    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para el recurso de Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Puede establecer estos valores mediante las [plantillas de Azure Resource Manager](./azure-web-apps-net-core.md#app-service-application-settings-with-azure-resource-manager), [Azure PowerShell](/powershell/module/az.websites/set-azwebapp) o la [CLI de Azure](/cli/azure/webapp/config/appsettings).

## <a name="enable-profiler-for-other-clouds"></a>Habilitación de Profiler para otras nubes

Actualmente, las únicas regiones que requieren modificaciones de punto de conexión son [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) y [Azure China](/azure/china/resources-developer-guide).

|Configuración de aplicación    | Nube del Gobierno de EE. UU. | Nube de China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Deshabilitación de Profiler

Para detener o reiniciar Profiler para la instancia de una aplicación, en la barra lateral izquierda, seleccione **WebJobs** y detenga el trabajo web llamado `ApplicationInsightsProfiler3`.

  ![Deshabilitación de Profiler para un trabajo web][disable-profiler-webjob]

Se recomienda tener habilitado el generador de perfiles en todas las aplicaciones para detectar cualquier problema de rendimiento lo antes posible.

Los archivos de Profiler pueden eliminarse cuando se usa WebDeploy para implementar los cambios en la aplicación web. Puede evitar la eliminación si se excluye la carpeta App_Data de la eliminación durante la implementación. 


## <a name="next-steps"></a>Pasos siguientes

* [Trabajo con Application Insights en Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png