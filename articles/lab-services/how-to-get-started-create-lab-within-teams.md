---
title: Introducción y creación de un laboratorio de Azure Lab Services en Teams
description: Obtenga información de cómo crear y empezar a usar un laboratorio de Azure Lab Services en Teams.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e406a73a086a742bcb30d0c45c1ebda629b6bdf2
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180236"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Introducción y creación de un laboratorio de Lab Services en Teams

En este artículo se muestra cómo agregar la aplicación **Azure Lab Services** a un equipo y cómo crear un laboratorio en el entorno de MS Teams.

## <a name="prerequisites"></a>Requisitos previos

En este tutorial, configurará un laboratorio con máquinas virtuales para su equipo. Para configurar un laboratorio en una cuenta de laboratorio, debe ser miembro de alguno de estos roles en la cuenta de laboratorio: Propietario, Creador de laboratorio o Colaborador. La cuenta que usó para crear una cuenta de laboratorio se agrega automáticamente al rol de propietario. Por lo tanto, para crear un laboratorio, puede usar la cuenta de usuario que utilizó para crear una cuenta de laboratorio.

Este es el flujo de trabajo típico al usar Azure Lab Services en Teams:

1. El usuario [crea una cuenta de laboratorio](tutorial-setup-lab-account.md#create-a-lab-account) en Azure Portal.
1. El [creador de una cuenta de laboratorio puede agregar otros usuarios](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) al rol **Creador de laboratorio**. Por ejemplo, el creador o administrador de la cuenta de laboratorio agrega formadores al rol **Creador de laboratorio** para que puedan crear laboratorios para sus clases.
1. Después, los educadores crean laboratorios, configuran previamente la VM de plantilla y publican el laboratorio para aprovisionar las VM para todos los usuarios del equipo.
1. Una vez que se publica el laboratorio, se asigna una VM a todos los usuarios de la lista de pertenencia del equipo cuando inician sesión por primera vez en Azure Lab Services, bien haciendo clic en la pestaña que contiene la aplicación **Azure Lab Services** en Teams (SSO) o accediendo al [sitio web de los laboratorios](https://labs.azure.com). A continuación, los usuarios pueden usar la VM para hacer el trabajo de clase y sus tareas.

> [!IMPORTANT]
> Azure Lab Services se puede usar en Teams solo si las cuentas de laboratorio se crean en el mismo inquilino que Teams.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Agregar la aplicación Azure Lab Services como pestaña a un equipo

Como propietario de un equipo, puede agregar la aplicación **Azure Lab Services** directamente en los canales de Teams y, a continuación, la aplicación estará disponible para que la puedan usar todos los miembros del equipo. Siga los tres pasos que se indican a continuación:

1. Vaya hasta el canal de Teams donde quiera agregar la aplicación y seleccione **+** para agregar una pestaña. 
1. Busque **Azure Lab Services** en las opciones de pestaña y agregue la aplicación. 

    > [!NOTE]
    > Solo los **Propietarios** del equipo pueden crear laboratorios para el equipo.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="Agregar una tabla":::
1. Seleccione la cuenta de Lab Services que quiera usar para crear laboratorios en este equipo. 

    Azure Lab Services usa el inicio de sesión único en el [sitio web de Azure Lab Services](https://labs.azure.com) y extrae todas las cuentas de laboratorio a las que tiene acceso. 

    Se muestran las cuentas que están en el mismo inquilino que Teams y para las que tiene acceso de **Propietario**, **Colaborador** o **Creador**. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="Bienvenido a ALS":::
1. Presione **Guardar** y la pestaña se agregará al canal.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="La pestaña de ALS creada":::

    Ahora puede seleccionar la pestaña **Azure Lab Services** en el canal e iniciar la administración de laboratorios como se describe en los artículos siguientes.

Una vez seleccionada la cuenta de laboratorio, los propietarios del equipo podrán crear laboratorios para el equipo. Todo el proceso de creación de laboratorios y todas las tareas del nivel de laboratorio pueden realizarse dentro de Teams. Los usuarios tendrán la opción de crear varios laboratorios en el mismo equipo y el propietario del equipo, con el acceso adecuado correspondiente al nivel de cuenta de laboratorio, solo verá los laboratorios asociados con el equipo específico.

## <a name="next-steps"></a>Pasos siguientes

Cuando se crea un laboratorio en Teams, la lista de usuarios del laboratorio se rellena y se sincroniza automáticamente con la pertenencia al equipo. Todos los usuarios del equipo, incluidos los propietarios, los miembros y los invitados, se agregarán automáticamente a la lista de usuarios del laboratorio. Azure Lab Services conserva la sincronización con la pertenencia al equipo y se desencadena una sincronización automática cada 24 horas. Para obtener detalles, consulte:

[Administración de las listas de usuarios de Lab Services en Teams](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Consulte también

Vea además los siguientes artículos:

- [Información general del uso de Azure Lab Services en Teams](lab-services-within-teams-overview.md)
- [Administración del grupo de máquinas virtuales del laboratorio en Teams](how-to-manage-vm-pool-within-teams.md)
- [Creación y administración de programaciones de laboratorio dentro de Teams](how-to-create-schedules-within-teams.md)
- [Acceso a una máquina virtual en Teams: vista del alumno](how-to-access-vm-for-students-within-teams.md)
- [Eliminación de laboratorios en Teams](how-to-delete-lab-within-teams.md)
