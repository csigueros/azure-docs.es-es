---
title: Información sobre la configuración de Azure Bastion
description: Obtenga información sobre las opciones de configuración disponibles para Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: 0f73cc08b2e6e44508a6535ba0d6c420e998a011
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732033"
---
# <a name="about-bastion-configuration-settings"></a>Información sobre la configuración de Bastion

En las secciones de este artículo se habla de los recursos y la configuración de Azure Bastion.

## <a name="skus"></a><a name="skus"></a>SKU

Una SKU también se conoce como nivel. Azure Bastion admite dos tipos de SKU: básica y estándar. La SKU se configura en Azure Portal durante el flujo de trabajo al configurar Bastion. Puede [pasar de la SKU básica a la estándar](#upgradesku).

* La **SKU básica** proporciona funcionalidad base, lo que permite a Azure Bastion administrar la conectividad RDP/SSH con Virtual Machines (VM) sin exponer direcciones IP públicas en las máquinas virtuales de la aplicación de destino. 
* La **SKU estándar** se encuentra en **versión preliminar**. La SKU estándar habilita características premium que permiten a Azure Bastion administrar la conectividad remota a mayor escala. 

En la tabla siguiente se muestran las características y las SKU correspondientes. 

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

### <a name="configuration-methods"></a>Métodos de configuración

Durante la versión preliminar, debe usar el Azure Portal si desea especificar la SKU estándar. Si usa la CLI de Azure o Azure PowerShell para configurar Bastion, no se puede especificar la SKU y por defecto aparece la SKU básica.

| Método | Valor | Vínculos |
| --- | --- | --- |
| Azure portal | Nivel: Básico o <br>Standard (vista previa) | [Inicio rápido: Configuración de Bastion a partir de las opciones de máquina virtual](quickstart-host-portal.md)<br>[Tutorial: Configuración de Bastion](tutorial-create-host-portal.md) |
| Azure PowerShell | Solo básico: sin configuración |[Configuración de Bastion: PowerShell](bastion-create-host-powershell.md) |
| Azure CLI |  Solo básico: sin configuración | [Configuración de Bastion - CLI](create-host-cli.md) |

### <a name="upgrade-a-sku"></a><a name="upgradesku"></a>Actualización de un SKU

Azure Bastion admite la actualización de una SKU básica a estándar. La SKU estándar se encuentra en versión preliminar. 

> [!NOTE]
> No se admite cambiar una SKU estándar por una SKU básica. Para cambiar a una versión anterior, debe eliminar Azure Bastion y volver a crearlo.
>

#### <a name="configuration-methods"></a>Métodos de configuración

Puede configurar este valor con el método siguiente:

| Método | Valor | Vínculos |
| --- | --- | --- |
| Azure portal |Nivel  | [Actualización de una SKU: versión preliminar](upgrade-sku.md)|

## <a name="instances-and-host-scaling-preview"></a><a name="instance"></a>Escalado de instancias y host (versión preliminar)

Una instancia es una máquina virtual de Azure optimizada que se crea al configurar Azure Bastion. Azure la administra completamente y ejecuta todos los procesos necesarios para Azure Bastion. Una instancia también se conoce como unidad de escalado. Se conecta a las máquinas virtuales cliente a través de una instancia de Azure Bastion. Al configurar las Azure Bastion con la SKU básica, se crean dos instancias. Si usa la SKU estándar, puede especificar el número de instancias. Esto se denomina **escalado de host**. 

Cada instancia puede admitir entre 10 y 12 conexiones RDP/SSH simultáneas. El número de conexiones por instancia depende de las acciones que se están llevando a cabo cuando se conecta a la máquina virtual cliente. Por ejemplo, si está realizando un uso intensivo de datos, se crea una carga mayor para que la instancia la procese. Una vez superadas las sesiones simultáneas, se requiere una unidad de escalado adicional (instancia). 

Las instancias se crean en AzureBastionSubnet. Para el escalado de host, AzureBastionSubnet debe ser de al menos /26. El uso de una subred más pequeña limita el número de instancias que puede crear. Para obtener más información sobre AzureBastionSubnet, consulte la sección [subredes](#subnet) de este artículo.

### <a name="configuration-methods"></a>Métodos de configuración

Puede configurar este valor con el método siguiente:

| Método | Valor | Vínculos |
| --- | --- | --- |
| Azure portal |Recuento de instancias  | [Configuración del escalado de host: versión preliminar](configure-host-scaling.md)|


## <a name="azure-bastion-subnet"></a><a name="subnet"></a>Subred de Azure Bastion

Azure Bastion requiere una subred dedicada: **AzureBastionSubnet**. Esta subred debe crearse en la misma red virtual en la que se implementa Azure Bastion. La subred debe tener la siguiente configuración:

* La subred debe llamarse *AzureBastionSubnet*.
* El tamaño de la subred debe ser de al menos /27 (/26, /25, etc.).
* Para el escalado de host, se recomienda una subred de al menos /26. El uso de un espacio de subred más pequeño limita el número de unidades de escalado. Para más información, consulte la sección de [Escalado de host](#instance) de este artículo.
* La subred debe estar en la misma red virtual y grupo de recursos que el host bastión.
* La subred no puede contener recursos adicionales.

### <a name="configuration-methods"></a>Métodos de configuración

Puede configurar este valor con los métodos siguientes:

| Método | Valor | Vínculos |
| --- | --- |--- |
| Azure portal | Subred  |[Inicio rápido: Configuración de Bastion a partir de las opciones de máquina virtual](quickstart-host-portal.md)<br>[Tutorial: Configuración de Bastion](tutorial-create-host-portal.md)|
| Azure PowerShell | -subnetName|[cmdlet](/powershell/module/az.network/new-azbastion#parameters) |
| Azure CLI |  --subnet-name | [command](/cli/azure/network/vnet#az_network_vnet_create) |

## <a name="public-ip-address"></a><a name="public-ip"></a>Dirección IP pública

Azure Bastion requiere una dirección IP pública. La dirección IP pública debe tener la siguiente configuración:

* La SKU de la dirección IP pública debe ser **estándar**.
* El método de asignación o asignación de direcciones IP públicas debe ser **estático**.
* El nombre de la dirección IP pública es el nombre del recurso por el que desea hacer referencia a esta dirección IP pública.
* Puede optar por usar una dirección IP pública que ya ha creado, siempre que cumpla los criterios requeridos por Azure Bastion y no esté en uso.

### <a name="configuration-methods"></a>Métodos de configuración

Puede configurar este valor con los métodos siguientes:

| Método | Valor | Vínculos |
| --- | --- |--- |
| Azure portal | Dirección IP pública |[Azure Portal](https://portal.azure.com)|
| Azure PowerShell | -publicIPAddress| [cmdlet](/powershell/module/az.network/new-azbastion#parameters)  |
| Azure CLI | --public-ip create |[command](/cli/azure/network/public-ip)
|

## <a name="next-steps"></a>Pasos siguientes

Para ver las preguntas más frecuentes, consulte las [preguntas más frecuentes de Azure Bastion](bastion-faq.md).
