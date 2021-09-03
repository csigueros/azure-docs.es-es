---
title: Migración de Azure Cloud Services (clásico) a Azure Cloud Services (soporte extendido)
description: Información general de la migración de Cloud Services (clásico) a Cloud Services (soporte extendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: ef2d8b68d6fb56f5d9b3508d550303aa9d9526d1
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860486"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Migración de Azure Cloud Services (clásico) a Azure Cloud Services (soporte extendido)

En este documento se proporciona información general para migrar Cloud Services (clásico) a Cloud Services (soporte extendido).     

[Cloud Services (soporte extendido)](overview.md) tiene la ventaja principal de proporcionar resistencia regional junto con la paridad de características con Azure Cloud Services implementado mediante Azure Service Manager. También ofrece algunas funcionalidades de Azure Resource Manager, como el control de acceso basado en roles (RBAC), etiquetas y directivas, y admite plantillas de implementación y vínculo privado. Ambos modelos de implementación (soporte extendido y clásico) están disponibles con [estructuras de precios similares](https://azure.microsoft.com/pricing/details/cloud-services/).

Cloud Services (soporte extendido) proporciona dos rutas de acceso para que los clientes migren desde Azure Service Manager a Azure Resource Manager: volver a implementar y migración en contexto. 

En la tabla siguiente se resalta la comparación entre estas dos opciones.  


| Volver a implementar | Migración en contexto | 
|---|---|
| Los clientes pueden implementar un nuevo servicio en la nube directamente en Azure Resource Manager y, a continuación, eliminar el antiguo servicio en la nube en Azure Service Manager mediante la validación. | La herramienta de migración en contexto permite una migración fluida y orquestada por la plataforma de las implementaciones existentes de Cloud Services (clásico) a Cloud Services (soporte extendido). | 
| La reimplementación permite a los clientes: <br><br> - Definir nombres de recursos. <br><br> - Organizar o reutilizar los recursos como se prefiera. <br><br> - Reutilizar los archivos de definición y configuración del servicio con cambios mínimos. | Para la migración local, la plataforma: <br><br> - Define los nombres de los recursos. <br><br> - Organiza cada implementación y los recursos relacionados en grupos de recursos individuales. <br><br> - Modifica el archivo de configuración y definición existente para Azure Resource Manager. | 
| Los clientes necesitan orquestar el tráfico a la nueva implementación. | La migración conserva la dirección IP y la ruta de acceso de datos sigue siendo la misma. | 
| Los clientes deben eliminar los servicios en la nube antiguos en Azure Resource Manager. | La plataforma elimina los recursos de Cloud Services (clásico) después de la migración. | 
| Se trata de una migración lift-and-shift que ofrece más flexibilidad, pero requiere tiempo adicional para migrar. | Es una migración automatizada que ofrece una migración rápida, pero con menos flexibilidad. | 

Al evaluar los planes de migración de Cloud Services (clásico) a Cloud Services (soporte extendido), es posible que desee investigar otros servicios de Azure, como: [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), [App Service](../app-service/overview.md), [Azure Kubernetes Service](../aks/intro-kubernetes.md) y [Azure Service Fabric](../service-fabric/overview-managed-cluster.md). Estos servicios seguirán incorporando funcionalidades adicionales, mientras que Cloud Services (soporte extendido) mantendrá principalmente la paridad de características con Cloud Services (clásico).

En función de la aplicación, Cloud Services (soporte extendido) puede requerir menos esfuerzo para pasar a Azure Resource Manager en comparación con otras opciones. Si la aplicación no evoluciona, Cloud Services (soporte extendido) es una opción viable para tener en cuenta, ya que proporciona una ruta de migración rápida. Por el contrario, si la aplicación evoluciona continuamente y necesita un conjunto de características más moderno, explore otros servicios de Azure para abordar mejor sus requisitos actuales y futuros.

## <a name="redeploy-overview"></a>Introducción a la reimplementación

La reimplementación de los servicios con [Cloud Services (soporte extendido)](overview.md) tiene los beneficios siguientes: 

- Admite roles web y de trabajo, de forma similar a Cloud Services (clásico).
- No hay ningún cambio en el diseño, la arquitectura o los componentes de los roles web y de trabajo. 
- No se requieren cambios en el código del entorno de ejecución, ya que el plano de datos es el mismo de los servicios en la nube. 
- Las versiones de Azure GuestOS y las actualizaciones asociadas están alineadas con Cloud Services (clásico). 
- El proceso de actualización subyacente con respecto a los dominios de actualización, cómo se aplica la actualización, la reversión y los cambios de servicio permitidos durante una actualización no cambiarán.

Un nuevo servicio de Cloud Services (soporte extendido) se puede implementar directamente en Azure Resource Manager con las siguientes herramientas de cliente:

- [Implementación de un servicio en la nube: Portal](deploy-portal.md)
- [Implementación de un servicio en la nube: PowerShell](deploy-powershell.md)
- [Implementación de un servicio en la nube: plantilla](deploy-template.md)
- [Implementación de un servicio en la nube: SDK](deploy-sdk.md)
- [Implementación de un servicio en la nube: Visual Studio](/visualstudio/azure/cloud-services-extended-support?context=%2fazure%2fcloud-services-extended-support%2fcontext%2fcontex)


## <a name="migration-tool-overview"></a>Introducción a la herramienta de migración

La migración compatible con la plataforma proporciona las siguientes ventajas principales:

- Permite una migración fluida orquestada por la plataforma sin tiempo de inactividad para la mayoría de los escenarios. Obtenga más información sobre los [escenarios compatibles](in-place-migration-technical-details.md).  
- Migra los servicios en la nube existentes en tres sencillos pasos: validar, preparar, confirmar (o anular). Obtenga más información sobre cómo [funciona la herramienta de migración]in-place-migration-overview.md#migration-steps).
- Proporciona la capacidad de probar las implementaciones migradas después de una preparación correcta. Confirma y finaliza la migración, mientras que la anulación cancela la migración.

La herramienta de migración emplea las mismas API y tiene la misma experiencia que la [migración de Virtual Machines (clásico)](../virtual-machines/migration-classic-resource-manager-overview.md). 

## <a name="setup-access-for-migration"></a>Configuración del acceso para la migración

Para realizar esta migración, deben haberlo agregado como coadministrador de la suscripción y registrar los proveedores necesarios. 

1. Inicie sesión en Azure Portal.
3. En el menú de Central, seleccione Suscripción. Si no lo ve, haga clic en Todos los servicios.
3. Busque la entrada de la suscripción adecuada y después examine el campo MI ROL. Para un coadministrador, el valor debe ser Administrador de cuenta. Si no puede agregar un coadministrador, póngase en contacto con un administrador del servicio o coadministrador de la suscripción para que le agreguen.

4. Registre la suscripción para el espacio de nombres Microsoft.ClassicInfrastructureMigrate mediante el [portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) o la [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Compruebe el estado del registro. El registro puede tardar unos minutos en completarse. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>¿En qué se diferencia la migración de Cloud Services (clásico) de Virtual Machines (clásico)?
Azure Service Manager admite dos productos de proceso diferentes, [Azure Virtual Machines (clásico)](/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) y [Azure Cloud Services (clásico)](../cloud-services/cloud-services-choose-me.md) o roles web o de trabajo. Los dos productos difieren en función del tipo de implementación que se encuentra dentro del servicio en la nube. Azure Cloud Services (clásico) usa el servicio en la nube que contiene implementaciones con roles web y de trabajo. Azure Virtual Machines (clásico) usa un servicio en la nube que contiene implementaciones con VM IaaS.

La lista de escenarios admitidos difiere entre Cloud Services (clásico) y Virtual Machines (clásico) debido a las diferencias en los tipos de implementación.

## <a name="migration-steps"></a>Pasos de migración
 
Los clientes pueden migrar sus implementaciones de Cloud Services (clásico) mediante las cuatro mismas operaciones que se usan para migrar Virtual Machines (clásico). 

1. **Validar migración**: Valida que escenarios no admitidos comunes no impidan la migración.
2. **Preparar migración**: Duplica los metadatos de recursos en Azure Resource Manager. Todos los recursos están bloqueados para las operaciones de creación, actualización y eliminación para asegurar que los metadatos de recursos estén sincronizados entre Azure Server Manager y Azure Resource Manager. Todas las operaciones de lectura funcionarán con las API de Cloud Services (clásico) y Cloud Services (soporte extendido).
3. **Anular migración**: Quita los metadatos de recursos de Azure Resource Manager. Desbloquea todos los recursos para las operaciones de creación, actualización y eliminación.
4. **Confirmar migración**: Quita los metadatos de recursos de Azure Service Manager. Desbloquea el recurso para las operaciones de creación, actualización y eliminación. Ya no se permite la anulación después de que se haya intentado la confirmación.

>[!NOTE]
> Preparar, anular y confirmar son idempotentes y, por tanto, si se produce un error, un reintento debería corregir el problema.

:::image type="content" source="media/in-place-migration-1.png" alt-text="La imagen muestra el diagrama de los pasos asociados con la migración.":::

Para obtener más información, consulte [Migración compatible con la plataforma de recursos de IaaS desde el modelo clásico al de Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Recursos y características admitidos disponibles para la migración asociada a Cloud Services (clásico)
-   Cuentas de almacenamiento
-   Virtual Networks
-   Grupos de seguridad de red
-   Direcciones IP públicas reservadas
-   Listas de control de acceso de punto de conexión
-   Rutas definidas por el usuario
-   Equilibrador de carga interno
-   Migración de certificados a un almacén de claves
-   Complementos y extensión (basados en XML y JSON)
-   Tareas en el inicio y en el final
-   Implementaciones con redes aceleradas
-   Implementaciones con uno o varios roles
-   Versión Básica de Load Balancer
-   Entrada, entrada de instancia, puntos de conexión internos
-   Direcciones IP públicas dinámicas
-   Nombre DNS 
-   Reglas de tráfico de red

## <a name="supported-configurations--migration-scenarios"></a>Configuraciones y escenarios de migración admitidos
Estos son los principales escenarios que implican combinaciones de recursos, características y Cloud Services. Esta lista no es exhaustiva.

| Servicio | Configuración | Comentarios | 
|---|---|---|
| [Azure AD Domain Services](../active-directory-domain-services/migrate-from-classic-vnet.md) | Redes virtuales que contienen Azure Active Directory Domain Services | Se admite la red virtual que contiene la implementación de Cloud Services y Azure AD Domain Services. El cliente primero debe migrar por separado Azure AD servicios de dominio y, a continuación, migrar la red virtual a la izquierda solo con la implementación del servicio en la nube. |
| Servicio en la nube | Servicio en la nube con una implementación en un solo espacio. | Se pueden migrar servicios en la nube que contienen una implementación de espacio de producción o de ensayo. |
| Servicio en la nube | Implementación no en una red virtual visible públicamente (implementación de red virtual predeterminada) | Un servicio en la nube puede estar en una red virtual visible públicamente, en una red virtual oculta o no en una red virtual.  Se admiten para la migración los servicios en la nube en una red virtual oculta y las redes virtuales visibles públicamente. El cliente puede usar la API de validación para saber si una implementación se encuentra dentro de una red virtual predeterminada o no y, por tanto, decidir si se puede migrar. |
|Servicio en la nube | Extensiones XML (BGInfo, depurador de Visual Studio, Web Deploy y depuración remota). | Se admiten todas las extensiones XML para la migración 
| Virtual Network | Red virtual que contiene varios servicios en la nube. | La red virtual contiene varios servicios en la nube que se admiten para la migración. La red virtual y todos los servicios en la nube que contiene se migrarán juntos a Azure Resource Manager. |
| Virtual Network | Migración de redes virtuales creadas a través del portal (requiere el uso de "grupo nombre-dl-grupo-de-recursos nombre-de-VNet" en el archivo .cscfg)  | Como parte de la migración, se cambiará el nombre de la red virtual en cscfg para usar el identificador de Azure Resource Manager para la red virtual. (subscription/id-suscripción/resource-group/nombre-del-grupo-de-recursos/resource/nombre-de-vnet) <br><br>Para administrar la implementación después de la migración, actualice la copia local del archivo .cscfg para empezar a usar el identificador de Azure Resource Manager en lugar del nombre de red virtual. <br><br>Un archivo .cscfg que use el esquema de nomenclatura anterior no superará la validación. 
| Virtual Network | Migración de la implementación con roles en una subred diferente. | Se admite la migración de un servicio en la nube con roles diferentes en subredes diferentes. |

## <a name="next-steps"></a>Pasos siguientes
- [Migración compatible con la plataforma de recursos de IaaS desde el modelo clásico al de Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migración a Cloud Services (soporte extendido) mediante [Azure Portal](in-place-migration-portal.md)
- Migración a Cloud Services (soporte extendido) mediante [PowerShell](in-place-migration-powershell.md)
