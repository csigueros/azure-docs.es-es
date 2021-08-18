---
title: Creación de un grupo de Azure Batch sin direcciones IP públicas
description: Aprenda a crear un grupo sin direcciones IP públicas
author: pkshultz
ms.topic: how-to
ms.date: 12/9/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 22c9163b0b8e809fba3c870393c03dd7c0d3c194
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113433767"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>Creación de un grupo de Azure Batch sin direcciones IP públicas

Al crear un grupo de Azure Batch, puede aprovisionar el grupo de configuración de máquina virtual sin una dirección IP pública. En este artículo se explica cómo configurar un grupo de Batch sin direcciones IP públicas.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>¿Por qué usar un grupo sin direcciones IP públicas?

De forma predeterminada, todos los nodos de ejecución de un grupo de configuración de máquina virtual de Azure Batch tienen asignada una dirección IP pública. El servicio Batch usa esta dirección para programar tareas y para la comunicación con los nodos de ejecución, incluido el acceso de salida a Internet.

Para restringir el acceso a estos nodos y reducir la detectabilidad de estos nodos desde Internet, puede aprovisionar el grupo sin direcciones IP públicas.

> [!IMPORTANT]
> La compatibilidad con grupos sin direcciones IP públicas en Azure Batch se encuentra actualmente en versión preliminar pública para las siguientes regiones: Centro de Francia, Este de Asia, Centro-oeste de EE. UU., Centro-sur de EE. UU., Oeste de EE. UU. 2, Este de EE. UU., Norte de Europa, Este de EE. UU. 2, Centro de EE. UU., Oeste de Europa, Centro-norte de EE. UU., Oeste de EE. UU., Este de Australia, Este de Japón y Oeste de Japón.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Requisitos previos

- **Autenticación**. Para usar un grupo sin direcciones IP públicas dentro de una [red virtual](./batch-virtual-network.md), la API de cliente de Batch debe usar la autenticación de Azure Active Directory (AD). La compatibilidad de Azure Batch con Azure AD se documenta en [Autenticación de soluciones de servicio de Batch con Active Directory](batch-aad-auth.md). Si no está creando el grupo dentro de una red virtual, se puede usar la autenticación de Azure AD o la autenticación basada en claves.

- **Una red virtual de Azure**. Si va a crear el grupo en una [red virtual](batch-virtual-network.md), siga estos requisitos y configuraciones. Para preparar una red virtual con una o varias subredes previamente, puede usar Azure Portal, Azure PowerShell, la interfaz de línea de comandos (CLI) de Azure u otros métodos.
  - La red virtual debe estar en la misma región y suscripción que la cuenta de Batch que se utiliza para crear el grupo.
  - La subred especificada para el grupo debe tener suficientes direcciones IP sin asignar para acoger el número de VM destinadas al grupo; esto es, la suma de las propiedades `targetDedicatedNodes` y `targetLowPriorityNodes` del grupo. Si la subred no tiene suficientes direcciones IP sin asignar, el grupo asigna parcialmente los nodos de proceso y se produce un error de cambio de tamaño.
  - Debe deshabilitar las directivas de red de punto de conexión y el servicio de vínculo privado. Esto se puede hacer mediante la CLI de Azure: ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resource-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> Para cada 100 nodos dedicados o de baja prioridad, Batch asigna un servicio de vínculo privado y un equilibrador de carga. Estos recursos están limitados por las [cuotas de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md) de la suscripción. En el caso de los grupos grandes, es posible que deba [solicitar un aumento de la cuota](batch-quota-limit.md#increase-a-quota) de uno o varios de estos recursos. Además, no se deben aplicar bloqueos de recursos a ningún recurso creado por Batch, ya que esto impide la limpieza de recursos como consecuencia de las acciones iniciadas por el usuario, como la eliminación de un grupo o el cambio de tamaño a cero.

## <a name="current-limitations"></a>Limitaciones actuales

1. Los grupos sin direcciones IP públicas deben usar la configuración de máquina virtual y no la configuración de Cloud Services.
1. La [configuración de punto de conexión personalizado](pool-endpoint-configuration.md) de los nodos de ejecución de Batch no funciona con los grupos sin direcciones IP públicas.
1. Como no hay ninguna dirección IP pública, no puede [usar sus propias direcciones IP públicas especificadas](create-pool-public-ip.md) con este tipo de grupo.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Creación de un grupo sin direcciones IP públicas en Azure Portal

1. Vaya a la cuenta de Batch en Azure Portal.
1. En la ventana **Configuración** que aparece a la izquierda, seleccione **Grupos**.
1. En la ventana **Grupos**, seleccione **Agregar**.
1. En la ventana **Agregar grupo**, seleccione la opción que desea utilizar en el menú desplegable **Tipo de imagen**.
1. Seleccione **el editor/la oferta/la SKU** correspondiente de la imagen.
1. Especifique los valores requeridos restantes, incluido el **tamaño de nodo**, los **nodos dedicados de destino** y los **nodos de baja prioridad**, además de cualquier otro valor opcional que desee.
1. Opcionalmente, seleccione una red virtual y una subred que quiera usar. Esta red virtual debe estar en el mismo grupo de recursos que el grupo que está creando.
1. En **Tipo de aprovisionamiento de dirección IP**, seleccione **NoPublicIPAddresses**.

![Captura de pantalla de la pantalla Agregar grupo con la opción NoPublicIPAddresses seleccionada.](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>Uso de la API REST de Batch para crear un grupo sin direcciones IP públicas

En el ejemplo siguiente se muestra cómo usar la [API REST de Azure Batch](/rest/api/batchservice/pool/add) para crear un grupo que use direcciones IP públicas.

### <a name="rest-api-uri"></a>URI DE LA API REST

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>Cuerpo de la solicitud

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>Acceso de salida a Internet

En un grupo sin direcciones IP públicas, las máquinas virtuales no podrán acceder a Internet a menos que establezca la configuración de red correctamente, por ejemplo, mediante el uso de [Virtual Network NAT](../virtual-network/nat-gateway/nat-overview.md). Tenga en cuenta que NAT solo permite el acceso de salida a Internet desde las máquinas virtuales de la red virtual. Los nodos de ejecución creados por Batch no serán accesibles públicamente, ya que no tienen asociadas direcciones IP públicas.

Otra manera de proporcionar conectividad de salida es usar una ruta definida por el usuario (UDR). Esto permite enrutar el tráfico a un equipo proxy que tenga acceso a Internet.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo [crear grupos en una red virtual](batch-virtual-network.md).
- Aprenda a [usar puntos de conexión privados con cuentas de Batch](private-connectivity.md).
