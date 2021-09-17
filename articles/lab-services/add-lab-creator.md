---
title: Incorporación de un usuario como creador de un laboratorio en Azure Lab Services
description: En este artículo se muestra cómo agregar un usuario al rol Creador de laboratorios para una cuenta de laboratorio en Azure Lab Services. Los creadores de laboratorios pueden crearlos en esta cuenta de laboratorio.
ms.topic: article
ms.date: 07/26/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: a2135ab6580d39d6c63f7e948a29f0f0ede4efd6
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634198"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Incorporación de creadores de laboratorios a una cuenta de laboratorio de Azure Lab Services
En este artículo se muestra cómo agregar usuarios como creadores de laboratorios a una cuenta de laboratorio en Azure Lab Services. Estos usuarios pueden, posteriormente, crear laboratorios en la cuenta de laboratorio. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Incorporación de una cuenta de usuario de Microsoft al rol Creador de laboratorios
Para configurar un laboratorio de clase en una cuenta de laboratorio, el usuario debe ser miembro del rol **Creador de laboratorio** en la cuenta de laboratorio. La cuenta que usó para crear la cuenta de laboratorio se agrega automáticamente a este rol. Si pretende usar la misma cuenta de usuario para crear un laboratorio de clase, puede omitir este paso. Para usar otra cuenta de usuario para crear un laboratorio de clase, siga los pasos siguientes: 

Para proporcionar a los educadores el permiso para crear laboratorios para sus clases agréguelos al rol **Creador de laboratorios**. Para obtener los pasos detallados, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).


1. En la página **Cuenta de laboratorio**, seleccione **Control de acceso (IAM)** .

1. Seleccione **Agregar** > **Agregar asignación de roles (versión preliminar)** .

    ![Página Control de acceso (IAM) con el menú Agregar asignación de roles abierto.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. En la pestaña **Rol**, seleccione el rol **Creador de laboratorios**.

    ![Página Agregar asignación de roles con la pestaña Rol seleccionada.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. En la pestaña **Miembros**, seleccione el usuario que quiere agregar al rol Creador de laboratorios.

1. En la pestaña **Revisión y asignación**, seleccione **Revisión y asignación** para asignar el rol.




    > [!NOTE]
    > Si va a agregar un usuario que no es de la cuenta de Microsoft como creador de laboratorio, consulte la sección [Incorporación de un usuario que no es de una cuenta de Microsoft como creador de laboratorio](#add-a-non-microsoft-account-user-as-a-lab-creator). 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Incorporación de un usuario que no es de una cuenta de Microsoft como creador de laboratorio
Para agregar un usuario como creador de laboratorio, se usan sus cuentas de correo electrónico. Se pueden usar los siguientes tipos de cuentas de correo electrónico:

- Una cuenta de correo electrónico proporcionada por la instancia de Azure Active Directory (AAD) de la universidad.
- Una cuenta de correo electrónico de Microsoft, como `@outlook.com`, `@hotmail.com`, `@msn.com` o `@live.com`.
- Una cuenta de correo electrónico que no sea de Microsoft, como las proporcionadas por Yahoo o Google. No obstante, estos tipos de cuentas se deben vincular a una cuenta de Microsoft.
- Una cuenta de GitHub. Esta cuenta se debe vincular a una cuenta de Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Uso de una cuenta de correo electrónico que no sea de Microsoft
Los creadores e instructores de laboratorio pueden usar cuentas de correo electrónico que no sean de Microsoft para registrarse e iniciar sesión en un laboratorio de clase.  Sin embargo, el inicio de sesión en el portal de los servicios de laboratorio exige que los instructores creen primero una cuenta de Microsoft que esté vinculada a su dirección de correo electrónico que no es de Microsoft.

Es posible que muchos instructores ya tengan una cuenta de Microsoft vinculada a sus direcciones de correo electrónico que no son de Microsoft. Por ejemplo, los instructores ya tienen una cuenta de Microsoft si han usado su dirección de correo electrónico con otros productos o servicios de Microsoft, como Office, Skype, OneDrive o Windows.  

Cuando los instructores inicien sesión en el portal de servicios de laboratorio, se les pedirá su dirección de correo electrónico y contraseña. Si el instructor intenta iniciar sesión con una cuenta que no es de Microsoft y que no tiene una cuenta de Microsoft vinculada, recibe el mensaje de error siguiente: 

![Mensaje de error](./media/how-to-configure-student-usage/cant-find-account.png)

Para suscribirse a una cuenta de Microsoft, los instructores deben ir a [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Uso de una cuenta de GitHub
Los instructores también pueden usar una cuenta de GitHub existente para registrarse e iniciar sesión en un laboratorio de clase. Si el instructor ya tiene una cuenta de Microsoft vinculada a su cuenta de GitHub, puede iniciar sesión y proporcionar su contraseña como se muestra en la sección anterior. Si aún no ha vinculado su cuenta de GitHub a una cuenta de Microsoft, debe seleccionar **Opciones de inicio de sesión**:

![Vínculo de opciones de inicio de sesión](./media/how-to-configure-student-usage/signin-options.png)

En la página **Opciones de inicio de sesión**, seleccione **Iniciar sesión con GitHub**.

![Vínculo de inicio de sesión con GitHub](./media/how-to-configure-student-usage/signin-github.png)

Por último, se le pide que cree una cuenta de Microsoft que esté vinculada a su cuenta de GitHub. Sucede automáticamente cuando el instructor selecciona **Siguiente**.  Luego el instructor inicia sesión inmediatamente y se conecta al laboratorio de clase.


## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- [Como propietario del laboratorio, crear y administrar laboratorios](how-to-manage-classroom-labs.md)
- [Como propietario del laboratorio, configurar y publicar plantillas](how-to-create-manage-template.md)
- [Como propietario del laboratorio, configurar y controlar el uso de un laboratorio](how-to-configure-student-usage.md)
- [Como usuario de laboratorio, obtener acceso a los laboratorios](how-to-use-classroom-lab.md)
