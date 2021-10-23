---
title: Eliminación de un laboratorio de Azure Lab Services desde Teams
description: Aprenda a eliminar un laboratorio de Azure Lab Services desde Teams.
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f93151e970fc38ca55b43c5322bf27862ad32b7a
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180521"
---
# <a name="delete-labs-within-teams"></a>Eliminación de laboratorios en Teams

En este artículo se muestra cómo eliminar un laboratorio de la aplicación **Azure Lab Services**.

## <a name="prerequisites"></a>Requisitos previos

* [Creación de una cuenta de laboratorio](tutorial-setup-lab-account.md#create-a-lab-account) en Azure Portal.
* [Introducción y creación de un laboratorio de Lab Services desde Teams](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Eliminación de laboratorios

Un laboratorio creado en Teams se puede eliminar desde el [sitio web de Lab Services](https://labs.azure.com) si se elimina el laboratorio directamente, tal como se explica en [Administración de laboratorios en Azure Lab Services](how-to-manage-classroom-labs.md). 

La eliminación de laboratorios también se desencadena cuando se elimina el equipo. Si se elimina el equipo en el que se ha creado el laboratorio, este se elimina automáticamente 24 horas después de que se desencadene la sincronización automática de la lista de usuarios. 

> [!IMPORTANT]
> La eliminación de la pestaña o la desinstalación de la aplicación no da lugar a la eliminación del laboratorio. 

Si se elimina la pestaña, los usuarios de la lista de miembros del equipo siguen teniendo acceso a las VM en el [sitio web de Lab Services](https://labs.azure.com), a menos que la eliminación del laboratorio se desencadene explícitamente mediante la eliminación del laboratorio en el sitio web o la del equipo. 

## <a name="next-steps"></a>Pasos siguientes

- [Información general del uso de Azure Lab Services en Teams](lab-services-within-teams-overview.md)
- [Administración de listas de usuarios de laboratorio dentro de Teams](how-to-manage-user-lists-within-teams.md)
- [Administración del grupo de máquinas virtuales del laboratorio en Teams](how-to-manage-vm-pool-within-teams.md)
- [Creación y administración de programaciones de laboratorio dentro de Teams](how-to-create-schedules-within-teams.md)
- [Acceso a una máquina virtual en Teams: vista del alumno](how-to-access-vm-for-students-within-teams.md)

