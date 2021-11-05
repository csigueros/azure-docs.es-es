---
title: Creación de una topología en estrella tipo hub-and-spoke con Azure Virtual Network Manager (Versión preliminar)
description: Aprenda a crear una topología de red en estrella de tipo hub-and-spoke con Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: c07eccfda6492a5957ce9d2f0df6e0133e5b9d3d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093100"
---
# <a name="create-a-hub-and-spoke-topology-with-azure-virtual-network-manager-preview"></a>Creación de una topología en estrella tipo hub-and-spoke con Azure Virtual Network Manager (Versión preliminar)

En este artículo, aprenderá a crear una topología en estrella tipo hub-and-spoke con Azure Virtual Network Manager. Con esta configuración, debe seleccionar una red virtual para que actúe como concentrador y todas las redes virtuales radiales tendrán de forma predeterminada una emparejamiento bidireccional solo con el centro de conectividad. También puede habilitar la conectividad directa entre redes virtuales radiales y permitir que estas usen la puerta de enlace de red virtual en el centro de conectividad.

> [!IMPORTANT]
> *Azure Virtual Network Manager* se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [**Condiciones de uso complementarias de las versiones preliminares de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerrequisitos

* Obtenga información sobre las topologías de red en estrella tipo [hub-and-spoke](concept-connectivity-configuration.md#hub-and-spoke-topology).
* Ha creado una [instancia de Azure Virtual Network Manager](create-virtual-network-manager-portal.md#create-virtual-network-manager).
* Identifique las redes virtuales que quiere usar en la configuración de tipo hub-and-spoke o cree nuevas [redes virtuales](../virtual-network/quick-create-portal.md). 

## <a name="create-a-network-group"></a><a name="group"></a> Creación de un grupo de red

Esta sección le ayudará a crear un grupo de redes que contenga las redes virtuales que va a usar para la topología en estrella tipo hub-and-spoke.

1. Vaya a la instancia de Azure Virtual Network Manager. En esta guía paso a paso se da por supuesto que ha creado una con la guía de [inicio rápido](create-virtual-network-manager-portal.md).

1. Seleccione **Grupos de red** en *Configuración* y, a continuación, seleccione **+ Agregar** para crear un nuevo grupo de red.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="Captura de pantalla del botón Agregar un grupo de red.":::

1. En la pestaña *Básicos*, escriba un **Nombre** y una **Descripción** para el grupo de red.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/basics.png" alt-text="Captura de pantalla de la pestaña Básicos para agregar un grupo de red.":::

1. Para agregar la red virtual manualmente, seleccione la pestaña **Miembros del grupo estático**. Para obtener más información, consulte [Miembros estáticos](concept-network-groups.md#static-membership).

    :::image type="content" source="./media/how-to-create-hub-and-spoke/static-group.png" alt-text="Captura de pantalla de la pestaña Miembros del grupo estático.":::

1. Para agregar redes virtuales dinámicamente, seleccione la pestaña **Instrucciones condicionales**. Para obtener más información, consulte [Pertenencia dinámica](concept-network-groups.md#dynamic-membership).

    :::image type="content" source="./media/how-to-create-hub-and-spoke/conditional-statements.png" alt-text="Captura de pantalla de la pestaña Instrucciones condicionales.":::

1. Una vez que esté satisfecho con las redes virtuales seleccionadas para el grupo de red, seleccione **Revisar y crear**. A continuación, seleccione **Crear** una vez que se pase la validación.
 
## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>Creación de una configuración de conectividad de tipo hub-and-spoke

Esta sección le guiará a través de los detalles para crear una configuración de tipo hub-and-spoke con el grupo de red que creó en la sección anterior.

1. En **Configuración**, seleccione *Configuración* y, después, **+ Agregar una configuración**.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/configuration-list.png" alt-text="Captura de pantalla de la lista de configuraciones.":::

1. Seleccione **Conectividad** en el menú desplegable.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Captura de pantalla del menú desplegable de configuración.":::

1. En la página *Agregar una configuración de conectividad*, escriba o seleccione la información siguiente:

    :::image type="content" source="./media/how-to-create-hub-and-spoke/connectivity-configuration.png" alt-text="Captura de pantalla de la página Agregar una configuración de conectividad.":::

    | Configuración | Valor |
    | ------- | ----- |
    | Nombre | Escriba un *nombre* para esta configuración. |
    | Descripción | *Opcional* Escriba una descripción sobre lo que hará esta configuración. |
    | Topología | Seleccione la topología **en estrella tipo hub-and-spoke**. |
    | Hub | Seleccione una red virtual que actuará como la red virtual del concentrador. |
    | Emparejamientos existentes | Active esta casilla si desea quitar todos los emparejamientos de VNet creados anteriormente entre las redes virtuales del grupo de red definido en esta configuración. |

1. A continuación, seleccione **+ Agregar grupos de red**. 

1. En la página *Agregar grupos de red*, seleccione los grupos de red que quiere agregar a esta configuración. Seguidamente, seleccione **Agregar** para guardar.

1. Verá que aparecen las tres opciones siguientes junto al nombre del grupo de red en *Grupos de red de spoke*:
    
    :::image type="content" source="./media/how-to-create-hub-and-spoke/spokes-settings.png" alt-text="Captura de pantalla de la configuración de los grupos de red de spoke." lightbox="./media/how-to-create-hub-and-spoke/spokes-settings-expanded.png":::

    * *Conectividad directa:* seleccione **Habilitar emparejamiento dentro del grupo de red** si quiere establecer el emparejamiento de VNet entre las redes virtuales del grupo de red de la misma región.
    * *Malla global:* seleccione **Habilitar conectividad de malla entre regiones** si quiere establecer el emparejamiento de VNet para todas las redes virtuales del grupo de red entre regiones.
    * *Puerta de enlace:* seleccione **Usar centro como puerta de enlace** si tiene una puerta de enlace de red virtual en la red virtual del centro que quiere que este grupo de red use para pasar el tráfico al entorno local.

    Seleccione la configuración que quiera habilitar para cada grupo de red.

1. Por último, seleccione **Agregar** para crear la configuración de conectividad en estrella tipo hub-and-spoke.

## <a name="deploy-the-hub-and-spoke-configuration"></a>Implementación de la configuración de tipo hub-and-spoke

Para que esta configuración tenga efecto en su entorno, deberá implementar la configuración en las regiones donde se crea la red virtual seleccionada.

1. En *Configuración*, seleccione **Implementaciones** y, a continuación, seleccione **Implementar una configuración**.

1. En *Implementar una configuración*, seleccione la siguiente configuración:

    :::image type="content" source="./media/how-to-create-hub-and-spoke/deploy.png" alt-text="Captura de pantalla de la página Implementar una configuración.":::

    | Configuración | Valor |
    | ------- | ----- |
    | Tipo de configuración | Seleccione **Conectividad**. |
    | Configurations | Seleccione el nombre de la configuración que ha creado en la sección anterior. |
    | Regiones de destino | Seleccione todas las regiones que se aplican a las redes virtuales que ha seleccionado para la configuración. |

1. Seleccione **Implementar** y, a continuación, seleccione **Aceptar** para confirmar la configuración en las regiones seleccionadas.

1. La implementación de la configuración puede tardar entre 15 y 20 minutos; seleccione el botón **Actualizar** para comprobar el estado de la implementación.

## <a name="confirm-deployment"></a>Confirmación de la implementación

1. Consulte [Ver la configuración aplicada](how-to-view-applied-configurations.md).

1. Para probar la *conectividad directa* entre radios, implemente una máquina virtual en cada red virtual radial. A continuación, inicie una solicitud ICMP de una máquina virtual a la otra.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [reglas de administración de seguridad](concept-security-admins.md).
- Obtenga información sobre cómo bloquear el tráfico de red con una [configuración de administración de seguridad](how-to-block-network-traffic-portal.md).
