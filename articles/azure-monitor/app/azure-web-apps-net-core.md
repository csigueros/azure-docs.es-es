---
title: Supervisión del rendimiento de Azure App Services con .NET Core | Microsoft Docs
description: Supervisión del rendimiento de aplicaciones para Azure App Services mediante ASP.NET Core. Carga y tiempo de respuesta de gráfico, información de dependencia y establecer alertas en el rendimiento.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: bf474ff535a9292fbadfd023830d15973bf39ad1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699449"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet-core"></a>Supervisión de aplicaciones para Azure App Service mediante ASP.NET Core 

Habilitar la supervisión en sus aplicaciones web basadas en ASP.NET Core que se ejecutan en [Azure App Services](../../app-service/index.yml) es ahora más fácil que nunca. Mientras que antes era necesario instrumentar manualmente la aplicación, ahora el agente o la extensión más reciente están integrados en la imagen de App Service de manera predeterminada. En este artículo le guiaremos a través de la habilitación de la supervisión de Application Insights para Azure Monitor y proporcionaremos instrucciones preliminares para automatizar el proceso para implementaciones a gran escala.

## <a name="enable-agent-based-monitoring"></a>Habilitación de la supervisión basada en agente

# <a name="windows"></a>[Windows](#tab/Windows)

> [!IMPORTANT]
> Se admiten las siguientes versiones de ASP.NET Core para la instrumentación automática en Windows: ASP.NET Core 3.1 y 5.0. Las versiones 2.0, 2.1, 2.2 y 3.0 se han retirado y ya no se admiten. Actualice a una [versión compatible](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) de .NET Core para que la instrumentación automática funcione.

En Windows **no se admite** el establecimiento como destino del marco completo desde ASP.NET Core. En su lugar, use [instrumentación manual](./asp-net-core.md) por medio de código.

En Windows, solo se admite la [implementación dependiente del marco](/dotnet/core/deploying/#publish-framework-dependent) y no se admite la [implementación independiente](/dotnet/core/deploying/#publish-self-contained).

Vea la sección [Habilitar supervisión](#enable-monitoring ) a continuación para empezar a configurar Application Insights con el recurso de App Service. 

# <a name="linux"></a>[Linux](#tab/Linux)

> [!IMPORTANT]
> Solo se admite ASP.NET Core 6.0 (versión preliminar) para la instrumentación automática en Linux.

> [!NOTE]
> La instrumentación automática de Linux del portal de App Services está en versión preliminar pública. Estas versiones preliminares se proporcionan sin contrato de nivel de servicio. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

 En Linux, se admiten la [implementación dependiente del marco](/dotnet/core/deploying/#publish-framework-dependent) y la [implementación independiente](/dotnet/core/deploying/#publish-self-contained). 

Vea la sección [Habilitar supervisión](#enable-monitoring ) a continuación para empezar a configurar Application Insights con el recurso de App Service. 

---
 

### <a name="enable-monitoring"></a>Habilitar supervisión 

1. **Seleccione Application Insights** en el panel de control de Azure del servicio de aplicaciones y, después, seleccione **Habilitar**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text=" Captura de pantalla de la pestaña Application Insights con la opción Habilitar seleccionada."::: 

2. Cree un nuevo recurso o seleccione un recurso de Application Insights existente para esta aplicación.

    > [!NOTE]
    > Al hacer clic en **Aceptar** para crear el nuevo recurso, se le pedirá **Aplicar la configuración de supervisión**. Con la selección de **Continuar** se vinculará el nuevo recurso de Application Insights a su servicio de aplicaciones. Al hacerlo, también se **desencadenará un reinicio del servicio de aplicaciones**. 

    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Captura de pantalla de la lista desplegable Cambiar el recurso."::: 

2. Después de especificar qué recurso se debe usar, puede elegir cómo quiere que Application Insights recopile los datos de cada plataforma para la aplicación. ASP.NET Core ofrece **recopilación recomendada** o **deshabilitada**.

    :::image type="content"source="./media/azure-web-apps-net-core/instrument-net-core.png" alt-text=" Captura de pantalla de la sección de instrumentación de la aplicación."::: 


## <a name="enable-client-side-monitoring"></a>Habilitación de la supervisión del lado cliente

La supervisión de cliente está **habilitada de manera predeterminada** para las aplicaciones de ASP.NET Core con la **recopilación recomendada**, independientemente de si está presente el valor "APPINSIGHTS_JAVASCRIPT_ENABLED" de la aplicación.

Si por algún motivo quiere deshabilitar la supervisión de cliente:

* **Parámetros** **>** **Configuración**
   * En Configuración de la aplicación, cree una **nueva configuración de la aplicación**:

     Nombre: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `false`

   * **Guarde** la configuración y **reinicie** la aplicación.


## <a name="automate-monitoring"></a>Automatización de la supervisión

Con el fin de habilitar la recopilación de datos de telemetría con Application Insights, la configuración de la aplicación deberá establecerse:

:::image type="content"source="./media/azure-web-apps-net-core/application-settings-net-core.png" alt-text="Captura de pantalla de la configuración de la aplicación de App Service con la configuración de Application Insights."::: 


### <a name="application-settings-definitions"></a>Definiciones de los valores de configuración de la aplicación

|Nombre del valor de configuración de la aplicación |  Definición | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensión principal; controla la supervisión en el entorno en tiempo de ejecución. | `~2` para Windows o `~3` para Linux |
|XDT_MicrosoftApplicationInsights_Mode |  En el modo predeterminado, solo están habilitadas las características esenciales para garantizar un rendimiento óptimo. | `disabled` o `recommended`. |
|XDT_MicrosoftApplicationInsights_PreemptSdk | Solo para aplicaciones de ASP.NET Core. Habilita la interoperabilidad con el SDK de Application Insights. Carga la extensión en paralelo con el SDK y la usa para enviar telemetría (deshabilita el SDK de Application Insights). |`1`|


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>Actualización del agente o la extensión de supervisión: .NET 

### <a name="upgrade-from-versions-289-and-up"></a>Actualización de las versiones 2.8.9 y posteriores

La actualización de la versión 2.8.9 se realiza automáticamente, sin acciones adicionales. Los nuevos bits de supervisión se proporcionan en segundo plano para el servicio de aplicación de destino y se recogerán en el reinicio de la aplicación.

Para comprobar qué versión de la extensión se ejecuta, vaya a `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

:::image type="content"source="./media/azure-web-apps/extension-version.png" alt-text="Captura de pantalla de la ruta de acceso URL para comprobar la versión de la extensión que está ejecutando." border="false"::: 

### <a name="upgrade-from-versions-100---265"></a>Actualización de las versiones 1.0.0 a 2.6.5

A partir de la versión 2.8.9 se usa la extensión de sitio preinstalada. Si usa una versión anterior, puede actualizarla de dos maneras:

* [Actualización mediante la habilitación a través del portal](#enable-agent-based-monitoring). (Incluso si tiene la extensión Application Insights para Azure App Service instalada, la interfaz de usuario solo muestra el botón **Habilitar**. En segundo plano, la anterior extensión de sitio privada se quitará).

* [Actualización a través de PowerShell](#enable-through-powershell):

    1. Establezca la configuración de la aplicación para habilitar la extensión de sitio preinstalada ApplicationInsightsAgent. Consulte [Habilitación mediante Powershell](#enable-through-powershell).
    2. Quite manualmente la extensión de sitio privada denominada extensión de Application Insights para Azure App Service.

Si se realiza la actualización desde una versión anterior a la 2.5.1, compruebe que los archivos DLL de ApplicationInsigths se quitan de la carpeta de la ubicación de la aplicación. [Vea los pasos para la solución de problemas](#troubleshooting).

## <a name="troubleshooting"></a>Solución de problemas

A continuación, se muestra nuestra guía paso a paso de solución de problemas para la supervisión basada en extensiones o agentes para aplicaciones basadas en ASP.NET Core que se ejecutan en Azure App Services.

# <a name="windows"></a>[Windows](#tab/windows)

1. Compruebe que la configuración de la aplicación `ApplicationInsightsAgent_EXTENSION_VERSION` se establece en un valor de "~2".
2. Vaya a `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="Captura de pantalla de la página de resultados del vínculo anterior."border ="false"::: 
    
    - Confirme que el `Application Insights Extension Status` es `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`. 
    
         Si no se está ejecutando, siga las [instrucciones para habilitar la supervisión de Application Insights](#enable-agent-based-monitoring).

    - Confirme que el origen de estado existe y tiene el siguiente aspecto: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`.

         Si no está presente un valor similar, significa que la aplicación no se admite o no se está ejecutando actualmente. Para asegurarse de que la aplicación se está ejecutando, intente visitar manualmente los puntos de conexión de la aplicación o la dirección URL de la aplicación, lo que permitirá que la información del entorno en tiempo de ejecución esté disponible.

    - Confirme que `IKeyExists` es `true`. Si es `false`, agregue `APPINSIGHTS_INSTRUMENTATIONKEY` y `APPLICATIONINSIGHTS_CONNECTION_STRING` con el GUID de ikey a la configuración de la aplicación.

    -  En caso de que la aplicación haga referencia a algún paquete de Application Insights, por ejemplo, si ya ha instrumentado (o ha intentado instrumentar) la aplicación con el [SDK de ASP.NET Core](./asp-net-core.md), es posible que la integración de App Service no surta efecto y que los datos no aparezcan en Application Insights. Para solucionar el problema, en el portal, active "Interoperabilidad con el SDK de Application Insights", y comenzará a ver los datos en Application Insights. 
    - 
        > [!IMPORTANT]
        > Esta funcionalidad está en versión preliminar. 

        :::image type="content"source="./media/azure-web-apps-net-core/interop.png" alt-text="Captura de pantalla de la opción de interoperabilidad habilitada."::: 
        
        Ahora los datos se van a enviar mediante un enfoque sin código, incluso si el SDK de Application Insights se usó originalmente o se intentó usar.

        > [!IMPORTANT]
        > Si la aplicación usó el SDK de Application Insights para enviar cualquier telemetría, se deshabilitará la telemetría —es decir, la telemetría personalizada— si existe, por ejemplo, los métodos Track*(), y se deshabilitará toda configuración personalizada, como el muestreo. 

# <a name="linux"></a>[Linux](#tab/linux)

1. Compruebe que la configuración de la aplicación `ApplicationInsightsAgent_EXTENSION_VERSION` está establecida en un valor de "~3".
2. Vaya a */home\LogFiles\ApplicationInsights\status* y abra *status_557de146e7fa_27_1.json*.

    Confirme que `AppAlreadyInstrumented` está establecido en false, `AiHostingStartupLoaded` en true y `IKeyExists` en true.

    A continuación se muestra un ejemplo del archivo JSON:

    ```json
        "AppType":".NETCoreApp,Version=v6.0",
                
        "MachineName":"557de146e7fa",
                
        "PID":"27",
                
        "AppDomainId":"1",
                
        "AppDomainName":"dotnet6demo",
                
        "InstrumentationEngineLoaded":false,
                
        "InstrumentationEngineExtensionLoaded":false,
                
        "HostingStartupBootstrapperLoaded":true,
                
        "AppAlreadyInstrumented":false,
                
        "AppDiagnosticSourceAssembly":"System.Diagnostics.DiagnosticSource, Version=6.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51",
                
        "AiHostingStartupLoaded":true,
                
        "IKeyExists":true,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "ConnectionString":"InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/"
    
    ```
    
    Si `AppAlreadyInstrumented` es true, indica que la extensión ha detectado que algún aspecto del SDK ya está presente en la aplicación y se interrumpirá.

##### <a name="no-data"></a>Sin datos

1. Enumere e identifique el proceso que hospeda una aplicación. Vaya al terminal y, en la línea de comandos, escriba `ps ax`. 
    
    La salida debe ser similar a: 

   ```bash
     PID TTY      STAT   TIME COMMAND
    
        1 ?        SNs    0:00 /bin/bash /opt/startup/startup.sh
    
       19 ?        SNs    0:00 /usr/sbin/sshd
    
       27 ?        SNLl   5:52 dotnet dotnet6demo.dll
    
       50 ?        SNs    0:00 sshd: root@pts/0
    
       53 pts/0    SNs+   0:00 -bash
    
       55 ?        SNs    0:00 sshd: root@pts/1
    
       57 pts/1    SNs+   0:00 -bash
   ``` 


1. Luego enumere las variables de entorno del proceso de la aplicación. En la línea de comandos, escriba `cat /proc/27/environ | tr '\0' '\n`.
    
    La salida debe ser similar a: 

    ```bash
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
    
    DOTNET_STARTUP_HOOKS=/DotNetCoreAgent/2.8.39/StartupHook/Microsoft.ApplicationInsights.StartupHook.dll
    
    APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/
    
    ```
    
1. Compruebe que `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_STARTUP_HOOKS` y `APPLICATIONINSIGHTS_CONNECTION_STRING` están definidos.

---

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>El sitio web predeterminado implementado con las aplicaciones web no admite la supervisión automática de cliente.

Al crear una aplicación web con los entornos de ejecución `ASP.NET Core` en Azure App Services, implementa una sola página HTML estática como sitio web de inicio. La página web estática también carga un elemento web administrado de ASP.NET en IIS. Esto permite probar la supervisión sin código del lado servidor, pero no admite la supervisión automática de cliente.

Si desea probar el servidor sin código y la supervisión de cliente para ASP.NET Core en una aplicación web de Azure App Services, se recomienda seguir las guías oficiales para [crear una aplicación web ASP.NET Core](../../app-service/quickstart-dotnetcore.md). A continuación, siga las instrucciones del artículo actual para habilitar la supervisión.

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>PHP y WordPress no se admiten

Los sitios de PHP y WordPress no se admiten. Actualmente, no hay ningún SDK/agente compatible oficialmente para la supervisión del lado servidor de estas cargas de trabajo. Sin embargo, la instrumentación manual de transacciones del lado cliente en un sitio de PHP o WordPress agregando el JavaScript del lado cliente a las páginas web puede realizarse mediante el [SDK de JavaScript](./javascript.md).

La tabla siguiente proporciona una explicación más detallada de lo que significan estos valores, sus causas subyacente y las correcciones recomendadas:

|Valor del problema |Explicación |Fix |
|---- |----|---|
| `AppAlreadyInstrumented:true` | Este valor indica que la extensión ha detectado que algún aspecto del SDK ya está presente en la aplicación y se interrumpirá. Puede deberse a una referencia a `Microsoft.ApplicationInsights.AspNetCore` o `Microsoft.ApplicationInsights`.  | Quite las referencias. Algunas de estas referencias se agregan de manera predeterminada en determinadas plantillas de Visual Studio y las versiones anteriores de Visual Studio pueden agregar referencias a `Microsoft.ApplicationInsights`. |
|`AppAlreadyInstrumented:true` | Si la aplicación se dirige a ASP.NET Core 2.1 o 2.2, este valor indica que la extensión ha detectado que algún aspecto del SDK ya está presente en la aplicación y se interrumpirá. | Se [recomienda](https://github.com/aspnet/Announcements/issues/287) que los clientes con .NET Core 2.1 o 2.2 usen el metapaquete Microsoft.AspNetCore.App en su lugar. Además, active Interop with Application Insights SDK (Interoperabilidad con SDK de Application Insights) en el portal (consulte las instrucciones anteriores).|
|`AppAlreadyInstrumented:true` | Este valor también puede deberse a la presencia de Microsoft.ApplicationsInsights dll en la carpeta de la aplicación de una implementación anterior. | Limpie la carpeta de la aplicación para asegurarse de que se han quitado estos archivos DLL. Compruebe el directorio "bin" de la aplicación local y el directorio "wwwroot" de App Service. (Para comprobar el directorio "wwwroot" de la aplicación web de App Service: herramientas avanzadas (Kudu) > Consola de depuración > CMD > home\site\wwwroot). |
|`IKeyExists:false`|Este valor indica que la clave de instrumentación no está presente en la AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Causas posibles: Es posible que los valores se hayan eliminado por accidente, que haya olvidado establecer los valores en el script de automatización, etc. | Asegúrese de que la configuración está presente en la configuración de la aplicación de App Service. |

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
