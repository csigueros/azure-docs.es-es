---
title: Red virtual administrada
description: En este artículo se explica la red virtual administrada en Azure Synapse Analytics.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/16/2021
ms.author: seshin
ms.reviewer: wiassaf
ms.openlocfilehash: 9866a2c773193cc20bd6b9e193e025fa65eddcc6
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446211"
---
# <a name="azure-synapse-analytics-managed-virtual-network"></a>Red virtual administrada de Azure Synapse Analytics

En este artículo se explica la red virtual administrada en Azure Synapse Analytics.

## <a name="managed-workspace-virtual-network"></a>Red virtual del área de trabajo administrada

Al crear un área de trabajo de Azure Synapse, puede elegir asociarla a Microsoft Azure Virtual Network. La red virtual asociada al área de trabajo se administra mediante Azure Synapse. Esta red virtual se denomina *red virtual de área de trabajo administrada*.

La red virtual de área de trabajo administrada aporta valor de cuatro formas:

- Con una red virtual de área de trabajo administrada puede dejar que Azure Synapse se ocupe de la pesada tarea de administrar la red virtual.
- No es necesario configurar reglas de grupo de seguridad de red de entrada en sus propias redes virtuales para permitir que el tráfico de administración de Azure Synapse entre en la red virtual. La configuración incorrecta de estas reglas de grupo de seguridad de red provoca la interrupción del servicio para los clientes.
- No es necesario crear una subred para los clústeres de Spark en función de la carga máxima.
- La red virtual de área de trabajo administrada, junto con los puntos de conexión privados administrados, sirven de protección contra la filtración de datos. Solo se pueden crear puntos de conexión privados administrados en áreas de trabajo que tengan asociadas una red virtual de área de trabajo administrada.

La creación de un área de trabajo con una red virtual de área de trabajo administrada asociada garantiza que el área de trabajo esté aislada por la red de otras áreas de trabajo. Azure Synapse ofrece varias funcionalidades de análisis en un área de trabajo: Integración de datos, grupo de Apache Spark sin servidor, grupo de SQL dedicado y grupo de SQL sin servidor.

Si el área de trabajo tiene una red virtual de área de trabajo administrada, en ella se implementan la integración de datos y los recursos de Spark. Una red virtual de área de trabajo administrada también proporciona aislamiento en el nivel de usuario para las actividades de Spark, ya que cada clúster de Spark está en su propia subred.

El grupo de SQL dedicado y el grupo de SQL sin servidor son funcionalidades de varios inquilinos y, por tanto, residen fuera de la red virtual de área de trabajo administrada. En la comunicación dentro del área de trabajo con el grupo de SQL dedicado y el grupo de SQL sin servidor se usan vínculos privados de Azure. Estos vínculos privados se crean automáticamente cuando se crea un área de trabajo con una red virtual de área de trabajo administrada asociada.

>[!IMPORTANT]
>Esta configuración del área de trabajo no se puede cambiar una vez creada el área de trabajo. Por ejemplo, no puede volver a configurar un área de trabajo que no tenga una red virtual de área de trabajo administrada asociada y asociarle una red virtual. De igual modo, no puede volver a configurar un área de trabajo que no tenga una red virtual de área de trabajo administrada asociada y desasociarle la red virtual.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Creación de un área de trabajo de Azure Synapse con una red virtual de área de trabajo administrada

Si aún no lo ha hecho, registre el proveedor de recursos de red. Al registrar un proveedor de recursos se configura la suscripción para que funcione con este. Elija *Microsoft.Network* en la lista de proveedores de recursos al [registrarse](../../azure-resource-manager/management/resource-providers-and-types.md).

Para crear un área de trabajo de Azure Synapse que tenga una red virtual de área de trabajo administrada asociada, seleccione la pestaña **Redes** en Azure Portal y active la casilla **Enable managed virtual network** (Habilitar red virtual administrada).

Si deja la casilla desactivada, el área de trabajo no tendrá asociada ninguna red virtual.

>[!IMPORTANT]
>En un área de trabajo que tenga una red virtual de área de trabajo administrada solo se pueden usar vínculos privados.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-networking-managed-virtual-network-outbound-traffic.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-networking-managed-virtual-network-outbound-traffic.png" alt-text="Captura de pantalla de la página de redes de Crear área de trabajo de Synapse, con la opción Red virtual administrada habilitada y la opción Permitir el tráfico de datos saliente solo a destinos aprobados establecida en Sí.":::

Después de elegir asociar una red virtual de área de trabajo administrada con el área de trabajo, puede permitir la conectividad de salida desde dicha red solo a destinos aprobados mediante [puntos de conexión privados administrados](./synapse-workspace-managed-private-endpoints.md) con el fin de protegerse contra la filtración de datos. Seleccione **Sí** para limitar el tráfico saliente de la red virtual de área de trabajo administrada hacia los destinos mediante puntos de conexión privados administrados. 



:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-allow-outbound-traffic.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-allow-outbound-traffic.png" alt-text="Captura de pantalla de la página Red virtual administrada, con la opción Permitir el tráfico de datos salientes solo a los destinos aprobados establecida en Sí.":::

Seleccione **No** para permitir el tráfico saliente desde el área de trabajo a cualquier destino.

También puede controlar los destinos en los que se crean puntos de conexión privados administrados desde el área de trabajo de Azure Synapse. De forma predeterminada, se permiten los puntos de conexión privados administrados a los recursos del mismo inquilino de AAD a los que pertenezca su suscripción. Si quiere crear un punto de conexión privado administrado a un recurso de un inquilino de AAD diferente de aquel al que pertenece la suscripción, puede agregar ese inquilino de AAD mediante **+ Agregar**. Puede seleccionar el inquilino de AAD de la lista desplegable o escribir manualmente el identificador de inquilino de AAD.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-private-endpoints-azure-ad.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-private-endpoints-azure-ad.png" alt-text="Captura de pantalla de la página Red virtual administrada, con el botón Agregar para inquilinos de Azure resaltado.":::

Después de crear el área de trabajo, puede comprobar si el área de trabajo de Azure Synapse está asociada a una red virtual de área de trabajo administrada; para ello, seleccione **Información general** en Azure Portal.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-overview-managed-virtual-network-enabled.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-overview-managed-virtual-network-enabled.png" alt-text="Captura de pantalla de la información general del área de trabajo de Azure Synapse que indica que está habilitada una red virtual administrada.":::

## <a name="next-steps"></a>Pasos siguientes

Creación de un [área de trabajo de Azure Synapse](../quickstart-create-workspace.md)

Más información sobre los [puntos de conexión privados administrados](./synapse-workspace-managed-private-endpoints.md)

[Creación de puntos de conexión privados administrados a los orígenes de datos](./how-to-create-managed-private-endpoints.md)
