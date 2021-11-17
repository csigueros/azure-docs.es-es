---
title: 'Inicio rápido: Creación de una topología de red de malla con Azure Virtual Network Manager mediante Azure Portal.'
description: Use este inicio rápido para aprender a crear una topología de red de malla con Virtual Network Manager mediante Azure Portal.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: template-quickstart, ignite-fall-2021
ms.openlocfilehash: 134f8b28d6505963cde1f44f0b6850643dae3436
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421765"
---
# <a name="quickstart-create-a-mesh-network-topology-with-azure-virtual-network-manager-using-the-azure-portal"></a>Inicio rápido: Creación de una topología de red de malla con Azure Virtual Network Manager mediante Azure Portal.

Introducción a Azure Virtual Network Manager mediante Azure Portal para administrar la conectividad de todas las redes virtuales.

En este inicio rápido, implementará tres redes virtuales y usará Azure Virtual Network Manager para crear una topología de red de malla. A continuación, comprobará si se aplicó la configuración de conectividad.

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="register-subscription-for-public-preview"></a>Registro de la suscripción en la versión preliminar pública

1. Vaya a la página [Características en versión preliminar](https://portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade).

1. Busque **AllowAzureNetworkManager**.

1. Seleccione la casilla situada junto al elemento *AllowAzureNetworkManager* y, a continuación, seleccione **+ Registrarse**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/registration.png" alt-text="Captura de pantalla de la página de registro en las características en versión preliminar.":::

## <a name="create-virtual-network-manager"></a>Creación de un administrador de redes virtuales

1. Seleccione **+ Crear un recurso** y busque **Network Manager**. A continuación, seleccione **Crear** para empezar a configurar Azure Virtual Network Manager.

1. En la pestaña *Aspectos básicos*, escriba o seleccione la siguiente información:

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-manager-basics.png" alt-text="Captura de pantalla de la página de conceptos básicos de Network Manager.":::

    | Configuración | Value |
    | ------- | ----- |
    | Subscription | Seleccione la suscripción en la que desea implementar Azure Virtual Network Manager. |
    | Resource group | Seleccione o cree un grupo de recursos para almacenar Azure Virtual Network Manager. En este ejemplo se usará **myAVNMResourceGroup** creado anteriormente.
    | Name | Escriba un nombre para esta instancia de Azure Virtual Network Manager. En este ejemplo, usaremos el nombre de **myAVNM**. |
    | Region | Seleccione la región para esta implementación. Azure Virtual Network Manager puede administrar redes virtuales en cualquier región. La región seleccionada es para donde se implementará la instancia de Virtual Network Manager. |
    | Descripción | *(Opcional)* Proporcione una descripción sobre esta instancia de Virtual Network Manager y la tarea que va a administrar. |
    | [Ámbito](concept-network-manager-scope.md#scope) | Defina el ámbito para el que se puede administrar Azure Virtual Network Manager.
    | [Características](concept-network-manager-scope.md#features) | Seleccione las características que quiere habilitar para Azure Virtual Network Manager. Las características disponibles son *Connectivity* (Conectividad), *SecurityAdmin* o *Select All* (Seleccionar todo). </br> Connectivity (Conectividad): permite crear una topología de red en estrella tipo hub-and-spoke completa o de malla entre redes virtuales dentro del ámbito. </br> SecurityAdmin: permite crear reglas de seguridad de red globales. |

1. Seleccione **Revisar y crear** y después **Crear** una vez superada la validación.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-manager-validation.png" alt-text="Captura de pantalla de la página de validación para crear un recurso de Network Manager.":::

## <a name="create-three-virtual-networks"></a>Creación de tres redes virtuales

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione **+ Crear un recurso** y busque la **red virtual**. A continuación, seleccione **Crear** para empezar a configurar la red virtual.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-vnet.png" alt-text="Captura de pantalla de la creación de una página de red virtual.":::

1. En la pestaña *Aspectos básicos*, escriba o seleccione la siguiente información:

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-mesh-vnet-basic.png" alt-text="Captura de pantalla de la creación de una página de red virtual.":::

    | Configuración | Value |
    | ------- | ----- |
    | Subscription | Seleccione la suscripción en la que desea implementar esta red virtual. |
    | Resource group | Seleccione o cree un nuevo grupo de recursos para almacenar la red virtual. En este inicio rápido se usará un nuevo grupo de recursos denominado **myAVNMResourceGroup**.
    | Name | Escriba **VNetA** como nombre de la red virtual. |
    | Region | Seleccione **Oeste de EE. UU.** |

1. En la página **Siguiente: Direcciones IP >** y configure los siguientes espacios de direcciones de red:

     :::image type="content" source="./media/create-virtual-network-manager-portal/create-mesh-vnet-ip.png" alt-text="Captura de pantalla de la página de creación de las direcciones IP de una red virtual":::.

    | Configuración | Value |
    | -------- | ----- |
    | Espacio de direcciones IPv4 | 10.0.0.0/16 |
    | Nombre de subred | default |
    | Espacio de direcciones de subred | 10.0.0.0/24 |

1. Seleccione **Revisar y crear** y después **Crear** una vez superada la validación para implementar la red virtual.

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-validation.png" alt-text="Captura de pantalla de la página de validación para crear una red virtual.":::

1. Repita los pasos 2 a 5 para crear dos redes virtuales más con la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | Subscription | Seleccione la misma suscripción que seleccionó en el paso 3. |
    | Resource group | Seleccione **myAVNMResourceGroup**. |
    | Name | Escriba **VNetB** para la segunda red virtual y **VNetC** para la tercera red virtual. |
    | Region | La región se seleccionará automáticamente al seleccionar el grupo de recursos. |
    | Direcciones IP de VNetB | Espacio de direcciones IPv4: 10.1.0.0/16 </br> Nombre de subred: predeterminado </br> Espacio de direcciones de subred: 10.1.0.0/24|
    | Direcciones IP de VNetC | Espacio de direcciones IPv4: 10.2.0.0/16 </br> Nombre de subred: predeterminado </br> Espacio de direcciones de subred: 10.2.0.0/24|

## <a name="create-a-network-group"></a>Creación de un grupo de red

1. Vaya la instancia de Azure Virtual Network Manager que ha creado.

1. Seleccione **Grupos de red** en *Configuración* y, a continuación, **+ Agregar**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-network-group.png" alt-text="Captura de pantalla del botón para agregar un grupo de red.":::

1. En la página *Agregar un grupo de red*, escriba un **nombre** para el grupo de red. En este ejemplo se usará el nombre **myNetworkGroup**. Seleccione **Siguiente: Miembros del grupo estático >** para empezar a agregar redes virtuales al grupo de red.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-group-basics.png" alt-text="Captura de pantalla de la pestaña Conceptos básicos de la creación de un grupo de red.":::

1. En la pestaña *Miembros del grupo estático*, seleccione **+ Agregar redes virtuales**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-virtual-networks-button.png" alt-text="Captura de pantalla del botón para agregar una red virtual":::.

1. En la página *Agregar redes virtuales*, seleccione las tres redes virtuales creadas anteriormente (VNetA, VNetB y VNetC). A continuación, seleccione **Agregar** para confirmar la selección.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-virtual-networks.png" alt-text="Captura de pantalla de la página Agregar redes virtuales a un grupo de red":::.

1. Seleccione **Revisar y crear** y luego seleccione **Crear** una vez superada la validación.

    :::image type="content" source="./media/create-virtual-network-manager-portal/review-create.png" alt-text="Captura de pantalla del botón Revisar y crear para un nuevo grupo de red":::.

1. Verá que el nuevo grupo de red se ha agregado a la página *Grupos de red*.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-groups-list.png" alt-text="Captura de pantalla de la página de grupos de red con el nuevo grupo de red agregado":::.

## <a name="create--a-connectivity-configuration"></a>Creación de una configuración de conectividad

1. En **Configuración**, seleccione *Configuración* y, después, **+ Agregar una configuración**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-configuration.png" alt-text="Captura de pantalla de la adición de un botón de configuración para Network Manager.":::

1. Seleccione **Conectividad** en el menú desplegable para empezar a crear una configuración de conectividad.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Captura de pantalla del menú desplegable de configuración.":::

1. En la página *Agregar una configuración de conectividad*, escriba o seleccione la información siguiente:

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration.png" alt-text="Captura de pantalla de la página Agregar una configuración de conectividad.":::

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba un nombre para esta configuración de conectividad. |
    | Descripción | *(Opcional)* Proporcione una descripción sobre esta configuración de conectividad. |
    | Topología | Seleccione el tipo de topología que quiere crear con esta configuración. En este ejemplo, usaremos la topología **Mesh** (Malla). |

1. Cuando seleccione la topología *Mesh* (Malla) aparecerá la opción **Global Mesh** (Malla global) y **Network Groups** (Grupos de red). *Global Mesh* (Malla global) no es necesario para esta configuración, ya que todas las redes virtuales están en la misma región. Seleccione **+ Agregar grupos de red** y, a continuación, seleccione el grupo de red que creó en la última sección. Haga clic en **Seleccionar** para agregar el grupo de red a la configuración.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-network-group-configuration.png" alt-text="Captura de pantalla de la adición de un grupo de red a una configuración de conectividad":::.

1. Seleccione **Agregar** para crear la configuración.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-connectivity-configuration.png" alt-text="Captura de pantalla de la página para crear una configuración de conectividad.":::

1. Verá la nueva configuración de conectividad agregada a la página *Configuración*.

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration-list.png" alt-text="Captura de pantalla de la lista de configuración de conectividad":::.

## <a name="deploy-the-connectivity-configuration"></a>Implementación de la configuración de conectividad

Para que las configuraciones se apliquen a su entorno, deberá confirmar la configuración por implementación. Deberá implementar la configuración en la región **Oeste de EE. UU.** donde se implementan las redes virtuales.

1. En *Configuración*, seleccione **Implementaciones** y, a continuación, seleccione **Implementar una configuración**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployments.png" alt-text="Captura de pantalla de la página de implementaciones en Network Manager":::.

1. Seleccione la siguiente configuración:

    :::image type="content" source="./media/create-virtual-network-manager-portal/deploy-configuration.png" alt-text="Captura de pantalla de la página Implementar una configuración.":::

    | Configuración | Value |
    | ------- | ----- |
    | Tipo de configuración | Seleccione el tipo de configuración que desea implementar. En este ejemplo, se implementará una configuración de **conectividad**. |
    | Configuraciones | Seleccione la configuración **myConnectivityConfig** creada en la sección anterior. |
    | Regiones de destino | Seleccione la región en la que implementar esta configuración. Se selecciona la región **Oeste de EE. UU.** , ya que todas las redes virtuales se crearon en esa región. |

1. Seleccione **Implementar** y, a continuación, seleccione **Aceptar** para confirmar que desea sobrescribir cualquier configuración existente.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-confirmation.png" alt-text="Captura de pantalla del mensaje de confirmación de implementación":::.

1. Ahora debería ver que la implementación aparece en la lista de la región seleccionada. La implementación de la configuración puede tardar entre 15 y 20 minutos en completarse.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-in-progress.png" alt-text="Captura de pantalla del estado de implementación de configuración en curso":::.

## <a name="confirm-configuration-deployment"></a>Confirmación de la implementación de la configuración

1. Seleccione **Actualizar** en la página *Implementación* para ver el estado actualizado de la configuración que ha confirmado.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-status.png" alt-text="Captura de pantalla del botón actualizar para el estado de implementación actualizado":::.

1. Vaya a la red virtual **VNetA** y seleccione **Administrador de redes** en *Configuración*. Verá la configuración que implementó con Azure Virtual Network Manager asociada a la red virtual.

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-configuration-association.png" alt-text="Captura de pantalla de la configuración de conectividad asociada a la red virtual VNetA.":::

1. También puede confirmar lo mismo para **VNetB** y **VNetC**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita Azure Virtual Network Manager, deberá asegurarse de que todo lo siguiente es cierto antes de poder eliminar el recurso:

* No hay ninguna configuración implementada en ninguna región.
* Se han eliminado todas las configuraciones.
* Se han eliminado todos los grupos de red.

1. Para quitar todas las configuraciones de una región, implemente una configuración **None** (Ninguna)en la región de destino. Seleccione **Implementar** y después **Aceptar** para confirmar.

    :::image type="content" source="./media/create-virtual-network-manager-portal/none-configuration.png" alt-text="Captura de pantalla de la implementación de una configuración de conectividad None":::.

1. Para eliminar una configuración, seleccione **Configuraciones** en *Configuración* en el panel izquierdo de Azure Virtual Network Manager. Active la casilla situada junto a la configuración que desea quitar y, a continuación, seleccione **Eliminar** en la parte superior de la página de recursos.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-configuration.png" alt-text="Captura de pantalla del botón Eliminar para una configuración de conectividad":::.

1. Para eliminar un grupo de red, seleccione **Grupos de red** en *Configuración* en el panel izquierdo de Azure Virtual Network Manager. Active la casilla situada junto al grupo de red que desea quitar y, a continuación, seleccione **Eliminar** en la parte superior de la página de recursos.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-network-group.png" alt-text="Captura de pantalla del botón Eliminar para el grupo de red":::.

1. Una vez quitados todos los grupos de red, ahora puede eliminar el recurso haciendo clic con el botón derecho en Azure Virtual Network Manager de la lista y seleccionando **Eliminar**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-network-manager.png" alt-text="Captura de pantalla del botón Eliminar de una instancia de Azure Virtual Network Manager":::.

1. Para eliminar el grupo de recursos, busque el grupo de recursos y seleccione **Eliminar el grupo de recursos**. Para confirmar que desea eliminar, escriba el nombre del grupo de recursos y, a continuación, seleccione **Eliminar**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-resource-group.png" alt-text="Captura de pantalla del botón Eliminar de un grupo de recursos":::.

## <a name="next-steps"></a>Pasos siguientes

Después de crear la instancia de Azure Virtual Network Manager, siga avanzando para aprender a bloquear el tráfico mediante la configuración de un administrador de seguridad:

> [!div class="nextstepaction"]
> [Bloqueo del tráfico de red con reglas de administración de seguridad](how-to-block-network-traffic-portal.md)
