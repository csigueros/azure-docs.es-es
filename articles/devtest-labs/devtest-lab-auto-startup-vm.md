---
title: Configuración del inicio automático para una máquina virtual
description: Aprenda a configurar el inicio automático para las máquinas virtuales de un laboratorio. Esta configuración permite que las máquinas virtuales del laboratorio se inicien automáticamente según una programación.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 828350cb130e990d6a6ce3f16f084d5629518293
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642954"
---
# <a name="auto-startup-lab-virtual-machines"></a>Inicio automático de máquinas virtuales del laboratorio  
Azure DevTest Labs permite configurar las máquinas virtuales del laboratorio para que se inicien y se apaguen automáticamente según una programación. Para información sobre cómo configurar el apagado automático, consulte [Administración de directivas de apagado automático para un laboratorio en Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

A diferencia del apagado automático en el que, cuando se activa la directiva, se incluyen todas las máquinas virtuales, la directiva de inicio automático requiere que un usuario del laboratorio seleccione explícitamente una máquina virtual y participe en esta programación. De este modo, no se dará sin querer el caso de que se inicien automáticamente máquinas virtuales no deseadas, causando un gasto inesperado.

En este artículo se muestra cómo configurar la directiva de inicio automático para un laboratorio.

## <a name="configure-autostart-settings-for-a-lab"></a>Configuración del inicio automático para un laboratorio 
1. Vaya a la página principal del laboratorio. 
2. Seleccione **Configuración y directivas** en el menú de la izquierda. 

    ![Captura de pantalla que muestra el menú "Configuración y directivas" en el laboratorio de DevTest.](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. En la página **Configuración y directivas**, realice los pasos siguientes:
    
    1. Seleccione **Activado** en **Permitir programar el inicio automático en las máquinas virtuales**; así habilitar la característica para este laboratorio. 
    2. Seleccione una hora de inicio (por ejemplo, 8:00:00 a.m.) en el campo **Inicio de la programación**. 
    3. Seleccione el valor de **Zona horaria** que se va a usar. 
    4. Seleccione, en **Día de la semana**, los días en los que las máquinas virtuales se deben iniciar automáticamente. 
    5. A continuación, seleccione **Guardar** en la barra de herramientas para guardar la configuración. 

        ![Configuración del inicio automático](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Esta directiva no aplica automáticamente el inicio automático a todas las máquinas virtuales del laboratorio. Para que las máquinas virtuales individuales **participen**, vaya a la página de la máquina virtual y habilite la opción de **Inicio automático** para ella.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Habilitación del inicio automático para una máquina virtual del laboratorio
En el siguiente procedimiento se indican los pasos necesarios para que una máquina virtual participe en la directiva de inicio automático del laboratorio. 

1. En la página principal del laboratorio, seleccione la **máquina virtual** en la lista **Mis máquinas virtuales**. 

    ![Menú Configuración y directivas](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. En la página de la **máquina virtual**, seleccione **Inicio automático** en el menú de la izquierda o en la lista **Programaciones**. 

    ![Selección del menú de inicio automático](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. En la página **Inicio automático**, seleccione **Activado** en la opción **Permitir que esta máquina virtual se programe para el inicio automático**.

    ![Habilitación del inicio automático para la máquina virtual](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. A continuación, seleccione **Guardar** en la barra de herramientas para guardar la configuración. 


## <a name="next-steps"></a>Pasos siguientes
Para información sobre cómo configurar la directiva de apagado automático para un laboratorio, consulte [Administración de directivas de apagado automático para un laboratorio en Azure DevTest Labs](devtest-lab-auto-shutdown.md).
