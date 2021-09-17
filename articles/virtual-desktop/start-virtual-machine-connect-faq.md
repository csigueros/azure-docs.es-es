---
title: 'Preguntas frecuentes sobre Iniciar VM al establecer la conexión de Azure Virtual Desktop: Azure'
description: Preguntas frecuentes y procedimientos recomendados para usar la característica Iniciar VM al establecer la conexión.
author: Heidilohr
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0ba2fcbc404a17b4b31d48b4e7d2e540a875f4f1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444072"
---
# <a name="start-vm-on-connect-faq"></a>Preguntas frecuentes sobre el inicio de máquina virtual al conectar

En este artículo se tratan las preguntas frecuentes sobre la característica Iniciar máquina virtual (VM) al establecer la conexión para los grupos de hosts de Azure Virtual Desktop.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>¿Se desasignan automáticamente las VM cuando un usuario deja de usarlas?

No. Tendrá que configurar directivas adicionales para cerrar la sesión de los usuarios y ejecutar scripts de Azure Automation para desasignar las VM.

Para configurar la directiva de desasignación:

1. Conéctese de forma remota a la VM para la que quiere establecer la directiva.

2. Abra el **Editor de directivas de grupo**, luego vaya a **Directiva de equipo local** > **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows** > **Servicios de escritorio remoto** > **Host de sesión de escritorio remoto** > **Límites de tiempo de sesión**.

3. Busque la directiva que indica **Establecer el límite de tiempo para las sesiones desconectadas**, a continuación, cambie su valor a **Habilitado**.

4. Una vez habilitada la directiva, seleccione **Finalizar una sesión desconectada**.

>[!NOTE]
>Asegúrese de establecer el límite de tiempo para la directiva "Finalizar una sesión desconectada" en un valor superior a cinco minutos. Un límite de tiempo bajo puede hacer que las sesiones de los usuarios finalicen si sus redes pierden la conexión durante demasiado tiempo, lo que da lugar a la pérdida de trabajo.

Al cerrar la sesión de los usuarios, no se desasignan las VM. Para obtener información sobre cómo desasignar máquinas virtuales, consulte [Introducción a la característica Start or stop VMs during off hours](../automation/automation-solution-vm-management.md) (Iniciar o detener máquinas virtuales durante las horas de desconexión) para grupos de hosts personales y [Escalado de hosts de sesión con Azure Automation](set-up-scaling-script.md) para grupos de hosts agrupados.

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>¿Pueden los usuarios apagar las VM desde sus clientes?

Sí. Los usuarios pueden apagar una VM mediante el menú Inicio dentro de su sesión, tal como lo harían con una máquina física. Sin embargo, apagar una VM no la desasignará. Para obtener información sobre cómo desasignar máquinas virtuales, consulte [Introducción a la característica Start or stop VMs during off hours](../automation/automation-solution-vm-management.md) (Iniciar o detener máquinas virtuales durante las horas de desconexión) para grupos de hosts personales y [Escalado de hosts de sesión con Azure Automation](set-up-scaling-script.md) para grupos de hosts agrupados.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo configurar Iniciar VM al establecer la conexión, consulte [Iniciar máquina virtual al establecer la conexión](start-virtual-machine-connect.md).

Si tiene preguntas más generales sobre Azure Virtual Desktop, consulte nuestras [preguntas frecuentes](faq.yml).
