---
title: Traslado de un equilibrador de carga externo de Azure a otra región de Azure mediante Azure Portal
description: Use una plantilla de Azure Resource Manager para trasladar un equilibrador de carga externo de Azure de una región de Azure a otra mediante Azure Portal.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: da37816b68e9984fdf801e3e84d50a57bf290e96
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273559"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Traslado de un equilibrador de carga externo a otra región mediante Azure Portal

Hay varios escenarios en los que quizá quiera trasladar un equilibrador de carga externo de una región a otra. Por ejemplo, puede que quiera crear otro equilibrador de carga externo con la misma configuración para realizar pruebas. También es posible que quiera trasladar un equilibrador de carga externo a otra región como parte del planeamiento para la recuperación ante desastres.

En un sentido literal, no se puede trasladar un equilibrador de carga externo de Azure de una región a otra. Aunque se puede usar una plantilla de Azure Resource Manager para exportar la configuración y dirección IP pública actuales de un equilibrador de carga externo. Después, puede preparar el recurso para otra región al exportar el equilibrador de carga y la dirección IP pública a una plantilla, modificar los parámetros para que coincidan con la región de destino y, a continuación, implementar la plantilla en la nueva región. Para más información sobre Resource Manager y sus plantillas, consulte [Exportación de grupos de recursos a plantillas](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Requisitos previos

- Asegúrese de que el equilibrador de carga externo de Azure se encuentra en la región de Azure desde la que va a realizar el traslado.

- Los equilibradores de carga externos de Azure no se pueden trasladar entre regiones. Tendrá que asociar el nuevo equilibrador de carga a los recursos de la región de destino.

- Deberá tener asignado el rol de colaborador de red u otro superior para exportar la configuración de un equilibrador de carga externo e implementar una plantilla para crear un equilibrador de carga externo en otra región.

- Identifique el diseño de red de origen y todos los recursos que está usando actualmente. Este diseño incluye, entre otros, equilibradores de carga, grupos de seguridad de red (NSG), direcciones IP públicas y redes virtuales.

- Compruebe que la suscripción a Azure permite crear equilibradores de carga externos en la región de destino. Para habilitar la cuota necesaria, póngase en contacto con el soporte técnico.

- Asegúrese de que la suscripción tiene suficientes recursos para admitir la adición de equilibradores de carga. Vea [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

## <a name="prepare-and-move"></a>Preparación y traslado
En los procedimientos siguientes se muestra cómo preparar el equilibrador de carga externo para el traslado mediante una plantilla de Resource Manager y cómo trasladar la configuración del equilibrador de carga externo a la región de destino mediante Azure Portal. En primer lugar, debe exportar la configuración de IP pública del equilibrador de carga externo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Exportación de la plantilla de la dirección IP pública e implementación desde el portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y después seleccione **Grupos de recursos**.
2. Busque el grupo de recursos que contiene la dirección IP pública de origen y selecciónelo.
3. Seleccione **Configuración** > **Exportar plantilla**.
4. Seleccione **Implementar** en **Exportar plantilla**.
5. Seleccione **PLANTILLA** > **Editar parámetros** para abrir el archivo parameters.json en el editor en línea.
8. Para editar el parámetro del nombre de la dirección IP pública, cambie la propiedad **value** en **parameters** del nombre de la dirección IP pública de origen al nombre de la dirección IP pública de destino. Escriba el nombre entre comillas.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Seleccione **Guardar** en el editor.

9.  Seleccione **PLANTILLA** > **Editar plantilla** para abrir el archivo template.json en el editor en línea.

10. Para editar la región de destino a la que se va a trasladar la dirección IP pública, cambie la propiedad **location** en **resources**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    Para obtener los códigos de ubicación de la región, consulte [Ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/). El código de una región es el nombre de la región sin espacios. Por ejemplo, el código de Centro de EE. UU. es **centralus**.
    
12. También puede cambiar otros parámetros de la plantilla si quiere o tiene que hacerlo, según sus requisitos:

    * **SKU**. Puede cambiar la SKU de la dirección IP pública de la configuración de estándar a básica o viceversa modificando la propiedad **name** en **sku** en el archivo template.json:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Para obtener información sobre las diferencias entre las IP públicas de la SKU básica y estándar, consulte [Creación, modificación o eliminación de una dirección IP pública](../virtual-network/virtual-network-public-ip-address.md).

    * **Método de asignación de IP pública** y **Tiempo de espera de inactividad**. Puede cambiar el método de asignación de IP pública cambiando la propiedad **publicIPAllocationMethod** de **Dynamic** a **Static** o de **Static** a **Dynamic**. Puede cambiar el tiempo de espera de inactividad cambiando la propiedad **idleTimeoutInMinutes** al valor deseado. El valor predeterminado es **4**.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Para obtener información sobre los métodos de asignación y los valores de tiempo de espera de inactividad, consulte [Creación, modificación o eliminación de una dirección IP pública](../virtual-network/virtual-network-public-ip-address.md).

 
13. Seleccione **Guardar** en el editor en línea.

14. Seleccione **ASPECTOS BÁSICOS** > **Suscripción** para elegir la suscripción donde se implementará la dirección IP pública de destino.

15. Seleccione **ASPECTOS BÁSICOS** > **Grupo de recursos** para elegir el grupo de recursos donde se implementará la dirección IP pública de destino. Puede seleccionar **Crear nuevo** para crear un grupo de recursos para la dirección IP pública de destino. Asegúrese de que el nombre no sea el mismo que el del grupo de recursos de origen de la dirección IP pública de origen existente.

16. Compruebe que **ASPECTOS BÁSICOS** > **Ubicación** está establecido en la ubicación de destino en la que quiere que se implemente la dirección IP pública.

17. En **CONFIGURACIÓN**, compruebe que el nombre coincide con el nombre que especificó anteriormente en el editor de parámetros.

18. Active la casilla **TÉRMINOS Y CONDICIONES**.

19. Seleccione **Comprar** para implementar la dirección IP pública de destino.

20. Si tiene otra dirección IP pública que se usa como NAT de salida del equilibrador de carga que se traslada, repita los pasos anteriores para exportar e implementar la segunda dirección IP pública de salida en la región de destino.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Exportación de la plantilla del equilibrador de carga externo e implementación del equilibrador de carga desde Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y después seleccione **Grupos de recursos**.
2. Busque el grupo de recursos que contiene el equilibrador de carga externo de origen y selecciónelo.
3. Seleccione **Configuración** > **Exportar plantilla**.
4. Seleccione **Implementar** en **Exportar plantilla**.
5. Seleccione **PLANTILLA** > **Editar parámetros** para abrir el archivo parameters.json en el editor en línea.

5. Para modificar el parámetro del nombre del equilibrador de carga externo, cambie la propiedad **value** del nombre del equilibrador de carga externo de origen por el nombre del equilibrador de carga externo de destino. Escriba el nombre entre comillas.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Para modificar el valor de la dirección IP pública de destino que trasladó en los pasos anteriores, debe obtener primero el identificador de recurso y luego pegarlo en el archivo parameters.json. Para obtener el identificador realice lo siguiente:

    1. En otra pestaña o ventana del explorador, inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione **Grupos de recursos**.
    2. Busque el grupo de recursos de destino que contiene la IP pública que trasladó en los pasos anteriores. Selecciónelo.
    3. Haga clic en **Configuración** > **Propiedades**.
    4. En la hoja de la derecha, resalte **Id. de recurso** y cópielo en el Portapapeles. También puede seleccionar **Copiar al Portapapeles** a la derecha de la ruta de acceso del **Id. de recurso**.
    5. Pegue el identificador de recurso en la propiedad **value** del editor **Editar parámetros** que está abierto en la otra ventana o pestaña del explorador:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Seleccione **Guardar** en el editor en línea.


7.  Si ha configurado reglas de salida y NAT de salida para el equilibrador de carga, verá una tercera entrada en este archivo para el identificador externo de la IP pública de salida. Repita los pasos anteriores en la región **target** para obtener el identificador de la dirección IP pública de salida. Péguelo en el archivo parameters.json:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Seleccione **PLANTILLA** > **Editar plantilla** para abrir el archivo template.json en el editor en línea.
9.  Para editar la región de destino a la que se va a trasladar la configuración del equilibrador de carga externo, cambie la propiedad **location** en **resources** del archivo template.json:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Para obtener los códigos de ubicación de la región, consulte [Ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/). El código de una región es el nombre de la región sin espacios. Por ejemplo, el código de Centro de EE. UU. es **centralus**.

11. También puede cambiar otros parámetros de la plantilla si quiere o tiene que hacerlo, según sus requisitos:

    * **SKU**. Puede cambiar la SKU del equilibrador de carga externo de la configuración de estándar a básica o viceversa modificando la propiedad **name** en **sku** en el archivo template.json:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Para obtener información sobre las diferencias entre los equilibradores de carga de la SKU básica y estándar, consulte [Introducción a Azure Standard Load Balancer](./load-balancer-overview.md).

    * **Reglas de equilibrio de carga**. Puede agregar o quitar reglas de equilibrio de carga de la configuración agregando o quitando entradas en la sección **loadBalancingRules** del archivo template.json:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Para obtener información sobre las reglas de equilibrio de carga, consulte [¿Qué es Azure Load Balancer?](load-balancer-overview.md)

    * **Sondeos**. Puede agregar o quitar sondeos del equilibrador de carga en la configuración agregando o quitando entradas en la sección **probes** del archivo template.json:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Para más información, consulte [Sondeos de estado de Load Balancer](load-balancer-custom-probe-overview.md).

    * **Reglas NAT de entrada**. Puede agregar o quitar reglas NAT de entrada del equilibrador de carga agregando o quitando entradas en la sección **inboundNatRules** del archivo template.json:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Para completar la adición o eliminación de una regla NAT de entrada, esta debe agregarse o quitarse como una propiedad **type** al final del archivo template.json:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Para obtener información sobre las reglas NAT de entrada, consulte [¿Qué es Azure Load Balancer?](load-balancer-overview.md)

    * **Reglas de salida**. Puede agregar o quitar reglas de salida de la configuración editando la propiedad **outboundRules** en el archivo template.json:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Para más información, consulte [Reglas de salida de Load Balancer](./load-balancer-outbound-connections.md#outboundrules).

12. Seleccione **Guardar** en el editor en línea.

13. Seleccione **ASPECTOS BÁSICOS** > **Suscripción** para elegir la suscripción en la que se implementará el equilibrador de carga externo de destino.

15. Seleccione **ASPECTOS BÁSICOS** > **Grupo de recursos** para elegir el grupo de recursos en el que se implementará el equilibrador de carga de destino. Puede seleccionar **Crear nuevo** para crear un grupo de recursos para el equilibrador de carga externo de destino. También puede elegir el grupo de recursos existente que creó anteriormente para la dirección IP pública. Asegúrese de que el nombre no es el mismo que el del grupo de recursos de origen del equilibrador de carga externo de origen existente.

16. Compruebe que **ASPECTOS BÁSICOS** > **Ubicación** está establecido en la ubicación de destino en la que quiere implementar el equilibrador de carga externo.

17. En **CONFIGURACIÓN**, compruebe que el nombre coincide con el nombre que especificó anteriormente en el editor de parámetros. Compruebe que los identificadores de recursos estén especificados para todas las direcciones IP públicas en la configuración.

18. Active la casilla **TÉRMINOS Y CONDICIONES**.

19. Seleccione **Comprar** para implementar la dirección IP pública de destino.

## <a name="discard"></a>Discard (Descartar)

Si quiere descartar la dirección IP pública de destino y el equilibrador de carga externo, elimine el grupo de recursos que los contiene. Para ello, en el portal, seleccione el grupo de recursos en el panel y, luego, **Eliminar** en la parte superior de la página de información general.

## <a name="clean-up"></a>Limpieza

Para confirmar los cambios y completar el traslado de la dirección IP pública y el equilibrador de carga externo, elimine la dirección IP pública de origen y el equilibrador de carga externo o el grupo de recursos. Para ello, en el portal, seleccione ese grupo de recursos en el panel y luego seleccione **Eliminar** en la parte superior de cada página.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha migrado un equilibrador de carga externo de Azure de una región a otra y ha limpiado los recursos de origen. Para más información sobre el traslado de recursos entre regiones y la recuperación ante desastres en Azure, consulte:


- [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Traslado de máquinas virtuales de Azure a otra región](../site-recovery/azure-to-azure-tutorial-migrate.md)