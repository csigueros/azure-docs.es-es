---
title: Tutorial de creación de un laboratorio
description: En este tutorial, se creará un laboratorio en Azure DevTest Labs desde Azure Portal. Los administradores de laboratorio configuran laboratorios, crean máquinas virtuales en los laboratorios y configuran las directivas.
ms.topic: tutorial
ms.date: 11/03/2021
ms.openlocfilehash: 4a26501511adf65d2db8509120a1d4a014ef73ba
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578612"
---
# <a name="tutorial-set-up-a-lab-in-devtest-labs-using-the-azure-portal"></a>Tutorial: Configuración de un laboratorio en DevTest Labs mediante Azure Portal

En este tutorial, va a crear un laboratorio mediante Azure Portal. El administrador de un laboratorio es quien configura un laboratorio en una organización, crea máquinas virtuales (VM) de Azure en él y configura directivas. Los usuarios de laboratorio (por ejemplo: desarrolladores y evaluadores) reclaman las máquinas virtuales en el laboratorio, se conectan a ellas y las usan. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de un laboratorio
> * Adición de una máquina virtual (VM) de Azure al laboratorio
> * Agregue un usuario y asígnele el rol **Usuario de DevTest Labs**.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-a-lab"></a>Creación de un laboratorio

En estos pasos se muestra cómo usar Azure Portal para crear un laboratorio en Azure DevTest Labs. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Escriba `DevTest Labs` en el cuadro de búsqueda y, luego, seleccione **DevTest Labs** en los resultados.

    :::image type="content" source="./media/tutorial-create-custom-lab/portal-search-devtest-labs.png" alt-text="Captura de pantalla de la búsqueda en el portal de DevTest Labs.":::

1. En la página **DevTest Labs**, seleccione **+ Crear**.

1. En la página **Create Devtest Lab** (Crear laboratorio de DevTest Labs), en la pestaña **Basic Settings** (Configuración básica), proporcione la siguiente información:

    |Propiedad | Descripción |
    |---|---|
    |Subscription| En la lista desplegable, seleccione la suscripción de Azure que se va a usar para el laboratorio.|
    |Grupo&nbsp;de recursos| En la lista desplegable, seleccione el grupo de recursos existente o seleccione **Crear**.|
    |Nombre del laboratorio| Escriba un nombre para el laboratorio.|
    |Location| En la lista desplegable, seleccione una ubicación para el laboratorio.|
    |Entornos públicos| Deje el valor predeterminado de **Activado**. El repositorio del entorno público contiene una lista de plantillas de Azure Resource Manager conservadas que permiten a los usuarios de los laboratorios crear recursos PaaS dentro de Labs.|

    :::image type="content" source="./media/tutorial-create-custom-lab/create-custom-lab-blade.png" alt-text="Captura de pantalla de la pestaña Basic Settings (Configuración básica) para crear laboratorios de DevTest Labs.":::

1. Seleccione **Revisar y crear** para validar la configuración y, luego, elija **Crear**. En este tutorial, sirven los valores predeterminados de las otras pestañas.

1. Cuando finalice el proceso de creación, seleccione **Ir al recurso** en la notificación de implementación.

    :::image type="content" source="./media/tutorial-create-custom-lab/creation-notification.png" alt-text="Captura de pantalla de la notificación de implementación de DevTest Labs.":::

1. La página **Información general** del laboratorio es similar a la siguiente imagen:

    :::image type="content" source="./media/tutorial-create-custom-lab/lab-home-page.png" alt-text="Captura de pantalla de la página Información general de DevTest Labs.":::

## <a name="add-a-vm-to-the-lab"></a>Adición de una máquina virtual al laboratorio

1. En la página **DevTest Lab**, seleccione **+ Agregar** en la barra de herramientas.

    :::image type="content" source="./media/tutorial-create-custom-lab/add-vm-to-lab-button.png" alt-text="Captura de pantalla de la página de información general de DevTest Labs y del botón Agregar.":::

1. En la página **Choose a base** (Elegir una base), seleccione una imagen de Marketplace para la máquina virtual. En esta guía se usa **Windows Server 2019 Datacenter**. Algunas opciones pueden variar si usa una imagen diferente.

1. En la pestaña **Basics Settings** (Configuración básica), proporcione la siguiente información:

    |Propiedad |Descripción |
    |---|---|
    |Nombre&nbsp;de máquina&nbsp;virtual| El cuadro de texto se rellena previamente con un nombre único generado de forma automática. El nombre corresponde al nombre de usuario de la dirección de correo electrónico, seguido por un número único de tres dígitos. Déjelo así o escriba un nombre único de su elección.|
    |Nombre de usuario| El cuadro de texto se rellena previamente con un nombre único generado de forma automática. El nombre corresponde al nombre de usuario dentro de la dirección de correo electrónico. Déjelo así o escriba el nombre que prefiera. A este usuario se le conceden privilegios de **administrador** en la máquina virtual.|
    |Use a saved secret (Usar un secreto guardado)| Para los fines de este tutorial, deje la casilla desactivada. Puede guardar primero los secretos en Azure Key Vault y, luego, usarlos aquí. Para más información, consulte [Almacenamiento de secretos en un almacén de claves](devtest-lab-store-secrets-in-key-vault.md). Si prefiere usar un secreto guardado, active la casilla y, luego, seleccione el secreto en la lista desplegable **Secreto**.|
    |Contraseña|Escriba una contraseña que tenga entre 8 y 123 caracteres.|
    |Save as default password (Guardar como contraseña predeterminada)| Active la casilla para guardar la contraseña en la instancia de Azure Key Vault asociada al laboratorio.|
    |Tamaño de la máquina virtual| Mantenga el valor predeterminado o seleccione **Cambiar tamaño** para seleccionar distintos componentes físicos. En este tutorial se usa **Standard_D4_v3**.|
    |Tipo de disco del sistema operativo|Mantenga el valor predeterminado o seleccione otra opción en la lista desplegable.|
    |Artifacts| No se usa en este tutorial.|

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-basic-settings.png" alt-text="Captura de pantalla de la página de configuración básica de máquinas virtuales.":::

1. Seleccione la pestaña **Configuración avanzada** y proporcione la siguiente información:

    |Propiedad |Descripción |
    |---|---|
    |Virtual network| Déjela así o seleccione otra red en la lista desplegable.|
    |Selector de&nbsp;subred| Déjelo así o seleccione otra subred en la lista desplegable.|
    |Dirección IP| Para los fines de este tutorial, deje el valor predeterminado **Compartido**. Cuando se selecciona **Compartido**, Azure DevTest Labs habilita RDP automáticamente para las máquinas virtuales Windows y SSH para las máquinas virtuales Linux. Si selecciona **Público**, se habilitan RDP y SSH sin cambios en DevTest Labs.  |
    |Fecha de expiración| Déjela como está, sin fecha de expiración, o seleccione el icono de calendario para establecer una fecha.|
    |Hacer que esta máquina sea reclamable| Deje esta opción en **No**. Para que un usuario de laboratorio pueda reclamar la máquina virtual, seleccione **Sí**. Marcar la máquina como reclamable significa que no se le asignará la propiedad en el momento de la creación. |
    |Número de instancias| Déjelo como está, en **1**. Número de instancias de máquina virtual que se crearán.|
    |Automation | Opcional. Al seleccionar **Ver plantilla de ARM**, se abrirá la plantilla en una nueva página. Puede copiar y guardar la plantilla para crear la misma máquina virtual más tarde. Una vez guardada, puede utilizar la plantilla de Azure Resource Manager para [implementar nuevas máquinas virtuales con Azure PowerShell](../azure-resource-manager/templates/overview.md).|

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-advanced-settings.png" alt-text="Página de configuración avanzada de la máquina virtual.":::

1. Vuelva a la pestaña **Basic Settings** (Configuración básica) y, luego, seleccione **Crear**.

1. Volverá a la página **DevTest Lab** (Laboratorio de DevTest Labs). En **Mi laboratorio**, seleccione **Máquinas virtuales que se pueden reclamar**.

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-vm-creation-status.png" alt-text="Captura de pantalla de la página de estado de creación de máquinas virtuales de laboratorio.":::

1. Deje pasar unos minutos y, si las máquinas virtuales no aparecen, seleccione **Actualizar**. Los tiempos de instalación variarán en función del hardware, la imagen base y los artefactos seleccionados. La instalación de las configuraciones usadas en este tutorial tardó aproximadamente 12 minutos.

## <a name="add-a-user-to-the-devtest-labs-user-role"></a>Adición de un usuario al rol de usuario de DevTest Labs

1. Vaya al grupo de recursos que contiene el laboratorio que ha creado. Debe tener el rol de [Administrador de acceso de usuario](../role-based-access-control/built-in-roles.md#user-access-administrator) o [Propietario](../role-based-access-control/built-in-roles.md#owner).

1. En el menú izquierdo, seleccione **Control de acceso (IAM)** .

1. Seleccione **Agregar** > **Agregar asignación de roles**.

    ![Página Control de acceso (IAM) con el menú Agregar asignación de roles abierto.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. En la pestaña **Rol**, seleccione el rol **Usuario de DevTest Labs**.

    ![Página Agregar asignación de roles con la pestaña Rol seleccionada.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. En la pestaña **Miembros**, seleccione el usuario al que quiere asignar el rol.

1. En la pestaña **Revisión y asignación**, seleccione **Revisión y asignación** para asignar el rol.

## <a name="clean-up-resources"></a>Limpieza de recursos

Elimine los recursos para evitar cargos por ejecutar el laboratorio y la máquina virtual en Azure. Si tiene previsto consultar el siguiente tutorial para acceder a la máquina virtual del laboratorio, puede limpiar los recursos cuando lo finalice. De lo contrario, siga estos pasos. 

1. Vuelva a la página principal del laboratorio que ha creado.

1. Seleccione **Eliminar** en el menú superior.

   :::image type="content" source="./media/tutorial-create-custom-lab/portal-lab-delete.png" alt-text="Captura de pantalla del botón Eliminar del laboratorio.":::

1. En la página **¿Seguro que quiere eliminarlo?** , escriba el nombre del laboratorio en el cuadro de texto y seleccione **Eliminar**.

1. Durante la eliminación, puede seleccionar **Notificaciones** en la parte superior de la pantalla para ver el progreso. La eliminación del laboratorio tarda un rato. Cuando se haya eliminado el laboratorio, continúe con el paso siguiente.

1. Si creó el laboratorio en un grupo de recursos existente, se quitarán todos los recursos del laboratorio. Si creó un grupo de recursos para este tutorial, ahora está vacío y se puede eliminar. No habría sido posible eliminar el grupo de recursos anteriormente mientras el laboratorio todavía pertenecía a él.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un laboratorio, ha agregado una máquina virtual y, luego, ha dado acceso al laboratorio a un usuario. Para más información sobre cómo acceder al laboratorio como un usuario de laboratorio, avance al tutorial siguiente:

> [!div class="nextstepaction"]
> [Tutorial: Acceso al laboratorio](tutorial-use-custom-lab.md)
