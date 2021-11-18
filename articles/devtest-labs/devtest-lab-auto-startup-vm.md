---
title: Configuración del inicio automático de una máquina virtual
description: Aprenda a configurar el inicio automático para las máquinas virtuales de un laboratorio. Esta configuración permite que las máquinas virtuales del laboratorio se inicien automáticamente según una programación.
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: ea4220af8a3d7ade997b9e64b7164757edfc165a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286723"
---
# <a name="start-up-lab-virtual-machines-automatically"></a>Inicio automático de máquinas virtuales de laboratorio

El inicio automático permite iniciar automáticamente máquinas virtuales (VM) en un laboratorio a una hora programada cada día. Primero debe crear una directiva de inicio automático. Luego, debe seleccionar qué máquinas virtuales deben seguir la directiva. El paso adicional de seleccionar de forma afirmativa las máquinas virtuales para el inicio automático está pensado para evitar el inicio involuntario de las máquinas virtuales, que conllevaría un aumento de los costos.

En este artículo se muestra cómo configurar una directiva de inicio automático en un laboratorio. Para información sobre cómo configurar el apagado automático, consulte [Administración de directivas de apagado automático para un laboratorio en Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

## <a name="configure-auto-start-settings-for-a-lab"></a>Configuración del inicio automático en un laboratorio 

La directiva no aplica automáticamente el inicio automático a ninguna máquina virtual del laboratorio. Después de configurar la directiva, siga los pasos que se indican en [Habilitación del inicio automático para una máquina virtual en el laboratorio](#enable-auto start-for-a-vm-in-the-lab).

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Vaya a su laboratorio en **DevTest Labs**.

1. En **Configuración**, seleccione **Configuración y directivas**. 

   :::image type="content" source="./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png" alt-text="Captura de pantalla que muestra el menú &quot;Configuración y directivas&quot; en DevTest Labs.":::

1. En la página **Configuración y directivas**, en **Programaciones**, seleccione **Inicio automático**.

1. En **Allow auto-start** (Permitir inicio automático) seleccione **Sí**. A continuación, aparecerá la información de programación.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/portal-lab-auto-start.png" alt-text="Captura de pantalla de la opción Inicio automático en Programaciones.":::
 
1. Proporcione la siguiente información de programación:

    |Propiedad | Descripción |
    |---|---|
    |Inicio programado| Escriba una hora de inicio.|
    |Zona horaria| Seleccione una zona horaria en la lista desplegable.|
    |Días de la semana| Seleccione cada cuadro junto al día en que quiere que se aplique la programación.|

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/auto-start-configuration.png" alt-text="Captura de pantalla de la configuración de programación de inicio automático.":::

1. Seleccione **Guardar**. 

## <a name="enable-auto-start-for-a-vm-in-the-lab"></a>Habilitación del inicio automático en una máquina virtual del laboratorio

Estos pasos continúan desde la sección anterior. Ahora que se ha creado una directiva de inicio automático, seleccione las máquinas virtuales en las que se aplicará.

1. Cierre la página **Configuración y directivas** para volver a la página **DevTest Labs**.

1. En **Mis máquinas virtuales**, seleccione una máquina virtual.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-vm.png" alt-text="Captura de pantalla de selección de una máquina virtual de la lista en Mis máquinas virtuales.":::

1. En la página **Máquina virtual**, en **Operaciones**, seleccione **Inicio automático**. 

1. En la página **Inicio automático**, seleccione **Sí** y, luego, **Guardar**.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-auto-start.png" alt-text="Captura de pantalla del menú de selección del inicio automático.":::

## <a name="next-steps"></a>Pasos siguientes

- [Administre la directiva de apagado automático para un laboratorio en Azure DevTest Labs](devtest-lab-auto-shutdown.md)
