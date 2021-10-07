---
title: Conexión a máquinas virtuales Windows
description: Obtenga información sobre cómo conectarse a las máquinas virtuales Windows en un laboratorio (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 8fe7b93ce7aa2cf5521bd53393b086e221e84d2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588017"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>Conexión a una máquina virtual Windows en el laboratorio (Azure DevTest Labs)
En este artículo se muestra cómo conectarse a máquina virtual Windows en el laboratorio. 

## <a name="connect-to-a-windows-vm"></a>Conexión a una máquina virtual Windows
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En la barra de búsqueda, busque y seleccione **DevTest Labs**. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="Búsqueda y selección de DevTest Labs":::    
1. En la lista de laboratorios, seleccione el **suyo**.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="Selección del laboratorio":::            
1. En la página principal del laboratorio, seleccione la máquina virtual Windows en la lista **Mis máquinas virtuales**. 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="Selección de la máquina virtual Windows":::                
1. En la página **Máquina virtual** de la VM, seleccione **Conectar** en la barra de herramientas. Para iniciar la máquina virtual en caso de estar detenida, seleccione **Iniciar**.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="Selección de conectar en la barra de herramientas":::                    
1. Si usa el explorador Edge, verá el vínculo al **archivo RDP descargado** en la parte inferior del mismo. 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="RDP descargado":::                        
1. Abra el archivo RDP y escriba las credenciales de máquina virtual que escribió al crearla. Ahora debe estar conectado a la máquina virtual Windows. 

## <a name="next-steps"></a>Pasos siguientes
[Procedimiento: Conexión a una máquina virtual Linux](connect-linux-virtual-machine.md)
