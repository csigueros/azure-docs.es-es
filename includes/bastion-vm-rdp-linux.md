---
author: cherylmc
ms.author: cherylmc
ms.date: 08/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: ebaa86a60ae11c50658bfcfe58bbd0184bf15576
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699720"
---
1. En [Azure Portal](https://portal.azure.com), vaya a la máquina virtual a la que desea conectarse. En la página **Información general**, seleccione **Conectar** y, luego, seleccione **Bastion** del menú desplegable.

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="Captura de pantalla de Conectar" lightbox="./media/bastion-vm-rdp/connect.png":::.

1. Después de seleccionar Bastion, seleccione **Usar Bastion**. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](../articles/bastion/quickstart-host-portal.md) (Configuración de Bastion).

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Captura de pantalla que muestre Usar Bastion":::.

1. En la página **Conectar mediante Azure Bastion**, despliegue la sección **Configuración de conexión** y seleccione **RDP**. Si tiene previsto usar un puerto de entrada diferente del puerto RDP estándar (3389), escriba el **Puerto**.

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/connection-settings.png" alt-text="Captura de pantalla que muestra el puerto" lightbox="./media/bastion-connect-vm-rdp-linux/connection-settings.png":::.

1. Escriba su **nombre de usuario** y **contraseña**, y haga clic en **Conectar**.

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/username-password.png" alt-text="Captura de pantalla que muestra la acción de hacer clic en Conectar" lightbox="./media/bastion-connect-vm-rdp-linux/username-password.png":::.

1. La conexión RDP con esta máquina virtual a través de Bastion se abrirá directamente en Azure Portal (a través de HTML5) mediante el puerto 443 y el servicio Bastion. 

   Es posible que el uso de teclas de método abreviado de teclado mientras está conectado a una máquina virtual no tenga el mismo comportamiento que las teclas de método abreviado en un equipo local. Por ejemplo, desde un ordenador cliente de Windows conectado a una máquina virtual de Windows, "CTRL+ALT+FIN" es el método abreviado de teclado para "CTRL+ALT+Suprimir". Desde un ordenador cliente Mac conectado a una máquina virtual de Windows, el método abreviado de teclado es "Fn+CTRL+ALT+Retroceso".