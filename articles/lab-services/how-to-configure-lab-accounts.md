---
title: Configuración del apagado automático de las VM en Azure Lab Services
description: En este artículo se describe cómo configurar el apagado automático de las VM en la cuenta de laboratorio.
ms.topic: how-to
ms.date: 08/17/2020
ms.openlocfilehash: 40b4f3971d2fd9fc84337bb38d49d04212b3f5ba
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228532"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Configuración del apagado automático de las máquinas virtuales de una cuenta de laboratorio

Cuando las máquinas virtuales no se están usando, se pueden habilitar varias características de control de costos para el apagado automático, a fin de impedir costos adicionales. La combinación de las tres características siguientes de apagado y desconexión automáticos detecta la mayoría de los casos en los que los usuarios dejan funcionando sin querer sus máquinas virtuales:
 
- Desconexión automática de los usuarios de las máquinas virtuales que el sistema operativo considere inactivas.
- Apagado automático de las máquinas virtuales cuando los usuarios se desconecten.
- Apagar automáticamente las máquinas virtuales que se han iniciado, pero a las que los usuarios no se conectan.

Puede encontrar más detalles sobre las características de apagado automático en la sección [Maximización del control de costos con la configuración de apagado automático](cost-management-guide.md#automatic-shutdown-settings-for-cost-control).

> [!IMPORTANT]
> Los laboratorios de Linux solo admiten el apagado automático cuando los usuarios se desconectan y cuando se inician las máquinas virtuales, pero los usuarios no se conectan.  La compatibilidad también varía en función de [distribuciones y versiones específicas de Linux](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions).  La configuración de apagado no es compatible con la imagen de [Data Science Virtual Machine imagen de Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). 

## <a name="enable-automatic-shutdown"></a>Habilitación del apagado automático

1. En [Azure Portal](https://portal.azure.com/), vaya a la página **Cuenta de laboratorio**.
1. Seleccione **Configuración del laboratorio** en el menú de la izquierda.
1. Seleccione las opciones de apagado automático apropiadas para su escenario.  

    > [!div class="mx-imgBorder"]
    > ![Configuración del apagado automático en la cuenta de laboratorio](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    Esta configuración se aplica a todos los laboratorios creados en la cuenta de laboratorio. Un creador de laboratorio (formador) puede invalidar esta configuración en el nivel de laboratorio. El cambio que se realice en esta configuración en la cuenta de laboratorio solo afectará a los laboratorios creados después.

    Para deshabilitar la configuración, desactive las casillas de esta página.

## <a name="next-steps"></a>Pasos siguientes

Para saber cómo el propietario de un laboratorio puede configurar o invalidar esta configuración en el nivel de laboratorio, consulte [Configuración del apagado automático de máquinas virtuales para un laboratorio](how-to-enable-shutdown-disconnect.md).
