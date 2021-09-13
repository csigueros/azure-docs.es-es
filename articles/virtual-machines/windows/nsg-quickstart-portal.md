---
title: Apertura de puertos en una VM desde Azure Portal
description: Aprenda a abrir un puerto y crear un punto de conexión en la máquina virtual mediante Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/27/2021
ms.author: cynthn
ms.openlocfilehash: 78fce318d07e8603830fe04b41df387e6a25f8d9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227272"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Apertura de puertos en una máquina virtual con Azure Portal

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles 

[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure
Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red

1. Busque y seleccione un grupo de recursos para la máquina virtual, elija **Agregar** y busque y seleccione **Grupo de seguridad de red**.

1. Seleccione **Crear**.

    Se abre la ventana **Crear un grupo de seguridad de red**.

    :::image type="content" source="media/nsg-quickstart-portal/create-nsg.png" alt-text="Creación de un grupo de seguridad de red.":::

1. Escriba un nombre para el grupo de seguridad de red. 

1. Seleccione o cree un grupo de recursos y luego seleccione una ubicación.

1. Seleccione **Crear** para crear el grupo de seguridad de red.

## <a name="create-an-inbound-security-rule"></a>Creación de una regla de seguridad de entrada

1. Seleccione el nuevo grupo de seguridad de red.

1. Seleccione **Reglas de seguridad de entrada** en el menú de la izquierda y, después, **Agregar**.

    :::image type="content" source="media/nsg-quickstart-portal/advanced.png" alt-text="Incorporación de una regla de seguridad de entrada.":::

1. Puede limitar **Origen** e **Intervalos de puertos de origen** según sea necesario, o dejar el valor predeterminado de *Cualquiera*.
1. Puede limitar **Destino** según sea necesario, o dejar el valor predeterminado de *Cualquiera*.
1. Elija un **servicio** común en el menú desplegable, como **HTTP**. También puede seleccionar **Personalizado** si quiere proporcionar un puerto específico para su uso. 

1. Si lo desea, cambie la **Prioridad** o el **Nombre**. La prioridad afecta al orden en que se aplican las reglas (cuanto más bajo es el valor numérico, antes se aplica la regla).

1. Para crear la regla, seleccione **Agregar**.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Asociación del grupo de seguridad de red con una subred

El paso final es asociar el grupo de seguridad de red a una subred o una interfaz de red específica. Para este ejemplo, vamos a asociar el grupo de seguridad de red con una subred. 

1. Seleccione **Subredes** en el menú de la izquierda y, después, **Asociar**.

1. Seleccione la red virtual y luego seleccione la subred adecuada.

    ![Asociar un grupo de seguridad de red con una red virtual](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. Cuando finalice, seleccione **Aceptar**.

## <a name="additional-information"></a>Información adicional

También puede [llevar a cabo los pasos en este artículo utilizando Azure PowerShell](nsg-quickstart-powershell.md).

Los comandos descritos en este artículo le permiten hacer que el tráfico fluya rápidamente a la máquina virtual. Los grupos de seguridad de red proporcionan muchas características excelentes y granularidad para controlar el acceso a sus recursos. Para más información, consulte [Filtrado del tráfico de red con un grupo de seguridad de red](../../virtual-network/tutorial-filter-network-traffic.md).

Para las aplicaciones web de alta disponibilidad, considere la posibilidad de colocar las máquinas virtuales detrás de un equilibrador de carga de Azure. El equilibrador de carga distribuye el tráfico a las máquinas virtuales, con un grupo de seguridad de red que proporciona el filtrado del tráfico. Para más información, consulte [Equilibrio de carga de máquinas virtuales Windows en Azure para crear una aplicación de alta disponibilidad](tutorial-load-balancer.md).

## <a name="next-steps"></a>Pasos siguientes
En este artículo ha creado un grupo de seguridad de red, creó una regla de entrada que permite el tráfico HTTP en el puerto 80 y luego ha asociado esta regla con una subred. 

Puede encontrar información sobre la creación de entornos más detallados en los siguientes artículos:
- [Información general sobre Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Grupos de seguridad](../../virtual-network/network-security-groups-overview.md)
