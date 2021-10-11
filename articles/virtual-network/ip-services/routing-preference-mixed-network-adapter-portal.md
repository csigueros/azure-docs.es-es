---
title: 'Tutorial: Configuración de ambas opciones de preferencias de enrutamiento para una máquina virtual: Azure Portal'
titlesuffix: Azure Virtual Network
description: Use este tutorial para aprender a configurar ambas opciones de preferencia de enrutamiento para una máquina virtual mediante Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: d11e77725dfb99060c96f62233833330867f1c0d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369692"
---
# <a name="tutorial-configure-both-routing-preference-options-for-a-virtual-machine-using-the-azure-portal"></a>Tutorial: Configuración de ambas opciones de preferencias de enrutamiento para una máquina virtual mediante Azure Portal

En este artículo se muestra cómo configurar ambas opciones de [preferencias de enrutamiento](routing-preference-overview.md) (Internet y red global de Microsoft) para una máquina virtual. Esta configuración se logra mediante dos interfaces de red virtual. Una interfaz de red se configura con una dirección IP pública enrutada a través de la red global de Microsoft. La otra interfaz de red se configura con una dirección IP pública enrutada a través de una red ISP.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una máquina virtual con una dirección IP pública con la preferencia de enrutamiento de **red de Microsoft**.
> * Crear una dirección IP pública con la preferencia de enrutamiento de **Internet**.
> * Crear una interfaz de red secundaria para la máquina virtual.
> * Asociar la dirección IP pública de preferencia de enrutamiento de **Internet** a la interfaz de red secundaria de la máquina virtual.
> * Adjuntar una interfaz de red secundaria a una máquina virtual.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-virtual-machine"></a>Creación de la máquina virtual

En esta sección, creará una máquina virtual y una dirección IP pública. Durante la configuración de la dirección IP pública, seleccionará **Red de Microsoft** para las preferencias de enrutamiento.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

3. En **Máquinas virtuales**, seleccione **+ Crear** y, a continuación, **Máquina virtual**.

4. En la pestaña **Conceptos básicos** de **Crear una máquina virtual**, escriba o seleccione la siguiente información.

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **TutorVMMixRoutePref-rg**. Seleccione **Aceptar**. |
    | **Detalles de instancia** |   |
    | Nombre de la máquina virtual | Escriba **myVM**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |
    | Opciones de disponibilidad | Seleccione **No se requiere redundancia de la infraestructura**. |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen2**. |
    | Instancia de Azure Spot | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
    | Size | Seleccione un tamaño. |
    | **Cuenta de administrador** |   |
    | Nombre de usuario | Especifique un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmación de la contraseña | Vuelva a escribir la contraseña. |
    | **Reglas de puerto de entrada** |
    | Puertos de entrada públicos | Seleccione **Permitir los puertos seleccionados**. |
    | Selección de puertos de entrada | Deje el valor predeterminado, **RDP (3389)** . </br> _**No se recomienda abrir el puerto 3389 desde Internet con cargas de trabajo de producción**_. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-virtual-machine.png" alt-text="Captura de pantalla de la creación de una máquina virtual.":::

5. Seleccione la pestaña **Siguiente: Discos** y, a continuación, **Siguiente: Redes**, o seleccione la pestaña **Redes**.

6. En la pestaña Redes, escriba o seleccione la siguiente información.

    | Configuración | Valor |
    | ------- | ----- |
    | **Interfaz de red** |   |
    | Virtual network | Deje el valor predeterminado de **(nuevo) TutorVMMixRoutePref-rg-vnet**. |
    | Subred | Deje el valor predeterminado de **(new) default (10.1.0.0/24)** . |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myPublicIP**. </br> Seleccione **Estándar** en la **SKU**. </br> En **Preferencia de enrutamiento**, seleccione **Red de Microsoft**. </br> Seleccione **Aceptar**. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-ms-rp.png" alt-text="Captura de pantalla de la creación de una dirección IP pública con preferencia de enrutamiento de Microsoft.":::

7. Seleccione **Revisar + crear**.

8. Seleccione **Crear**.

## <a name="create-the-public-ip-address"></a>Crear la dirección IP pública

En esta sección, creará una dirección IP pública con la preferencia de enrutamiento de **Internet**.

1. En el cuadro de búsqueda del portal, escriba **Dirección IP pública**. En los resultados de la búsqueda, escriba **Direcciones IP públicas**.

2. Seleccione **+ Create** (+ Crear).

3. En **Crear dirección IP pública**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | Versión de la dirección IP | Deje el valor predeterminado, **IPv4**. |
    | SKU | Deje el valor predeterminado **Estándar**. |
    | Nivel | Deje el valor predeterminado, **Regional**. |
    | **Configuración de dirección IP IPv4** |   |
    | Name | Escriba **myPublicIP-IR**. |
    | Preferencia de enrutamiento | seleccione **Internet**. |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **TutorVMMixRoutePref-rg**. |
    | Location | Seleccione **Oeste de EE. UU. 2**. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-internet-rp.png" alt-text="Captura de pantalla de la creación de una dirección IP pública con preferencia de enrutamiento de Internet.":::

4. Seleccione **Crear**.

## <a name="create-the-secondary-nic"></a>Creación de una NIC secundaria

En esta sección, creará una interfaz de red secundaria para la máquina virtual que creó anteriormente.

1. En el cuadro de búsqueda del portal, escriba **Interfaz de red**. En los resultados de búsqueda, seleccione **Interfaces de red**.

2. Seleccione **+ Create** (+ Crear).

3. En la hoja **Crear interfaz de red**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **TutorVMMixRoutePref-rg**. |
    | Name | Escriba **myVMNic2**. |
    | Region | Seleccione **Oeste de EE. UU. 2**. |
    | Virtual network | Seleccione **TutorVMMixRoutePref-rg-vnet**. |
    | Subnet | Seleccione **TutorVMMixRoutePref-rg-vnet/default (10.1.0.0/24)** . |
    | Grupo de seguridad de red | Seleccione **myVM-nsg**. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-network-interface.png" alt-text="Captura de pantalla de la creación de una interfaz de red secundaria.":::

4. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear** en la parte inferior de la página.

5. Seleccione **Crear**.

## <a name="associate-the-public-ip-address-with-secondary-nic"></a>Asociación de la dirección IP pública con la NIC secundaria

En esta sección, asociará la dirección IP pública de preferencias de enrutamiento de **Internet** que creó anteriormente con la interfaz de red que creó en la sección anterior.

1. En el cuadro de búsqueda del portal, escriba **Dirección IP pública**. En los resultados de la búsqueda, escriba **Direcciones IP públicas**.

2. Seleccione **myPublicIP-IR**.

3. En la página de **información general** de **myPublic-IR**, seleccione **Asociar**.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/associate-public-ip.png" alt-text="Captura de pantalla de la página de información general de myPublicIP-IR con el botón Asociar.":::

4. En **Asociar dirección IP pública**, seleccione **Interfaz de red** en el cuadro desplegable **Tipo de recurso**.

5. Seleccione **myVMNic2** en el cuadro desplegable **Interfaz de red**.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/select-ip-association-resource.png" alt-text="Captura de pantalla de la selección del recurso que se asociará a la dirección IP pública.":::

6. Seleccione **Aceptar**.

## <a name="attach-secondary-network-interface-to-virtual-machine"></a>Conexión de una interfaz de red secundaria a una máquina virtual

En esta sección, asociará la interfaz de red secundaria que creó anteriormente a la máquina virtual.

1. En el cuadro de búsqueda del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. Seleccione **myVM**.

3. Detenga **myVM** si se está ejecutando. De lo contrario, continúe con el paso siguiente.

4. En **myVM**, seleccione **Redes** en **Configuración**.

5. En la página **Redes** de **myVM**, seleccione **Asociar interfaz de red**.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-01.png" alt-text="Captura de pantalla de la página de información general de redes de myVM.":::

6. En **Asociar interfaz de red**, seleccione **myVMNic2** en el cuadro desplegable.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-02.png" alt-text="Captura de pantalla de la interfaz de red de conexión.":::

7. Seleccione **Aceptar**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine las direcciones IP públicas y la máquina virtual mediante los pasos siguientes:

1. Escriba **Grupo de recursos** en el cuadro de búsqueda que se encuentra en la parte superior del portal.

2. En los resultados de la búsqueda, seleccione **Grupos de recursos**.

3. Seleccione **TutorVMMixRoutePref-rg**.

4. Seleccione **Eliminar grupo de recursos**.

5. Escriba **myResourceGroup** para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente artículo para aprender a crear un prefijo de dirección IP pública:
> [!div class="nextstepaction"]
> [Configuración de preferencias de enrutamiento de un clúster de Kubernetes mediante la CLI de Azure](routing-preference-azure-kubernetes-service-cli.md)