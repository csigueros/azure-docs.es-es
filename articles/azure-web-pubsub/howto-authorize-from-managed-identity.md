---
title: Autorización de la solicitud a recursos de Web PubSub con Azure AD desde identidades administradas
description: En este artículo se proporciona información sobre la autorización de solicitudes a recursos de Web PubSub con Azure AD desde identidades administradas.
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: bb27176e33d94db0c1bb6e0770ae45f765b0429a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714226"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-managed-identities"></a>Autorización de la solicitud a recursos de Web PubSub con Azure AD desde identidades administradas
El servicio Azure Web PubSub admite la autorización de Azure Active Directory (Azure AD) desde [identidades administradas de recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md). 

En este artículo se muestra cómo configurar el recurso y los códigos de Web PubSub para autorizar la solicitud a un recurso de Web PubSub desde una identidad administrada.

## <a name="configure-managed-identities"></a>Configuración de identidades administradas

El primer paso es configurar las identidades administradas.

Aquí hay un ejemplo para configurar `System-assigned managed identity` en `Virtual Machine` mediante Azure Portal.

1. Abra [Azure Portal](https://portal.azure.com/) y busque y seleccione una máquina virtual.
1. En el menú **Configuración**, seleccione **Identidad**.
1. En la pestaña **Asignado por el sistema**, cambie **Estado** a **Activado**.
   ![Captura de pantalla de la máquina virtual: identidad](./media/aad-authorization/identity-virtual-machine.png)
1. Haga clic en el botón **Guardar** para confirmar el cambio.

### <a name="how-to-create-user-assigned-managed-identities"></a>Creación de identidades administradas asignadas por el usuario
- [Creación de una identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

### <a name="how-to-configure-managed-identities-on-other-platforms"></a>Configuración de identidades administradas en otras plataformas

- [Configurar identidades administradas para recursos de Azure en una VM mediante Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Configuración de identidades administradas de recursos de Azure en una VM de Azure mediante PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Configuración de identidades administradas para recursos de Azure en una VM de Azure mediante la CLI de Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Configuración de identidades administradas para recursos de Azure en una máquina virtual de Azure mediante plantillas](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Configuración de una máquina virtual con identidades administradas de recursos de Azure mediante un SDK de Azure](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="how-to-configure-managed-identities-for-app-service-and-azure-functions"></a>Configuración de identidades administradas para App Service y Azure Functions

- [Cómo usar identidades administradas para App Service y Azure Functions](../app-service/overview-managed-identity.md).

## <a name="add-role-assignments-on-azure-portal"></a>Adición de asignaciones de roles en Azure Portal  

En este ejemplo se muestra cómo asignar un `Web PubSub Service Owner` rol a una identidad asignada por el sistema a través de un recurso de Web PubSub. 

> [!Note]
> Un rol se puede asignar a cualquier ámbito, por ejemplo, al grupo de administración, la suscripción, el grupo de recursos o un único recurso. Para más información sobre el ámbito, consulte [Descripción del ámbito de Azure RBAC](../role-based-access-control/scope-overview.md).
1. Abra [Azure Portal](https://portal.azure.com/) y vaya al recurso de Web PubSub.

1. Seleccione **Control de acceso (IAM)** para mostrar la configuración de control de acceso de Azure Web PubSub.

   A continuación se muestra un ejemplo de la página Control de acceso (IAM) de un grupo de recursos.

1. Haga clic en la pestaña **Asignaciones de roles** para ver todas las asignaciones de roles en este ámbito.

   En la siguiente captura de pantalla se muestra un ejemplo de la página Control de acceso (IAM) de un recurso de Web PubSub.

   ![Captura de pantalla del control de acceso.](./media/aad-authorization/access-control.png)

1. Haga clic en **Agregar > Agregar asignación de roles**.

1. En la pestaña **Roles**, seleccione `Web PubSub Service Owner`.

1. Haga clic en **Siguiente**.

   ![Captura de pantalla de la adición de la asignación de roles](./media/aad-authorization/add-role-assignment.png)

1. En la pestaña **Miembros**, en la sección **Asignar acceso a**, seleccione **Identidad administrada**.

1. Haga clic en **Seleccionar miembros**.

1. En el panel **Selección de identidades administradas**, elija **Identidad administrada asignada por el sistema > Máquina virtual**.

1. Busque y seleccione la máquina virtual a la que quiere asignar el rol.

1. Haga clic en **Seleccionar** para confirmar la selección.

2. Haga clic en **Siguiente**.

   ![Captura de pantalla de la asignación de roles a identidades administradas](./media/aad-authorization/assign-role-to-managed-identities.png)

3. Haga clic en **Revisar y asignar** para confirmar el cambio.

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
- [Autorización de solicitudes a recursos de Web PubSub con Azure AD desde aplicaciones de Azure](howto-authorize-from-application.md)