---
author: baanders
description: Archivo de inclusión que describe cómo configurar un punto de conexión y una ruta de Event Grid
ms.service: digital-twins
ms.topic: include
ms.date: 7/21/2021
ms.author: baanders
ms.openlocfilehash: 0311f53845a4ee18bf595d9efe2593d486818fb8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748567"
---
### <a name="create-the-event-grid-topic"></a>Creación del tema de Event Grid

[Event Grid](../articles/event-grid/overview.md) es un servicio de Azure que ayuda a enrutar y enviar eventos de Azure Services a otros lugares de Azure. Puede crear un [tema de Event Grid](../articles/event-grid/concepts.md) para recopilar determinados eventos de un origen y, después, los suscriptores pueden escuchar el tema para recibir los eventos a medida que llegan.

En Azure Cloud Shell, ejecute el comando siguiente para crear un tema de Event Grid:

```azurecli-interactive
az eventgrid topic create --resource-group <your-resource-group> --name <name-for-your-event-grid-topic> --location <region>
```

La salida de este comando es información sobre el tema de Event Grid que ha creado. Guarde el **nombre** que proporcionó al tema de Event Grid, ya que lo usará más adelante.

### <a name="create-the-endpoint"></a>Creación del punto de conexión

A continuación, cree en Azure Digital Twins un punto de conexión de Event Grid que conecte su instancia a su tema de Event Grid. Use el comando siguiente, rellenando el nombre del tema de Event Grid y los demás campos de marcador de posición según sea necesario.

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

La salida de este comando es información sobre el punto de conexión que ha creado.

Busque el campo `provisioningState` en la salida y compruebe que el valor es "Succeeded" (Correcto).

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/output-endpoints.png" alt-text="Captura de pantalla con el resultado de la consulta al punto de conexión en la instancia de Cloud Shell de Azure Portal que muestra dicho punto de conexión con un valor de Succeeded para el estado provisioningState.":::

El valor también puede ser "Provisioning" (En aprovisionamiento), lo que significa que el punto de conexión todavía se está creando. Si es así, espere unos segundos y ejecute el siguiente comando para comprobar el estado del punto de conexión. Repita hasta que `provisioningState` sea "Succeeded" (Correcto).

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Guarde el **nombre** del punto de conexión, ya que lo usará más adelante.

### <a name="create-the-route"></a>Creación de la ruta

A continuación, cree una ruta de Azure Digital Twins que envíe eventos al punto de conexión de Event Grid que acaba de crear. 

Esto puede hacerse con el siguiente comando de la CLI (rellene el nombre del punto de conexión y los demás campos de marcador de posición según sea necesario). Este comando reenvía todos los eventos que se producen en el grafo de gemelos. Para limitar los eventos a solo los específicos si lo desea, use [filtros](../articles/digital-twins/how-to-manage-routes.md?tabs=portal%2Cportal2%2Cportal3#filter-events).

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

La salida de este comando es información sobre la ruta que ha creado.

>[!NOTE]
>Los puntos de conexión (del paso anterior) deben finalizar el aprovisionamiento antes de poder configurar una ruta de eventos que los use. Si se produce un error en la creación de la ruta porque los puntos de conexión no están listos, espere unos minutos y vuelva a intentarlo.