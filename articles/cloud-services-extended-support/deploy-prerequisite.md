---
title: Requisitos previos para la implementación de Azure Cloud Services (soporte extendido)
description: Requisitos previos para la implementación de Azure Cloud Services (soporte extendido)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f21dc3251a64a3a432320e1d7a1f64abc124b61e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752080"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Requisitos previos para la implementación de Azure Cloud Services (soporte extendido)

Para garantizar una implementación correcta de Cloud Services (soporte extendido), revise los pasos siguientes y complete cada punto antes de intentar realizar la implementación. 

## <a name="required-service-configuration-cscfg-file-updates"></a>Actualizaciones necesarias del archivo de configuración de servicio (.cscfg)

### <a name="1-virtual-network"></a>1) Red virtual
Las implementaciones del servicio en la nube (soporte extendido) deben estar en una red virtual. La red virtual se puede crear por medio de [Azure Portal](../virtual-network/quick-create-portal.md), [PowerShell](../virtual-network/quick-create-powershell.md), la [CLI de Azure](../virtual-network/quick-create-cli.md) o una [plantilla de ARM](../virtual-network/quick-create-template.md). Además, debe haber una referencia a la red virtual y las subredes en el archivo de configuración de servicio (.cscfg), en la sección [NetworkConfiguration](schema-cscfg-networkconfiguration.md). 

En el caso de las redes virtuales que pertenecen al mismo grupo de recursos que el servicio en la nube, basta con una referencia al nombre de la red virtual en el archivo de configuración de servicio (.cscfg). Si la red virtual y el servicio en la nube se encuentran en dos grupos de recursos diferentes, es necesario especificar el id. de Azure Resource Manager completo de la red virtual en el archivo de configuración de servicio (.cscfg).
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Red virtual ubicada en el mismo grupo de recursos
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
  </AddressAssignments> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Red virtual ubicada en otro grupo de recursos
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
   </AddressAssignments>
```
### <a name="2-remove-the-old-plugins"></a>2) Eliminación de complementos antiguos

Quite la configuración antigua de Escritorio remoto del archivo de configuración de servicio (.cscfg).  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```
Quite la configuración de diagnóstico antigua de cada rol del archivo de configuración del servicio (.cscfg).

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
```

## <a name="required-service-definition-file-csdef-updates"></a>Actualizaciones necesarias del archivo de definición de servicio (.csdef)

> [!NOTE]
> Los cambios en el archivo de definición de servicio (.csdef) requieren que se vuelva a generar el archivo de paquete (.cspkg). Compile y reempaquete el archivo .cspkg después de realizar los siguientes cambios en el archivo .csdef para obtener la configuración más reciente del servicio en la nube.

### <a name="1-virtual-machine-sizes"></a>1) Tamaños de máquina virtual
Los tamaños siguientes están en desuso en Azure Resource Manager. Pero si quiere seguir usándolos, actualice el nombre `vmsize` con la convención de nomenclatura de Azure Resource Manager asociada.  

| Nombre de tamaño anterior | Nombre de tamaño actualizado | 
|---|---|
| ExtraSmall | Standard_A0 | 
| Pequeño | Standard_A1 |
| Media | Standard_A2 | 
| Grande | Standard_A3 | 
| ExtraLarge | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 Por ejemplo, `<WorkerRole name="WorkerRole1" vmsize="Medium"` se convertiría en `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"`.
 
> [!NOTE]
> Para recuperar una lista de los tamaños disponibles, vea la [lista de SKU de recursos](/rest/api/compute/resourceskus/list) y aplique los siguientes filtros: <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) Eliminación de los complementos antiguos de Escritorio remoto
Las implementaciones que usan los complementos antiguos de Escritorio remoto deben eliminar los módulos del archivo de definición de servicio (.csdef) y cualquier certificado asociado. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```
Las implementaciones que usaron los complementos de diagnóstico antiguos necesitan que se quite la configuración de cada rol del archivo de definición de servicio (.csdef).

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```
## <a name="access-control"></a>Control de acceso

La suscripción que contiene recursos de red debe tener acceso [de colaborador de red](../role-based-access-control/built-in-roles.md#network-contributor) o superior para Cloud Services (soporte extendido). Para más información, consulte el artículo sobre los [roles integrados de RBAC](../role-based-access-control/built-in-roles.md).

## <a name="key-vault-creation"></a>Creación de Key Vault 

Key Vault se usa para almacenar certificados asociados a Cloud Services (soporte extendido). Agregue los certificados a Key Vault y haga referencia a las huellas digitales del certificado en el archivo de configuración de servicio. También debe habilitar las "Directivas de acceso" (en el portal) en "Azure Virtual Machines para la implementación" en Key Vault, de modo que el recurso de Cloud Services (soporte extendido) pueda recuperar el certificado almacenado como secretos de Key Vault. Puede crear un almacén de claves en [Azure Portal](../key-vault/general/quick-create-portal.md) o con [PowerShell](../key-vault/general/quick-create-powershell.md). El almacén de claves debe crearse en la misma región y suscripción que el servicio en la nube. Para más información, consulte [Uso de certificados con Azure Cloud Services (soporte extendido)](certificates-and-key-vault.md).

## <a name="next-steps"></a>Pasos siguientes 
- Revise los [requisitos previos de implementación](deploy-prerequisite.md) de Cloud Services (soporte extendido).
- Implemente un servicio de Cloud Services (soporte extendido) mediante [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), una [plantilla](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
- Vea las [preguntas más frecuentes](faq.yml) sobre Cloud Services (soporte extendido).
- Visite el [repositorio de ejemplos de Cloud Services (soporte extendido)](https://github.com/Azure-Samples/cloud-services-extended-support)
