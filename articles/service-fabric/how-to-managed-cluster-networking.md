---
title: Configuración de la red para clústeres administrados de Service Fabric
description: Información sobre cómo configurar el clúster administrado de Service Fabric para las reglas de grupos de seguridad de red, el acceso a puertos RDP, las reglas de equilibrio de carga, etc.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 0299118a7715a566cccc0dd1fb7bc83aa9c5e06c
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545667"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters"></a>Configuración de la red para clústeres administrados de Service Fabric

Los clústeres administrados de Service Fabric se crean con una configuración de red predeterminada. Esta configuración consta de una instancia de [Azure Load Balancer](../load-balancer/load-balancer-overview.md) con una dirección IP pública, una red virtual con una subred asignada y un grupo de seguridad de red configurado para la funcionalidad esencial del clúster. También hay aplicadas reglas de grupos de seguridad de red opcionales (como, por ejemplo, permitir todo el tráfico saliente de forma predeterminada) pensadas para facilitar la configuración del cliente. En este documento se explica cómo modificar las siguientes opciones de configuración de red, entre otras cosas:

- [Administración de reglas de grupos de seguridad de red](#nsgrules)
- [Administración del acceso RDP](#rdp)
- [Administración de la configuración del equilibrador de carga](#lbconfig)
- [Habilitación de IPv6](#ipv6)
- [Traer su propia red virtual](#byovnet)
- [Traer su propio equilibrador de carga](#byolb)

<a id="nsgrules"></a>
## <a name="manage-nsg-rules"></a>Administración de reglas de grupos de seguridad de red

### <a name="nsg-rules-guidance"></a>Orientaciones sobre las reglas del grupo de seguridad de red

Tenga en cuenta estas consideraciones al crear nuevas reglas del grupo de seguridad de red para el clúster administrado.

* Los clústeres administrados de Service Fabric reservan el intervalo de prioridad de la regla de grupo de seguridad de red del 0 al 999 para la funcionalidad esencial. No se pueden crear reglas de grupos de seguridad de red personalizadas con un valor de prioridad inferior a 1000.
* Los clústeres administrados de Service Fabric reservan el intervalo de prioridad de 3001 a 4000 para crear reglas de grupo de seguridad de red opcionales. Estas reglas se agregan automáticamente para que las configuraciones sean rápidas y sencillas. Para invalidar estas reglas puede agregar reglas de grupo de seguridad de red personalizadas en el intervalo de prioridad de 1000 a 3000.
* Las reglas de grupo de seguridad de red personalizadas deben tener una prioridad en el intervalo de 1000 a 3000.

### <a name="apply-nsg-rules"></a>Aplicación de las reglas de grupo de seguridad de red
Los clústeres administrados de Service Fabric le permiten asignar reglas de grupo de seguridad de red directamente en el recurso de clúster de la plantilla de implementación.

Use la propiedad [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) del recurso *Microsoft.ServiceFabric/managedclusters* (versión `2021-05-01` o posterior) para asignar reglas de grupo de seguridad de red. Por ejemplo:

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "properties": {
    "networkSecurityRules": [
      {
        "name": "AllowCustomers",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33000-33499",
        "access": "Allow",
        "priority": 2001,
        "direction": "Inbound"
      },
      {
        "name": "AllowARM",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "AzureResourceManager",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33500-33699",
        "access": "Allow",
        "priority": 2002,
        "direction": "Inbound"
      },
      {
        "name": "DenyCustomers",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33700-33799",
        "access": "Deny",
        "priority": 2003,
        "direction": "Outbound"
      },
      {
        "name": "DenyRDP",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "destinationPortRange": "3389",
        "access": "Deny",
        "priority": 2004,
        "direction": "Inbound",
        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
      }
    ],
    "fabricSettings": [
      "..."
    ]
  }
}
```

## <a name="clientconnection-and-httpgatewayconnection-default-and-optional-rules"></a>Reglas predeterminadas y opcionales de ClientConnection y HttpGatewayConnection
### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>Regla de grupo de seguridad de red: SFMC_AllowServiceFabricGatewayToSFRP

Se agrega una regla de grupo de seguridad de red predeterminada para permitir que el proveedor de recursos de Service Fabric tenga acceso a los puertos clientConnectionPort y httpGatewayConnectionPort del clúster. Esta regla permite el acceso a los puertos a través de la etiqueta de servicio "ServiceFabric".

>[!NOTE]
>Esta regla se agrega siempre y no se puede invalidar.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayToSFRP",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.",
        "protocol": "TCP",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "ServiceFabric",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 500,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>Regla de grupo de seguridad de red: SFMC_AllowServiceFabricGatewayPorts

Esta regla opcional permite a los clientes acceder a SFX, conectarse al clúster mediante PowerShell y usar puntos de conexión de API de clúster de Service Fabric desde Internet abriendo los puertos LB para clientConnectionPort y httpGatewayPort.

>[!NOTE]
>Esta regla no se agregará si hay una regla personalizada con los mismos valores de acceso, dirección y protocolo para el mismo puerto. Puede invalidar esta regla con reglas de grupo de seguridad de red personalizadas.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3001,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

<a id="rdp"></a>
## <a name="enable-access-to-rdp-ports-from-internet"></a>Permitir el acceso a los puertos RDP desde Internet

Los clústeres administrados de Service Fabric no permiten el acceso entrante a los puertos RDP desde Internet de forma predeterminada. Para abrir el acceso entrante a los puertos RDP desde Internet, establezca la siguiente propiedad en un recurso de clúster administrado de Service Fabric.

```json
"allowRDPAccess": true
```

Si la propiedad allowRDPAccess está establecida en true, se agregará la siguiente regla de grupo de seguridad de red a la implementación del clúster.

```json
{
    "name": "SFMC_AllowRdpPort",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

Los clústeres administrados de Service Fabric crean automáticamente reglas NAT de entrada para cada instancia de un tipo de nodo. Para buscar las asignaciones de puertos para llegar a instancias específicas (nodos de clúster), siga estos pasos:

Use Azure Portal para buscar el clúster administrado creado por las reglas NAT de entrada para Protocolo de Escritorio remoto (RDP).

1. Vaya al grupo de recursos de clúster administrado dentro de la suscripción cuyo nombre tiene el siguiente formato: SFC_{cluster-id}

2. Seleccione el equilibrador de carga para el clúster con el siguiente formato: LB-{cluster-name}

3. En la página de su equilibrador de carga, seleccione Reglas NAT de entrada. Revise las reglas NAT de entrada para confirmar la asignación de puerto front-end de entrada a puerto de destino para un nodo. 

   La siguiente captura de pantalla muestra las reglas NAT de entrada para tres tipos de nodos:

   ![Reglas NAT de entrada][Inbound-NAT-Rules]

   De forma predeterminada, para los clústeres de Windows, la asignación del puerto de front-end empieza a 50000 y el puerto de destino es el puerto 3389, que se asigna al servicio RDP en el nodo de destino.
   >[!NOTE]
   > Si usa la característica BYOLB y desea RDP, debe configurar un grupo NAT por separado para hacerlo. Esto no creará automáticamente ninguna regla NAT para esos tipos de nodo.

4. Conéctese de forma remota al nodo específico (instancia del conjunto de escalado). Puede usar el nombre de usuario y la contraseña que estableció cuando creó el clúster u otras credenciales que haya configurado.

La siguiente captura de pantalla muestra cómo usar Conexión a Escritorio remoto para conectarse al nodo de aplicaciones (instancia 0) en un clúster Windows:

![Conexión del Escritorio remoto][sfmc-rdp-connect]

<a id="lbconfig"></a>
## <a name="modify-default-load-balancer-configuration"></a>Modificación de la configuración predeterminada del equilibrador de carga

### <a name="load-balancer-ports"></a>Puertos del equilibrador de carga

Los clústeres administrados de Service Fabric crean una regla de grupo de seguridad de red en el intervalo de prioridad predeterminado de todos los puertos del equilibrador de carga (LB) configurados en la sección "loadBalancingRules" en las propiedades de *ManagedCluster*. Esta regla abre los puertos del equilibrador de carga para el tráfico entrante desde Internet.  

>[!NOTE]
>Esta regla se agrega en el intervalo de prioridad opcional y se puede invalidar mediante la adición de reglas de grupo de seguridad de red personalizadas.

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>Sondeos del equilibrador de carga

Los clústeres administrados de Service Fabric crean automáticamente sondeos del equilibrador de carga para los puertos de Fabric Gateway y para todos los puertos configurados en la sección `loadBalancingRules` de las propiedades del clúster administrado.

```json
{
  "value": [
    {
        "name": "FabricTcpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19000,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "FabricHttpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "probe1_tcp_8080",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 8080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
            {
                "id": "<>"
            }
        ]
      },
      "type": "Microsoft.Network/loadBalancers/probes"
    }
  ]
}
```

<a id="ipv6"></a>
## <a name="enable-ipv6-preview"></a>Habilitar IPv6 (versión preliminar)
Los clústeres administrados no habilitan IPv6 de forma predeterminada. Esta característica habilitará la toda la funcionalidad IPv4/IPv6 de pila doble desde el front-end del equilibrador de carga a los recursos de back-end. Los cambios que se realicen en la configuración del equilibrador de carga del clúster administrado o en las reglas de grupos de seguridad de red afectarán al enrutamiento de IPv4 e IPv6.

> [!NOTE]
> Esta configuración no está disponible en Portal y no se puede cambiar una vez creado el clúster.

1. Establezca la siguiente propiedad en un recurso de clúster administrado de Service Fabric.
   ```json
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                "enableIpv6": true
                },
            }
   ```

2. Implemente el clúster administrado habilitado para IPv6. Personalice la [plantilla de ejemplo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-IPv6) según lo necesite o cree la suya propia.
   En el siguiente ejemplo, crearemos un grupo de recursos llamado `MyResourceGroup` en `westus` e implementaremos un clúster con esta característica habilitada.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```
   Tras la implementación, los recursos y la red virtual del clúster serán de doble pila. Como resultado, el equilibrador de carga de front-end del clúster tendrá creada una dirección DNS única, por ejemplo, `mycluster-ipv6.southcentralus.cloudapp.azure.com`, que se asocia a una dirección IPv6 pública en Azure Load Balancer y a direcciones IPv6 privadas en las máquinas virtuales. 


<a id="byovnet"></a>
## <a name="bring-your-own-virtual-network-preview"></a>Traer su propia red virtual (versión preliminar)
Esta característica permite a los clientes usar una red virtual existente, especificando para ello una subred dedicada en la que el clúster administrado implementará sus recursos. Esto puede ser útil si ya hay una red virtual y una subred configuradas con las directivas de seguridad relacionadas y el enrutamiento del tráfico que se quieren usar. Tras implementar en una red virtual existente, es fácil usar e incorporar otras características de red como Azure ExpressRoute, Azure VPN Gateway, un grupo de seguridad de red y emparejamiento de redes virtuales. También puede [traer su propia instancia de Azure Load Balancer](#byolb) si es necesario.

> [!NOTE]
> Esta configuración no se puede cambiar una vez creado el clúster, y el clúster administrado asignará un grupo de seguridad de red a la subred proporcionada. No invalide la asignación del grupo de seguridad de red o el tráfico podría interrumpirse.

**Para traer su propia red virtual:**

1. Obtenga el servicio `Id` de su suscripción de la aplicación de proveedor de recursos de Service Fabric.
   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > Asegúrese de que se encuentra en la suscripción correcta; el identificador de la entidad de seguridad cambia si la suscripción está en otro inquilino.

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   Anote el **identificador** de la salida anterior como **principalId** para usarlo en un paso posterior.

   |El nombre de la definición de roles|Id. de definición de roles|
   |----|-------------------------------------|
   |Colaborador de la red|4d97b98b-1d4f-4787-a291-c67834d212e7|

   Anote los valores de propiedad `Role definition name` y `Role definition ID` para usarlos en un paso posterior.

2. Agregue una asignación de roles a la aplicación de proveedor de recursos de Service Fabric. Agregar una asignación de roles es una acción única aislada. Para agregar el rol, ejecute los siguientes comandos de PowerShell o configure una plantilla de Azure Resource Manager (ARM) como se detalla a continuación. 

   En los siguientes pasos, vamos a empezar con una red virtual existente denominada ExistingRG-vnet en el grupo de recursos ExistingRG. La subred recibe un nombre predeterminado.

   Obtenga la información necesaria de la red virtual existente.

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzVirtualNetwork -Name ExistingRG-vnet -ResourceGroupName ExistingRG
   ```
   Anote el siguiente nombre de subred y el valor de propiedad `Id` que se devuelve en la sección `Subnets` de la respuesta para usarlos en pasos posteriores.

   ```JSON
   Subnets:[
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/virtualNetworks/ExistingRG-vnet/subnets/default"
   }]
   ```

   Ejecute el siguiente comando de PowerShell usando el identificador de entidad de seguridad, el nombre de definición de rol del paso 2 y el ámbito de asignación `Id` obtenidos anteriormente:
   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
   ```

   También puede agregar la asignación de roles usando una plantilla de Azure Resource Manager (ARM) configurada con los valores adecuados de `principalId`, `roleDefinitionId` y `vnetName``subnetName`:

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('VNetRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > VNetRoleAssignmentID debe ser un [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16). Si vuelve a implementar una plantilla que incluya esta asignación de roles, asegúrese de que el GUID es el mismo que el que usó originalmente. Se recomienda ejecutar este recurso aislado o quitarlo de la plantilla de clúster tras la implementación, ya que solo debe crearse una vez.

   Este es un ejemplo completo de una [plantilla de Azure Resource Manager (ARM) que crea una subred de red virtual y realiza una asignación de roles](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOVNET/createVNet-assign-role.json) que puede usar en este paso.

3. Configure la propiedad `subnetId` para la implementación del clúster después de configurar el rol como se muestra aquí:

   ```JSON
    "resources": [
        {
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            },
            "properties": {
                "subnetId": "subnetId",
            ...
            }
   ```
   Consulte esta [plantilla de ejemplo para traer su propio clúster de red virtual](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOVNET) o personalice la suya propia.

4. Implemente la plantilla de Azure Resource Manager (ARM) de clúster administrado que ha configurado.

   En el siguiente ejemplo, crearemos un grupo de recursos llamado `MyResourceGroup` en `westus` e implementaremos un clúster con esta característica habilitada.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   Cuando trae su propia subred de red virtual, el proveedor de recursos sigue creando y administrando el punto de conexión público, pero en la subred configurada. La característica no permite especificar la dirección IP pública ni reutilizar la dirección IP estática en Azure Load Balancer. Puede [traer su propia instancia de Azure Load Balancer](#byolb), ya sea junto con esta característica o de forma aislada, si necesita esos u otros escenarios de equilibrador de carga que no se admiten de forma nativa.

<a id="byolb"></a>
## <a name="bring-your-own-azure-load-balancer-preview"></a>Traer su propia instancia de Azure Load Balancer (versión preliminar)
Los clústeres administrados crean una instancia de Azure Load Balancer y un nombre de dominio completo con una dirección IP pública estática para los tipos de nodo principal y secundario. Esta característica permite crear o volver a usar una instancia de Azure Load Balancer para los tipos de nodo secundarios para el tráfico entrante y saliente. Si trae su propia instancia de Azure Load Balancer, puede hacer lo siguiente:

* Usar una dirección IP estática de Load Balancer configurada previamente para el tráfico público o privado
* Asignar una instancia de Load Balancer a un tipo de nodo específico
* Configure reglas de grupo de seguridad de red por tipo de nodo porque cada tipo de nodo se implementa en su propia subred con un NSG único. 
* Mantener las directivas y los controles existentes que pueda tener implementados

> [!NOTE]
> Una vez implementado el clúster de un tipo de nodo, no se puede cambiar de opción predeterminada a personalizada, pero sí se puede modificar la configuración personalizada del equilibrador de carga tras la implementación.

**Requisitos de la característica**
 * Se admiten tipos de SKU estándar y básicas de Azure Load Balancer.
 * Debe haber grupos de NAT y back-end configurados en la instancia de Azure Load Balancer existente. Vea un [ejemplo completo de cómo crear y asignar roles aquí](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOLB/createlb-and-assign-role.json). 

Estos son un par de escenarios de ejemplo en los que los clientes pueden hacer uso de esto:

En este ejemplo, un cliente quiere enrutar el tráfico a dos tipos de nodo a través de una instancia de Azure Load Balancer existente configurada con una dirección IP estática.
![Ejemplo 1 para traer su propio equilibrador de carga][sfmc-byolb-example-1]

En este ejemplo, un cliente quiere enrutar el tráfico a través de instancias de Azure Load Balancer existentes como ayuda para administrar el flujo de tráfico a sus aplicaciones que se encuentran en tipos de nodo independientes. Si elige la configuración de este ejemplo, cada tipo de nodo estará detrás de su propio grupo de seguridad de red que puede administrar.
![Ejemplo 2 para traer su propio equilibrador de carga][sfmc-byolb-example-2]

Para configurar para traer su propio equilibrador de carga:

1. Obtenga el servicio `Id` de su suscripción de la aplicación de proveedor de recursos de Service Fabric:

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > Asegúrese de que se encuentra en la suscripción correcta; el identificador de la entidad de seguridad cambia si la suscripción está en otro inquilino.

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   Anote el **identificador** de la salida anterior como **principalId** para usarlo en un paso posterior.

   |El nombre de la definición de roles|Id. de definición de roles|
   |----|-------------------------------------|
   |Colaborador de la red|4d97b98b-1d4f-4787-a291-c67834d212e7|

   Anote los valores de propiedad `Role definition name` y `Role definition ID` para usarlos en un paso posterior.

2. Agregue una asignación de roles a la aplicación de proveedor de recursos de Service Fabric. Agregar una asignación de roles es una acción única aislada. Para agregar el rol, ejecute los siguientes comandos de PowerShell o configure una plantilla de Azure Resource Manager (ARM) como se detalla a continuación.

   En los siguientes pasos, empezaremos con un equilibrador de carga existente denominado Existing-LoadBalancer1 en el grupo de recursos Existing-RG. La subred recibe un nombre predeterminado.

   Obtenga la información de la propiedad `Id` necesaria de la instancia de Azure Load Balancer existente. Haremos esto: 

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzLoadBalancer -Name "Existing-LoadBalancer1" -ResourceGroupName "Existing-RG"
   ```
   Anote el siguiente `Id`, que usaremos en el siguiente paso:
   ```JSON
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/loadBalancers/Existing-LoadBalancer1"
   }
   ```
   Ejecute el siguiente comando de PowerShell usando el identificador de entidad de seguridad, el nombre de definición de rol del paso 2 y el ámbito de asignación `Id` que acabamos de obtener:

   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/loadBalancers/<LoadBalancerName>"
   ```

   También puede agregar la asignación de roles usando una plantilla de Azure Resource Manager (ARM) configurada con los valores adecuados de `principalId`, `roleDefinitionId` y `vnetName``subnetName`:

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('loadBalancerRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > loadBalancerRoleAssignmentID debe ser un [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16). Si vuelve a implementar una plantilla que incluya esta asignación de roles, asegúrese de que el GUID es el mismo que el que usó originalmente. Se recomienda ejecutar este recurso aislado o quitarlo de la plantilla de clúster tras la implementación, ya que solo debe crearse una vez.

3. Configure la conectividad saliente necesaria. Todos los nodos deben poder enrutar la salida en el puerto 443 al proveedor de recursos de Service Fabric. Puede usar la etiqueta de servicio `ServiceFabric` en el grupo de seguridad de red para limitar el destino del tráfico al punto de conexión de Azure.

4. Opcionalmente, configure un puerto de aplicación de entrada y un sondeo relacionado en la instancia de Azure Load Balancer.

5. Opcionalmente, configure las reglas de grupo de seguridad de red del clúster administrado aplicadas al tipo de nodo para permitir cualquier tráfico necesario que se haya configurado en la instancia de Azure Load Balancer o el tráfico se bloqueará.

   Consulte la [plantilla de ejemplo para traer su propio equilibrador de carga de Azure Resource Manager (ARM)](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOLB) para ver un ejemplo sobre cómo abrir reglas de entrada.

6. Implementación de la plantilla de ARM de clúster administrado configurada

   En el siguiente ejemplo, crearemos un grupo de recursos llamado `MyResourceGroup` en `westus` e implementaremos un clúster con esta característica habilitada.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   Después de la implementación, el tipo de nodo secundario se configura para usar el equilibrador de carga especificado en el tráfico entrante y saliente. Los puntos de conexión de puerta de enlace y la conexión de cliente de Service Fabric seguirán apuntando al DNS público de la dirección IP estática del tipo de nodo principal del clúster administrado.


## <a name="next-steps"></a>Pasos siguientes
[Opciones de configuración del clúster administrado de Service Fabric](how-to-managed-cluster-configuration.md)
[Introducción a los clústeres administrados de Service Fabric](overview-managed-cluster.md)

<!--Image references-->
[Inbound-NAT-Rules]: ./media/how-to-managed-cluster-networking/inbound-nat-rules.png
[sfmc-rdp-connect]: ./media/how-to-managed-cluster-networking/sfmc-rdp-connect.png
[sfmc-byolb-example-1]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-1.png
[sfmc-byolb-example-2]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-2.png

