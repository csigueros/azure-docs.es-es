---
title: Configuración del apagado automático de las máquinas virtuales de un laboratorio en Azure Lab Services
description: Obtenga información sobre cómo habilitar o deshabilitar el apagado automático de las máquinas virtuales cuando se desconecta una conexión a escritorio remoto.
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: 2b3e589e66749e1b1d9b2348a00cd3c3c432a4e5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180312"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Configuración del apagado automático de las máquinas virtuales de un laboratorio

En este artículo se muestra cómo configurar el apagado automático de las máquinas virtuales de un laboratorio.

Cuando las máquinas virtuales no se están usando, se pueden habilitar varias características de control de costos para el apagado automático, a fin de impedir costos adicionales. La combinación de las tres características siguientes de apagado y desconexión automáticos detecta la mayoría de los casos en los que los usuarios dejan funcionando sin querer sus máquinas virtuales:
 
* Desconexión automática de los usuarios de las máquinas virtuales que el sistema operativo considere inactivas.
* Apagado automático de las máquinas virtuales cuando los usuarios se desconecten.
* Apagar automáticamente las máquinas virtuales que se han iniciado, pero a las que los usuarios no se conectan.

Puede encontrar más detalles sobre las características de apagado automático en la sección [Maximización del control de costos con la configuración de apagado automático](cost-management-guide.md#automatic-shutdown-settings-for-cost-control).

Un administrador de cuentas de laboratorio puede configurar esta opción para la cuenta de laboratorio en la que crea laboratorios. Para más información, consulte [Configuración del apagado automático de las máquinas virtuales de una cuenta de laboratorio](how-to-configure-lab-accounts.md). Como propietario de un laboratorio, puede invalidar la configuración al crear un laboratorio o después de su creación. 

## <a name="configure-for-the-lab-level"></a>Configuración en el nivel de laboratorio

Se puede configurar la opción de apagado automático en [Azure Lab Services](https://labs.azure.com/).

* al crear un laboratorio (en **Directivas de laboratorio**), o
* una vez creado el laboratorio (en **Configuración**).

> [!div class="mx-imgBorder"]
> ![Configuración al crear el laboratorio](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Asegúrese de revisar los detalles sobre el apagado automático en la sección [Maximización del control de costos con la configuración de apagado automático](cost-management-guide.md#automatic-shutdown-settings-for-cost-control).

> [!WARNING]
> Si apaga el sistema operativo (SO) Windows o Linux en una máquina virtual antes de desconectar en ella una sesión RDP, la característica de apagado automático no funcionará correctamente.  
## <a name="next-steps"></a>Pasos siguientes

[Panel para laboratorios](use-dashboard.md)
