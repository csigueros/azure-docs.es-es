---
title: 'Cómo bloquear el tráfico de red con Azure Virtual Network Manager (versión preliminar): Azure Portal'
description: Aprenda a bloquear el tráfico de red mediante reglas de seguridad en Azure Virtual Network Manager con Azure Portal.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 20adb2a748f839d63122a1fc5904746a29af33ff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093656"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-portal"></a>Cómo bloquear el tráfico de red con Azure Virtual Network Manager (versión preliminar): Azure Portal

En este artículo se muestra cómo crear una regla de administración de seguridad para bloquear el tráfico de red entrante en el puerto RDP 3389 que puede agregar a una colección de reglas. Para obtener más información, consulte [Reglas de administración de seguridad](concept-security-admins.md).

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerrequisitos

Antes de empezar a configurar las reglas de administración de seguridad, confirme que ha realizado los pasos siguientes:

* Comprende todos los elementos de una [regla de administración de seguridad](concept-security-admins.md).
* Ha creado una [instancia de Azure Virtual Network Manager](create-virtual-network-manager-portal.md).

## <a name="create-a-securityadmin-configuration"></a>Creación de una configuración de SecurityAdmin

1. En **Configuración**, seleccione *Configuraciones* y, después, **+ Agregar una configuración**.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/create-security-admin.png" alt-text="Captura de pantalla de la opción para agregar una configuración de administración de seguridad.":::

1. Seleccione **SecurityAdmin** en el menú desplegable.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/security-admin-drop-down.png" alt-text="Captura de pantalla del menú desplegable para agregar una configuración.":::

1. Escriba un *Nombre* para identificar esta configuración de seguridad.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/security-configuration-name.png" alt-text="Captura de pantalla del campo de nombre de la configuración de seguridad.":::

## <a name="add-a-rule-collection"></a>Adición de una colección de reglas

1. Seleccione **+ Agregar una colección de reglas** para crear el conjunto de reglas.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule-collection-button.png" alt-text="Captura de pantalla del botón para agregar una colección de reglas.":::

1. Escriba un *Nombre* para identificar esta colección de reglas y, a continuación, seleccione los *grupos de red* a los que quiere aplicar el conjunto de reglas.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/rule-collection-target.png" alt-text="Captura de pantalla del nombre de la colección de reglas y los grupos de red de destino.":::

## <a name="add-a-security-rule"></a>Agregar una regla de seguridad

1. Seleccione **+ Agregar una regla** en la página *Agregar una colección de reglas*.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule-button.png" alt-text="Captura de pantalla del botón para agregar una regla.":::

1. Escriba o seleccione la siguiente información y, a continuación, seleccione **Agregar** para agregar la regla a la colección de reglas.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule.png" alt-text="Captura de pantalla de la página para agregar una regla.":::

    | Configuración | Valor |
    | ------- | ----- |
    | Nombre | Escriba el nombre **Deny_RDP** como nombre de la regla. |
    | Descripción | Escriba una descripción sobre la regla. |
    | Prioridad* | Escriba un valor entre 0 y 99 para determinar la prioridad de la regla. Cuanto menor sea el valor, mayor será la prioridad. Escriba **1** para este ejemplo.|
    | Acción* | Seleccione **Denegar** para bloquear el tráfico. Para obtener más información, consulte [Acciones](concept-security-admins.md#action).
    | Dirección* | Seleccione **Entrante**, ya que con esta regla quiere denegar el tráfico entrante. |
    | Protocolo* | Seleccione el protocolo **TCP**. HTTP y HTTPS son puertos TCP. |
    | Tipo de origen | Seleccione el tipo de origen de la **dirección IP** o las **etiquetas de servicio**. |
    | Direcciones IP de origen | Este campo aparecerá al seleccionar el tipo de origen *Dirección IP*. Escriba una dirección IPv4 o IPv6 o bien un intervalo mediante la notación CIDR. Al definir más de una dirección o bloques de direcciones, sepárelos mediante una coma. Déjelo en blanco para este ejemplo.|
    | Etiqueta de servicio de origen | Este campo aparecerá al seleccionar el tipo de origen *Etiqueta de servicio*. Seleccione etiquetas de servicio para los servicios que quiere especificar como origen. Consulte [Etiquetas de servicio disponibles](../virtual-network/service-tags-overview.md#available-service-tags) para obtener la lista de etiquetas admitidas. |
    | Puerto de origen | Escriba un número de puerto único o un intervalo de puertos como (1024-65535). Al definir más de un puerto o intervalos de puertos, sepárelos mediante una coma. Para especificar cualquier puerto, escriba *. Déjelo en blanco para este ejemplo.|
    | Tipo de destino | Seleccione el tipo de destino de la **dirección IP** o las **etiquetas de servicio**. |
    | Direcciones IP de destino | Este campo aparecerá al seleccionar el tipo de destino *Dirección IP*. Escriba una dirección IPv4 o IPv6 o bien un intervalo mediante la notación CIDR. Al definir más de una dirección o bloques de direcciones, sepárelos mediante una coma. |
    | Etiqueta de servicio de destino | Este campo aparecerá al seleccionar el tipo de destino *Etiqueta de servicio*. Seleccione etiquetas de servicio para los servicios que quiere especificar como destino. Consulte [Etiquetas de servicio disponibles](../virtual-network/service-tags-overview.md#available-service-tags) para obtener la lista de etiquetas admitidas. |
    | Puerto de destino | Escriba un número de puerto único o un intervalo de puertos como (1024-65535). Al definir más de un puerto o intervalos de puertos, sepárelos mediante una coma. Para especificar cualquier puerto, escriba *. Escriba **3389** para este ejemplo. |

1. Repita los pasos del 1 al 3 de nuevo si quiere agregar más reglas a la colección de reglas.

1. Una vez que esté satisfecho con todas las reglas que quería crear, seleccione **Guardar** para agregar la colección de reglas a la configuración de administración de seguridad.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/save-rule-collection.png" alt-text="Captura de pantalla de una regla en una colección de reglas.":::

1. A continuación, seleccione **Agregar** para crear la configuración.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/create-security-configuration.png" alt-text="Captura de pantalla del botón Agregar para crear una configuración de seguridad.":::

## <a name="deploy-the-security-admin-configuration"></a>Implementación de la configuración de administración de seguridad

Si acaba de crear una nueva configuración de administración de seguridad, asegúrese de implementarla para que se aplique a las redes virtuales del grupo de red.

1. En *Configuración*, seleccione **Implementaciones** y, a continuación, seleccione **Implementar una configuración**.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/deploy-configuration.png" alt-text="Captura de pantalla del botón para implementar una configuración.":::

1. Seleccione el tipo de configuración **SecurityAdmin** y la configuración que creó en la última sección. A continuación, elija las regiones en las que quiere implementar esta configuración y seleccione **Implementar**.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/deploy-security-configuration.png" alt-text="Captura de pantalla de la página de implementación de una configuración de seguridad.":::

1. Seleccione **Aceptar** para confirmar que quiere sobrescribir cualquier configuración existente e implementar la configuración de administración de seguridad.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/confirm-security.png" alt-text="Captura de pantalla del mensaje de confirmación para implementar una configuración de seguridad.":::

## <a name="update-existing-security-admin-configuration"></a>Actualización de una configuración de administración de seguridad existente

* Si la configuración de administración de seguridad que está actualizando se aplica a un grupo de red que contiene miembros estáticos, deberá implementar la configuración de nuevo para que su efecto sea efectivo.
* Las configuraciones de administración de seguridad se aplican automáticamente a los miembros dinámicos de un grupo de red.

## <a name="verify-security-admin-rules"></a>Comprobación de las reglas de administración de seguridad

Vaya a la configuración de **Redes** de una máquina virtual de una de las redes virtuales a las que aplicó las reglas de administración de seguridad. Si no tiene ninguna, implemente una máquina virtual de prueba en una de las redes virtuales. Ahora verá una nueva sección debajo de las reglas del grupo de seguridad de red sobre las reglas de seguridad que aplica Azure Virtual Network Manager.

:::image type="content" source="./media/how-to-block-network-traffic-portal/vm-security-rules.png" alt-text="Captura de pantalla de las reglas de administración de seguridad en la configuración de red de la máquina virtual." lightbox="./media/how-to-block-network-traffic-portal/vm-security-rules-expanded.png":::

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las [reglas de administración de seguridad](concept-security-admins.md).
