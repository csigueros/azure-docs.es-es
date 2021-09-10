---
title: Cambios posteriores a la migración a Azure Cloud Services (soporte extendido)
description: Información general de los cambios posteriores a la migración a Cloud Services (soporte extendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: df9a41b6832c9ca93aa7078b0d06082f1441a5e5
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439477"
---
# <a name="post-migration-changes"></a>Cambios posteriores a la migración
La implementación de Cloud Services (clásico) se convierte en una implementación de Cloud Services (soporte extendido). Para obtener más información, consulte [Documentación de Cloud Services (soporte extendido)](deploy-prerequisite.md).  

## <a name="changes-to-deployment-files"></a>Cambios en los archivos de implementación 

Se realizan cambios menores en los archivos .csdef y .cscfg del cliente para que los archivos de implementación cumplan con los requisitos de Azure Resource Manager y Cloud Services (soporte extendido). Después de la migración, se recuperan los nuevos archivos de implementación o se actualizan los archivos existentes. Esto será necesario para las operaciones de actualización y eliminación.  

- Virtual Network usa el identificador de recurso de Azure Resource Manager completo en lugar de simplemente el nombre del recurso en la sección NetworkConfiguration del archivo .cscfg. Por ejemplo, `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. En el caso de las redes virtuales que pertenecen al mismo grupo de recursos que el servicio en la nube, puede optar por actualizar el archivo .cscfg para que vuelva a usar solo el nombre de la red virtual.  

- Los tamaños clásicos, como Small, Large y ExtraLarge, se sustituyen por los nuevos nombres de tamaño, Standard_A*. Los nombres de tamaño deben cambiarse a sus nuevos nombres en el archivo .csdef. Para obtener más información, consulte [Requisitos previos para implementar Cloud Services (soporte extendido)](deploy-prerequisite.md#required-service-definition-file-csdef-updates).

- Use la API Get para obtener la copia más reciente de los archivos de implementación. 
    - Obtenga la plantilla mediante el [portal](../azure-resource-manager/templates/export-template-portal.md), [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates), la [CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) y la [API REST](/rest/api/resources/resourcegroups/exporttemplate). 
    - Obtenga el archivo .csdef mediante [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) o la [API REST](/rest/api/compute/cloudservices/rest-get-package). 
    - Obtenga el archivo .cscfg mediante [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) o la [API REST](/rest/api/compute/cloudservices/rest-get-package). 
    
 

## <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Cambios en la automatización del cliente, la canalización de CI/CD, los scripts personalizados, los paneles personalizados, las herramientas personalizadas, etc.  

Los clientes tienen que actualizar sus herramientas y automatización para empezar a usar las nuevas API y comandos para administrar su implementación. Los clientes pueden adoptar fácilmente las nuevas características y funcionalidades de Azure Resource Manager y Cloud Services (soporte extendido) como parte de este cambio. 

- Cambios en los nombres de recursos y grupos de recursos después de la migración
    - Como parte de la migración, cambian los nombres de unos pocos recursos, como el servicio en la nube, las direcciones IP públicas, etc. Es posible que estos cambios deban reflejarse en los archivos de implementación antes de actualizar el servicio en la nube. [Obtenga más información sobre los nombres de los recursos que cambian](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- Vuelva a crear las reglas y directivas necesarias para administrar y escalar servicios en la nube 
    - Las [reglas de escalabilidad automática](configure-scaling.md) no se migran. Después de la migración, vuelva a crear las reglas de escalabilidad automática.  
    - Las [alertas](enable-alerts.md) no se migran. Después de la migración, vuelva a crear las alertas.
    - Key Vault se crea sin ninguna directiva de acceso. [Cree las directivas adecuadas](../key-vault/general/assign-access-policy-portal.md) en Key Vault para ver o administrar los certificados. Los certificados estarán visibles en configuración en la pestaña denominada Secretos.


## <a name="changes-to-certificate-management-post-migration"></a>Cambios en la administración de certificados después de la migración 

Como práctica estándar para administrar los certificados, todos los archivos de certificado .pfx válidos deben agregarse al almacén de certificados en Key Vault y la actualización funcionaría perfectamente a través de cualquier cliente: Portal, PowerShell o API REST.

Actualmente, Azure Portal realiza una validación para que compruebe si todos los certificados necesarios se cargan en el almacén de certificados de Key Vault y advierte si no se encuentra un certificado. Sin embargo, si tiene previsto usar certificados como secretos, estos certificados no se pueden validar para su huella digital y cualquier operación de actualización que implique la adición de secretos producirá un error a través del portal. Se recomienda a los clientes que utilicen PowerShell o la API REST para continuar con las actualizaciones relacionadas con los secretos.


## <a name="changes-for-update-via-visual-studio"></a>Cambios para actualizar a través de Visual Studio
Si estaba publicando actualizaciones a través de Visual Studio directamente, primero tendría que descargar el archivo CSCFG más reciente de la implementación posterior a la migración. Use este archivo como referencia para agregar detalles de configuración de red al archivo CSCFG actual en el proyecto de Visual Studio. A continuación, compile la solución y publíquela. Es posible que tenga que elegir el almacén de claves y el grupo de recursos para esta actualización.


## <a name="next-steps"></a>Pasos siguientes
- [Migración compatible con la plataforma de recursos de IaaS desde el modelo clásico al de Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migración a Cloud Services (soporte extendido) mediante [Azure Portal](in-place-migration-portal.md)
- Migración a Cloud Services (soporte extendido) mediante [PowerShell](in-place-migration-powershell.md)
