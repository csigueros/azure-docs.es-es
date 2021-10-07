---
title: Supervisión del rendimiento de Azure App Services con Node.js | Microsoft Docs
description: Supervisión del rendimiento de aplicaciones de Azure App Services mediante Node.js. Carga y tiempo de respuesta de gráfico, información de dependencia y establecer alertas en el rendimiento.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 5f80666d4a184da40979a38f6d7f17782291c9e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699396"
---
# <a name="application-monitoring-for-azure-app-service-and-nodejs"></a>Supervisión de aplicaciones para Azure App Service y Node.js

Ahora, habilitar la supervisión en las aplicaciones web basadas en Node.js que se ejecutan en [Azure App Services](../../app-service/index.yml) es más fácil que nunca. Mientras que antes era necesario instrumentar manualmente la aplicación, ahora el agente o la extensión más reciente están integrados en la imagen de App Service de manera predeterminada. En este artículo le guiaremos a través de la habilitación de la supervisión de Application Insights para Azure Monitor y proporcionaremos instrucciones preliminares para automatizar el proceso para implementaciones a gran escala.

> [!NOTE]
> Si se detectan tanto la supervisión basada en agentes como la instrumentación manual basada en SDK, solo se respetará la configuración de la instrumentación manual. Esto es para evitar que se envíen datos duplicados. Para más información sobre este tema, consulte la [sección de solución de problemas](#troubleshooting) que encontrará a continuación.

## <a name="enable-agent-based-monitoring"></a>Habilitación de la supervisión basada en agente

Puede supervisar las aplicaciones de Node.js que se ejecutan en Azure App Service sin ningún cambio de código, solo con un par de pasos sencillos. Las aplicaciones de Application Insights para Node.js se integran con App Service en Linux, en los contenedores basados en código y los personalizados, y con App Service en Windows para las aplicaciones basadas en código. La integración se encuentra en versión preliminar pública. La integración agrega el SDK de Node.js, que se encuentra en la fase de disponibilidad general. 

1. **Seleccione Application Insights** en el panel de control de Azure del servicio de aplicaciones y, después, seleccione **Habilitar**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Captura de pantalla de la pestaña Application Insights con la opción Habilitar seleccionada."::: 

2. Cree un nuevo recurso o seleccione un recurso de Application Insights existente para esta aplicación.

     > [!NOTE]
     > Al hacer clic en **Aceptar** para crear el nuevo recurso, se le pedirá **Aplicar la configuración de supervisión**. Con la selección de **Continuar** se vinculará el nuevo recurso de Application Insights a su servicio de aplicaciones. Al hacerlo, también se **desencadenará un reinicio del servicio de aplicaciones**. 
    
    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Captura de pantalla de la lista desplegable Cambiar el recurso."::: 
    
3. Una vez que haya especificado qué recurso usar ya está listo para continuar. 


    :::image type="content"source="./media/azure-web-apps-nodejs/app-service-node.png" alt-text="Captura de pantalla de la instrumentación de la aplicación."::: 


## <a name="enable-client-side-monitoring"></a>Habilitación de la supervisión del lado cliente

Para habilitar la supervisión del lado cliente para la aplicación de Node.js, debe [agregar manualmente el SDK de JavaScript del lado cliente a la aplicación](./javascript.md).

## <a name="automate-monitoring"></a>Automatización de la supervisión

Con el fin de habilitar la recopilación de datos de telemetría con Application Insights, la configuración de la aplicación deberá configurarse:

:::image type="content"source="./media/azure-web-apps-nodejs/application-settings-nodejs.png" alt-text="Captura de pantalla de la configuración de la aplicación de App Service con la configuración de Application Insights disponible."::: 


### <a name="application-settings-definitions"></a>Definiciones de los valores de configuración de la aplicación

|Nombre del valor de configuración de la aplicación |  Definición | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensión principal; controla la supervisión en el entorno en tiempo de ejecución. | `~2` en Windows o `~3` en Linux |
|XDT_MicrosoftApplicationInsights_NodeJS |  Marca para controlar si el agente Node.js está incluido. | 0 o 1 solo se aplican en Windows. |


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="troubleshooting"></a>Solución de problemas

A continuación, se muestra nuestra guía paso a paso de solución de problemas para la supervisión basada en extensiones o agentes para aplicaciones basadas en Node.js que se ejecutan en Azure App Services.

# <a name="windows"></a>[Windows](#tab/windows)

1. Compruebe que la configuración de la aplicación `ApplicationInsightsAgent_EXTENSION_VERSION` se establece en un valor de "~2".
2. Vaya a `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="Captura de pantalla de la página de resultados del vínculo anterior."border ="false"::: 

    - Confirme que el `Application Insights Extension Status` es `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`. 

         Si no se está ejecutando, siga las [instrucciones para habilitar la supervisión de Application Insights](#enable-agent-based-monitoring).

    - Vaya a *D:\local\Temp\status.json* y abra *status.json*.

    Confirme que `SDKPresent` está establecido en false, `AgentInitializedSuccessfully` en true y que `IKey` tiene un iKey válido.

    A continuación se muestra un ejemplo del archivo JSON:

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    Si `SDKPresent` es true, indica que la extensión ha detectado que algún aspecto del SDK ya está presente en la aplicación y se interrumpirá.


# <a name="linux"></a>[Linux](#tab/linux)

1. Compruebe que la configuración de la aplicación `ApplicationInsightsAgent_EXTENSION_VERSION` está establecida en un valor de "~3".
2. Vaya a */var/log/applicationinsights/* y abra *status.json*.

    Confirme que `SDKPresent` está establecido en false, `AgentInitializedSuccessfully` en true y que `IKey` tiene un iKey válido.

    A continuación se muestra un ejemplo del archivo JSON:

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    Si `SDKPresent` es true, indica que la extensión ha detectado que algún aspecto del SDK ya está presente en la aplicación y se interrumpirá.
---

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

## <a name="release-notes"></a>Notas de la versión

Para obtener las actualizaciones y correcciones de errores más recientes, [consulte las notas de la versión](web-app-extension-release-notes.md).

## <a name="next-steps"></a>Pasos siguientes
* [Supervisión de Azure Functions con Application Insights](monitor-functions.md).
* [Microsoft Azure Diagnostics](../agents/diagnostics-extension-to-application-insights.md) para enviar este tipo de información a Application Insights.
* [Supervise las métricas del estado del servicio](../data-platform.md) para asegurarse de que el servicio está disponible y responde adecuadamente.
* [Reciba notificaciones de alerta](../alerts/alerts-overview.md) cada vez que se produzcan eventos de operaciones o las métricas traspasen un umbral.
* Use [aplicaciones y páginas web de Application Insights para JavaScript](javascript.md) para obtener la telemetría del cliente de los exploradores que visitan una página web.
* [Configure pruebas web de disponibilidad](monitor-web-app-availability.md) para recibir una alerta si el sitio deja de estar activo.