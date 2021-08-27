---
title: Configuración del control de eventos de gemelo a gemelo
titleSuffix: Azure Digital Twins
description: Vea cómo crear una función de Azure para propagar eventos por medio del grafo de Twins.
author: baanders
ms.author: baanders
ms.date: 8/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 68229c648968711bf65c0870c2fb38903376b1d6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252584"
---
# <a name="set-up-twin-to-twin-event-handling-with-azure-functions"></a>Configuración del control de eventos de gemelo a gemelo con Azure Functions

Un grafo de Azure Digital Twins totalmente conectado se controla por medio de la propagación de eventos. Los datos llegan a Azure Digital Twins desde orígenes externos como IoT Hub y luego se propagan por medio del grafo de Azure Digital Twins, con lo que se actualizan los gemelos pertinentes según corresponda.

Por ejemplo, imagine un gráfico que representa las plantas y las salas de un edificio, donde cada planta contiene varias salas. Es posible que quiera configurar un flujo de datos de gemelo a gemelo de modo que cada vez que se actualice la propiedad de temperatura de un gemelo de sala, se calcule una nueva temperatura media para todas las salas de la misma planta, y la propiedad de temperatura del gemelo de planta se actualice para reflejar la nueva temperatura media en todas las salas que contiene (incluida la que se ha actualizado). 

En este artículo va a ver cómo se envían eventos de gemelo a gemelo, lo que permite actualizar gemelos como respuesta a cambios de propiedades u otros datos de otro gemelo del grafo. Actualmente, las actualizaciones de gemelo a gemelo se controlan mediante la configuración de una [función de Azure](../azure-functions/functions-overview.md) que busca eventos del ciclo de vida de los gemelos que debieran afectar a otras áreas del grafo y realiza cambios en otros gemelos en consecuencia.

## <a name="prerequisites"></a>Requisitos previos

En este artículo se usa **Visual Studio**. Puede descargar la versión más reciente de [Descargas de Visual Studio](https://visualstudio.microsoft.com/downloads/).

Para configurar el control de gemelo a gemelo, necesita una **instancia de Azure Digital Twins** con la que trabajar. Para obtener instrucciones para crear una instancia, vea [Configuración de una instancia de Azure Digital Twins y autenticación](./how-to-set-up-instance-portal.md). La instancia debe contener al menos **dos gemelos** entre los que quiera enviar datos.

Opcionalmente, puede que además quiera configurar [ingesta de telemetría automática mediante IoT Hub](how-to-ingest-iot-hub-data.md) para los gemelos. No es necesario para enviar datos de gemelo a gemelo, pero es una parte importante de una solución completa en la que el grafo de Twins esté controlado por telemetría activa.

## <a name="set-up-endpoint-and-route"></a>Configuración del punto de conexión y la ruta

Para configurar el control de eventos de gemelo a gemelo, primero cree un **punto de conexión** en Azure Digital Twins y una **ruta** a ese punto de conexión. Los gemelos sometidos a una actualización van a usar la ruta para enviar información sobre sus eventos de actualización al punto de conexión (donde Event Grid puede seleccionarlos más adelante y pasarlos a una función de Azure para su procesamiento).

[!INCLUDE [digital-twins-twin-to-twin-resources.md](../../includes/digital-twins-twin-to-twin-resources.md)]

## <a name="create-the-azure-function"></a>Creación de la función de Azure

Luego, cree una función de Azure que escuche en el punto de conexión y reciba eventos gemelos que se envíen allí a través de la ruta. 

1. En primer lugar, cree un proyecto de Azure Functions en Visual Studio en la máquina. Para obtener instrucciones sobre cómo hacerlo, vea [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project).

2. Agregue los siguientes paquetes al proyecto (puede usar el administrador de paquetes NuGet de Visual Studio o comandos `dotnet` en una herramienta de línea de comandos).

    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid)

3. Rellene la lógica de la función. Puede ver código de función de ejemplo de varios escenarios en el repositorio [azure-digital-twins-getting-started](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/azure-functions) como ayuda para empezar a trabajar.

5. Publique la aplicación de funciones en Azure. Para obtener instrucciones sobre cómo publicar una aplicación de funciones, vea [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

### <a name="configure-the-function-app"></a>Configuración de la Function App

Para que la función pueda acceder a Azure Digital Twins, necesita cierta información sobre la instancia y permiso para acceder a ella. En esta sección va a **asignar un rol de acceso** para la función y a **establecer la configuración de la aplicación** para que pueda encontrar la instancia y acceder a ella.

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="connect-the-function-to-event-grid"></a>Conexión de la función a Event Grid

Luego, suscriba la función de Azure al tema de Event Grid que ha creado anteriormente. Esto garantiza que los datos puedan fluir desde un gemelo actualizado a través del tema de Event Grid a la función.

Para ello, va a crear una **suscripción a Event Grid** que envíe datos desde el tema de Event Grid creado anteriormente a la función de Azure.

Use el siguiente comando de la CLI, rellenando los marcadores de posición del identificador de suscripción, el grupo de recursos, la aplicación de funciones y el nombre de la función.

```azurecli-interactive
az eventgrid event-subscription create --name <name-for-your-event-subscription> --source-resource-id /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.EventGrid/topics/<your-event-grid-topic> \ --endpoint-type azurefunction --endpoint /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.Web/sites/<your-function-app-name>/functions/<function-name> 
```

Ahora, la función puede recibir eventos a través del tema de Event Grid. La configuración del flujo de datos está terminada.

## <a name="test-and-verify-results"></a>Prueba y comprobación de resultados

El último paso es comprobar que el flujo funciona; para ello, actualice un gemelo y compruebe que los gemelos relacionados se actualizan conforme a la lógica de la función de Azure.

Para iniciar el proceso, actualice el gemelo que es el origen del flujo de eventos. Puede usar la [CLI de Azure](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update), el [SDK de Azure Digital Twins](how-to-manage-twin.md#update-a-digital-twin) o [API de REST de Azure Digital Twins](how-to-use-postman.md?tabs=data-plane) para efectuar la actualización.

Luego, consulte la instancia de Azure Digital Twins del gemelo relacionado. Puede usar la [CLI de Azure](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) o [API de REST o el SDK de Azure Digital Twins](how-to-query-graph.md#run-queries-with-the-api). Compruebe que el gemelo ha recibido los datos y se ha actualizado según lo previsto.

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha configurado el control de eventos de gemelo a gemelo en Azure Digital Twins. Luego, configure una función de Azure para desencadenar este flujo automáticamente en función de la telemetría entrante de dispositivos IoT Hub: [Ingesta de telemetría desde IoT Hub](how-to-ingest-iot-hub-data.md).
