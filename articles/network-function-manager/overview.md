---
title: Acerca de Azure Network Function Manager
description: Obtenga información sobre Azure Network Function Manager, un servicio de orquestación nativo de nube totalmente administrado que permite implementar y aprovisionar funciones de red en Azure Stack Edge Pro con GPU para una experiencia híbrida coherente mediante Azure Portal.
author: cherylmc
ms.service: network-function-manager
ms.topic: overview
ms.date: 06/16/2021
ms.author: cherylmc
ms.custom: references_regions
ms.openlocfilehash: df063a5b19e9287e1275683719d2d41a13200b5e
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128937"
---
# <a name="what-is-azure-network-function-manager-preview"></a>¿Qué es Azure Network Function Manager? (versión preliminar)


Azure Network Function Manager ofrece una experiencia de [Azure Marketplace](https://azure.microsoft.com/marketplace/) para implementar funciones de red, como redes troncales de paquetes móviles, dispositivos perimetrales SD-WAN y servicios VPN, en el [dispositivo Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) que se ejecuta en el entorno local. Ahora puede implementar rápidamente un servicio de red móvil privado o una solución SD-WAN en el dispositivo perimetral directamente desde el portal de administración de Azure. Network Function Manager incorpora funciones de red de un ecosistema creciente de [asociados](#partners). Esta versión preliminar se admite en [Azure Stack Edge Pro con GPU](../databox-online/azure-stack-edge-gpu-overview.md). 

## <a name="preview-features"></a><a name="preview"></a>Características en vista previa

* **Experiencia de administración coherente:** Network Function Manager proporciona una experiencia de administración de Azure coherente en las funciones de red de distintos asociados implementadas en el dispositivo perimetral de empresa. Esto permite simplificar la gobernanza y la administración. Puede usar las herramientas y SDK de Azure con los que ya está familiarizado para automatizar la implementación de funciones de red mediante plantillas declarativas. También puede aplicar el control de acceso basado en rol de Azure [Azure RBAC](../role-based-access-control/overview.md) para una implementación global de funciones de red en los dispositivos Azure Stack Edge.

* **Experiencia de Azure Marketplace para funciones de red 5G:** acelere la implementación de una solución de red móvil privada en el dispositivo Azure Stack Edge al seleccionar desde su elección de función de red troncal de paquete móvil 5G y LTE directamente desde Azure Marketplace.

* **Experiencia perfecta de nube a dispositivo perimetral para soluciones SD-WAN y VPN:** Network Function Manager amplía la experiencia de administración de Azure en las funciones de red de Marketplace con las que está familiarizado desde la nube pública al dispositivo perimetral. Esto permite aprovechar una experiencia de implementación coherente en la selección de funciones de red de asociados de SD-WAN y VPN implementadas en la nube pública de Azure o un dispositivo Azure Stack Edge.

* **Experiencia de Azure Managed Applications en funciones de red en dispositivos perimetrales de empresa:** Network Function Manager ofrece una experiencia de implementación simplificada en funciones de red especializadas, como redes troncales de paquetes móviles, en el dispositivo Azure Stack Edge. Se ha validado previamente la administración del ciclo de vida de las imágenes de funciones de red aprobadas de los asociados. Puede tener la confianza de que los recursos de funciones de red se implementan en un estado coherente en toda la flota. Para obtener más información, vea [Azure Managed Applications](../azure-resource-manager/managed-applications/overview.md).

* **Aceleración de red y elección de asignación de IP dinámica o estática para funciones de red:** Network Function Manager y [Azure Stack Edge Pro](../databox-online/azure-stack-edge-gpu-overview.md) ofrecen un rendimiento de red mejorado para las cargas de trabajo de funciones de red. Ahora se pueden implementar funciones de red especializadas, como redes troncales de paquetes móviles, en el dispositivo Azure Stack Edge con un rendimiento de ruta de acceso de datos más rápido en la red del punto de acceso y la red del plano de usuario. También puede elegir entre la asignación de IP estática o dinámica para diferentes interfaces virtuales en la implementación de funciones de red. Consulte al asociado de funciones de red la compatibilidad con estas capacidades de redes.  

## <a name="azure-managed-applications-for-network-functions"></a><a name="managed"></a>Azure Managed Applications para funciones de red

Las funciones de red disponibles para implementarse mediante Network Function Manager están diseñadas para ejecutarse específicamente en el dispositivo Azure Stack Edge. La oferta de función de red se publica en Azure Marketplace como una [aplicación administrada de Azure](../azure-resource-manager/managed-applications/overview.md). Los clientes pueden implementar la oferta directamente desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) o desde el recurso de dispositivo de Network Function Manager mediante Azure Portal. 

:::image type="content" source="./media/overview/managed-app-workflow.png" alt-text="Diagrama del flujo de trabajo de la aplicación administrada.":::

Todas las ofertas de funciones de red que están disponibles para implementarse mediante Network Function Manager tienen una aplicación administrada que está disponible en Azure Marketplace. Las aplicaciones administradas permiten a los asociados:

* Compilar una experiencia de implementación personalizada para su función de red con la experiencia de Azure Portal. 

* Proporcionar una plantilla de Resource Manager especializada que les permita crear la función de red directamente en el dispositivo Azure Stack Edge. 

* Facturar los costos de las licencias de software directamente o a través de Azure Marketplace. 

* Exponer las propiedades personalizadas y los medidores de recursos. 

Los asociados de funciones de red pueden crear diferentes recursos según la implementación del dispositivo, las licencias de configuración y las necesidades de administración. Como ocurre con todas las instancias de Azure Managed Applications, cuando un cliente crea una función de red en el dispositivo Azure Stack Edge, se crean dos grupos de recursos en su suscripción: 

* **Grupo de recursos cliente**: este grupo de recursos incluye un marcador de posición de aplicación para la aplicación administrada. Los asociados pueden usarlo para exponer las propiedades personalizadas que elijan. 

* **Grupo de recursos administrado**: no se pueden configurar ni cambiar los recursos de este grupo directamente, ya que es el editor de la aplicación administrada quien lo controla. Este grupo de recursos contiene el recurso de **funciones de red**  de Network Function Manager.

:::image type="content" source="./media/overview/managed-app-resource-groups.png" alt-text="Diagrama de grupos de recursos de la aplicación administrada.":::

## <a name="network-function-configuration-process"></a><a name="configuration"></a>Proceso de configuración de funciones de red 

Los asociados de funciones de red que ofrecen sus aplicaciones administradas de Azure con Network Function Manager proporcionan una experiencia que configura automáticamente la función de red como parte del proceso de implementación. Una vez que la implementación de la aplicación administrada se realiza correctamente y la instancia de la función de red se aprovisiona en Azure Stack Edge, cualquier configuración adicional que pueda ser necesaria para la función de red debe realizarse por medio del portal de administración de asociados de funciones de red. Consulte con el asociado de funciones de red la experiencia de administración de un extremo a otro de las funciones de red implementadas en el dispositivo Azure Stack Edge. 

## <a name="prerequisites"></a><a name="prereq"></a>Requisitos previos

### <a name="azure-stack-edge-pro-with-gpu-installed-and-activated"></a><a name="edge-pro"></a>Azure Stack Edge Pro con GPU instalado y activado

El servicio Azure Network Function Manager está habilitado en el dispositivo Azure Stack Edge Pro. Antes de implementar funciones de red, confirme que Azure Stack Edge Pro está instalado y activado. El recurso de Azure Stack Edge debe implementarse en una región que sea compatible con los recursos de Network Function Manager. Para obtener más información, vea [Disponibilidad en regiones](#regions). Asegúrese de seguir todos los pasos de los [inicios rápidos](../databox-online/azure-stack-edge-gpu-quickstart.md) y los [tutoriales](../databox-online/azure-stack-edge-gpu-deploy-checklist.md) de Azure Stack Edge Pro.

También debe comprobar que el **Estado** del dispositivo,que se encuentra en la sección de propiedades del recurso de Azure Stack Edge en el portal de administración de Azure, sea **En línea**.

:::image type="content" source="./media/overview/properties.png" alt-text="Captura de pantalla de las propiedades." lightbox="./media/overview/properties.png":::

### <a name="partner-prerequisites"></a><a name="partner-prereq"></a>Requisitos previos de los asociados 

Los clientes pueden elegir entre uno o varios [asociados](#partners) de Network Function Manager para implementar su función de red en un dispositivo Azure Stack Edge. Cada asociado tiene requisitos de red para la implementación de su función de red en un dispositivo Azure Stack Edge. Vea la documentación del producto de los asociados de funciones de red para realizar las siguientes tareas de configuración:

* [Configuración de la red en distintos puertos](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
* [Habilitación de la red de proceso en el dispositivo Azure Stack Edge](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network). 


### <a name="azure-account"></a><a name="account"></a>Cuenta de Azure

El servicio Azure Network Function Manager consta de los recursos de **dispositivo** y **función de red** de Network Function Manager. Los recursos de dispositivo y función de red están en suscripciones de Azure. El identificador de suscripción de Azure que se usa para activar el dispositivo Azure Stack Edge Pro y los recursos de Network Function Manager debe ser el mismo. 

Incorpore el identificador de suscripción de Azure de la versión preliminar de Network Function Manager al completar el [formulario de versión preliminar de Azure Function Manager](https://go.microsoft.com/fwlink/?linkid=2163583). En la versión preliminar, los asociados de Azure Network Function Manager deben habilitar el mismo identificador de suscripción de Azure para implementar sus funciones de red desde Azure Marketplace. Asegúrese de que el identificador de suscripción de Azure está incorporado para la versión preliminar en ambos lugares. 

## <a name="resource-provider-registration-and-permissions"></a><a name="permissions"></a>Permisos y registro del proveedor de recursos

Los recursos de Azure Network Function Manager están en el proveedor de recursos Microsoft.HybridNetwork. Una vez incorporado el identificador de suscripción de Azure para la versión preliminar con el servicio Network Function Manager, registre el identificador de suscripción en el proveedor de recursos Microsoft.HybridNetwork. Para obtener más información sobre cómo registrarse, vea [Tipos y proveedores de recursos de Azure](../azure-resource-manager/management/resource-providers-and-types.md).

Las cuentas que se usan para crear el recurso de dispositivo de Network Function Manager deben asignarse a un rol personalizado al que se asignen las acciones necesarias de la tabla siguiente. Para obtener más información, vea [Roles personalizados](../role-based-access-control/custom-roles.md).

| Nombre | Acción|
|---|---|
| Microsoft.DataBoxEdge/dataBoxEdgeDevices/read|Necesario para leer el recurso de Azure Stack Edge en el que se van a implementar las funciones de red. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action |Necesario para leer la sección de propiedades del recurso de Azure Stack Edge. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write |Necesario para crear el recurso de dispositivo de Network Function Manager en el recurso de Azure Stack Edge.|
| Microsoft.HybridNetwork/devices/* | Necesario para crear, actualizar y eliminar el recurso de dispositivo de Network Function Manager. |

Las cuentas que se usan para crear el recurso de aplicaciones administradas de Azure deben asignarse a un [rol personalizado](../role-based-access-control/custom-roles.md) que tenga asignadas las acciones necesarias de la tabla siguiente: 

|Nombre |Acción |
|---|---|
|[Rol Colaborador de la aplicación administrada](../role-based-access-control/built-in-roles.md#managed-application-contributor-role)|Necesario para crear recursos de aplicación administrada.|

## <a name="managed-identity"></a><a name="managed-identity"></a>Identidad administrada 

Los asociados de funciones de red que ofrecen sus aplicaciones administradas de Azure con Network Function Manager proporcionan una experiencia que permite implementar una aplicación administrada que está asociada a un recurso de dispositivo de Network Function Manager existente. Al implementar la aplicación administrada del asociado en Azure Portal, debe proporcionar un recurso de identidad administrada asignada por el usuario de Azure que tenga acceso al recurso de dispositivo de Network Function Manager. La identidad administrada asignada por el usuario permite al proveedor de recursos de aplicación administrada y al editor de la función de red los permisos adecuados para el recurso de dispositivo de Network Function Manager que se implementa fuera del grupo de recursos administrado. Para obtener más información, vea [Administración de una identidad administrada asignada por el usuario en Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Para crear una identidad administrada asignada por el usuario para implementar funciones de red:

1. Cree una identidad administrada asignada por el usuario y asígnela a un rol personalizado con permisos para Microsoft.HybridNetwork/devices/join/action. Para obtener más información, vea [Administración de una identidad administrada asignada por el usuario en Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Proporcione esta identidad administrada al crear la aplicación administrada de un asociado en Azure Portal. Para obtener más información, vea [Asignación de acceso de una identidad administrada a un recurso mediante Azure Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).


## <a name="port-requirements-and-firewall-rules"></a><a name="port-firewall"></a>Requisitos de puertos y reglas de firewall

Los servicios de Network Function Manager (NFM) que se ejecutan en Azure Stack Edge requieren conectividad saliente con el servicio en la nube NFM para que el tráfico de administración implemente funciones de red. NFM está totalmente integrado con el servicio Azure Stack Edge. Revise los requisitos de puertos de redes y las reglas de firewall del dispositivo [Azure Stack Edge](../databox-online/azure-stack-edge-gpu-system-requirements.md#networking-port-requirements).  

Los asociados de funciones de red tienen requisitos diferentes para las reglas de configuración de puertos y firewall para administrar el tráfico al portal de administración de asociados. Consulte al asociado de funciones de red los requisitos específicos.  

## <a name="region-availability"></a><a name="regions"></a>Disponibilidad en regiones

El recurso de Azure Stack Edge, el dispositivo de Azure Network Function Manager y las aplicaciones administradas de Azure de las funciones de red deben estar en la misma región de Azure. El dispositivo físico de Azure Stack Edge Pro GPU no tiene que estar en la misma región. 

* **Disponibilidad de recursos:** en la versión preliminar, los recursos de Network Function Manager están disponibles en las siguientes regiones:

   [!INCLUDE [Preview- available regions](../../includes/network-function-manager-regions-include.md)]

* **Disponibilidad del dispositivo:** para ver una lista de todos los países o regiones donde el dispositivo Azure Stack Edge Pro GPU está disponible, vaya a la página [Precios de Azure Stack Edge Pro GPU](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro). En la pestaña **Azure Stack Edge Pro**, vea la sección  **Disponibilidad** .

Con la versión actual, Network Function Manager es un servicio regional. En caso de interrupciones en toda una región, las operaciones de administración de los recursos de Network Function Manager se verán afectadas, pero las funciones de red que se ejecutan en el dispositivo Azure Stack Edge no. 

## <a name="partner-solutions"></a><a name="partners"></a>Soluciones de asociados

Vea la [página de asociados](partners.md) de Network Function Manager para acceder a un ecosistema creciente de asociados que ofrecen sus aplicaciones administradas de Marketplace para redes móviles privadas, soluciones SD-WAN y VPN.

## <a name="faq"></a><a name="faq"></a>P+F

Para acceder a las preguntas más frecuentes, vea [Preguntas más frecuentes sobre Network Function Manager](faq.md).

## <a name="next-steps"></a>Pasos siguientes

[Creación de un recurso de dispositivo](create-device.md).
