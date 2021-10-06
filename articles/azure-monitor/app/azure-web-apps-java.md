---
title: Supervisión del rendimiento de Azure App Services con Java | Microsoft Docs
description: Supervisión del rendimiento de aplicaciones de Azure App Services mediante Java. Carga y tiempo de respuesta de gráfico, información de dependencia y establecer alertas en el rendimiento.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: d673524b30eae13e24758aff23a68bd2b38c2e3e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699406"
---
# <a name="application-monitoring-for-azure-app-service-and-java"></a>Supervisión de aplicaciones para Azure App Service y Java

Para la supervisión de las aplicaciones web basadas en Java que se ejecutan en [Azure App Services](../../app-service/index.yml) no es necesario modificar el código. En este artículo se le guiará por la habilitación de la supervisión de Application Insights para Azure Monitor y se proporcionarán instrucciones preliminares a fin de automatizar el proceso para implementaciones a gran escala.

## <a name="enable-application-insights"></a>Habilitación de Application Insights

La manera recomendada de habilitar la supervisión de aplicaciones para aplicaciones Java que se ejecutan en Azure App Services es mediante Azure Portal. Al activar la supervisión de aplicaciones en Azure Portal, la aplicación se instrumentará automáticamente con Application Insights.  

### <a name="auto-instrumentation-through-azure-portal"></a>Instrumentación automática desde Azure Portal

Este método no necesita ningún cambio de código ni configuraciones avanzadas, por lo que es la manera más fácil de empezar a trabajar con la supervisión de Azure App Services. Puede aplicar configuraciones adicionales y, después, en función del escenario específico, evaluar si se necesita una supervisión más avanzada por medio de la [instrumentación manual](./java-2x-get-started.md?tabs=maven).

### <a name="enable-backend-monitoring"></a>Habilitación de la supervisión de back-end

Puede activar la supervisión de las aplicaciones Java que se ejecutan en Azure App Service con un solo clic, no se requiere ningún cambio de código. Application Insights para Java se integra con App Service en Linux, en los contenedores basados en código y los personalizados, y con App Service en Windows para las aplicaciones basadas en código. Es importante saber cómo se supervisará la aplicación. La integración agrega [Application Insights Java 3.x](./java-in-process-agent.md) y recibirá la telemetría recopilada de manera automática.

1. **Seleccione Application Insights** en el panel de control de Azure del servicio de aplicaciones y, después, seleccione **Habilitar**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Captura de pantalla de la pestaña Application Insights con la opción Habilitar seleccionada."::: 

2. Cree un nuevo recurso o seleccione un recurso de Application Insights existente para esta aplicación.

    > [!NOTE]
    > Al seleccionar **Aceptar** para crear el recurso, se le pedirá **Aplicar la configuración de supervisión**. Con la selección de **Continuar** se vinculará el nuevo recurso de Application Insights a su servicio de aplicaciones. Al hacerlo, también se **desencadenará un reinicio del servicio de aplicaciones**. 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Captura de pantalla de la lista desplegable Cambiar el recurso."::: 

3. Este paso no es necesario. Después de especificar qué recurso usar, puede configurar el agente de Java. Si no configura el agente de Java, se aplican las configuraciones predeterminadas. 

    El [conjunto de configuraciones](./java-standalone-config.md) completo está disponible, solo tiene que pegar un [archivo JSON](./java-standalone-config.md#an-example) válido. **Excluya la cadena de conexión y las configuraciones que se encuentran en versión preliminar**: podrá agregar los elementos que actualmente se encuentran en versión preliminar a medida que estén disponibles con carácter general.

    Una vez que modifique las configuraciones mediante Azure Portal, la variable de entorno APPLICATIONINSIGHTS_CONFIGURATION_FILE se rellenará automáticamente y aparecerá en el panel de configuración de App Service. Esta variable contendrá el contenido JSON completo que ha pegado en el cuadro de texto de configuración de la aplicación Java en Azure Portal. 

    :::image type="content"source="./media/azure-web-apps-java/create-app-service-ai.png" alt-text="Captura de pantalla de la instrumentación de la aplicación."::: 
    

## <a name="enable-client-side-monitoring"></a>Habilitación de la supervisión del lado cliente

Para habilitar la supervisión del lado cliente para la aplicación Java, debe [agregar manualmente el SDK de JavaScript del lado cliente a la aplicación](./javascript.md).

## <a name="automate-monitoring"></a>Automatización de la supervisión

### <a name="application-settings"></a>Configuración de la aplicación

Para habilitar la recopilación de datos de telemetría con Application Insights, solo es necesario configurar los valores siguientes de la aplicación:

|Nombre del valor de configuración de la aplicación |  Definición | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Controla la supervisión en tiempo de ejecución | `~2` para Windows o `~3` para Linux |
|XDT_MicrosoftApplicationInsights_Java |  Marca para controlar que se incluye el agente de Java | 0 o 1 solo se aplican en Windows
|APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL | Solo se usa si es necesario depurar la integración de Application Insights con App Service | debug

:::image type="content"source="./media/azure-web-apps-java/application-settings-java.png" alt-text="Captura de pantalla de la configuración de la aplicación de App Service con la configuración de Application Insights disponible."::: 

> [!NOTE]
> Profiler y Snapshot Debugger no están disponibles para aplicaciones de Java

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]

## <a name="troubleshooting"></a>Solución de problemas

A continuación se muestra nuestra guía paso a paso de solución de problemas para aplicaciones basadas en Java que se ejecutan en Azure App Services.

1. Compruebe que la configuración de la aplicación `ApplicationInsightsAgent_EXTENSION_VERSION` está establecida en un valor de "~2" en Windows y "~3" en Linux
1. Examine el archivo de registro para ver que el agente se ha iniciado correctamente: vaya a `https://yoursitename.scm.azurewebsites.net/ y, en SSH, cambie al directorio raíz; el archivo de registro se encuentra en LogFiles/ApplicationInsights. 
  
    :::image type="content"source="./media/azure-web-apps-java/app-insights-java-status.png" alt-text="Captura de pantalla de la página de resultados del vínculo anterior."::: 

1. Después de habilitar la supervisión de aplicaciones para la aplicación de Java, puede validar que el agente funciona si examina las métricas activas, incluso antes de implementar una aplicación en App Service verá algunas solicitudes del entorno. Recuerde que el conjunto completo de telemetría solo estará disponible cuando la aplicación esté implementada y en ejecución. 
1. Establezca la variable de entorno APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL en "debug" si no ve ningún error y no hay telemetría.
1. A veces, la versión más reciente del agente de Java de Application Insights no está disponible en App Service; las versiones más recientes tardan un par de meses en implementarse en todas las regiones. En caso de que necesite la versión más reciente del agente de Java para supervisar la aplicación en App Service, puede cargar el agente de forma manual: 
    * Carga del archivo .jar del agente de Java para App Service
        * Obtenga la versión más reciente de la [CLI de Azure](/cli/azure/install-azure-cli-windows?tabs=azure-cli)
        * Obtenga la versión más reciente del [agente de Java de Application Insights](./java-in-process-agent.md)
        * Implemente el agente de Java de App Service: un comando de ejemplo para implementar el archivo .jar del agente de Java: `az webapp deploy --src-path applicationinsights-agent-{VERSION_NUMBER}.jar --target-path java/applicationinsights-agent-{VERSION_NUMBER}.jar --type static --resource-group {YOUR_RESOURCE_GROUP} --name {YOUR_APP_SVC_NAME}` o use [esta guía](../../app-service/quickstart-java.md?tabs=javase&pivots=platform-linux#configure-the-maven-plugin) para realizar la implementación con el complemento Maven.
    * Una vez que se cargue el archivo .jar del agente, vaya a las configuraciones de App Service, agregue una nueva variable de entorno, JAVA_OPTS, y establezca su valor en `-javaagent:D:/home/{PATH_TO_THE_AGENT_JAR}/applicationinsights-agent-{VERSION_NUMBER}.jar`
    * Deshabilite Application Insights desde la pestaña Application Insights.
    * Reiniciar la aplicación

    > [!NOTE]
    > Si ha establecido la variable de entorno JAVA_OPTS, tendrá que deshabilitar Application Insights en el portal. Como alternativa, si prefiere habilitar Application Insights desde el portal, asegúrese de no establecer la variable JAVA_OPTS en la configuración de App Service. 


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