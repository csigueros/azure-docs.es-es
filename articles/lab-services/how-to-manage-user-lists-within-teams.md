---
title: Administración de las listas de usuarios de Azure Lab Services desde Teams
description: Aprenda a administrar las listas de usuarios de Azure Lab Services desde Teams.
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 598edd21943364d25f59848236f838cb10fa3ce5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180198"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Administración de las listas de usuarios de Lab Services desde Teams

Cuando se crea un laboratorio en Teams (consulte [Introducción y creación de un laboratorio de Lab Services desde Teams](how-to-get-started-create-lab-within-teams.md)), la lista de usuarios del laboratorio se rellena y se sincroniza automáticamente con la pertenencia al equipo. Todos los usuarios del equipo, incluidos los propietarios, los miembros y los invitados, se agregarán automáticamente a la lista de usuarios del laboratorio. Azure Lab Services mantiene una sincronización con la pertenencia al equipo y se desencadena una sincronización automática cada 24 horas. 

## <a name="sync-users"></a>Sincronización de usuarios

Los educadores pueden usar el botón **Sincronizar** para desencadenar una sincronización manual una vez que se actualice la pertenencia al equipo. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Sincronización de usuarios":::

Una vez completada la sincronización automática o manual, se aplica lo siguiente en función de si se ha publicado el laboratorio.

* Si el laboratorio no se ha publicado al menos una vez:
    * Los usuarios se agregarán o eliminarán de la lista de usuarios del laboratorio según los cambios en la pertenencia al equipo. 
* Si el laboratorio se ha publicado al menos una vez, además de agregar o eliminar usuarios, la capacidad del laboratorio se actualizará automáticamente.
    * Si hay nuevas adiciones al equipo, se crearán nuevas máquinas virtuales.
    * Si se ha eliminado algún usuario del equipo, también se eliminará la máquina virtual asociada.

## <a name="next-steps"></a>Pasos siguientes

Una vez configurada la máquina virtual de la plantilla y cuando el educador selecciona publicar la plantilla, se creará el número de máquinas virtuales equivalente al número de usuarios de la lista de usuarios del laboratorio. Una vez publicado el laboratorio y creadas las máquinas virtuales, los usuarios se registrarán automáticamente en el laboratorio y las máquinas virtuales se les asignarán en su primer inicio de sesión en Azure Lab Services, es decir, cuando accedan por primera vez a la pestaña con la aplicación **Azure Lab Services**. 

Para publicar la máquina virtual de la plantilla, vaya a la ventana Lab Services de Teams y seleccione la pestaña **Plantilla** -> **...**  -> **Publicar**.

Para administrar grupos de máquinas virtuales, consulte [Administración de un grupo de máquinas virtuales en Lab Services desde Teams](how-to-manage-vm-pool-within-teams.md).

### <a name="also-review"></a>Revisar también

Vea los artículos siguientes:

- [Información general del uso de Azure Lab Services en Teams](lab-services-within-teams-overview.md)
- [Introducción y creación de un laboratorio de Lab Services desde Teams](how-to-get-started-create-lab-within-teams.md)
- [Creación de programaciones de Lab Services desde Teams](how-to-create-schedules-within-teams.md)
- [Acceso a una máquina virtual (vista de alumno) en Lab Services desde Teams](how-to-access-vm-for-students-within-teams.md)

