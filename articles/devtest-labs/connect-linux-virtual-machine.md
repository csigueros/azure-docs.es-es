---
title: Conexión a las máquinas virtuales Linux
description: Obtenga información sobre cómo conectarse a las máquinas virtuales Linux en un laboratorio (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 956c27235dcdf9d57c908ce2e840e3c77518875b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676055"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Conexión a una máquina virtual Linux en el laboratorio (Azure DevTest Labs)
En este artículo se muestra cómo conectarse a máquina virtual Linux en el laboratorio. 

## <a name="connect-to-a-linux-vm"></a>Conexión a una máquina virtual Linux
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En la barra de búsqueda, busque y seleccione **DevTest Labs**. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="Búsqueda y selección de DevTest Labs":::    
1. En la lista de laboratorios, seleccione el **suyo**.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Selección del laboratorio":::            
1. En la página principal del laboratorio, seleccione la máquina virtual Linux en la lista **Mis máquinas virtuales**. 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Selección de la máquina virtual Linux":::        
5. En la página **Información general**, puede ver el nombre de dominio completo (FQDN) o la dirección IP de la máquina virtual. También puede ver el puerto como se muestra en la siguiente imagen.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="El nombre de dominio completo de la máquina virtual":::    

    Tenga en cuenta que el botón **Conectar** está atenuado aunque se inicie la máquina virtual. Este comportamiento es por diseño.
6.  Use SSH para conectarse a la máquina virtual Linux. En el siguiente ejemplo se conecta a la máquina virtual con el FQDN `mydtl07172452621450000.eastus.cloudapp.azure.com`, con el nombre de usuario `vmuser` y el puerto `51637`. Escriba la contraseña para que el usuario se conecte a la máquina virtual. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    Puede usar herramientas como [Putty](https://www.putty.org/) o cualquier otro cliente de SSH para conectarse a la máquina virtual. 

    Después de conectarse mediante SSH, puede instalar y configurar un entorno de escritorio ([xfce](https://www.xfce.org)) y un escritorio remoto ([xrdp](http://xrdp.org)).  Para información detallada, consulte [Instalación y configuración del escritorio remoto para conectarse a una máquina virtual Linux en Azure](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Pasos siguientes
[Procedimientos: Conexión a una máquina virtual Windows](connect-windows-virtual-machine.md)
