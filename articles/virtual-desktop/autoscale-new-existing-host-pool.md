---
title: Planes de escalado de Azure Virtual Desktop para grupos hosts (versión preliminar)
description: Aquí se explica cómo asignar planes de escalado a grupos de hosts nuevos o existentes de la implementación.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b61869e09003456f55a6d69cae01356a06bc633d
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181577"
---
# <a name="enable-scaling-plans-for-existing-and-new-host-pools-preview"></a>Habilitación de planes de escalado para grupos de hosts nuevos y existentes (versión preliminar)

> [!IMPORTANT]
> La característica de escalado automático está actualmente en vista previa.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Puede habilitar planes de escalado para todos los grupos de hosts existentes de la implementación. Al aplicar el plan de escalado al grupo de hosts, también se aplica a todos los hosts de sesión de ese grupo de hosts. El escalado también se aplica automáticamente a los nuevos hosts de sesión que cree en el grupo de hosts asignado.

Si deshabilita un plan de escalado, todos los recursos asignados permanecen en el estado de escalado en el que se encontraban en el momento de la deshabilitación.

## <a name="assign-a-scaling-plan-to-an-existing-host-pool"></a>Asignación de un plan de escalado a un grupo de hosts existente

Para asignar un plan de escalado a un grupo de hosts existente:

1. Abra [Azure Portal](https://portal.azure.com).

2. Vaya a **Windows Virtual Desktop**.

3. Seleccione **Grupos de hosts**, vaya a **Plan de escalado** y seleccione **Nuevo**.

4. Seleccione **Plan de escalado** y luego **+ Asignar** para asignar un plan de escalado a un grupo de hosts sin asignar, como se muestra en la captura de pantalla siguiente.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la ventana del plan de escalado. El botón + Agregar de la esquina superior izquierda está resaltado con un borde rojo.](media/assign-scaling-plan.png)

    Si ha habilitado el plan de escalado durante la implementación, también tiene la opción de deshabilitarlo para el grupo de hosts seleccionado en el menú **Plan de escalado** si desactiva la casilla **Habilitar plan de escalado**, como se muestra en la captura de pantalla siguiente.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la ventana del plan de escalado. La casilla "Habilitar escalado automático" está activada y resaltada con un borde rojo.](media/enable-autoscale.png)

## <a name="edit-an-existing-scaling-plan"></a>Edición de un plan de escalado existente

Para editar un plan de escalado existente:

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com/).

2. Vaya a **Azure Virtual Desktop**.

3. Seleccione **Plan de escalado** y luego el nombre del plan de escalado que quiere editar. Se debería abrir la ventana de configuración.

4. Para editar el nombre para mostrar, la descripción, la zona horaria o las etiquetas de exclusión del plan, vaya a la pestaña **Propiedades**.

5. Para asignar grupos de hosts o editar programaciones, vaya a la pestaña **Administrar**.

## <a name="next-steps"></a>Pasos siguientes

- Revise cómo crear un plan de escalado en [Escalabilidad automática (versión preliminar) de hosts de sesión de Azure Virtual Desktop](autoscale-new-existing-host-pool.md).
- Aprenda a solucionar problemas del plan de escalado en [Habilitación de diagnósticos del plan de escalado](autoscale-diagnostics.md).