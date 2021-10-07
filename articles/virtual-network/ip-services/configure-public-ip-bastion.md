---
title: Administración de una dirección IP pública con Azure Bastion
titleSuffix: Azure Virtual Network
description: Conozca las formas en que se usa una dirección IP pública con Azure Bastion y cómo cambiar la configuración.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 22fa5e1843cdff45a5bea9fb61393a0579278964
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367832"
---
# <a name="manage-a-public-ip-address-with-azure-bastion"></a>Administración de una dirección IP pública con Azure Bastion

Las direcciones IP públicas están disponibles en dos SKU: estándar y básica. La selección de la SKU determina las características de la dirección IP. La SKU determina los recursos a los que se puede asociar la dirección IP.

Azure Bastion se implementa para proporcionar conectividad de administración segura a las máquinas virtuales de una red virtual. El servicio Azure Bastion permite conectarse sin fisuras mediante RDP y SSH a las máquinas virtuales de la red virtual. Asimismo, permite conectarse sin exponer una dirección IP pública en la máquina virtual. Las conexiones se realizan directamente desde Azure Portal, sin necesidad de un software o un cliente/agente adicional. Azure Bastion admite direcciones IP públicas de SKU estándar.

Un host de Azure Bastion requiere una dirección IP pública para su configuración.

En este artículo, aprenderá a crear un host de Azure Bastion mediante una dirección IP pública existente en su suscripción. Azure Bastion no admite el cambio de la dirección IP pública después de la creación.  Azure Bastion no admite prefijos de IP pública.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Una dirección IP pública de SKU estándar en la suscripción. La dirección IP no puede estar asociada a ningún recurso. Para más información sobre cómo crear una IP pública de SKU estándar, consulte [Creación de una dirección IP pública: Azure Portal](../../virtual-network/create-public-ip-portal.md).
    - Para los fines de los ejemplos de este artículo, asigne a la nueva dirección IP pública el nombre **myStandardPublicIP**.

## <a name="create-azure-bastion-using-existing-ip"></a>Creación de Azure Bastion mediante la dirección IP existente

En esta sección, creará un host de Azure Bastion. Seleccionará la dirección IP que creó en los requisitos previos como dirección IP pública del host de Bastion.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Bastion**.

3. En los resultados de la búsqueda, haga clic en **Instancias de Bastion**.

4. Seleccione **+ Create** (+ Crear).

5. En **Crear una instancia de Bastion**, escriba o seleccione la siguiente información.

    | Configuración | Value | 
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **myResourceGroup** en **Nombre**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |  |
    | Nombre | Escriba **MyBastionHost**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |
    | **Configurar la red virtual** |   |
    | Virtual network | Seleccione **Crear nuevo**. </br> Escriba **myVNet** en **Nombre**. </br> Deje el espacio de direcciones predeterminado **10.4.0.0/16**. </br> Deje la subred predeterminada **10.4.0.0/24**. </br> En el cuadro de texto bajo la subred **predeterminada**, introduzca **AzureBastionSubnet**. </br> En el intervalo de direcciones, escriba **10.4.1.0/27**. </br> Seleccione **Aceptar**. |
    | Subred | Seleccione **AzureBastionSubnet**. |
    | **Dirección IP pública** |   |
    | Dirección IP pública | Seleccione **Usar existente**. |
    | Elección de una IP pública | Seleccione **myStandardPublicIP**. |

6. Seleccione la pestaña **Revisar y crear** o seleccione el botón **Revisar y crear**.

7. Seleccione **Crear**.

> [!NOTE]
> Para más información sobre Azure Bastion, consulte [¿Qué es Azure Bastion?](../../bastion/bastion-overview.md)

## <a name="change-or-remove-public-ip-address"></a>Cambio o eliminación de IP pública

Azure Bastion no admite el cambio de la dirección IP pública después de la creación.

## <a name="more-information"></a>Más información

* No es necesario tener una IP pública independiente en la máquina virtual cuando se conecta a través de Azure Bastion. El tráfico se enruta primero a la dirección IP pública de Bastion. A continuación, Bastion enruta las conexiones RDP o SSH a la dirección IP privada asociada a la máquina virtual. 

## <a name="caveats"></a>Advertencias

* Las direcciones IPv6 públicas no se admiten para Azure Bastion en este momento.  

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a crear una instancia de Azure Bastion y a usar una dirección IP pública existente. 

- Para más información sobre Azure Bastion, consulte [¿Qué es Azure Bastion?](../../bastion/bastion-overview.md)
- Para más información sobre las IP públicas en Azure, consulte [Direcciones IP públicas](../../virtual-network/public-ip-addresses.md).
