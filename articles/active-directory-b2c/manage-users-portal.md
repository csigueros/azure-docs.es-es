---
title: Creación y eliminación de cuentas de usuario consumidor de Azure AD B2C en Azure Portal
description: Aprenda a usar Azure Portal para crear y eliminar usuarios consumidores en el directorio de Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 02c0197af40854bfbcff04401dbc4e9ca0d990dc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573904"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Uso de Azure Portal para crear y eliminar usuarios consumidores en Azure AD B2C

Puede haber escenarios en los que desee crear manualmente cuentas de consumidor en el directorio de Azure Active Directory B2C (Azure AD B2C). Aunque las cuentas de consumidor de un directorio de Azure AD B2C se crean normalmente cuando los usuarios se suscriben para utilizar una de las aplicaciones, puede crearlas mediante programación y mediante Azure Portal. Este artículo se centra en el método de Azure Portal para la creación y eliminación de usuarios.

Para agregar o eliminar usuarios, la cuenta debe tener asignado el rol *Administrador de usuarios* o *Administrador de empresa*.

## <a name="types-of-user-accounts"></a>Tipos de cuentas de usuario

Como se describe en [Introducción a las cuentas de usuario de Azure AD B2C](user-overview.md), hay tres tipos de cuentas de usuario que se pueden crear en un directorio de Azure AD B2C:

* Work
* Invitado
* Consumidor

Este artículo se centra en el trabajo con **cuentas de consumidor** en Azure Portal. Para obtener información acerca de la creación y eliminación de cuentas de trabajo y de invitado, consulte [Adición o eliminación de usuarios con Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Creación de un usuario consumidor

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En el menú de la izquierda, seleccione **Azure AD B2C**. O bien, seleccione **Todos los servicios** y busque y seleccione **Azure AD B2C**.
1. En **Administrar**, seleccione **Usuarios**.
1. Seleccione **Nuevo usuario**.
1. Seleccione **Crear usuario de Azure AD B2C**.
1. Elija un **Método de inicio de sesión**  y escriba una dirección de **Correo electrónico** o un **Nombre de usuario** para el nuevo usuario. El método de inicio de sesión que seleccione aquí debe coincidir con el valor que ha especificado para el proveedor de identidades de la *Cuenta local* del inquilino de Azure AD B2C (consulte **Administrar** > **Proveedores de identidades** en el inquilino de Azure AD B2C).
1. Escriba un **Nombre** para el usuario. Suele ser el nombre completo (nombre y apellidos) del usuario.
1. (Opcional) Puede **Bloquear inicio de sesión** si desea retrasar la posibilidad de que el usuario inicie sesión. Puede habilitar el inicio de sesión más adelante; para ello, edite el **Perfil** del usuario en Azure Portal.
1. Elija **Autogenerate password** (Generar automáticamente la contraseña) o **Let me create password** (Permitirme crear la contraseña).
1. Escriba el **Nombre** y el **Apellido** del usuario.
1. Seleccione **Crear**.

A menos que haya seleccionado **Bloquear inicio de sesión**, el usuario ahora puede iniciar sesión con el método de inicio de sesión (correo electrónico o nombre de usuario) que se haya especificado.

## <a name="reset-a-users-password"></a>Restablecimiento de la contraseña del usuario

Como administrador, puede restablecer la contraseña de un usuario si este la olvida. Al restablecer la contraseña del usuario, se genera automáticamente una contraseña temporal para el usuario. La contraseña temporal nunca expira. La próxima vez que el usuario inicie sesión, la contraseña seguirá funcionando, sin importar cuánto tiempo haya transcurrido desde que se generó la contraseña temporal. Después, el usuario debe restablecer la contraseña en una permanente. 

> [!IMPORTANT]
> Antes de restablecer la contraseña de un usuario, [configure un flujo para forzar el restablecimiento de la contraseña en Azure Active Directory B2C](force-password-reset.md); de lo contrario, el usuario no podrá iniciar sesión.

Para restablecer la contraseña de un usuario:

1. En el directorio de Azure AD B2C, seleccione **Usuarios** y, a continuación, seleccione el usuario para el que quiere restablecer la contraseña.
1. Busque y seleccione el usuario que necesita el restablecimiento y haga clic en **Restablecer contraseña**.

    Se muestra la página **Alain Charon - Perfil** con la opción **Restablecer contraseña**.

    ![Página de perfil del usuario, con la opción Restablecer contraseña resaltada](media/manage-users-portal/user-profile-reset-password-link.png)

1. En la página **Restablecer contraseña**, seleccione **Restablecer contraseña**.
1. Copie la contraseña y proporciónela al usuario. El usuario deberá cambiar la contraseña durante el siguiente proceso de inicio de sesión.


## <a name="delete-a-consumer-user"></a>Eliminación de un usuario consumidor

1. En el directorio de Azure AD B2C, seleccione **Usuarios** y, a continuación, seleccione el usuario que desea eliminar.
1. Seleccione **Eliminar** y, después, **Sí** para confirmar la eliminación.

Para más información sobre cómo restaurar un usuario durante los primeros 30 días después de la eliminación o para eliminar de forma permanente un usuario, consulte [Restauración o eliminación de un usuario eliminado recientemente con Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Pasos siguientes

Para escenarios de administración automatizada de usuarios, por ejemplo, la migración de usuarios de otro proveedor de identidades al directorio de Azure AD B2C, consulte [Azure AD B2C: migración de usuarios](user-migration.md).
