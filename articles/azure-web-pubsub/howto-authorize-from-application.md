---
title: Autorización de solicitudes a recursos de Web PubSub con Azure AD desde aplicaciones de Azure
description: En este artículo se proporciona información sobre la autorización de solicitudes a recursos de Web PubSub con Azure AD desde aplicaciones de Azure.
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: ef06bd1bc74b8065cb1f7d12cd6a21584feecbba
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706919"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-azure-applications"></a>Autorización de solicitudes a recursos de Web PubSub con Azure AD desde aplicaciones de Azure

El servicio Azure Web PubSub admite solicitudes de autorización de Azure Active Directory (Azure AD) procedentes de [aplicaciones de Azure](../active-directory/develop/app-objects-and-service-principals.md). 

En este artículo se muestra cómo configurar el recurso y los códigos de Web PubSub para autorizar la solicitud a un recurso de Web PubSub desde una aplicación de Azure.

## <a name="register-an-application"></a>Registro de una aplicación

El primer paso consiste en registrar una aplicación de Azure.

1. En [Azure Portal](https://portal.azure.com/), busque y seleccione **Azure Active Directory**.
2. En la sección **Administrar**, seleccione **Registros de aplicaciones**.
3. Haga clic en **Nuevo registro**.

    ![Captura de pantalla del registro de una aplicación](./media/aad-authorization/register-an-application.png)

4. Escriba un **Nombre** para mostrar para la aplicación.
5. Haga clic en **Registrar** para confirmar el registro.

Cuando haya registrado la aplicación, puede encontrar el **identificador de aplicación (cliente)** y el **identificador de directorio (inquilino)** en su página de información general. Estos GUID pueden ser útiles en los pasos siguientes.

![Captura de pantalla de una aplicación](./media/aad-authorization/application-overview.png)

Para más información sobre el registro de una aplicación, vea
- [Inicio rápido: Registro de una aplicación con la plataforma de identidad de Microsoft](../active-directory/develop/quickstart-register-app.md).

## <a name="add-credentials"></a>Adición de credenciales

Puede agregar certificados y secretos de cliente (una cadena) como credenciales al registro de la aplicación cliente confidencial.

### <a name="client-secret"></a>Secreto del cliente

La aplicación necesita un secreto de cliente para demostrar su identidad al solicitar un token. Para agregar un secreto de cliente, siga estos pasos.

1. En la sección **Administrar**, seleccione **Certificados y secretos**.
1. En la pestaña **Secretos de cliente**, haga clic en **Nuevo secreto de cliente**.
![Captura de pantalla de la creación de un secreto de cliente](./media/aad-authorization/new-client-secret.png)
1. Escriba una **descripción** para el secreto de cliente y elija una **hora de expiración**.
1. Copie el valor del **secreto de cliente** y péguelo en una ubicación segura. 
    > [!NOTE]
    > El secreto solo se mostrará una vez.
### <a name="certificate"></a>Certificado

También puede cargar una certificación en lugar de crear un secreto de cliente.

![Captura de pantalla de la carga de una certificación](./media/aad-authorization/upload-certificate.png)

Para más información sobre cómo agregar credenciales, vea

- [Adición de credenciales](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Adición de asignaciones de roles en Azure Portal

En este ejemplo se muestra cómo asignar un rol `Web PubSub Service Owner` a una entidad de servicio (aplicación) a través de un recurso de Web PubSub. 

> [!Note]
> Un rol se puede asignar a cualquier ámbito, por ejemplo, al grupo de administración, la suscripción, el grupo de recursos o un único recurso. Para más información sobre el ámbito, consulte [Descripción del ámbito de Azure RBAC](../role-based-access-control/scope-overview.md).
1. En [Azure Portal](https://portal.azure.com/), vaya al recurso de Web PubSub.

1. Seleccione **Control de acceso (IAM)** para mostrar la configuración de control de acceso de Azure Web PubSub.

1. Haga clic en la pestaña **Asignaciones de roles** para ver todas las asignaciones de roles en este ámbito.

   En la siguiente captura de pantalla se muestra un ejemplo de la página Control de acceso (IAM) de un recurso de Web PubSub.

   ![Captura de pantalla del control de acceso.](./media/aad-authorization/access-control.png)

1. Haga clic en **Agregar > Agregar asignación de roles**.

1. En la pestaña **Roles**, seleccione `Web PubSub App Server`.

1. Haga clic en **Siguiente**.

   ![Captura de pantalla de la adición de la asignación de roles](./media/aad-authorization/add-role-assignment.png)

1. En la pestaña **Miembros**, en la sección **Asignar acceso a**, seleccione **Usuario, grupo o entidad de servicio**.

1. Haga clic en **Seleccionar miembros**.

3. Busque y seleccione la aplicación a la que quiere asignar el rol.

1. Haga clic en **Seleccionar** para confirmar la selección.

4. Haga clic en **Siguiente**.

   ![Captura de pantalla de la asignación de un rol a entidades de servicio](./media/aad-authorization/assign-role-to-service-principals.png)

5. Haga clic en **Revisar y asignar** para confirmar el cambio.

> [!IMPORTANT]
> Las asignaciones de roles de Azure pueden tardar hasta 30 minutos en propagarse.
Para obtener más información sobre cómo asignar y administrar asignaciones de roles de Azure, consulte estos artículos:
- [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md)
- [Asignación de roles de Azure mediante la API REST](../role-based-access-control/role-assignments-rest.md)
- [Asignación de roles de Azure mediante Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Asignación de roles de Azure mediante la CLI de Azure](../role-based-access-control/role-assignments-cli.md)
- [Asignación de roles de Azure mediante plantillas de Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

## <a name="sample-codes"></a>Códigos de ejemplo

Oficialmente, se admiten 4 lenguajes de programación:

- [C#](./howto-create-serviceclient-with-net-and-azure-identity.md)
- [Python](./howto-create-serviceclient-with-python-and-azure-identity.md)
- [Java](./howto-create-serviceclient-with-java-and-azure-identity.md)
- [JavaScript](./howto-create-serviceclient-with-javascript-and-azure-identity.md)

## <a name="next-steps"></a>Pasos siguientes

Consulte los artículos relacionados siguientes:
- [Introducción a Azure AD para Web PubSub](concept-azure-ad-authorization.md)
- [Autorización de la solicitud a recursos de Web PubSub con Azure AD desde identidades administradas](howto-authorize-from-managed-identity.md)