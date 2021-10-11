---
title: 'Tutorial: Configuración de la preferencia de enrutamiento de una máquina virtual mediante Azure Portal'
description: En este tutorial, aprenderá a crear una máquina virtual con una dirección IP pública con preferencia de enrutamiento mediante Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 6a06ec95b6970e4c3f8d4bde4cc180bf832f3a6a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369759"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Tutorial: Configuración de la preferencia de enrutamiento de una VM mediante Azure Portal 

En este tutorial se muestra cómo configurar las preferencias de enrutamiento para una máquina virtual. El tráfico de Internet enlazado desde la máquina virtual se enrutará a través de la red del ISP si elige **Internet** como opción de preferencia de enrutamiento. El enrutamiento predeterminado se hará a través de la red global de Microsoft.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una máquina virtual con una dirección IP pública con la preferencia de enrutamiento de **Internet**.
> * Comprobar que la dirección IP pública está establecida en la preferencia de enrutamiento de **Internet**.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-machine"></a>Crear máquina virtual

En esta sección, creará una máquina virtual y una dirección IP pública. Durante la configuración de la dirección IP pública, seleccionará **Internet** para las preferencias de enrutamiento.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

3. En **Máquinas virtuales**, seleccione **+ Crear** y, a continuación, **Máquina virtual**.

4. En la pestaña **Conceptos básicos** de **Crear una máquina virtual**, escriba o seleccione la siguiente información.

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **TutorVMRoutePref-rg**. Seleccione **Aceptar**. |
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

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-virtual-machine.png" alt-text="Captura de pantalla de la creación de una máquina virtual.":::

5. Seleccione la pestaña **Siguiente: Discos** y, a continuación, **Siguiente: Redes**, o seleccione la pestaña **Redes**.

6. En la pestaña Redes, escriba o seleccione la siguiente información.

    | Configuración | Valor |
    | ------- | ----- |
    | **Interfaz de red** |   |
    | Virtual network | Deje el valor predeterminado de **(nuevo) TutorVMRoutePref-rg-vnet**. |
    | Subred | Deje el valor predeterminado de **(new) default (10.1.0.0/24)** . |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myPublicIP**. </br> Seleccione **Estándar** en la **SKU**. </br> En **Preferencias de enrutamiento**, seleccione **Internet**. </br> Seleccione **Aceptar**. |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-public-ip.png" alt-text="Captura de pantalla de creación de la dirección de IP pública.":::

7. Seleccione **Revisar + crear**.

8. Seleccione **Crear**.

## <a name="verify-internet-routing-preference"></a>Comprobación de la preferencia de enrutamiento de Internet

En esta sección, buscará la dirección IP pública creada anteriormente y comprobará la preferencia de enrutamiento de Internet.

1. En el cuadro de búsqueda del portal, escriba **Dirección IP pública**. En los resultados de la búsqueda, escriba **Direcciones IP públicas**.

2. En **Direcciones IP públicas**, seleccione **myPublicIP.**

3. Seleccione **Propiedades** en **Configuración**.

4. Compruebe que **Internet** se muestra en **Preferencia de enrutamiento**. 

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/verify-routing-preference.png" alt-text="Captura de pantalla de la comprobación de la preferencia de enrutamiento de Internet.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine las direcciones IP públicas mediante los siguientes pasos:

1. Escriba **Grupo de recursos** en el cuadro de búsqueda que se encuentra en la parte superior del portal.

2. En los resultados de la búsqueda, seleccione **Grupos de recursos**.

3. Seleccione **TutorVMRoutePref-rg**.

4. Seleccione **Eliminar grupo de recursos**.

5. Escriba **myResourceGroup** para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En el siguiente artículo aprenderá a crear una máquina virtual con preferencia de enrutamiento mixto:
> [!div class="nextstepaction"]
> [Configuración de ambas opciones de preferencias de enrutamiento para una máquina virtual](routing-preference-mixed-network-adapter-portal.md)

