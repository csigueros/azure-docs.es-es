---
title: 'Tutorial: Creación de una red radial'
description: En este tutorial, aprenderá a crear una red radial con Azure Virtual Network Manager. A continuación, protegerá todas las redes virtuales con una directiva de seguridad.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 672b1218a52ee94e226c5b7c763c8e7622b60267
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093387"
---
# <a name="tutorial-create-a-secured-hub-and-spoke-network"></a>Tutorial: Creación de una red radial

En este tutorial, aprenderá a crear una topología en estrella tipo hub-and-spoke con Azure Virtual Network Manager. A continuación, implementará una puerta de enlace de red virtual en la red virtual del concentrador para permitir que los recursos de las redes virtuales radiales se comuniquen con redes remotas mediante VPN. También configurará una opción de seguridad para bloquear el tráfico de red saliente a Internet en los puertos 80 y 443. Por último, comprobará que las configuraciones se aplicaron correctamente; para ello, consulte la configuración de la red virtual y la máquina virtual.

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Crear varias redes virtuales.
> * Implementar una puerta de enlace de red virtual.
> * Crear una topología en estrella tipo hub-and-spoke.
> * Crear una configuración de seguridad que bloquee el tráfico en los puertos 80 y 443.
> * Comprobar que se aplicaron las configuraciones.

## <a name="prerequisite"></a>Requisito previo

* Antes de poder completar los pasos de este tutorial, primero debe [crear una instancia de Azure Virtual Network Manager](create-virtual-network-manager-portal.md).

## <a name="create-virtual-networks"></a>Creación de redes virtuales

Este procedimiento le guiará a través de la creación de tres redes virtuales. Uno estará en la región *Oeste de EE. UU.* y los otros dos estarán en la región *Este de EE. UU.* .

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione **+ Crear un recurso** y busque la **Red virtual**. A continuación, seleccione **Crear** para comenzar a configurar la red virtual.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-vnet.png" alt-text="Captura de pantalla de la creación de una página de red virtual.":::

1. En la pestaña *Aspectos básicos*, escriba o seleccione la siguiente información:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-hub-vnet-basic.png" alt-text="Captura de pantalla de la pestaña de aspectos básicos de la red virtual radial.":::

    | Configuración | Valor |
    | ------- | ----- |
    | Suscripción | Seleccione la suscripción en la que quiere implementar esta red virtual. |
    | Resource group | Seleccione o cree un nuevo grupo de recursos para almacenar la red virtual. En este inicio rápido se usará un grupo de recursos denominado **myAVNMResourceGroup**.
    | Nombre | Escriba **VNet-A-WestUS** en el nombre de la red virtual. |
    | Region | Seleccione la región **Oeste de EE. UU.** |

 1. En la pestaña *Direcciones IP*, configure el siguiente espacio de direcciones de red:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-hub-vnet-addresses.png" alt-text="Captura de pantalla de la pestaña de direcciones IP de la red virtual radial.":::

    | Configuración | Value |
    | -------- | ----- |
    | Espacio de direcciones IPv4 | Escriba **10.3.0.0/16** como espacio de direcciones. |
    | Nombre de subred | Escriba el nombre **predeterminado** para la subred. |
    | Espacio de direcciones de subred | Escriba el espacio de direcciones de subred de **10.3.0.0/24**. |

1. Seleccione **Revisar y crear** y, a continuación, seleccione **Crear** para implementar la red virtual.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-vnet-validation.png" alt-text="Captura de pantalla de la página de validación de la red virtual radial.":::

1. Repita los pasos del 2 al 5 para crear dos redes virtuales más en el mismo grupo de recursos con la siguiente información:

    **Segunda red virtual**:
    * Nombre: **VNet-A-EastUS**
    * Región: **Este de EE. UU.**
    * Espacio de direcciones IPv4: **10.4.0.0/16**
    * Nombre de subred: **predeterminado**
    * Espacio de direcciones de subred: **10.4.0.0/24**

    **Tercera red virtual:**
    * Nombre: **VNet-B-EastUS**
    * Región: **Este de EE. UU.**
    * Espacio de direcciones IPv4: **10.5.0.0/16**
    * Nombre de subred: **predeterminado**
    * Espacio de direcciones de subred: **10.5.0.0/24**

## <a name="deploy-a-virtual-network-gateway"></a>Implementación de una puerta de enlace de red virtual

Implemente una puerta de enlace de red virtual en la red virtual del centro de conectividad. Esta puerta de enlace de red virtual es necesaria para que los radios *usen el centro de conectividad como puerta de enlace*.

1. Seleccione **+ Crear un recurso** y busque la **Puerta de enlace de red virtual**. A continuación, seleccione **Crear** para comenzar a configurar la puerta de enlace de red virtual.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-gateway.png" alt-text="Captura de pantalla de la creación de una página de la puerta de enlace de red virtual.":::

1. En la pestaña *Aspectos básicos*, escriba o seleccione la siguiente configuración:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/gateway-basics.png" alt-text="Captura de pantalla de la creación de una pestaña de aspectos básicos de la puerta de enlace de red virtual." lightbox="./media/tutorial-create-secured-hub-and-spoke/gateway-basics-expanded.png":::

1. Seleccione **Revisar y crear** y, a continuación, seleccione **Crear** una vez que se supere la validación. La implementación de una puerta de enlace de red virtual puede tardar unos 30 minutos. Puede pasar a la sección siguiente mientras espera a que se complete esta implementación.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/gateway-validation.png" alt-text="Captura de pantalla de la creación de una página de validación de la puerta de enlace de red virtual.":::

## <a name="create-a-network-group"></a>Creación de un grupo de red

1. Vaya a la instancia de Azure Virtual Network Manager. En este tutorial se da por hecho que ha creado una con la guía de [inicio rápido](create-virtual-network-manager-portal.md).

1. Seleccione **Grupos de red** en *Configuración* y, a continuación, seleccione **+ Agregar** para crear un nuevo grupo de red.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="Captura de pantalla del botón Agregar un grupo de red.":::

1. En la pestaña *Datos básicos*, especifique la siguiente información:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-basics.png" alt-text="Captura de pantalla de la pestaña Conceptos básicos para crear un grupo de redes.":::

    | Configuración | Valor |
    | ------- | ----- |
    | Nombre | Escriba **myNetworkGroupB** en el nombre del grupo de red. |
    | Descripción | Proporcione una descripción sobre este grupo de red. |

1. Seleccione la pestaña **Instrucciones condicionales**; en el *Parámetro*, seleccione **Nombre** en el menú desplegable. En el *Operador*, seleccione **Contiene**. En la *Condición*, escriba **VNet-** . Esta instrucción condicional agregará las tres redes virtuales creadas anteriormente a este grupo de red.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-conditional.png" alt-text="Captura de pantalla de la pestaña Crear instrucciones condicionales del grupo de red.":::

1. Seleccione **Evaluar** si necesita comprobar las redes virtuales seleccionadas.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/evaluate-vnet.png" alt-text="Captura de pantalla de la página de redes virtuales efectivas.":::

1. Seleccione **Revisar y crear** y, a continuación, seleccione **Crear** una vez superada la validación.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-validation.png" alt-text="Captura de pantalla de la página de creación de la validación de grupos de red.":::

## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>Creación de una configuración de conectividad de tipo hub-and-spoke

1. En **Configuración**, seleccione *Configuración* y, después, **+ Agregar una configuración**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-configuration.png" alt-text="Captura de pantalla del botón Agregar una configuración para Network Manager.":::

1. Seleccione **Conectividad** en el menú desplegable.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Captura de pantalla del menú desplegable de configuración.":::

1. Escriba y seleccione la siguiente información para la configuración de conectividad:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/connectivity-configuration.png" alt-text="Captura de pantalla de la página Agregar una configuración de conectividad.":::

    | Configuración | Valor |
    | ------- | ----- |
    | Nombre | Escriba **HubA** como nombre de la configuración. |
    | Descripción | Proporcione una descripción sobre lo que hará esta configuración de conectividad. |
    | Topología | Seleccione **Radial**. |

1. Al seleccionar la *topología en estrella tipo hub-and-spoke*, aparecerán más campos. Seleccione la siguiente configuración:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/hub-configuration.png" alt-text="Captura de pantalla de la selección de un centro de conectividad para la configuración de la conectividad.":::

    | Configuración | Value |
    | -------- | ----- |
    | Hub | Seleccione **VNet-A-West** como red virtual del centro de conectividad. |
    | Emparejamientos existentes | Deje esta opción **desactivada**. |
    | Grupos de redes radiales | Seleccione **Agregar grupos de red** y agregue **myNetworkGroupB** a la configuración. |

1. Después de agregar el grupo de red, seleccione las siguientes opciones. A continuación, seleccione Agregar para crear la configuración de conectividad.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-options.png" alt-text="Captura de pantalla de las opciones para la configuración del grupo de red.":::

    | Configuración | Valor |
    | ------- | ----- |
    | Transitividad | Active la casilla **Habilitar el emparejamiento del grupo de red**. Esta configuración permitirá que las redes virtuales radiales del grupo de redes de la misma región se comuniquen directamente entre sí. |
    | Malla global | Deje esta opción **desactivada**. Puesto que ambos radios están en la misma región, esta configuración no es necesaria. |
    | Puerta de enlace | Seleccione **Uso del centro de conectividad como puerta de enlace**. |

## <a name="deploy-the-connectivity-configuration"></a>Implementación de la configuración de conectividad

Asegúrese de que la puerta de enlace de red virtual se ha implementado correctamente antes de implementar la configuración de conectividad. Si implementa una configuración radial mediante la opción **Uso del centro de conectividad como puerta de enlace** habilitada y no hay ninguna puerta de enlace, se producirá un error en la implementación. Para obtener más información, consulte [Uso del centro de conectividad como una puerta de enlace](concept-connectivity-configuration.md#use-hub-as-a-gateway).

1. En *Configuración*, seleccione **Implementaciones** y, a continuación, seleccione **Implementar una configuración**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployments.png" alt-text="Captura de pantalla de la página de implementaciones en Network Manager.":::

1. Seleccione el tipo de configuración de **Conectividad** y la configuración de **HubA** que creó en la última sección. A continuación, seleccione **Oeste de EE. UU.** y **Este de EE. UU.** como región de destino y seleccione **Implementar**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deploy-configuration.png" alt-text="Captura de pantalla de la página Implementar una configuración.":::

1. Seleccione **Aceptar** para confirmar que quiere sobrescribir cualquier configuración existente e implementar la configuración de administrador de seguridad. 

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-confirmation.png" alt-text="Captura de pantalla del mensaje de confirmación de la implementación.":::

1. Ahora debería ver que la implementación aparece en la lista de esas regiones. La implementación de la configuración puede tardar entre 15 y 20 minutos en completarse.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="Captura de pantalla de la implementación en curso en la lista de implementación.":::

## <a name="create-security-configuration"></a>Creación de la configuración de seguridad

1. En **Opciones de configuración**, seleccione *Configuración* de nuevo y, a continuación, **+ Agregar una configuración**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-security-configuration.png" alt-text="Captura de pantalla de la adición de otra configuración para Network Manager.":::

1. Seleccione **SecurityAdmin** en el menú para empezar a crear una configuración de SecurityAdmin.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-drop-down.png" alt-text="Captura de pantalla de SecurityAdmin en el menú desplegable.":::

1. Escriba el nombre **mySecurityConfig** para la configuración y, a continuación, seleccione **+ Agregar una colección de reglas**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-admin-configuration.png" alt-text="Captura de pantalla de la página de configuración del administrador de seguridad.":::

1. Escriba el nombre **myRuleCollection** para la colección de reglas y seleccione **myNetworkGroupB** para el grupo de red de destino. Después, seleccione **+ Agregar una regla**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-rule-collection.png" alt-text="Captura de pantalla de la página para agregar una colección de reglas.":::

1. Escriba y seleccione la siguiente configuración y, a continuación, seleccione **Agregar**:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-rule.png" alt-text="Captura de pantalla de la página para agregar una regla.":::

1. Seleccione **Guardar** para agregar la colección de reglas a la configuración.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/save-rule-collection.png" alt-text="Captura de pantalla del botón Guardar para una colección de reglas.":::

1. Seleccione **Agregar** para crear la configuración del administrador de seguridad.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-configuration.png" alt-text="Captura de pantalla del botón Agregar para crear la configuración.":::

## <a name="deploy-the-security-admin-configuration"></a>Implementación de la configuración de administrador de seguridad

1. En *Configuración*, seleccione **Implementaciones** y, a continuación, seleccione **Implementar una configuración**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployments.png" alt-text="Captura de pantalla de la página de implementaciones en Virtual Network Manager.":::

1. Seleccione el tipo de configuración de **SecurityAdmin** y la configuración **mySecurityConfig** que creó en la última sección. A continuación, seleccione **Oeste de EE. UU.** y **Este de EE. UU.** como región de destino y seleccione **Implementar**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deploy-security.png" alt-text="Captura de pantalla de la implementación de una configuración de seguridad.":::

1. Seleccione **Aceptar** para confirmar que quiere sobrescribir cualquier configuración existente e implementar la configuración de administrador de seguridad.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/confirm-security.png" alt-text="Captura de pantalla del mensaje de confirmación para implementar una configuración de seguridad.":::

1. Ahora debería ver que la implementación aparece en la lista de las regiones seleccionadas. La implementación de la configuración puede tardar entre 15 y 20 minutos en completarse.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-in-progress.png" alt-text="Captura de pantalla de la implementación de seguridad en curso en la lista de implementación.":::

## <a name="verify-deployment-of-configurations"></a>Comprobación de la implementación de configuraciones

### <a name="verify-from-a-virtual-network"></a>Comprobación desde una red virtual

1. Vaya a la red virtual **VNet-A-WestUS** y seleccione **Administrador de red** en *Configuración*. Verá que se aplicó la configuración de conectividad **HubA**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-connectivity-configuration.png" alt-text="Captura de pantalla de la configuración de conectividad aplicada a una red virtual.":::

1. En **Configuración**, seleccione *Emparejamiento*. Verá los emparejamientos de red virtual creados por la instancia de Virtual Network Manager con *AVNM* en el nombre.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-peerings.png" alt-text="Captura de pantalla de los emparejamientos de redes virtuales creados por Virtual Network Manager.":::

1. Seleccione la pestaña **SecurityAdmin** para ver las reglas de administración de seguridad aplicadas a esta red virtual.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-admin-configuration.png" alt-text="Captura de pantalla de las reglas de administración de seguridad aplicadas a la red virtual.":::

### <a name="verify-from-a-vm"></a>Comprobación desde una VM

1. Implemente una VM de Windows en **VNet-A-EastUS**. 

1. Vaya a la VM de prueba creada en *VNet-A-EastUS* y seleccione **Redes** en *Configuración*. Seleccione **Reglas del puerto de salida** y verá que se aplica la regla de administración de seguridad.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vm-security-rules.png" alt-text="Captura de pantalla de las reglas de seguridad de red de la VM de prueba.":::

1. Seleccione el nombre de la interfaz de red.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vm-network-settings.png" alt-text="Captura de pantalla de la configuración de red de la VM de prueba.":::

1. A continuación, seleccione **Rutas eficaces** en *Soporte técnico y solución de problemas* para ver las rutas de los emparejamientos de redes virtuales. La ruta `10.3.0.0/16` con el próximo salto de `VNetGlobalPeering` es la ruta a la red virtual del centro de conectividad. La ruta `10.5.0.0/16` con el próximo salto de `ConnectedGroup` es la ruta a la otra red virtual radial. Todas las redes virtuales radiales estarán en un elemento *ConnectedGroup* cuando la **Transitividad** esté habilitada.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/effective-routes.png" alt-text="Captura de pantalla de las rutas eficaces de la interfaz de red de VM de prueba." lightbox="./media/tutorial-create-secured-hub-and-spoke/effective-routes-expanded.png" :::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita Azure Virtual Network Manager, debe asegurarse de que todos los elementos siguientes se cumplen antes de poder eliminar el recurso:

* No hay implementaciones de configuraciones en ninguna región.
* Se han eliminado todas las configuraciones.
* Se han eliminado todos los grupos de red.

Use la [lista de comprobación para quitar componentes](concept-remove-components-checklist.md) y asegurarse de que no haya recursos secundarios disponibles antes de eliminar el grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo bloquear el tráfico de red con una [configuración de administración de seguridad](how-to-block-network-traffic-portal.md).
