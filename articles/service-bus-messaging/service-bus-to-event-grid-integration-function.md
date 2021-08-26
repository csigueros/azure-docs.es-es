---
title: Uso de Azure Functions para administrar eventos de Service Bus mediante Event Grid
description: En este artículo, se proporcionan los pasos necesarios para administrar los eventos de Service Bus mediante Event Grid utilizando Azure Functions.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: fb18eb5df675690a6bb5b796349c140a61949df1
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "122207430"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Tutorial: Respuesta a eventos de Azure Service Bus recibidos mediante Azure Event Grid utilizando Azure Functions
En este tutorial, aprenderá a responder a eventos de Azure Service Bus que se reciben a través de Azure Event Grid con Azure Functions y Azure Logic Apps. 

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Creación de un espacio de nombres de Service Bus
> * Preparación de una aplicación de ejemplo para enviar mensajes
> * Envío de mensaje al tema de Service Bus
> * Recepción de mensajes mediante Logic Apps
> * Configuración de una función de prueba en Azure
> * Conexión de la función y el espacio de nombres mediante Event Grid
> * Recepción de mensajes mediante Azure Functions

[!INCLUDE [service-bus-event-grid-prerequisites](./includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>Requisitos previos adicionales
Instale [Visual Studio 2019](https://www.visualstudio.com/vs) e incluya la carga de trabajo **Desarrollo de Azure**. Esta carga de trabajo contiene las **herramientas de Azure Functions** que necesita para crear, compilar e implementar proyectos de Azure Functions en Visual Studio. 

## <a name="deploy-the-function-app"></a>Implementar la aplicación de función 

>[!NOTE]
> Para más información acerca de cómo crear e implementar una aplicación de Azure Functions, consulte [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md).

1. Abra el archivo **ReceiveMessagesOnEvent.cs** que se encuentra en el proyecto **FunctionApp1** de la solución **SBEventGridIntegration.sln**. 
1. Reemplace `<SERCICE BUS NAMESPACE - CONNECTION STRING>` por la cadena de conexión del espacio de nombres de Service Bus. Debería ser la misma que utilizó en el archivo **Program.cs** que se encuentra en el proyecto **MessageSender** de la misma solución. 
1. Haga clic con el botón derecho en **FunctionApp1** y seleccione **Publicar**. 
1. En la página **Publicar**, seleccione **Iniciar**. Es posible que estos pasos no sean iguales que los que se muestran, pero el proceso de publicación debería ser similar. 
1. En el **Asistente para publicación**, en la página **Destino**, seleccione **Azure** como **Destino**. 
1. En la página **Destino específico**, seleccione **Azure Function App (Windows)** . 
1. En la página **Functions instance** (Instancia de Functions), seleccione **Create a new Azure function** (Crear una función de Azure). 
1. En la página **Function App (Windows)** , siga estos pasos:
    1. Escriba el **nombre** de la aplicación de funciones.
    1. Seleccione una **suscripción** de Azure.
    1. Seleccione un **grupo de recursos** existente o cree uno nuevo. En este tutorial, seleccione el grupo de recursos que tiene el espacio de nombres de Service Bus. 
    1. Seleccione el **tipo de plan** de App Service.
    1. Seleccione **una ubicación**. Seleccione la misma ubicación que el espacio de nombres de Service Bus. 
    1. Seleccione un almacén de **Azure Storage** existente o haga clic en **Nuevo** para crear la cuenta de almacenamiento que desee utilizar en la aplicación de funciones. 
    1. Seleccione **Crear** para crear la aplicación de funciones. 
1. En la página **Functions instance** (Instancia de Functions) del **Asistente para publicación**, seleccione **Finalizar**. 
1. En la página **Publicar** de Visual Studio, seleccione **Publicar** para publicar la aplicación de funciones en Azure. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Publicación de la aplicación de funciones":::    
1. En la ventana **Salida**, consulte los mensajes relacionados con la creación y la publicación, y confirme que los dos procesos se han realizado correctamente. 
1. Ahora, en la página **Publicar**, seleccione **Manage in Azure portal** (Administrar en Azure Portal). 
1. En Azure Portal, en la página **Aplicación de funciones**, seleccione **Funciones** en el menú de la izquierda y confirme que puede ver dos funciones: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Dos funciones para controlar los desencadenadores Event Grid y HTTP":::

    > [!NOTE]
    > `EventGridTriggerFunction` utiliza un [desencadenador de Event Grid](../azure-functions/functions-bindings-event-grid-trigger.md), mientras que `HTTPTriggerFunction` utiliza un [desencadenador de HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md).
1. Seleccione **EventGridTriggerFunction** en la lista. Se recomienda utilizar el desencadenador de Event Grid con Azure Functions, ya que tiene algunas ventajas frente al desencadenador de HTTP. Para más información, consulte [Azure Functions como controlador de eventos para eventos de Event Grid](../event-grid/handler-functions.md).
1. En la página **Función** de **EventGridTriggerFunction**, seleccione **Supervisar** en el menú de la izquierda. 
1. Seleccione **Configurar** y configure Application Insights para que capture el registro de invocación. Utilice esta página para supervisar las ejecuciones de la función mediante los eventos de Service Bus recibidos desde Event Grid. 
1. En la página **Application Insights**, escriba el nombre del recurso, seleccione su **ubicación** y haga clic en **Aceptar**. 
1. Seleccione la función **EventGridTriggerFunction** en la parte superior (menú de la ruta de navegación) para volver a la página **Función**. 
1. Compruebe que se encuentra en la página **Supervisión**. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="Página Supervisión de la función antes de las invocaciones":::
    
    Mantenga esta página abierta en una pestaña del explorador web. Tendrá que actualizarla más adelante para ver las invocaciones de esta función.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Conexión de la función y el espacio de nombres mediante Event Grid
En esta sección va a asociar la función y el espacio de nombres de Service Bus mediante Azure Portal. 

Para crear una suscripción de Azure Event Grid, siga estos pasos:

1. En Azure Portal, vaya a su espacio de nombres y, luego, en el panel izquierdo, seleccione **Eventos**. Se abre la ventana del espacio de nombres, con dos suscripciones de Event Grid que se muestran en el panel derecho. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Service Bus: página de eventos":::
2. Seleccione **+Suscripción de eventos** en la barra de herramientas. 
3. En la página **Crear suscripción de eventos**, realice los siguientes pasos:
    1. Escriba el **nombre** de la suscripción. 
    2. Escriba un **nombre** para el **tema del sistema**. Los temas del sistema se crean para los recursos de Azure, como la cuenta de Azure Storage y Azure Service Bus. Para más información acerca de los temas del sistema, consulte [Introducción a los temas del sistema](../event-grid/system-topics.md).
    2. Seleccione **Función de Azure** en **Tipo de punto de conexión** y haga clic en **Seleccionar un extremo**. 

        ![Service Bus: suscripción a Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. En la página **Seleccionar la función de Azure**, seleccione la suscripción, el grupo de recursos, la aplicación de funciones, el espacio y la función, y haga clic en **Confirmar selección**. 

        ![Función: selección del punto de conexión](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. En la página **Crear suscripción de eventos**, cambie a la pestaña **Filtros** y realice las siguientes tareas:
        1. Seleccione **Habilitar el filtrado del asunto**.
        2. Escriba el nombre de la suscripción en el tema de Service Bus (**S1**) que creó anteriormente.
        3. Seleccione el botón **Crear**. 

            ![Filtro de suscripción a eventos](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Cambie a la pestaña **Suscripciones a eventos** de la página **Eventos** y confirme que ve la suscripción de eventos en la lista.

    ![Suscripción a eventos en la lista](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>Supervisión de la aplicación de Functions
Los mensajes que envió anteriormente al tema de Service Bus se reenvían a la suscripción (S1). Event Grid reenvía los mensajes de la suscripción a la función de Azure. En este paso del tutorial, va a comprobar si se invocó la función y consultará los mensajes informativos registrados. 

1. En la página de la aplicación de funciones de Azure, cambie a la pestaña **Supervisión** si aún no está activa. Verá una entrada para cada mensaje publicado en el tema de Service Bus. Si no las ve, actualice la página al cabo de unos minutos. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="Página Supervisión de la función después de las invocaciones":::
2. Seleccione la invocación en la lista para ver los detalles. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="Detalles de la invocación de la función" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    También puede usar la pestaña **Registros** de la página **Supervisión** para ver la información de registro a medida que se envían los mensajes. Puede que haya algo de retraso, así que espere unos minutos para ver los mensajes registrados. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="Registros de la función" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Solución de problemas
Si no ve ninguna invocación de la función después de esperar un tiempo y de actualizar, siga estos pasos: 

1. Asegúrese de que los mensajes han llegado al tema de Service Bus. Consulte el contador de **mensajes entrantes** en la página **Tema de Service Bus**. En este caso, la aplicación **MessageSender** se ejecutó dos veces, por lo que aparecerán 10 mensajes (5 mensajes por cada ejecución).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Página Tema de Service Bus: mensajes entrantes":::    
1. Confirme que **no hay mensajes activos** en la suscripción de Service Bus. 
    Si no ve ningún evento en esta página, compruebe si el valor de **Recuento de mensajes activos** es cero en la página **Service Bus Subscription** (Suscripción de Service Bus). Si es mayor que cero, por alguna razón, los mensajes de la suscripción no se están reenviando a la función del controlador (el controlador de la suscripción de eventos). Compruebe que la suscripción de eventos esté configurada correctamente. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Recuento de mensajes activos en la suscripción de Service Bus":::    
1. También puede ver los **eventos entregados** en la página **Eventos** del espacio de nombres de Service Bus. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Página Eventos: eventos entregados" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. También puede consultar si los eventos se han entregado en la página **Suscripción de eventos**. Para acceder a esta página, seleccione la suscripción de eventos en la página **Eventos**. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Página Suscripción de eventos: eventos entregados":::
    


## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [Azure Event Grid](../event-grid/index.yml).
* Más información acerca de [Azure Functions](../azure-functions/index.yml).
* Más información acerca de la [característica Logic Apps de Azure App Service](../logic-apps/index.yml).
* Aprenda más sobre [Azure Service Bus](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png