---
title: Configuración del apagado automático de las VM en Azure Lab Services
description: En este artículo se describe cómo configurar el apagado automático de las VM en la cuenta de laboratorio.
ms.topic: how-to
ms.date: 08/17/2020
ms.openlocfilehash: fabadbfc61a10b7ca53d0aa4711051a12347bd1d
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180768"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Configuración del apagado automático de las máquinas virtuales de una cuenta de laboratorio

Cuando las máquinas virtuales no se están usando, se pueden habilitar varias características de control de costos para el apagado automático, a fin de impedir costos adicionales. La combinación de las tres características siguientes de apagado y desconexión automáticos detecta la mayoría de los casos en los que los usuarios dejan funcionando sin querer sus máquinas virtuales:
 
- Desconexión automática de los usuarios de las máquinas virtuales que el sistema operativo considere inactivas.
- Apagado automático de las máquinas virtuales cuando los usuarios se desconecten.
- Apagar automáticamente las máquinas virtuales que se han iniciado, pero a las que los usuarios no se conectan.

Puede encontrar más detalles sobre las características de apagado automático en la sección [Maximización del control de costos con la configuración de apagado automático](cost-management-guide.md#automatic-shutdown-settings-for-cost-control).

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
