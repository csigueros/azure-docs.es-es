---
title: Protección del tráfico de salida de Azure SignalR mediante puntos de conexión privados compartidos
titleSuffix: Azure SignalR Service
description: Procedimiento para proteger el tráfico de salida mediante puntos de conexión privados compartidos para evitar que el tráfico vaya a la red pública
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 07/08/2021
ms.author: dayshen
ms.openlocfilehash: 4be77c9dce68c55a37713c42ceee7fc2b7dbf6d9
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220197"
---
# <a name="secure-azure-signalr-outbound-traffic-through-shared-private-endpoints"></a>Protección del tráfico de salida de Azure SignalR mediante puntos de conexión privados compartidos

Si usa el [modo sin servidor](concept-service-mode.md#serverless-mode) en Azure SignalR Service, es posible que tenga tráfico de salida hacia el flujo ascendente. Los elementos del flujo ascendente, como una aplicación web de Azure y Azure Functions, se pueden configurar para que acepten las conexiones de una lista de redes virtuales y rechacen las conexiones externas cuyo origen sea una red pública. Puede crear una [conexión de punto de conexión privado](../private-link/private-endpoint-overview.md) de salida para acceder a estos puntos de conexión.

   :::image type="content" alt-text="Información general del punto de conexión privado compartido." source="media\howto-shared-private-endpoints\shared-private-endpoint-overview.png" :::

Este método de salida está sujeto a los requisitos siguientes:

+ El elemento del flujo ascendente debe ser Azure Web Apps o Azure Functions.

+ El servicio Azure SignalR Service debe estar en el nivel Estándar.

+ Azure Web Apps o Azure Functions deben estar en determinadas SKU. Consulte [Uso de puntos de conexión privados para Azure Web Apps](../app-service/networking/private-endpoint.md).

## <a name="shared-private-link-resources-management-apis"></a>API de administración de recursos de vínculo privado compartido

Los puntos de conexión privados de recursos protegidos que se crean mediante las API de Azure SignalR Service se conocen como *recursos de vínculo privado compartido*. Eso se debe a que se "comparte" el acceso a un recurso, como una función de Azure, que se ha integrado en el [servicio Azure Private Link](https://azure.microsoft.com/services/private-link/). Estos puntos de conexión privados se crean dentro del entorno de ejecución de Azure SignalR Service y no son directamente visibles.

En este momento, puede usar la API REST de administración para crear o eliminar *recursos de vínculo privado compartido*. En el resto de este artículo, usaremos la [CLI de Azure](/cli/azure/) para mostrar las llamadas a la API REST.

> [!NOTE]
> Los ejemplos de este artículo se basan en los siguientes supuestos:
> * El identificador de recurso de este servicio Azure SignalR Service es _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr_.
> * El identificador de recurso de la función de Azure del flujo ascendente es _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func.

El resto de los ejemplos muestran cómo se puede configurar el servicio _contoso-signalr_ para que sus llamadas ascendentes a la función se dirijan mediante un punto de conexión privado en lugar de una red pública.

### <a name="step-1-create-a-shared-private-link-resource-to-the-function"></a>Paso 1: Crear un recurso de vínculo privado compartido a la función

Puede realizar la siguiente llamada API con la [CLI de Azure](/cli/azure/) para crear un recurso de vínculo privado compartido:

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview --body @create-pe.json
```

El contenido del archivo *create-pe.json*, que representa el cuerpo de la solicitud a la API, es el siguiente:

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve"
      }
}
```

El proceso de creación de un punto de conexión privado de salida es una operación de larga duración (asincrónica), Como en todas las operaciones asincrónicas de Azure, la llamada a `PUT` devuelve un valor de encabezado `Azure-AsyncOperation` similar al siguiente:

```plaintext
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview"
```

Este identificador URI se puede sondear periódicamente para obtener el estado de la operación.

Si usa la CLI, puede sondear el estado realizando una consulta manual del valor `Azure-AsyncOperationHeader`.

```donetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview
```

Espere hasta que el estado cambie a "Correcto" antes de continuar con los pasos siguientes.

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-function"></a>Paso 2a: Aprobar la conexión de punto de conexión privado para la función

> [!NOTE]
> En esta sección, se usa Azure Portal para recorrer el flujo de aprobación de un punto de conexión privado a la función de Azure. Como alternativa, puede usar la [API REST](/rest/api/appservice/web-apps/approve-or-reject-private-endpoint-connection) que está disponible mediante el proveedor de App Service.

> [!IMPORTANT]
> Después de aprobar la conexión de punto de conexión privado, deja de poder accederse a la función desde la red pública. Es posible que tenga que crear otros puntos de conexión privados en su propia red virtual para acceder al punto de conexión de la función.

1. En Azure Portal, seleccione la pestaña **Redes** de la aplicación de funciones y vaya a **Conexiones de punto de conexión privado**. Haga clic en **Configurar las conexiones de punto de conexión privado**. Una vez que la operación asincrónica se ha realizado correctamente, debe haber una solicitud de una conexión de punto de conexión privado con el mensaje de solicitud de la llamada API anterior.

   :::image type="content" alt-text="Captura de pantalla de Azure Portal en la que se muestra el panel &quot;Conexiones de punto de conexión privado&quot;." source="media\howto-shared-private-endpoints\portal-function-approve-private-endpoint.png" :::

1. Seleccione el punto de conexión privado que creó Azure SignalR Service. En la columna **Punto de conexión privado**, identifique la conexión de punto de conexión privado con el nombre que se especificó en la API anterior y seleccione **Aprobar**.

   Asegúrese de que la conexión del punto de conexión privado aparece como se muestra en la siguiente captura de pantalla. El estado puede tardar entre uno y dos minutos en actualizarse en el portal.

   :::image type="content" alt-text="Captura de pantalla de Azure Portal en la que se muestra el estado &quot;Aprobado&quot; en el panel &quot;Conexiones de punto de conexión privado&quot;." source="media\howto-shared-private-endpoints\portal-function-approved-private-endpoint.png" :::

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Paso 2b: Consultar el estado del recurso de vínculo privado compartido

La aprobación tarda minutos en propagarse a Azure SignalR Service. Para confirmar que el recurso de vínculo privado compartido se ha actualizado después de la aprobación, también puede obtener el "Estado de la conexión" mediante la API GET.

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview
```

Esto devolvería un elemento JSON, donde el estado de conexión se mostraría como "estado" debajo de la sección "propiedades".

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve",
        "status": "Approved",
        "provisioningState": "Succeeded"
      }
}

```

Si el valor de "Estado de aprovisionamiento" (`properties.provisioningState`) del recurso es `Succeeded` y el valor de "Estado de la conexión" (`properties.status`) es `Approved`, significa que el recurso de vínculo privado compartido es funcional y que Azure SignalR Service se puede comunicar mediante el punto de conexión privado.

### <a name="step-3-verify-upstream-calls-are-from-a-private-ip"></a>Paso 3: Comprobar que las llamadas ascendentes procedan de una dirección IP privada

Una vez configurado el punto de conexión privado, puede comprobar que las llamadas entrantes procedan de una dirección IP privada comprobando el encabezado `X-Forwarded-For` en el lado ascendente.

:::image type="content" alt-text="Captura de pantalla de Azure Portal en la que se muestra que las solicitudes entrantes proceden de una dirección IP privada." source="media\howto-shared-private-endpoints\portal-function-log.png" :::

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los puntos de conexión privados:

+ [¿Qué son los puntos de conexión privados?](../private-link/private-endpoint-overview.md)