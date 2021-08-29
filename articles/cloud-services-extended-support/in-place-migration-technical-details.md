---
title: Detalles técnicos y requisitos para migrar A Azure Cloud Services (soporte extendido)
description: Proporciona detalles técnicos y requisitos para la migración desde Azure Cloud Services (clásico) a Azure Cloud Services (soporte extendido)
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
author: hirenshah1
ms.author: hirshah
ms.openlocfilehash: 55ce5305962562876a97dfd7677e6af5e1eb9e3a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747566"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Detalles técnicos de la migración a Azure Cloud Services (soporte extendido)   

En este artículo se describen los detalles técnicos relacionados con la herramienta de migración en relación con Cloud Services (clásico). 

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Detalles acerca de las características o escenarios admitidos para la migración 


### <a name="extensions-and-plugin-migration"></a>Migración de extensiones y complementos 
- Se migrarán todas las extensiones habilitadas y admitidas. 
- No se migrarán las extensiones deshabilitadas. 
- Los complementos son un concepto heredado y deben quitarse antes de la migración. Se admiten para la migración y, después de la migración, pero si es necesario habilitar la extensión, es necesario quitar primero el complemento antes de instalar la extensión. Los complementos y extensiones de Escritorio remoto se ven afectados por esto.   
 
### <a name="certificate-migration"></a>Migración de certificados
- En Cloud Services (soporte extendido), los certificados se almacenan en una instancia de Key Vault. Como parte de la migración, se crea una instancia de Key Vault para los clientes que tienen el nombre de la instancia de Cloud Services, y se transfieren todos los certificados de Azure Service Manager a Key Vault. 
- La referencia a esta instancia de Key Vault se especifica en la plantilla o se pasa a través de PowerShell o la CLI de Azure. 

### <a name="service-configuration-and-service-definition-files"></a>Archivos de configuración del servicio y de definición de servicio
- Los archivos .cscfg y .csdef deben actualizarse para Cloud Services (soporte extendido) con cambios menores. 
- Los nombres de recursos, como las SKU de redes virtuales y de VM, son diferentes. Consulte [Traducción de recursos y convención de nomenclatura después de la migración](#translation-of-resources-and-naming-convention-post-migration).
- Los clientes pueden recuperar sus nuevas implementaciones a través de [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) y la [API REST](/rest/api/compute/cloudservices/get). 

### <a name="cloud-service-and-deployments"></a>Cloud Services e implementaciones
- Cada implementación de Cloud Services (soporte extendido) es un Cloud Service independiente. Las implementaciones ya no se agrupan en un servicio en la nube mediante espacios.
- Si tiene dos espacios en su instancia de Cloud Services (clásico), debe eliminar un espacio (ensayo) y usar la herramienta de migración para mover el otro espacio (producción) a Azure Resource Manager. 
- La dirección IP pública de la implementación de Cloud Services sigue siendo la misma después de la migración a Azure Resource Manager y se expone como recurso de IP de SKU básico (dinámico o estático). 
- El nombre DNS y el dominio (cloudapp.azure.net) para el servicio en la nube migrado siguen siendo los mismos. 

### <a name="virtual-network-migration"></a>Migración de redes virtuales
- Si una implementación de Cloud Services está en una red virtual, durante la migración todos los Cloud Services y los recursos de red virtual asociados se migran juntos. 
- Después de la migración, la red virtual se coloca en un grupo de recursos diferente que la instancia de Cloud Services. 
- En el caso de las redes virtuales con varias instancias de Cloud Services, cada instancia de Cloud Services se migra una después de otra. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Migración de implementaciones que no están en una red virtual
- En 2017, Azure comenzó a crear automáticamente nuevas implementaciones (sin la red virtual especificada por el cliente) en una red virtual "predeterminada" creada por la plataforma. Estas redes virtuales predeterminadas están ocultas a los clientes.   
- Como parte de la migración, esta red virtual predeterminada se expondrá a los clientes una vez que esté en Azure Resource Manager. Para administrar o actualizar la implementación en Azure Resource Manager, los clientes tienen que agregar esta información de red virtual en la sección NetworkConfiguration del archivo .cscfg.    
- La red virtual predeterminada, cuando se migra a Azure Resource Manager, se coloca en el mismo grupo de recursos que la implementación de Cloud Services.
- Las implementaciones de Cloud Services creadas antes de este momento no estarán en ninguna red virtual y no se podrán migrar con la herramienta. Considere la posibilidad de volver a implementar estas implementaciones de Cloud Services directamente en Azure Resource Manager. 
- Para comprobar si una implementación es válida para la migración, ejecute la API de validación en la implementación. El resultado de la API de validación contendrá un mensaje de error que menciona explícitamente si esta implementación es válida para la migración.     

### <a name="load-balancer"></a>Load Balancer   
- En el caso de una instancia de Cloud Services que usa un punto de conexión público, un equilibrador de carga creado por la plataforma asociado a la instancia de Cloud Services se expone dentro de la suscripción del cliente en Azure Resource Manager. El equilibrador de carga es un recurso de solo lectura, y las actualizaciones solo se restringen a través de los archivos de configuración del servicio (.cscfg) y de definición de servicio (.csdef). 

### <a name="key-vault"></a>Key Vault
- Como parte de la migración, Azure crea automáticamente una nueva instancia de Key Vault y migra todos los certificados a ella. La herramienta no permite usar una instancia de Key Vault existente. 
- Cloud Services (soporte extendido) requiere una instancia de Key Vault ubicada en la misma región y suscripción. Esta instancia de Key Vault se crea automáticamente como parte de la migración. 

## <a name="resources-and-features-not-available-for-migration"></a>Recursos y características no disponibles para la migración
Estos son los principales escenarios que implican combinaciones de recursos, características y Cloud Services. Esta lista no es exhaustiva. 

| Recurso | Pasos siguientes/solución alternativa | 
|---|---|
| Reglas de escalabilidad automática | La migración se completa, pero las reglas se anulan. [Vuelva a crear las reglas](./configure-scaling.md) después de la migración en Cloud Services (soporte extendido). | 
| Alertas | La migración se completa, pero las alertas se anulan. [Vuelva a crear las reglas](./enable-alerts.md) después de la migración en Cloud Services (soporte extendido). | 
| VPN Gateway | Quite VPN Gateway antes de comenzar la migración y, después, vuelva a crearlo una vez que la migración se complete. | 
| Puertas de enlace de ExpressRoute (en la misma suscripción que solo la red virtual) | Quite la puerta de enlace de ExpressRoute antes de comenzar la migración y, después, vuelva a crearla una vez que la migración se complete. | 
| Quota  | La cuota no se migra. [Solicite una nueva cuota](../azure-resource-manager/templates/error-resource-quota.md#solution) en Azure Resource Manager antes de la migración para que la validación sea correcta. | 
| Grupos de afinidad | No compatible. Quite todos los grupos de afinidad antes de la migración.  | 
| Redes virtuales con el [emparejamiento de red virtual](../virtual-network/virtual-network-peering-overview.md)| Antes de migrar una red virtual que está emparejada a otra red virtual, elimine el emparejamiento, migre la red virtual a Resource Manager y vuelva a crear el emparejamiento. Esto puede provocar un tiempo de inactividad en función de la arquitectura. | 
| Redes virtuales que contienen entornos de App Service | No compatible | 
| Redes virtuales que contienen servicios de HDInsight | No compatible. 
| Redes virtuales que contienen implementaciones de Azure API Management | No compatible. <br><br> Para migrar la red virtual, cambie la red virtual de la implementación de API Management. Se trata de una operación sin tiempo de inactividad. | 
| Circuitos ExpressRoute clásicos | No compatible. <br><br>Estos circuitos se deben migrar a Azure Resource Manager antes de comenzar la migración de PaaS. Para obtener más información, consulte la [transición de los circuitos ExpressRoute del modelo de implementación clásica al modelo de implementación de Resource Manager](../expressroute/expressroute-howto-move-arm.md). |  
| Control de acceso basado en roles | Después de la migración, el URI del recurso cambia de `Microsoft.ClassicCompute` a `Microsoft.Compute`. Las directivas de RBAC deben actualizarse después de la migración. | 
| Application Gateway | No compatible. <br><br> Quite Application Gateway antes de comenzar la migración y, después, vuelva a crearlo una vez que la migración se complete en Azure Resource Manager. | 

## <a name="unsupported-configurations--migration-scenarios"></a>Configuraciones y escenarios de migración no admitidos

| Configuración/escenario  | Pasos siguientes/solución alternativa | 
|---|---|
| Migración de algunas implementaciones anteriores que no están en una red virtual | Algunas implementaciones de Cloud Services que no están en una red virtual no se admiten para la migración. <br><br> 1. Use la API de validación para comprobar si la implementación es válida para la migración. <br> 2. Si es válida, las implementaciones se moverán a Azure Resource Manager en una red virtual con el prefijo "DefaultRdfeVnet". | 
| Migración de implementaciones que contienen implementaciones de espacio de producción y de ensayo mediante direcciones IP dinámicas | La migración de un servicio en la nube de dos espacios requiere la eliminación del espacio de ensayo. Una vez eliminado el espacio de ensayo, migre el espacio de producción como instancia independiente de Cloud Services (soporte extendido) en Azure Resource Manager. A continuación, vuelva a implementar el entorno de ensayo como nueva instancia de Cloud Services (soporte extendido) y haga que sea intercambiable con el primero. | 
| Migración de implementaciones que contienen implementaciones de espacio de producción y de ensayo mediante direcciones IP reservadas | No compatible. | 
| Migración de la implementación de producción y de ensayo en una red virtual diferente|La migración de un servicio en la nube de dos espacios requiere la eliminación del espacio de ensayo. Una vez eliminado el espacio de ensayo, migre el espacio de producción como instancia independiente de Cloud Services (soporte extendido) en Azure Resource Manager. A continuación, se puede vincular una nueva implementación de Cloud Services (soporte extendido) a la implementación migrada con la propiedad intercambiable habilitada. Los archivos de implementaciones de la antigua implementación del espacio de ensayo se pueden volver a usar para crear esta nueva implementación intercambiable. | 
| Migración de Cloud Services vacío (Cloud Services sin implementación) | No compatible. | 
| Migración de una implementación que contiene el complemento de Escritorio remoto y las extensiones de Escritorio remoto | Opción 1: quite el complemento de Escritorio remoto antes de la migración. Esto requiere cambios en los archivos de implementación. A continuación, la migración se completará. <br><br> Opción 2: quite la extensión de Escritorio remoto y migre la implementación. Después de la migración, quite el complemento e instale la extensión. Esto requiere cambios en los archivos de implementación. <br><br> Quite el complemento y la extensión antes de la migración. [No se recomienda el uso de complementos](./deploy-prerequisite.md#required-service-definition-file-csdef-updates) en Cloud Services (soporte extendido).| 
| Redes virtuales con implementaciones PaaS e IaaS |No compatible <br><br> Mueva las implementaciones PaaS o IaaS a una red virtual diferente. Esto provocará un tiempo de inactividad. | 
Implementaciones de Cloud Serivces que usan tamaños de rol heredados (como Small o ExtraLarge). | La migración se completará, pero los tamaños de rol se actualizarán para usar los tamaños de rol modernos. No hay ningún cambio en las propiedades de costo o SKU, y la máquina virtual no se reiniciará para este cambio. Actualice todos los artefactos de implementación para que hagan referencia a estos nuevos tamaños de rol modernos. Para obtener más información, consulte [Tamaños de VM disponibles](available-sizes.md).|
| Migración de Cloud Services a una red virtual diferente | No compatible <br><br> 1. Mueva la implementación a una red virtual clásica diferente antes de la migración. Esto provocará un tiempo de inactividad. <br> 2. Migre la nueva red virtual a Azure Resource Manager. <br><br> Or <br><br> 1. Migre la red virtual a Azure Resource Manager. <br>2. Mueva Cloud Services a una nueva red virtual. Esto provocará un tiempo de inactividad. | 
| Cloud Services en una red virtual, pero sin tener asignada una subred explícita | No compatible. La mitigación implica mover el rol a una subred, lo que requiere un reinicio de rol (tiempo de inactividad) | 

## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Traducción de recursos y convención de nomenclatura después de la migración
Como parte de la migración, se cambian los nombres de los recursos, y pocas características de Cloud Services se exponen como recursos de Azure Resource Manager. En la tabla se resumen los cambios específicos a una migración de Cloud Services.

| Cloud Services (clásico) <br><br> Nombre del recurso | Cloud Services (clásico) <br><br> Sintaxis| Cloud Services (soporte extendido) <br><br> Nombre del recurso| Cloud Services (soporte extendido) <br><br> Sintaxis | 
|---|---|---|---|
| Servicio en la nube | `cloudservicename` | Sin asociar| Sin asociar |
| Implementación (creada en el portal) <br><br> Implementación (creada fuera del portal)  | `deploymentname` | Cloud Services (soporte extendido) | `cloudservicename` |  
| Virtual Network | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Sin asociar |  Virtual Network (creada fuera del portal) <br><br> Virtual Network (creada en el portal) <br><br> Virtual Network (predeterminado) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `VNet-cloudservicename`|
| Sin asociar | Sin asociar | Key Vault | `KV-cloudservicename` | 
| Sin asociar | Sin asociar | Grupo de recursos para implementaciones de Cloud Services | `cloudservicename-migrated` | 
| Sin asociar | Sin asociar | Grupo de recursos para Virtual Network | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Sin asociar | Sin asociar | IP pública (dinámica) | `cloudservicenameContractContract` | 
| Nombre de IP reservada | `reservedipname` | IP reservada (creada fuera del portal) <br><br> IP reservada (creada en el portal) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Sin asociar| Sin asociar | Load Balancer | `LB-cloudservicename`|



## <a name="migration-issues-and-how-to-handle-them"></a>Problemas de migración y cómo abordarlo. 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>La migración se ha bloqueado en una operación durante mucho tiempo. 
- La confirmación, preparación y anulación pueden tardar mucho tiempo en función del número de implementaciones. Se agotará el tiempo de espera de las operaciones después de 24 horas.   
- Las operaciones de confirmación, preparación y anulación son idempotentes. La mayoría de los problemas se pueden corregir volviendo a intentar las operaciones. Podría haber errores transitorios, que pueden desaparecer en pocos minutos, se recomienda volver a intentar la operación después de un intervalo. Si la migración se realiza mediante Azure Portal y la operación está bloqueada en un estado "en curso", use PowerShell para volver a intentarla. 
- Póngase en contacto con soporte técnico para que le ayude a migrar o revertir la implementación desde el back-end. 

### <a name="migration-failed-in-an-operation"></a>Error de la migración en una operación. 
- Si se produce un error en la validación, se debe a que la implementación o la red virtual contienen un escenario, una característica o un recurso no admitidos. Use la lista de escenarios no admitidos para encontrar la solución alternativa en los documentos.  
- La operación de preparación primero realiza una validación que incluye algunas validaciones costosas (que no se cubren en la validación). El error de preparación puede deberse a un escenario no admitido. Busque el escenario y la solución alternativa en los documentos públicos. Se debe llamar a la anulación para volver al estado original y desbloquear la implementación para las operaciones de actualización y eliminación.
- Si se produce un error en la anulación, vuelva a intentar la operación. Si hay errores en los reintentos, póngase en contacto con el soporte técnico.
- Si se produce un error en la confirmación, vuelva a intentar la operación. Si hay errores en los reintentos, póngase en contacto con el soporte técnico. Incluso en caso de error en la confirmación, no debería haber ningún problema del plano de datos en la implementación. La implementación debería poder controlar el tráfico del cliente sin ningún problema. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>El portal se actualizó después de la preparación. La experiencia se reinició, y la confirmación o anulación ya no son visibles. 
- El portal almacena la información de migración localmente y, por tanto, después de la actualización, se iniciará desde la fase de validación incluso si la instancia de Cloud Services se encuentra en la fase de preparación.  
- Puede usar el portal para seguir los pasos de validación y preparación de nuevo para mostrar el botón Anular o Confirmar. No producirá ningún error.
- Los clientes pueden usar PowerShell o la API REST para anular o confirmar. 

### <a name="how-much-time-can-the-operations-takebr"></a>¿Cuánto tiempo pueden tardar las operaciones?<br>
La validación está diseñada para ser rápida. La preparación es la operación de más larga duración y tarda algún tiempo en función del número total de instancias de rol que se van a migrar. La anulación y confirmación también pueden llevar tiempo, pero tardarán menos tiempo en comparación con la preparación. Se agotará el tiempo de espera de todas las operaciones después de 24 horas.

## <a name="next-steps"></a>Pasos siguientes
Para obtener ayuda para migrar la implementación de Cloud Services (clásico) a Cloud Services (soporte extendido), vea la página de aterrizaje [Soporte técnico y solución de problemas](support-help.md).