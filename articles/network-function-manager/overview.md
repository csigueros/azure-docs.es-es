---
title: Acerca de Azure Network Function Manager
description: Obtenga información sobre Azure Network Function Manager, un servicio de orquestación nativo de nube totalmente administrado que permite implementar y aprovisionar funciones de red en Azure Stack Edge Pro con GPU para una experiencia híbrida coherente mediante Azure Portal.
author: prmitt
ms.service: network-function-manager
ms.topic: overview
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 55ea5ebe1dbeeb0d3e1d17c2085c19d471396b21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024114"
---
# <a name="what-is-azure-network-function-manager"></a>¿Qué es Azure Network Function Manager?

Azure Network Function Manager ofrece una experiencia de [Azure Marketplace](https://azure.microsoft.com/marketplace/) para implementar funciones de red, como redes troncales de paquetes móviles, dispositivos perimetrales SD-WAN y servicios VPN, en el [dispositivo Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) que se ejecuta en el entorno local. Ahora puede implementar rápidamente un servicio de red móvil privado o una solución SD-WAN en el dispositivo perimetral directamente desde el portal de administración de Azure. Network Function Manager incorpora funciones de red de un ecosistema creciente de [asociados](#partners). Network Function Manager se admite en [Azure Stack Edge Pro con GPU](../databox-online/azure-stack-edge-gpu-overview.md).

## <a name="features"></a><a name="features"></a> Características

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

## <a name="region-availability"></a><a name="regions"></a>Disponibilidad en regiones

El recurso de Azure Stack Edge, el dispositivo de Azure Network Function Manager y las aplicaciones administradas de Azure de las funciones de red deben estar en la misma región de Azure. El dispositivo físico de Azure Stack Edge Pro GPU no tiene que estar en la misma región.

* **Disponibilidad de recursos:** los recursos de Network Function Manager están disponibles en las siguientes regiones:

   [!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

* **Disponibilidad del dispositivo:** para ver una lista de todos los países o regiones donde el dispositivo Azure Stack Edge Pro GPU está disponible, vaya a la página [Precios de Azure Stack Edge Pro GPU](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro). En la pestaña **Azure Stack Edge Pro**, vea la sección  **Disponibilidad** .

Con la versión actual, Network Function Manager es un servicio regional. En caso de interrupciones en toda una región, las operaciones de administración de los recursos de Network Function Manager se verán afectadas, pero las funciones de red que se ejecutan en el dispositivo Azure Stack Edge no.

## <a name="partner-solutions"></a><a name="partners"></a>Soluciones de asociados

Vea la [página de asociados](partners.md) de Network Function Manager para acceder a un ecosistema creciente de asociados que ofrecen sus aplicaciones administradas de Marketplace para redes móviles privadas, soluciones SD-WAN y VPN.

## <a name="faq"></a><a name="faq"></a>P+F

Para acceder a las preguntas más frecuentes, vea [Preguntas más frecuentes sobre Network Function Manager](faq.md).

## <a name="next-steps"></a>Pasos siguientes

[Creación de un recurso de dispositivo](create-device.md).
