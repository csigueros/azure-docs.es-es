---
title: Supervisión del rendimiento de Azure App Service con ASP.NET | Microsoft Docs
description: Supervisión del rendimiento de aplicaciones para Azure App Service mediante ASP.NET. Carga y tiempo de respuesta de gráfico, información de dependencia y establecer alertas en el rendimiento.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 5e85a12d017f5fc812ac4f910ce9517d73cc41c7
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154608"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet"></a>Supervisión de aplicaciones para Azure App Service y ASP.NET 

Ahora es más fácil que nunca habilitar la supervisión en sus aplicaciones web basadas en ASP.NET que se ejecutan en [Azure App Service](../../app-service/index.yml). Mientras que antes era necesario instrumentar manualmente la aplicación, ahora el agente o la extensión más reciente están integrados en la imagen de App Service de manera predeterminada. En este artículo le guiaremos a través de la habilitación de la supervisión de Application Insights para Azure Monitor y proporcionaremos instrucciones preliminares para automatizar el proceso para implementaciones a gran escala.

> [!NOTE]
> Agregar manualmente una extensión de sitio de Application Insights a través de **Herramientas de desarrollo** > **Extensiones** está en desuso. Este método de instalación de extensiones dependía de actualizaciones manuales para cada nueva versión. La versión estable más reciente de la extensión ahora viene [preinstalada](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte de la imagen de App Service. Los archivos se encuentran en `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` y se actualizan automáticamente con cada versión estable. Si sigue las instrucciones basadas en agentes para habilitar la supervisión a continuación, se quita automáticamente la extensión en desuso.

> [!NOTE]
> Si se detectan tanto la supervisión basada en agentes como la instrumentación manual basada en SDK, solo se respetará la configuración de la instrumentación manual. Esto es para evitar que se envíen datos duplicados. Para más información sobre este tema, consulte la [sección de solución de problemas](#troubleshooting) que encontrará a continuación.

## <a name="enable-agent-based-monitoring"></a>Habilitación de la supervisión basada en agente

> [!NOTE]
> No se admite la combinación de APPINSIGHTS_JAVASCRIPT_ENABLED y urlCompression. Para más información, consulte la explicación de la [sección Solución de problemas](#appinsights_javascript_enabled-and-urlcompression-is-not-supported).

1. **Seleccione "Application Insights"** en el panel de control de Azure de su servicio de aplicaciones y, a continuación, **Habilitar**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Captura de pantalla de la pestaña &quot;Application Insights&quot; con la opción &quot;Habilitar&quot; seleccionada"::: 

2. Cree un nuevo recurso o seleccione un recurso de Application Insights existente para esta aplicación. 

    > [!NOTE]
    > Al hacer clic en **Aceptar** para crear el nuevo recurso, se le pedirá **Aplicar la configuración de supervisión**. Con la selección de **Continuar** se vinculará el nuevo recurso de Application Insights a su servicio de aplicaciones. Al hacerlo, también se **desencadenará un reinicio del servicio de aplicaciones**. 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Captura de pantalla de la lista desplegable &quot;Cambiar el recurso&quot;"::: 

3. Después de especificar qué recurso se debe usar, puede elegir cómo quiere que Application Insights recopile los datos de cada plataforma para la aplicación. La supervisión de aplicaciones ASP.NET está activada de manera predeterminada con dos niveles diferentes de la colección.

    :::image type="content"source="./media/azure-web-apps-net/instrument-net.png" alt-text="Captura de pantalla que muestra la página de extensiones de sitio de Application Insights con la opción Crear recurso seleccionada."::: 
 
     A continuación se muestra un resumen de los datos recopilados para cada ruta:
            
    | data | Recopilación básica de ASP.NET | Recopilación recomendada de ASP.NET |
    | --- | --- | --- |
    | Agrega las tendencias de uso de CPU, memoria y E/S. |Sí |Sí |
    | Recopila las tendencias de uso y habilita la correlación entre los resultados de disponibilidad y las transacciones. | Sí |Sí |
    | Recopila las excepciones no controladas por el proceso de host. | Sí |Sí |
    | Mejora la precisión de las métricas de APM con carga, cuando se usa el muestreo. | Sí |Sí |
    | Correlaciona los microservicios entre los límites de solicitud y dependencia. | No (solo funcionalidades de APM de instancia única) |Sí |

4. Para configurar el muestreo, algo que antes se podía controlar mediante el archivo "applicationinsights.config", ahora puede interactuar con él a través de la configuración de la aplicación con el prefijo `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor` correspondiente. 

    - Por ejemplo, para cambiar el porcentaje de muestreo inicial, puede crear una configuración de la aplicación de `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` y un valor de `100`.
    - Para deshabilitar el muestreo, establezca `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage` en un valor de `100`.
    - Los valores admitidos incluyen los siguientes:
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_EvaluationInterval`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MaxTelemetryItemsPerSecond`
        
    - Para obtener la lista de valores y definiciones de procesador de telemetría de muestreo adaptable compatibles, puede consultar el [código](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) y la [documentación de muestreo](./sampling.md#configuring-adaptive-sampling-for-aspnet-applications).


## <a name="enable-client-side-monitoring"></a>Habilitación de la supervisión del lado cliente

La supervisión de cliente está habilitada en ASP.NET. Para habilitar la supervisión de cliente:

* **Parámetros** **>** **Configuración**
   * En Configuración de la aplicación, cree una **nueva configuración de la aplicación**:

     Nombre: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `true`

   * **Guarde** la configuración y **reinicie** la aplicación.

Para deshabilitar la supervisión de cliente, quite el par clave-valor asociado de la configuración de la aplicación o establezca el valor en false.

## <a name="automate-monitoring"></a>Automatización de la supervisión

Con el fin de habilitar la recopilación de datos de telemetría con Application Insights, la configuración de la aplicación deberá configurarse:

:::image type="content"source="./media/azure-web-apps-net/application-settings-net.png" alt-text="Captura de pantalla de la configuración de la aplicación de App Service con la configuración de &quot;Application Insights&quot;"::: 

### <a name="application-settings-definitions"></a>Definiciones de los valores de configuración de la aplicación

|Nombre del valor de configuración de la aplicación |  Definición | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensión principal; controla la supervisión en el entorno en tiempo de ejecución. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  En el modo predeterminado, solo están habilitadas las características esenciales para garantizar un rendimiento óptimo. | `default` o `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controla si se activa el motor `InstrumentationEngine` de reescritura binaria. Esta configuración tiene implicaciones de rendimiento y afecta a la hora de inicio o al arranque en frío. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controla si el texto de la tabla SQL y Azure se captura junto con las llamadas de dependencia. Advertencia de rendimiento: el tiempo de inicio en frío de la aplicación se verá afectado. Esta configuración requiere el elemento `InstrumentationEngine`. | `~1` |

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>Actualización del agente o la extensión de supervisión: .NET 

### <a name="upgrade-from-versions-289-and-up"></a>Actualización de las versiones 2.8.9 y posteriores

La actualización de la versión 2.8.9 se realiza automáticamente, sin acciones adicionales. Los nuevos bits de supervisión se proporcionan en segundo plano para el servicio de aplicación de destino y se recogen en el reinicio de la aplicación.

Para comprobar qué versión de la extensión se ejecuta, vaya a `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

:::image type="content"source="./media/azure-web-apps/extension-version.png" alt-text="Captura de pantalla de la ruta de acceso de URL para comprobar la versión de la extensión que está ejecutando" border="false"::: 

### <a name="upgrade-from-versions-100---265"></a>Actualización de las versiones 1.0.0 a 2.6.5

A partir de la versión 2.8.9 se usa la extensión de sitio preinstalada. Si tiene una versión anterior, puede actualizarla de dos maneras:

* [Actualización mediante la habilitación a través del portal](#enable-agent-based-monitoring). (Incluso si tiene la extensión Application Insights para Azure App Service instalada, la interfaz de usuario solo muestra el botón **Habilitar**. En segundo plano, la anterior extensión de sitio privada se quitará).

* [Actualización a través de PowerShell](#enable-through-powershell):

    1. Establezca la configuración de la aplicación para habilitar la extensión de sitio preinstalada ApplicationInsightsAgent. Consulte [Habilitación mediante Powershell](#enable-through-powershell).
    2. Quite manualmente la extensión de sitio privada denominada extensión de Application Insights para Azure App Service.

Si se realiza la actualización desde una versión anterior a la 2.5.1, compruebe que los archivos DLL de ApplicationInsigths se quitan de la carpeta de la ubicación de la aplicación. [Vea los pasos para la solución de problemas](#troubleshooting).

## <a name="troubleshooting"></a>Solución de problemas

A continuación figura nuestra guía paso a paso de solución de problemas relacionados con la supervisión basada en extensiones o agentes para aplicaciones basadas en ASP.NET que se ejecutan en Azure App Service.

1. Compruebe que la configuración de la aplicación `ApplicationInsightsAgent_EXTENSION_VERSION` se establece en un valor de "~2".
2. Vaya a `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="Captura de pantalla de la página de resultados del vínculo anterior"border ="false"::: 
    
    - Confirme que el `Application Insights Extension Status` es `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`. 
    
         Si no se está ejecutando, siga las [instrucciones para habilitar la supervisión de Application Insights](#enable-agent-based-monitoring).

    - Confirme que el origen de estado existe y tiene el siguiente aspecto: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`.

         Si no está presente un valor similar, significa que la aplicación no se admite o no se está ejecutando actualmente. Para asegurarse de que la aplicación se está ejecutando, intente visitar manualmente los puntos de conexión de la aplicación o la dirección URL de la aplicación, lo que permitirá que la información del entorno en tiempo de ejecución esté disponible.

    - Confirme que `IKeyExists` es `true`. Si es `false`, agregue `APPINSIGHTS_INSTRUMENTATIONKEY` y `APPLICATIONINSIGHTS_CONNECTION_STRING` con el GUID de ikey a la configuración de la aplicación.

    - Confirme que no hay ninguna entrada para `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly` y `AppContainsAspNetTelemetryCorrelationAssembly`.

         Si existe alguna de estas entradas, quite los siguientes paquetes de la aplicación: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource` y `Microsoft.AspNet.TelemetryCorrelation`.

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>El sitio web predeterminado implementado con las aplicaciones web no admite la supervisión automática de cliente.

Al crear una aplicación web con los entornos de ejecución `ASP.NET` en Azure App Service, implementa una sola página HTML estática como sitio web de inicio. La página web estática también carga un elemento web administrado de ASP.NET en IIS. Esto permite probar la supervisión sin código del lado servidor, pero no admite la supervisión automática de cliente.

Si desea probar el servidor sin código y la supervisión de cliente para ASP.NET en una aplicación web de Azure App Service, se recomienda seguir las guías oficiales para [crear una aplicación web de ASP.NET Framework](../../app-service/quickstart-dotnetcore.md?tabs=netframework48) y después usar las instrucciones del artículo actual para habilitar la supervisión.


### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>No se admite APPINSIGHTS_JAVASCRIPT_ENABLED ni urlCompression

Si usa APPINSIGHTS_JAVASCRIPT_ENABLED=true en casos donde el contenido está codificado, podría obtener errores como los siguientes:

- Error de reescritura de dirección URL 500
- Error de módulo de reescritura de dirección URL 500.53 con el mensaje Las reglas de reescritura saliente no se pueden aplicar cuando el contenido de la respuesta HTTP está codificado ("gzip").

Esto se debe a que la configuración de la aplicación APPINSIGHTS_JAVASCRIPT_ENABLED está establecida en true y la codificación de contenido está presente al mismo tiempo. Este escenario aún no se admite. La solución consiste en quitar APPINSIGHTS_JAVASCRIPT_ENABLED de la configuración de la aplicación. Esto significa que si la instrumentación de JavaScript del lado cliente o explorador sigue siendo necesaria, se necesitan referencias del SDK manuales para las páginas web. Siga las [instrucciones](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) para la instrumentación manual con el SDK de JavaScript.

Para obtener la información más reciente sobre la extensión o el agente de Application Insights, consulte las [notas de la versión](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>PHP y WordPress no se admiten

Los sitios de PHP y WordPress no se admiten. Actualmente, no hay ningún SDK/agente compatible oficialmente para la supervisión del lado servidor de estas cargas de trabajo. Sin embargo, la instrumentación manual de transacciones del lado cliente en un sitio de PHP o WordPress agregando el JavaScript del lado cliente a las páginas web puede realizarse mediante el [SDK de JavaScript](./javascript.md).

La tabla siguiente proporciona una explicación más detallada de lo que significan estos valores, sus causas subyacente y las correcciones recomendadas:

|Valor del problema|Explicación|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Este valor indica que la extensión ha detectado que algún aspecto del SDK ya está presente en la aplicación y se interrumpirá. Puede deberse a una referencia a `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` o `Microsoft.ApplicationInsights`.  | Quite las referencias. Algunas de estas referencias se agregan de manera predeterminada en determinadas plantillas de Visual Studio y las versiones anteriores de Visual Studio pueden agregar referencias a `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Si la aplicación se dirige a ASP.NET Core 2.1 o 2.2, este valor indica que la extensión ha detectado que algún aspecto del SDK ya está presente en la aplicación y se interrumpirá. | Se [recomienda](https://github.com/aspnet/Announcements/issues/287) que los clientes con .NET Core 2.1 o 2.2 usen el metapaquete Microsoft.AspNetCore.App en su lugar. Además, active Interop with Application Insights SDK (Interoperabilidad con SDK de Application Insights) en el portal (consulte las instrucciones anteriores).|
|`AppAlreadyInstrumented:true` | Este valor también puede deberse a la presencia de los archivos DLL anteriores en la carpeta de la aplicación de una implementación anterior. | Limpie la carpeta de la aplicación para asegurarse de que se han quitado estos archivos DLL. Compruebe el directorio "bin" de la aplicación local y el directorio "wwwroot" de App Service. (Para comprobar el directorio "wwwroot" de la aplicación web de App Service: herramientas avanzadas (Kudu) > Consola de depuración > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Este valor indica que la extensión detectó referencias a `Microsoft.AspNet.TelemetryCorrelation` en la aplicación y se interrumpirá. | Quite la referencia.
|`AppContainsDiagnosticSourceAssembly**:true`|Este valor indica que la extensión detectó referencias a `System.Diagnostics.DiagnosticSource` en la aplicación y se interrumpirá.| Para ASP.NET, quite la referencia. 
|`IKeyExists:false`|Este valor indica que la clave de instrumentación no está presente en la AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Causas posibles: Es posible que los valores se hayan eliminado por accidente, que haya olvidado establecer los valores en el script de automatización, etc. | Asegúrese de que la configuración está presente en la configuración de la aplicación de App Service.

## <a name="release-notes"></a>Notas de la versión

Para obtener las actualizaciones y correcciones de errores más recientes, [consulte las notas de la versión](web-app-extension-release-notes.md).

## <a name="next-steps"></a>Pasos siguientes

* [Ejecute el generador de perfiles en la aplicación activa](./profiler.md).
* [Supervisión de Azure Functions con Application Insights](monitor-functions.md).
* [Microsoft Azure Diagnostics](../agents/diagnostics-extension-to-application-insights.md) para enviar este tipo de información a Application Insights.
* [Supervise las métricas del estado del servicio](../data-platform.md) para asegurarse de que el servicio está disponible y responde adecuadamente.
* [Reciba notificaciones de alerta](../alerts/alerts-overview.md) cada vez que se produzcan eventos de operaciones o las métricas traspasen un umbral.
* Use [aplicaciones y páginas web de Application Insights para JavaScript](javascript.md) para obtener la telemetría del cliente de los exploradores que visitan una página web.
* [Configure pruebas web de disponibilidad](monitor-web-app-availability.md) para recibir una alerta si el sitio deja de estar activo.