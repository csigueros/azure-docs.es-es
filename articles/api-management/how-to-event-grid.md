---
title: Envío de eventos de Azure API Management a Event Grid
description: En este inicio rápido se habilitan eventos de Event Grid para la instancia de Azure API Management y luego se envían esos eventos a una aplicación de ejemplo.
author: dlepow
ms.topic: how-to
ms.service: api-management
ms.author: danlep
ms.date: 07/12/2021
ms.custom: ''
ms.openlocfilehash: 9e81fbe26c8b98a0694789567cef9126d5ca02fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747796"
---
# <a name="send-events-from-api-management-to-event-grid-preview"></a>Envío de eventos de API Management a Event Grid (versión preliminar)

API Management se integra con [Azure Event Grid](../event-grid/overview.md) para poder enviar notificaciones de eventos a otros servicios y desencadenar procesos de descarga. Event Grid es un servicio de enrutamiento de eventos totalmente administrado que usa un modelo de publicación-suscripción. Event Grid tiene compatibilidad integrada para servicios de Azure, como [Azure Functions](../azure-functions/functions-overview.md) y [Azure Logic Apps](../logic-apps/logic-apps-overview.md), y puede proporcionar alertas de eventos a los servicios que no son de Azure mediante webhooks.

Por ejemplo, mediante la integración con Event Grid, puede compilar una aplicación que actualice una base de datos, cree una cuenta de facturación y envíe una notificación por correo electrónico cada vez que se agrega un usuario a la instancia de API Management.

En este artículo se va a suscribir a eventos de Event Grid en la instancia de API Management, va a desencadenar eventos y va a enviar los eventos a un punto de conexión que procesa los datos. Para que sea sencillo, los eventos se van a enviar a una aplicación web de ejemplo que recopila y muestra los mensajes:

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-intro.png" alt-text="Eventos de API Management en el visor de Event Grid":::

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
- Si aún no tiene una instancia del servicio API Management, realice el inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
- Habilite una [identidad administrada asignada por el sistema](api-management-howto-use-managed-service-identity.md#create-a-system-assigned-managed-identity) en la instancia de API Management.
- Cree un [grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) si no tiene ninguno en el que implementar el punto de conexión de ejemplo.

## <a name="create-an-event-endpoint"></a>Creación de un punto de conexión de evento

En esta sección va a usar una plantilla de Resource Manager para implementar una aplicación web de ejemplo compilada previamente en Azure App Service. Posteriormente se va a suscribir a los eventos de Event Grid de la instancia de API Management y va a especificar esta aplicación como punto de conexión al que se van a enviar los eventos.

Para implementar la aplicación de ejemplo puede usar la CLI de Azure, Azure PowerShell o Azure Portal. En el ejemplo siguiente se usa el comando [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) de la CLI de Azure.

* Establezca `RESOURCE_GROUP_NAME` en el nombre de un grupo de recursos existente.
* Establezca `SITE_NAME` en un nombre único para la aplicación web.

  El nombre del sitio debe ser único dentro de Azure porque forma parte del nombre de dominio completo (FQDN) de la aplicación web. En una sección posterior va a ir al FQDN de la aplicación en un explorador web para ver los eventos.

```azurecli-interactive
RESOURCE_GROUP_NAME=<your-resource-group-name>
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Una vez que haya realizado correctamente la implementación (podría tardar unos minutos), abra un explorador y vaya a la aplicación web para asegurarse de que se está ejecutando:

`https://<your-site-name>.azurewebsites.net`

Debería ver la aplicación de ejemplo representada sin ningún mensaje de evento.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-api-management-events"></a>Suscripción a eventos de API Management

En Event Grid, suscríbase a un *tema* para indicarle a qué eventos desea realizar un seguimiento y adónde enviarlos. Aquí va a crear una suscripción a eventos en la instancia de API Management.

1. Vaya a la instancia de API Management en [Azure Portal](https://portal.azure.com).
1. Seleccione **Eventos (versión preliminar) > + Suscripción a eventos**. 
1. En la pestaña **Básico**:
    * Escriba un **Nombre** descriptivo para la suscripción a eventos.
    * En **Tipos de eventos**, seleccione uno o varios tipos de eventos de API Management para enviar a Event Grid. Para el ejemplo de este artículo, seleccione al menos **Microsoft.APIManagement.ProductCreated**. 
    * En **Detalles del punto de conexión**, seleccione el tipo de evento **Web Hook**, haga clic en **Seleccionar un punto de conexión** y escriba la dirección URL de la aplicación web seguida de `api/updates`. Ejemplo: `https://myapp.azurewebsites.net/api/updates`.
    * Seleccione **Confirmar selección**.
1. Deje la configuración de las pestañas restantes en sus valores predeterminados y seleccione **Crear**.

    :::image type="content" source="media/how-to-event-grid/create-event-subscription.png" alt-text="Creación de una suscripción a eventos en Azure Portal":::

## <a name="trigger-and-view-events"></a>Desencadenamiento y visualización de eventos

Ahora que la aplicación de ejemplo está en funcionamiento y que se ha suscrito a la instancia de API Management con Event Grid, está listo para generar eventos.

Como ejemplo, [cree un producto](./api-management-howto-add-products.md) en la instancia de API Management. Si la suscripción a eventos incluye el evento **Microsoft.APIManagement.ProductCreated**, la creación del producto desencadena un evento que se inserta en el punto de conexión de la aplicación web. 

Vaya a la aplicación web Visor de Event Grid para ver el evento `ProductCreated`. Seleccione el botón situado junto al evento para mostrar los detalles. 

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-product-created.png" alt-text="Evento creado por un producto en el visor de Event Grid":::

## <a name="event-grid-event-schema"></a>Esquema de eventos de Event Grid

Los datos de eventos de API Management incluyen `resourceUri`, que identifica el recurso de API Management que ha desencadenado el evento. Para obtener detalles sobre el esquema de mensajes de eventos de API Management, vea la documentación de Event Grid:

[Esquema de eventos de Azure Event Grid para API Management](../event-grid/event-schema-api-management.md)

## <a name="next-steps"></a>Pasos siguientes

* [Elija entre los servicios de mensajería de Azure: Event Grid, Event Hubs y Service Bus](../event-grid/compare-messaging-services.md)
* Obtenga más información sobre la [suscripción a eventos](../event-grid/subscribe-through-portal.md).