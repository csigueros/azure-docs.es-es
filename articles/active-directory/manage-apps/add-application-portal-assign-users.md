---
title: 'Inicio rápido: Creación y asignación de una cuenta de usuario'
titleSuffix: Azure AD
description: Cree una cuenta de usuario en el inquilino de Azure Active Directory y asígnela a una aplicación.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 28f5cfe158c13928d9443df22551bcfd6e7d8e85
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058406"
---
# <a name="quickstart-create-and-assign-a-user-account-in-azure-active-directory"></a>Inicio rápido: Creación y asignación de una cuenta de usuario en Azure Active Directory

En esta guía de inicio rápido, usará el centro de administración de Azure Active Directory para crear una cuenta de usuario en el inquilino de Azure Active Directory (Azure AD). Después de crear la cuenta, puede asignarla a la aplicación empresarial que agregó al inquilino.

Para probar los pasos de este inicio rápido, se recomienda usar un entorno que no sea de producción.

## <a name="prerequisites"></a>Requisitos previos

Para crear una cuenta de usuario y asignarla a una aplicación empresarial, necesita lo siguiente:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
- Finalización de los pasos de [Inicio rápido: Adición de una aplicación empresarial en Azure Active Directory](add-application-portal.md).

## <a name="create-a-user-account"></a>Crea una cuenta de usuario

Para crear una cuenta de usuario en el inquilino de Azure AD:

1. Vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con uno de los roles enumerados en los requisitos previos.
1. En el menú izquierdo, seleccione **Usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior del panel.

    :::image type="content" source="media/add-application-portal-assign-users/new-user.png" alt-text="Adición de una nueva cuenta de usuario en el inquilino de Azure AD.":::
    
1. En el campo **Nombre de usuario**, escriba el nombre de usuario de la cuenta usuario. Por ejemplo, `contosouser1@contoso.com`. Asegúrese de cambiar `contoso.com` por el nombre del dominio del inquilino.
1. En el campo **Nombre**, escriba el nombre del usuario de la cuenta. Por ejemplo, `contosouser1`.
1. Deje la opción **Generar automáticamente la contraseña** seleccionada y, a continuación, seleccione **Mostrar contraseña**. Anote el valor que se muestra en el cuadro Contraseña.
1. Seleccione **Crear**.

## <a name="assign-a-user-account-to-an-enterprise-application"></a>Asignación de una cuenta de usuario a una aplicación empresarial

Para asignar una cuenta de usuario a una aplicación empresarial:

1. En el [centro de administración de Azure Active Directory](https://aad.portal.azure.com), seleccione **Aplicaciones empresariales** y, a continuación, busque y seleccione la aplicación a la que desea asignar la cuenta de usuario. Por ejemplo, la aplicación que creó en el inicio rápido anterior llamada **Azure AD SAML Toolkit 1**.
1. En el panel izquierdo, seleccione **Usuarios y grupos** y, a continuación, seleccione **Agregar usuario o grupo**.

    :::image type="content" source="media/add-application-portal-assign-users/assign-user.png" alt-text="Asignación de una cuenta de usuario a una aplicación en el inquilino de Azure AD.":::

1. En el panel **Agregar asignación**, seleccione **Ninguno seleccionado** en **Usuarios y grupos**.
1. Busque y seleccione el usuario que desea asignar a la aplicación. Por ejemplo, `contosouser1@contoso.com`.
1. Elija **Seleccionar**.
1. En el panel **Agregar asignación**, seleccione **Asignar** en la parte inferior del panel.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene previsto completar el siguiente inicio rápido, mantenga la aplicación que ha creado. De lo contrario, puede considerar la posibilidad de eliminarla para limpiar el inquilino. Para más información, consulte [Inicio rápido: Eliminación de una aplicación del inquilino](delete-application-portal.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo configurar el inicio de sesión único para una aplicación empresarial.
> [!div class="nextstepaction"]
> [Habilitar el inicio de sesión único](add-application-portal-setup-sso.md)
