---
title: Creación de una topología de red de malla con Azure Virtual Network Manager (versión preliminar)
description: Aprenda cómo crear una topología de red de malla con Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fea577a1aa3d2a26d218a7d7c97140d5f58661f2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017308"
---
# <a name="create-a-mesh-network-topology-with-azure-virtual-network-manager-preview"></a>Creación de una topología de red de malla con Azure Virtual Network Manager (versión preliminar)

En este artículo, descubrirá cómo crear una topología de red de malla mediante Azure Virtual Network Manager. Con esta configuración, todas las redes virtuales de la misma región en el mismo grupo de red pueden comunicarse entre sí. Puede permitir la conectividad entre regiones habilitando la configuración de malla global en la configuración de conectividad.

> [!IMPORTANT]
> *Azure Virtual Network Manager* se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [**Condiciones de uso complementarias de las versiones preliminares de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Obtenga información sobre la topología de red de [malla](concept-connectivity-configuration.md#mesh-network-topology).
* Ha creado una [instancia de Azure Virtual Network Manager](create-virtual-network-manager-portal.md#create-virtual-network-manager).
* Identifique las redes virtuales que quiere usar en la configuración de red o cree [redes virtuales](../virtual-network/quick-create-portal.md).

## <a name="create-a-network-group"></a>Creación de un grupo de red

Esta sección le ayudará a crear un grupo de redes que contenga las redes virtuales que va a usar para la topología en estrella tipo hub-and-spoke.

1. Vaya a la instancia de Azure Virtual Network Manager. En esta guía paso a paso se da por hecho que ha creado una con la guía de [inicio rápido](create-virtual-network-manager-portal.md).

1. Seleccione **Grupos de red** en *Configuración* y, a continuación, seleccione **+ Agregar** para crear un nuevo grupo de red.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="Captura de pantalla del botón Agregar un grupo de red.":::

1. En la pestaña *Básicos*, escriba un **Nombre** y una **Descripción** para el grupo de red.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/basics.png" alt-text="Captura de pantalla de la pestaña Básicos para agregar un grupo de red.":::

1. Para agregar la red virtual manualmente, seleccione la pestaña **Miembros del grupo estático**. Para obtener más información, consulte [Miembros estáticos](concept-network-groups.md#static-membership).

    :::image type="content" source="./media/how-to-create-hub-and-spoke/static-group.png" alt-text="Captura de pantalla de la pestaña Miembros del grupo estático.":::

1. Para agregar redes virtuales dinámicamente, seleccione la pestaña **Instrucciones condicionales**. Para obtener más información, consulte [Pertenencia dinámica](concept-network-groups.md#dynamic-membership).

    :::image type="content" source="./media/how-to-create-hub-and-spoke/conditional-statements.png" alt-text="Captura de pantalla de la pestaña Instrucciones condicionales.":::

1. Una vez que esté satisfecho con las redes virtuales seleccionadas para el grupo de red, seleccione **Revisar y crear**. A continuación, seleccione **Crear** una vez que se pase la validación.

## <a name="create-a-mesh-connectivity-configuration"></a>Creación de una configuración de conectividad de malla

En esta sección se le guiará por el proceso de cómo crear una configuración de malla con el grupo de red que se ha creado en la sección anterior.

1. En **Configuración**, seleccione *Configuración* y, después, **+ Agregar una configuración**.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/configuration-list.png" alt-text="Captura de pantalla de la lista de configuraciones.":::

1. Seleccione **Conectividad** en el menú desplegable.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Captura de pantalla del menú desplegable de configuración.":::

1. En la página *Agregar una configuración de conectividad*, escriba o seleccione la información siguiente:

    :::image type="content" source="./media/how-to-create-mesh-network/connectivity-configuration.png" alt-text="Captura de pantalla de la página Agregar una configuración de conectividad.":::

    | Configuración | Valor |
    | ------- | ----- |
    | Nombre | Escriba un *nombre* para esta configuración. |
    | Descripción | *Opcional* Escriba una descripción sobre lo que hará esta configuración. |
    | Topología | Seleccione la topología de **malla.** |
    | Malla global | Seleccione esta opción para habilitar la conectividad entre regiones entre redes virtuales del mismo grupo de red. |

1. A continuación, seleccione **+ Agregar grupos de red**. 

1. En la página *Agregar grupos de red*, seleccione los grupos de red que quiere agregar a esta configuración. A continuación, seleccione **Agregar** para guardar.

1. Seleccione **Agregar** de nuevo para crear la configuración de conectividad de malla.

## <a name="deploy-the-mesh-configuration"></a>Implementación de la configuración de malla

Para que esta configuración tenga efecto en su entorno, deberá implementar la configuración en las regiones donde se crean las redes virtuales seleccionadas.

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

1. Consulte [Ver las configuraciones aplicadas](how-to-view-applied-configurations.md).

1. Para probar la conectividad entre redes virtuales, implemente una máquina virtual de prueba en cada red virtual e inicie una solicitud ICMP entre ellas.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [reglas de administración de seguridad](concept-security-admins.md).
- Obtenga información sobre cómo bloquear el tráfico de red con una [configuración de administración de seguridad](how-to-block-network-traffic-portal.md).
