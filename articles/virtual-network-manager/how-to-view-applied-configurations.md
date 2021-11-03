---
title: Visualización de las configuraciones que aplicó Azure Virtual Network Manager (versión preliminar)
description: Aprenda a ver las configuraciones que aplicó Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0d0f0d0ed5eb100aff4df02a65ab4902da1b7c16
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091177"
---
# <a name="view-configurations-applied-by-azure-virtual-network-manager-preview"></a>Visualización de las configuraciones que aplicó Azure Virtual Network Manager (versión preliminar)

Azure Virtual Network Manager proporciona varias maneras de comprobar si las configuraciones se aplican correctamente. En este artículo, veremos cómo puede comprobar las configuraciones aplicadas tanto a nivel de red virtual como de máquina virtual. También veremos las operaciones que realizará en el registro de actividad.

## <a name="virtual-network-visibility"></a>Visibilidad de la red virtual

Una vez que Virtual Network Manager haya implementado la configuración, puede verla aplicada desde el recurso de red virtual. 

1. Vaya al recurso de red virtual y seleccione **Administrador de redes** en *Configuración*. En la pestaña Conectividad, verá todas las configuraciones de conectividad a las que está asociada la red virtual. 

    :::image type="content" source="./media/how-to-view-applied-configurations/vnet-connectivity.png" alt-text="Captura de pantalla de la configuración de conectividad asociada a una red virtual.":::

2. Seleccione la pestaña **SecurityAdmin** para ver todas las reglas de seguridad aplicadas actualmente a la red virtual.

    :::image type="content" source="./media/how-to-view-applied-configurations/vnet-security.png" alt-text="Captura de pantalla de las reglas de seguridad asociadas a una red virtual.":::

## <a name="virtual-machine-visibility"></a>Visibilidad de la máquina virtual

En el nivel de máquina virtual, puede ver las reglas de seguridad que aplicó Virtual Network Manager y las rutas más eficaces para las configuraciones de conectividad.

### <a name="applied-security-rules"></a>Reglas de seguridad aplicadas

1. Vaya a la máquina virtual de una red virtual que tenga una configuración que haya aplicado Virtual Network Manager. A continuación, seleccione **Redes** en *Configuración* en el menú de la izquierda.

    :::image type="content" source="./media/how-to-view-applied-configurations/virtual-machine.png" alt-text="Captura de pantalla de la página de información general de la máquina virtual.":::

1. Verá una lista de grupos de seguridad de red entrantes y también una sección para las reglas de seguridad de entrada que aplicó Virtual Network Manager.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-inbound-rules.png" alt-text="Captura de pantalla de las reglas de seguridad de salida de máquinas virtuales.":::

1. Seleccione la pestaña **Reglas de puerto de salida** para ver las reglas de seguridad de salida de la máquina virtual.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-outbound-rules.png" alt-text="Captura de pantalla de las reglas de seguridad de entrada de máquinas virtuales.":::

### <a name="effective-routes"></a>Rutas eficaces

1. Para ver las rutas efectivas de la configuración de conectividad aplicada, seleccione el nombre de la interfaz de red en la configuración *Redes* de la máquina virtual.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-network-interface.png" alt-text="Captura de pantalla de la selección de la tarjeta de interfaz de red de la máquina virtual.":::

1. A continuación, seleccione **Rutas eficaces** en *Soporte técnico y solución de problemas*.

    :::image type="content" source="./media/how-to-view-applied-configurations/network-interface.png" alt-text="Captura de pantalla del botón rutas efectivas de una tarjeta de interfaz de red de máquina virtual.":::

1. Las rutas con el tipo de próximo salto *ConnectedGroup* forman parte de la configuración de malla o cuando la [*conectividad directa*](concept-connectivity-configuration.md#direct-connectivity) está habilitada para un grupo de red. Las rutas entre las redes virtuales de tipo hub-and-spoke aparecerán como un tipo de próximo salto *VNetPeering* o *GlobalVNetPeering*.

    :::image type="content" source="./media/how-to-view-applied-configurations/effective-routes.png" alt-text="Captura de pantalla de las rutas eficaces que muestran los grupos conectados y las rutas de concentrador." lightbox="./media/how-to-view-applied-configurations/effective-routes-expanded.png":::

    > [!NOTE]
    > El espacio de direcciones de la red virtual del centro de conectividad también se **incluye** en *ConnectedGroup*. Por lo tanto, si se produce un error en el emparejamiento de redes virtuales entre las redes virtuales de tipo hub-and-spoke, pueden comunicarse entre sí porque están en un grupo conectado.
    > 

### <a name="effective-security-rules"></a>Reglas de seguridad vigentes

1. Para ver reglas de seguridad efectivas de una configuración de reglas de seguridad aplicada, seleccione el nombre de la interfaz de red en la configuración de *Redes* de la máquina virtual.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-network-interface.png" alt-text="Captura de pantalla de la selección de la tarjeta de interfaz de red de la máquina virtual de las reglas de seguridad.":::

1. A continuación, seleccione **Reglas de seguridad efectivas** en *Soporte técnico y solución de problemas*.

    :::image type="content" source="./media/how-to-view-applied-configurations/network-interface-security-rules.png" alt-text="Captura de pantalla del botón de las reglas de seguridad vigentes para una tarjeta de interfaz de red de VM.":::

1. Seleccione el nombre de la instancia de Azure Virtual Network Manager para ver las reglas de administración de seguridad asociadas a la máquina virtual.

    :::image type="content" source="./media/how-to-view-applied-configurations/effective-security-rules.png" alt-text="Captura de pantalla de las reglas de seguridad vigentes asociadas a la máquina virtual.":::

## <a name="activity-log"></a>Registro de actividad

Puede ver el registro de actividad del recurso de Azure Virtual Network Manager para ver los cambios que usted o el administrador de red hayan realizado. Para ver el registro de actividad, vaya al recurso de Network Manager en Azure Portal. Seleccione **Registro de actividades** en el menú del panel izquierdo. Si es necesario, ajuste el *Intervalo de tiempo* y agregue más filtros para restringir la lista de operaciones. También puede ver el *registro de actividad* si busca el servicio en la parte superior de Azure Portal.

:::image type="content" source="./media/how-to-view-applied-configurations/activity-log.png" alt-text="Captura de pantalla de la página del registro de actividad de Network Manager.":::

### <a name="list-of-operations"></a>Lista de operaciones

La lista siguiente contiene las operaciones que verá en el registro de actividad:

| Nombre | Descripción |
| ---- | ----------- |
| Commit | La implementación de una configuración se ha confirmado en una región. |
| Eliminación de ConnectivityConfiguration | Eliminación de una configuración de conectividad de Network Manager. |
| Eliminación de NetworkGroups | Eliminación de un grupo de red de Network Manager.|
| Eliminar reglas | Eliminación de una regla de una colección de reglas. |
| Eliminación de RuleCollections | Eliminación de una colección de reglas de una configuración de administrador de seguridad. |
| Eliminación de SecurityAdminConfigurations | Eliminación de una configuración de administrador de seguridad de Network Manager. |
| ListDeploymentStatus | Permite ver el estado de implementación de una configuración de administración de seguridad o conectividad. |
| ListActiveConnectivityConfiguration | Permite ver la lista de configuraciones de conectividad aplicadas a la red virtual.|
| ListActiveSecurityAdminRules | Permite ver la lista de configuraciones de seguridad del administrador aplicadas a la red virtual. |
| Escritura de ConnectivityConfiguration. | Creación de una nueva configuración de conectividad. |
| Escritura de NetworkGroups | Creación de un nuevo grupo de red. |
| Escritura de NetworkManager | Creación de una nueva instancia de Azure Virtual Network Manager. |
| Reglas de escritura | Creación de una nueva regla de seguridad para agregarla a una colección de reglas. |
| Escritura de RuleCollections | Creación de una nueva colección de reglas para agregarla a una configuración de administrador de seguridad. |
| Escritura de SecurityAdminConfiguration | Creación de una nueva configuración de administrador de seguridad. |

## <a name="next-steps"></a>Pasos siguientes

- Cree una instancia de [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) con Azure Portal.
- Consulte [Preguntas más frecuentes (P+F) sobre Network Manager](faq.md) para ver las preguntas más frecuentes.
