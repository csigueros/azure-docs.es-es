---
title: Creación programaciones de Azure Lab Services en Teams
description: Más información sobre cómo crear programaciones de Azure Lab Services en Teams.
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 5427105d0bac3fd652764345c1d31fd0ef8bb2db
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180578"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Creación y administración de programaciones de Azure Lab Services en Teams

Las programaciones le permiten configurar un laboratorio educativo de forma que las máquinas virtuales que contiene se inicien y apaguen automáticamente a una hora determinada. Puede definir una programación puntual o periódica. Los procedimientos siguientes le proporcionan los pasos para crear y administrar programaciones para un laboratorio educativo: 

Aquí se muestra cómo afectan las programaciones a las máquinas virtuales de laboratorio: 

- La máquina virtual de plantilla no se incluye en las programaciones. 
- Solo se inician las máquinas virtuales asignadas. Esto significa que, si un usuario final (alumno) no reclama una máquina, esta no se iniciará en las horas programadas. 
- Todas las máquinas virtuales (tanto las reclamadas por un usuario como si no) se detienen según la programación del laboratorio. 

> [!IMPORTANT]
> La hora de ejecución programada de las máquinas virtuales no cuentan en la cuota asignada a un usuario. La cuota es el tiempo fuera del horario programado que un alumno pasa en las máquinas virtuales. 

Los usuarios pueden crear, editar y eliminar programaciones de laboratorio dentro de los equipos del mismo modo que en el [sitio web de laboratorios](https://labs.azure.com). Consulte el artículo sobre [cómo crear y administrar programaciones](how-to-create-schedules-within-teams.md).

## <a name="automatic-shutdown-and-disconnect-settings"></a>Valores de apagado y desconexión automática

Cuando las máquinas virtuales no se están usando, se pueden habilitar varias características de control de costos para el apagado automático, a fin de impedir costos adicionales. La combinación de las tres características siguientes de apagado y desconexión automáticos detecta la mayoría de los casos en los que los usuarios dejan funcionando sin querer sus máquinas virtuales:
 
- Desconexión automática de los usuarios de las máquinas virtuales que el sistema operativo considere inactivas.
- Apagado automático de las máquinas virtuales cuando los usuarios se desconecten.
- Apagar automáticamente las máquinas virtuales que se han iniciado, pero a las que los usuarios no se conectan.

Para más información, consulte el artículo sobre [cómo configurar el valor de apagado automático para un laboratorio](how-to-enable-shutdown-disconnect.md).

## <a name="next-steps"></a>Pasos siguientes

Vea los artículos siguientes:

- [Información general del uso de Azure Lab Services en Teams](lab-services-within-teams-overview.md)
- [Introducción y creación de un laboratorio en Teams](how-to-get-started-create-lab-within-teams.md)
- [Administración de listas de usuarios de laboratorio dentro de Teams](how-to-manage-user-lists-within-teams.md)
- [Administración del grupo de máquinas virtuales del laboratorio en Teams](how-to-manage-vm-pool-within-teams.md)
- [Acceso a una máquina virtual en Teams: vista del alumno](how-to-access-vm-for-students-within-teams.md)
