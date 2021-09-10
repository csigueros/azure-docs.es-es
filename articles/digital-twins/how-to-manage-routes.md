---
title: Administración de puntos de conexión y rutas
titleSuffix: Azure Digital Twins
description: Consulte cómo configurar y administrar puntos de conexión y rutas de eventos para datos de Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 7/30/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: dd63a04616848acfb3971a97f8363498e6ba4e55
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835747"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Administración de puntos de conexión y rutas en Azure Digital Twins

En Azure Digital Twins, se pueden enrutar [notificaciones de eventos](concepts-event-notifications.md) a los servicios de bajada o recursos de proceso conectados. Para ello, primero se configuran los **puntos de conexión** que pueden recibir los eventos. Después, puede crear [rutas de eventos](concepts-route-events.md) que especifican qué eventos generados por Azure Digital Twins se entregan a cada punto de conexión.

Este artículo le guiará a través del proceso de creación de puntos de conexión y rutas utilizando [Azure Portal](https://portal.azure.com), las [API de REST](/rest/api/azure-digitaltwins/), el [SDK de .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) y la [CLI de Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true).

## <a name="prerequisites"></a>Requisitos previos

* Necesitará una **cuenta de Azure**, que [se puede configurar de forma gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Necesitará una **instancia de Azure Digital Twins** en su suscripción de Azure. Si no dispone de una instancia, puede crear una con los pasos de [Configuración de una instancia y autenticación](how-to-set-up-instance-portal.md). Tenga a mano los siguientes valores de configuración para usarlos más adelante en este artículo:
    - Nombre de instancia
    - Grupo de recursos

Estos detalles se pueden encontrar en [Azure Portal](https://portal.azure.com) después de configurar la instancia. Inicie sesión en el portal y busque el nombre de la instancia en la barra de búsqueda del portal.
 
:::image type="content" source="media/how-to-manage-routes/search-field-portal.png" alt-text="Captura de pantalla de la barra de búsqueda de Azure Portal." lightbox="media/how-to-manage-routes/search-field-portal.png":::

En los resultados, seleccione la instancia para ver su página Información general:

:::image type="content" source="media/how-to-manage-routes/instance-details.png" alt-text="Captura de pantalla de la página Información general de una instancia de Azure Digital Twins en Azure Portal. El nombre y el grupo de recursos están resaltados.":::

Siga las instrucciones que se indican a continuación si piensa usar la CLI de Azure mientras sigue esta guía.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Creación de un punto de conexión para Azure Digital Twins

Estos son los tipos de puntos de conexión admitidos que puede crear para la instancia:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Para obtener más información sobre los distintos tipos de puntos de conexión, vea [Elección entre los distintos servicios de mensajería de Azure](../event-grid/compare-messaging-services.md).

En esta sección se explica cómo crear estos puntos de conexión mediante [Azure portal](https://portal.azure.com) o la [CLI de Azure](/cli/azure/dt/endpoint?view=azure-cli-latest&preserve-view=true). También puede administrar los puntos de conexión con las [API del plano de control DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints).

### <a name="prerequisite-create-endpoint-resources"></a>Requisito previo: Creación de recursos de punto de conexión

Para vincular un punto de conexión a Azure Digital Twins, es necesario que ya exista el tema de Event Grid, el centro de eventos o el tema de Service Bus que se use para el punto de conexión.

Utilice el gráfico siguiente para ver qué recursos se deben configurar antes de crear el punto de conexión.

| Tipo de punto de conexión | Recursos necesarios (vinculados a instrucciones de creación) |
| --- | --- |
| Punto de conexión de Event Grid | [Tema de Event Grid](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Punto de conexión de Event Hubs | [Espacio de nombres de&nbsp;Event&nbsp;Hubs](../event-hubs/event-hubs-create.md)<br/><br/>[centro de eventos](../event-hubs/event-hubs-create.md)<br/><br/>(Opcional) [regla de autorización](../event-hubs/authorize-access-shared-access-signature.md) para la autenticación basada en claves | 
| Extremo del bus de servicio | [Espacio de nombres de Service Bus](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Tema de Service Bus](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (Opcional) [regla de autorización](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) para la autenticación basada en claves|

### <a name="create-the-endpoint"></a>Creación del punto de conexión 

Una vez que haya creado los recursos de punto de conexión, puede usarlos para un punto de conexión de Azure Digital Twins. 

# <a name="portal"></a>[Portal](#tab/portal) 

Para crear un punto de conexión, vaya a la página de la instancia en [Azure Portal](https://portal.azure.com) (para buscar la instancia, escriba su nombre en la barra de búsqueda del portal).

1. En el menú de la instancia, seleccione _Endpoints_ (Puntos de conexión). Luego, en la página *Endpoints* (Puntos de conexión) que le sigue, seleccione *+ Create an endpoint* (+ Crear un punto de conexión). Se abrirá la página *Crear un punto de conexión*, donde rellenará los campos en los pasos siguientes.

    :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-grid.png" alt-text="Captura de pantalla de la creación de un punto de conexión de tipo Event Grid en Azure Portal." lightbox="media/how-to-manage-routes/create-endpoint-event-grid.png":::

1. Escriba un **Nombre** para el punto de conexión y elija el **Tipo de punto de conexión**.

1. Complete los demás detalles necesarios para el tipo de punto de conexión, incluida la suscripción y los recursos del punto de conexión descritos [antes](#prerequisite-create-endpoint-resources).
    1. Solo para los puntos de conexión de centro de eventos y Service Bus, debe seleccionar un **Tipo de autenticación**. Puede usar la autenticación basada en claves con una regla de autorización creada previamente, o bien la autenticación basada en identidades si va a utilizar el punto de conexión con una [identidad administrada](concepts-security.md#managed-identity-for-accessing-other-resources-preview) para la instancia de Azure Digital Twins. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="Captura de pantalla de la creación de un punto de conexión de tipo Event Hubs en Azure Portal." lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Seleccione _Guardar_ para terminar de crear el punto de conexión.

>[!IMPORTANT]
> Para usar correctamente la autenticación basada en identidades para el punto de conexión, tendrá que crear una identidad administrada para la instancia mediante los pasos descritos en [Eventos de rutas con una identidad administrada](how-to-route-with-managed-identity.md).

Después de crear el punto de conexión, puede comprobar que se ha creado correctamente si examina el icono de notificación de la barra superior de Azure Portal: 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-notifications.png" alt-text="Captura de pantalla de la notificación para comprobar la creación de un punto de conexión en Azure Portal.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Si se produce un error en la creación del punto de conexión, observe el mensaje de error y vuelva a intentarlo al cabo de unos minutos.

También se puede ver el punto de conexión que se ha creado de vuelta en la página *Endpoints* (Puntos de conexión) de la instancia de Azure Digital Twins.

Ahora, Event Grid, el centro de eventos o el tema de Service Bus está disponible como punto de conexión dentro de Azure Digital Twins, con el nombre que haya elegido para el punto de conexión. Normalmente se usará ese nombre como destino de una **ruta de eventos**, que se creará [más tarde en este artículo](#create-an-event-route).

# <a name="cli"></a>[CLI](#tab/cli) 

En los siguientes ejemplos se muestra cómo crear puntos de conexión con el comando [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) para la [CLI de Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true). Reemplace los marcadores de posición de los comandos con los detalles de los recursos propios.

Para crear un punto de conexión de Event Grid:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Para crear un punto de conexión de Event Hubs (autenticación basada en claves):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Para crear un punto de conexión de tema de Service Bus (autenticación basada en claves):
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Después de ejecutar correctamente estos comandos, Event Grid, el centro de eventos o el tema de Service Bus estará disponible como un punto de conexión dentro de Azure Digital Twins, con el nombre que haya proporcionado con el argumento `--endpoint-name`. Normalmente se usará ese nombre como destino de una **ruta de eventos**, que se creará [más tarde en este artículo](#create-an-event-route).

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Creación de un punto de conexión con autenticación basada en identidades

También puede crear un punto de conexión que tenga autenticación basada en identidades, para usarlo con una [identidad administrada](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Esta opción solo está disponible para los puntos de conexión de centro de eventos y de tipo Service Bus (no se admite para Event Grid).

A continuación se muestra el comando de la CLI para crear este tipo de punto de conexión. Necesitará los valores siguientes para conectarse a los marcadores de posición del comando:
* el id. de recurso de Azure de la instancia de Azure Digital Twins
* un nombre de punto de conexión
* un tipo de punto de conexión
* el espacio de nombres del recurso de punto de conexión
* el nombre del centro de eventos o del tema de Service Bus
* la ubicación de la instancia de Azure Digital Twins

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

---

### <a name="create-an-endpoint-with-dead-lettering"></a>Creación de un punto de conexión con colas de mensajes fallidos

Cuando un punto de conexión no puede entregar un evento en un período de tiempo determinado o después de haber intentado entregarlo un número determinado de veces, podrá enviar el evento sin entregar a una cuenta de almacenamiento. Este proceso se conoce como **colas de mensajes fallidos**.

Puede configurar los recursos de almacenamiento necesarios utilizando [Azure Portal](https://ms.portal.azure.com/#home) o la [CLI de Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true). Sin embargo, para crear un punto de conexión con colas de mensajes fallidos habilitadas, deberá usar la [CLI de Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) o las [API de plano de control](concepts-apis-sdks.md#overview-control-plane-apis).

Para obtener más información acerca de la opción para poner en cola los mensajes fallidos, consulte [Rutas de eventos](concepts-route-events.md#dead-letter-events). Para obtener instrucciones sobre cómo configurar un punto de conexión con mensajes fallidos, continúe con el resto de esta sección.

#### <a name="set-up-storage-resources"></a>Configuración de recursos de almacenamiento

Antes de establecer la ubicación de mensajes fallidos, debe tener una [cuenta de almacenamiento](../storage/common/storage-account-create.md?tabs=azure-portal) con un [contenedor](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) configurado en la cuenta de Azure. 

Cuando cree el punto de conexión más tarde, proporcionará el URI de este contenedor. La ubicación de la cola de mensajes fallidos se proporcionará al punto de conexión como un URI de contenedor con un [token de SAS](../storage/common/storage-sas-overview.md). Ese token necesita permiso `write` para el contenedor de destino dentro de la cuenta de almacenamiento. El **URI de SAS de cola de mensajes fallidos** totalmente estructurado tendrá el formato `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>`.

Siga los pasos que se indican a continuación para configurar estos recursos de almacenamiento en su cuenta de Azure, con el fin de preparar la configuración de la conexión del punto de conexión en la siguiente sección.

1. Siga los pasos descritos en [Creación de una cuenta de Storage](../storage/common/storage-account-create.md?tabs=azure-portal) para crear una **cuenta de almacenamiento** en la suscripción de Azure. Anote el nombre de la cuenta de almacenamiento para usarlo más adelante.
1. Siga los pasos descritos en [Creación de un contenedor de blobs de Azure](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) para crear un **contenedor** en la nueva cuenta de almacenamiento. Anote el nombre de contenedor para usarlo más adelante.

##### <a name="create-a-sas-token"></a>Creación de un token de SAS

A continuación, cree un **token de SAS** para la cuenta de almacenamiento que el punto de conexión pueda usar para acceder a ella.

# <a name="portal"></a>[Portal](#tab/portal)

1. Para empezar, vaya a la cuenta de almacenamiento en [Azure Portal](https://ms.portal.azure.com/#home) (puede buscarla por su nombre en la barra de búsqueda del portal).
1. En la página de la cuenta de almacenamiento, elija el vínculo _Firma de acceso compartido_ en la barra de navegación izquierda para empezar a configurar el token de SAS.

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-1.png" alt-text="Captura de pantalla de la página de la cuenta de almacenamiento en Azure Portal." lightbox="./media/how-to-manage-routes/generate-sas-token-1.png":::

1. En la página *Firma de acceso compartido*, en *Servicios permitidos* y *Tipos de recursos permitidos*, seleccione la configuración que quiera. Es preciso que seleccione al menos una casilla en cada categoría. En *Permisos permitidos*, elija **Escritura** (también puede seleccionar otros permisos si lo desea).
1. Establezca los valores que quiera para el resto de la configuración.
1. Cuando finalice, seleccione el botón _Generar SAS y cadena de conexión_ para generar el token de SAS. 

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-2.png" alt-text="Captura de pantalla de la página de la cuenta de almacenamiento en Azure Portal que muestra la selección de todos los valores para generar un token de SAS." lightbox="./media/how-to-manage-routes/generate-sas-token-2.png"::: 

1. Se generarán varios valores de cadena de conexión y SAS en la parte inferior de la misma página, debajo de las selecciones de configuración. Desplácese hacia abajo para ver los valores y use el icono *Copiar al portapapeles* para copiar el valor del **token de SAS**. Guárdela para usarla más adelante.

    :::image type="content" source="./media/how-to-manage-routes/copy-sas-token.png" alt-text="Captura de pantalla de la página de la cuenta de almacenamiento de Azure Portal que destaca cómo copiar el token de SAS para usarlo en el secreto de mensajes fallidos." lightbox="./media/how-to-manage-routes/copy-sas-token.png":::

# <a name="cli"></a>[CLI](#tab/cli)

1. Recupere las claves de la cuenta de almacenamiento con el siguiente comando y copie el valor de una de las claves:

    ```azurecli
    az storage account keys list --account-name <storage-account-name>
    ```

1. Seleccione una fecha de expiración y genere el token de SAS para la cuenta de almacenamiento mediante el siguiente comando:

    ```azurecli
    az storage account generate-sas --account-name <storage-account-name> --account-key <storage-account-key> --expiry <expiration-date> --services bfqt --resource-types o --permissions w
    ```

    La salida de este comando es el token de SAS. Copie el valor del token de SAS para usarlo más adelante.

    > [!NOTE]
    > Este comando incluye los *servicios* "**b** lob", "**f** ile", "**q** ueue", y "**t** able"; un *tipo de recurso* "**o** bject"; y permite *permisos* "**w** rite".
    >
    > Para más información acerca del comando `az storage account generate-sas` y sus parámetros, consulte la [referencia de la CLI de Azure](/cli/azure/storage/account?view=azure-cli-latest&preserve-view=true#az_storage_account_generate_sas).

---

#### <a name="create-the-dead-letter-endpoint"></a>Creación del punto de conexión de cola de mensajes fallidos

# <a name="portal"></a>[Portal](#tab/portal)

Para crear un punto de conexión con las colas de mensajes fallidos habilitadas, debe usar los [comandos de la CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) o las [API del plano de control](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) en lugar de Azure Portal para crear el punto de conexión.

Para obtener instrucciones sobre cómo hacerlo con el CLI de Azure, cambie a la pestaña CLI de esta sección.

# <a name="cli"></a>[CLI](#tab/cli)

Para crear un punto de conexión que tenga habilitada la cola de mensajes fallidos, agregue el siguiente parámetro de cola de mensajes fallidos al comando [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) para la [CLI de Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true).

El valor del parámetro es el **URI de SAS de cola de mensajes fallidos** formado por el nombre de la cuenta de almacenamiento, el nombre del contenedor y el token de SAS que ha recopilado en la [sección anterior](#set-up-storage-resources). Este parámetro crea el punto de conexión con la autenticación basada en claves.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Agregue este parámetro al final de los comandos de creación del punto de conexión desde la sección [Crear el punto de conexión](#create-the-endpoint) anterior para crear un punto de conexión del tipo deseado que tenga habilitadas la cola de mensajes fallidos.

Como alternativa, puede crear puntos de conexión de cola de mensajes fallidos mediante las [API de plano de control de Azure Digital Twins](concepts-apis-sdks.md#overview-control-plane-apis) en lugar de la CLI. Para ello, consulte la [documentación de DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) para ver cómo estructurar la solicitud y agregar los parámetros de cola de mensajes fallidos.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Creación de un punto de conexión de cola de mensajes fallidos con autenticación basada en identidades

También puede crear un punto de conexión de cola de mensajes fallidos que tenga autenticación basada en identidades, para usarlo con una [identidad administrada](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Esta opción solo está disponible para los puntos de conexión de centro de eventos y de tipo Service Bus (no se admite para Event Grid).

Para crear este tipo de punto de conexión, use el mismo comando de la CLI anterior para [crear un punto de conexión con autenticación basada en identidades](#create-an-endpoint-with-identity-based-authentication), con un campo adicional en la carga de JSON para un elemento `deadLetterUri`.

Estos son los valores que necesitará para conectarse a los marcadores de posición del comando:
* el id. de recurso de Azure de la instancia de Azure Digital Twins
* un nombre de punto de conexión
* un tipo de punto de conexión
* el espacio de nombres del recurso de punto de conexión
* el nombre del centro de eventos o del tema de Service Bus
* detalles del **URI de SAS de cola de mensajes fallidos**: nombre de la cuenta de almacenamiento, nombre de contenedor
* la ubicación de la instancia de Azure Digital Twins

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

---

#### <a name="message-storage-schema"></a>Esquema de almacenamiento de mensajes

Una vez configurado el punto de conexión con mensajes fallidos, los mensajes fallidos que están en cola se almacenarán con el formato siguiente en la cuenta de almacenamiento:

`<container>/<endpoint-name>/<year>/<month>/<day>/<hour>/<event-ID>.json`

Los mensajes fallidos en cola coinciden con el esquema del evento original que se diseñó para entregarse al punto de conexión original.

A continuación se muestra un ejemplo de un mensaje fallido en cola de una [notificación de creación gemela](concepts-event-notifications.md#digital-twin-lifecycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Crear una ruta de eventos

Para enviar datos de Azure Digital Twins a un punto de conexión, debe definir una **ruta de eventos**. Estas rutas permiten a los desarrolladores conectar el flujo de eventos a lo largo del sistema y a los servicios de nivel inferior. Una ruta puede permitir que se seleccionen varias notificaciones y tipos de eventos. Obtenga más información sobre las rutas de eventos en [Enrutamiento de eventos de Azure Digital Twins](concepts-route-events.md).

**Requisito previo**: Debe crear puntos de conexión como se describió anteriormente en este artículo para poder continuar con la creación de una ruta. Puede continuar con la creación de una ruta de evento una vez finalizada la configuración de los puntos de conexión.

>[!NOTE]
>Si ha implementado recientemente los puntos de conexión, compruebe que se haya completado la implementación **antes** de intentar usarlos para una nueva ruta de evento. Si se produce un error en la implementación de la ruta porque los puntos de conexión no están listos, espere unos minutos y vuelva a intentarlo.
>
> Si crea scripts de este flujo, puede que le interese dejar entre 2 y 3 minutos de tiempo de espera para que el servicio del punto de conexión termine de implementarse antes de pasar a la configuración de la ruta.

Una definición de ruta puede contener estos elementos:
* El nombre de ruta que quiere usar.
* Nombre del punto de conexión que quiere usar.
* Filtro que define los eventos que se envían al punto de conexión.
    - Para deshabilitar la ruta de modo que no se envíe ningún evento, use un valor de filtro de `false`.
    - Para habilitar una ruta que no tenga filtrado específico, use un valor de filtro de `true`.
    - Para más información sobre cualquier otro tipo de filtro, consulte la sección [Eventos de filtro](#filter-events) a continuación.

Si no hay ningún nombre de ruta, no se enruta ningún mensaje fuera de Azure Digital Twins. Si hay un nombre de ruta y el filtro es `true`, todos los mensajes se enrutan al punto de conexión. Si hay un nombre de ruta y se agrega un filtro diferente, los mensajes se filtrarán de acuerdo con este.

Las rutas de eventos pueden crearse con [Azure Portal](https://portal.azure.com), las [API del plano de datos de EventRoutes](/rest/api/digital-twins/dataplane/eventroutes) o los [comandos deaz dt route de la CLI](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true). En el resto de esta sección se le guía a través del proceso de creación.

# <a name="portal"></a>[Portal](#tab/portal2)

Para crear una ruta de eventos, vaya a la página de detalles de la instancia de Azure Digital Twins en [Azure Portal](https://portal.azure.com) (para buscar la instancia, escriba su nombre en la barra de búsqueda del portal).

En el menú de la instancia, seleccione _Event routes_ (Rutas de eventos). Luego, en la página *Event routes* (Rutas de eventos) que le sigue, seleccione *+ + Create an event route* (+ Crear una ruta de evento). 

En la página *Create an event route* (Crear una ruta de evento) que se abre, elija como mínimo:
* Un nombre para la ruta en el campo _Name_ (Nombre).
* El valor de _Endpoint_ (Punto de conexión) que quiere usar para crear la ruta. 

Para que la ruta esté habilitada, también se debe **agregar un filtro de ruta de evento** de al menos `true`. (Si se deja el valor predeterminado de `false`, se creará la ruta, pero no se le enviarán eventos). Para ello, cambie el botón de conmutación de _Advanced editor_ (Editor avanzado) para habilitarlo y escriba `true` en el cuadro *Filter* (Filtro).

:::image type="content" source="media/how-to-manage-routes/create-event-route-no-filter.png" alt-text="Captura de pantalla de la creación de una ruta de eventos para una instancia en Azure Portal." lightbox="media/how-to-manage-routes/create-event-route-no-filter.png":::

Cuando termine, seleccione el botón _Save_ (Guardar) para crear la ruta de eventos.

# <a name="cli"></a>[CLI](#tab/cli2)

Las rutas se pueden administrar mediante los comandos [az dt route](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true) de la CLI de Azure Digital Twins. 

Para obtener más información sobre el uso de la CLI y los comandos disponibles, vea [Conjunto de comandos de la CLI de Azure Digital Twins](concepts-cli.md).

# <a name="net-sdk"></a>[SDK de .NET](#tab/sdk2)

En esta sección se muestra cómo crear una ruta de eventos mediante el [SDK de .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

`CreateOrReplaceEventRouteAsync` es la llamada de SDK que se usa para agregar una ruta de evento. A continuación se muestra un ejemplo del uso:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Todas las funciones del SDK cuentan con versiones sincrónicas y asincrónicas.

#### <a name="event-route-sample-sdk-code"></a>Código del SDK de ejemplo de ruta de eventos

En el método de ejemplo siguiente se muestra cómo crear, enumerar y eliminar una ruta de eventos con el SDK de C#:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

---

## <a name="filter-events"></a>Filtrado de eventos

Tal y como se ha descrito anteriormente, las rutas tienen un campo **Filter** (Filtro). Si el valor de filtro de la ruta es `false`, no se enviará ningún evento al punto de conexión. 

Después de habilitar el filtro mínimo de `true`, los puntos de conexión recibirán una variedad de eventos de Azure Digital Twins:
* Telemetría que desencadena [Digital Twins](concepts-twins-graph.md) mediante la API del servicio Azure Digital Twins.
* Notificaciones relativas a cambios de propiedades de gemelos, desencadenadas en los cambios de una propiedad de cualquier gemelo en la instancia de Azure Digital Twins.
* Eventos de ciclo de vida, desencadenados cuando se crean o se eliminan gemelos o relaciones.

Se pueden restringir los tipos de eventos que se envían mediante la definición de un filtro más específico.

>[!NOTE]
> Los filtros **distinguen mayúsculas de minúsculas** y deben coincidir con el caso de la carga útil. 
>
> Para los filtros de telemetría, esto significa que el uso de mayúsculas debe coincidir con el de la telemetría enviada por el dispositivo, y no necesariamente con el uso de mayúsculas definido en el modelo del gemelo.

# <a name="portal"></a>[Portal](#tab/portal3)

Para agregar un filtro de eventos mientras se crea una ruta de eventos, use la sección "Agregar un filtro de ruta de eventos" de la página *Creación de una ruta de eventos*. 

Puede seleccionar entre algunas opciones básicas de filtros comunes o usar las opciones avanzadas de filtro para escribir sus propios filtros personalizados.

### <a name="use-the-basic-filters"></a>Uso de los filtros básicos

Para usar los filtros básicos, expanda la opción _Event types_ (Tipos de evento) y seleccione las casillas correspondientes a los eventos que quiere enviar al punto de conexión. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-1.png" alt-text="Captura de pantalla de la creación de una ruta de eventos con un filtro básico en Azure Portal, se resaltan las casillas de los eventos.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

El cuadro de texto de filtro se rellena automáticamente con el texto del filtro que ha seleccionado:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-2.png" alt-text="Captura de pantalla de la creación de una ruta de eventos con un filtro básico en Azure Portal, se resalta el texto del filtro rellenado automáticamente después de seleccionar los eventos.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="use-the-advanced-filters"></a>Uso de los filtros avanzados

También puede usar la opción de filtro avanzado para escribir sus propios filtros personalizados.

Para crear una ruta de evento con opciones avanzadas de filtro, cambie el botón de conmutación de _Advanced editor_ (Editor avanzado) para habilitarlo. Después, puede escribir sus propios filtros de eventos en el cuadro *Filter* (Filtro):

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-advanced.png" alt-text="Captura de pantalla de la creación de una ruta de evento con un filtro avanzado en Azure Portal.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

# <a name="api"></a>[API](#tab/api)

Puede usar las API para escribir filtros personalizados. Para agregar un filtro, puede usar una solicitud PUT a `https://<Your-Azure-Digital-Twins-host-name>/eventRoutes/<event-route-name>?api-version=2020-10-31` con el siguiente cuerpo:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

---

### <a name="supported-route-filters"></a>Filtros de ruta admitidos

Estos son los filtros de ruta admitidos.

| Nombre de filtro | Descripción | Esquema de texto de filtro | Valores admitidos | 
| --- | --- | --- | --- |
| True o False | Permite crear una ruta sin filtrado o deshabilitar una ruta para que no se envíen eventos. | `<true/false>` | `true`: la ruta está habilitada sin filtrado. <br> `false`: la ruta está deshabilitada. |
| Tipo | [Tipo de evento](concepts-route-events.md#types-of-event-messages) que fluye a través de la instancia de Digital Twins. | `type = '<event-type>'` | Estos son los posibles valores de tipo de evento: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Source | Nombre de la instancia de Azure Digital Twins. | `source = '<host-name>'`| Estos son los posibles valores de nombre de host: <br> **Para notificaciones**: `<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net` <br> **Para telemetría**: `<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net/<twin-ID>`|
| Asunto | Descripción del evento en el contexto del origen del evento anterior. | `subject = '<subject>'` | Estos son los posibles valores para el asunto: <br>**Para notificaciones**: el asunto es `<twin-ID>` <br> o un formato de URI para los asuntos, que se identifican de forma única mediante varias partes o id.:<br>`<twin-ID>/relationships/<relationship-ID>`<br> **Para telemetría**: el asunto es la ruta de acceso del componente (si la telemetría se emite desde un componente gemelo), como `comp1.comp2`. Si no se emite la telemetría desde un componente, el campo de asunto está vacío. |
| Esquema de datos | Id. del modelo de DTDL. | `dataschema = '<model-dtmi-ID>'` | **Para telemetría**: el esquema de datos es el identificador del modelo del gemelo o el componente que emite la telemetría. Por ejemplo: `dtmi:example:com:floor4;2` <br>**Para notificaciones (crear o eliminar)** : se puede acceder al esquema de datos en el cuerpo de la notificación en `$body.$metadata.$model`. <br>**Para notificaciones (actualizar)** : Se puede acceder al esquema de datos en el cuerpo de la notificación en `$body.modelId`|
| Tipo de contenido | Tipo de contenido del valor de datos. | `datacontenttype = '<content-type>'` | El tipo de contenido es `application/json`. |
| Versión de especificación | Versión del esquema de evento que se usa. | `specversion = '<version>'` | La versión debe ser `1.0`. Esto indica la versión de esquema de CloudEvents es la 1.0. |
| Cuerpo de la notificación | Referencia a cualquier propiedad en el campo `data` de una notificación | `$body.<property>` | Para ver ejemplos de notificaciones, consulte [Notificaciones de eventos](concepts-event-notifications.md). Se puede hacer referencia a cualquier propiedad del campo `data` mediante `$body`

>[!NOTE]
> Actualmente, Azure Digital Twins no permite filtrar eventos basados en campos dentro de una matriz, lo que incluye filtrar por propiedades en una sección `patch` de una [notificación de cambio de gemelo digital](concepts-event-notifications.md#digital-twin-change-notifications).

Los siguientes tipos de datos se admiten como valores devueltos por las referencias a los datos anteriores:

| Tipo de datos | Ejemplo |
|-|-|-|
|**String**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twin-ID>')`|
|**Entero**|`$body.errorCode > 200`|
|**Double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

Se admiten los siguientes operadores al definir filtros de ruta:

|Familia|Operadores|Ejemplo|
|-|-|-|
|Lógicos|Y, O, ( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|De comparación|<, <=, >, >=, =, !=|`$body.temperature <= 5.5`

Se admiten las siguientes funciones al definir filtros de ruta:

|Función|Descripción|Ejemplo|
|--|--|--|
|STARTS_WITH(x,y)|Devuelve true si el valor `x` comienza con la cadena `y`.|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x,y) | Devuelve true si el valor `x` termina con la cadena `y`.|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| Devuelve true si el valor `x` contiene la cadena `y`.|`CONTAINS(subject, '<twin-ID>')`|

Al implementar o actualizar un filtro, puede que el cambio tarde unos minutos en reflejarse en la canalización de datos.

## <a name="monitor-event-routes"></a>Supervisión de rutas de eventos

Las métricas de enrutamiento, como el recuento, la latencia y la tasa de errores, se pueden ver en [Azure Portal](https://portal.azure.com/). 

En la página principal del portal, busque la instancia de Azure Digital Twins para ver sus detalles. Seleccione la opción **Métricas** en el menú de navegación de la instancia de Azure Digital Twins para abrir la página *Métricas*.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Captura de pantalla que muestra la página de métricas de Azure Digital Twins":::

Desde aquí, puede ver las métricas de la instancia y crear vistas personalizadas.

Para obtener más información sobre cómo ver las métricas de Azure Digital Twins, consulte [Visualización de métricas con Azure Monitor](troubleshoot-metrics.md).

## <a name="next-steps"></a>Pasos siguientes

Lea acerca de los diferentes tipos de mensajes de evento que se pueden recibir:
* [Notificaciones de eventos](concepts-event-notifications.md)