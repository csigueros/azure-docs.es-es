---
title: Administración de un grupo de máquinas virtuales en Azure Lab Services desde Teams
description: Aprenda a administrar un grupo de máquinas virtuales en Azure Lab Services desde Teams.
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 035344fa947403b77c35d716636472730d65752b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180160"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Administración de un grupo de máquinas virtuales en Lab Services desde Teams

La creación de la máquina virtual (VM) se inicia en cuanto se publica por primera vez la máquina virtual de la plantilla. Se crearán tantas máquinas virtuales como número de usuarios haya en la lista de usuarios del laboratorio. Las máquinas virtuales se asignan automáticamente a los alumnos tras su primer inicio de sesión en Azure Lab Services. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Publicación de una plantilla y administración de un grupo de máquinas virtuales

Para publicar la plantilla, vaya a la ventana Lab Services de Teams y seleccione la pestaña **Plantilla** -> **...**  -> **Publicar**.

Una vez configurada la máquina virtual de la plantilla y cuando el educador selecciona publicar la plantilla, se creará el número de máquinas virtuales equivalente al número de usuarios de la lista de usuarios del laboratorio. Una vez publicado el laboratorio y creadas las máquinas virtuales, los usuarios se registrarán automáticamente en el laboratorio y las máquinas virtuales se les asignarán en su primer inicio de sesión en Azure Lab Services, es decir, cuando accedan por primera vez a la pestaña con la aplicación **Azure Lab Services**. 

Cuando se desencadena una sincronización de la lista de usuarios, la capacidad del laboratorio (número de máquinas virtuales del laboratorio) se actualizará automáticamente en función de los cambios en la pertenencia al equipo. Se crearán nuevas máquinas virtuales a medida que se agreguen nuevos usuarios y las máquinas virtuales asignadas a los usuarios quitados del equipo también se eliminarán. Para más información, consulte [Administración de usuarios en Teams](how-to-manage-user-lists-within-teams.md). 

Los educadores pueden seguir accediendo a las máquinas virtuales de los alumnos directamente desde la pestaña Grupo de máquinas virtuales. Además, los educadores pueden tener acceso a las máquinas virtuales que se les asignan desde la pestaña **Grupo de máquinas virtuales** o haciendo clic en el botón **Mis máquinas virtuales** (esquina superior derecha de la pantalla). 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="Grupo de máquinas virtuales":::

## <a name="next-steps"></a>Pasos siguientes

Vea los artículos siguientes:

- [Información general del uso de Azure Lab Services en Teams](lab-services-within-teams-overview.md)
- [Introducción y creación de un laboratorio de Lab Services desde Teams](how-to-get-started-create-lab-within-teams.md)
- [Administración de las listas de usuarios de Lab Services desde Teams](how-to-manage-user-lists-within-teams.md)
- [Creación de programaciones de Lab Services desde Teams](how-to-create-schedules-within-teams.md)
- [Acceso a una máquina virtual (vista de alumno) en Lab Services desde Teams](how-to-access-vm-for-students-within-teams.md)


