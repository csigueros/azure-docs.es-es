---
title: Organización de suscripciones en grupos de administración y asignación de roles a usuarios de Azure Security Center
description: Obtenga información sobre cómo organizar suscripciones de Azure en grupos de administración en Azure Security Center y asignar roles a los usuarios de la organización.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/11/2021
ms.custom: subject-rbac-steps
ms.author: memildin
ms.openlocfilehash: 14cad6e5d95492b889a4e1a09abac9bd3ee8e603
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111887313"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>Organización de suscripciones en grupos de administración y asignación de roles a usuarios

En esta página se explica cómo administrar la posición de seguridad de la organización a gran escala mediante la aplicación de directivas de seguridad a todas las suscripciones vinculadas al inquilino de Azure Active Directory.

Para obtener visibilidad de la posición de seguridad de todas las suscripciones vinculadas al inquilino de Azure AD, deberá asignar un rol de Azure con permisos de lectura suficientes en el grupo de administración raíz.

## <a name="organize-your-subscriptions-into-management-groups"></a>Organización de las suscripciones en grupos de administración

### <a name="introduction-to-management-groups"></a>Introducción a los grupos de administración

Use los grupos de administración para administrar de un modo eficaz el acceso, las directivas y los informes en **grupos de suscripciones**, además de administrar todas las inversiones en Azure mediante acciones en el grupo de administración raíz. Puede organizar las suscripciones en grupos de administración y aplicar las directivas de gobernanza a los grupos de administración. Todas las suscripciones dentro de un grupo de administración heredan automáticamente las directivas que se aplican al grupo de administración. 

A cada inquilino de Azure AD se le da un grupo de administración de nivel superior único llamado **grupo de administración raíz**. Este grupo de administración raíz está integrado en la jerarquía de manera que contiene todos los grupos de administración y suscripciones. Este grupo permite que las directivas globales y las asignaciones de roles de Azure se apliquen en el nivel de directorio. 

El grupo de administración raíz se crea de manera automática cuando realiza cualquiera de las siguientes acciones: 
- Abra **Grupos de administración** en [Azure Portal](https://portal.azure.com).
- Cree un grupo de administración mediante una llamada API.
- Crea un grupo de administración con PowerShell. Puede encontrar las instrucciones de PowerShell en [Creación de grupos de administración para la administración de los recursos y la organización](../governance/management-groups/create-management-group-portal.md).

Los grupos de administración no son necesarios para incorporar Security Center, pero se recomienda crear al menos uno para que se cree el grupo de administración raíz. Una vez creado el grupo, todas las suscripciones del inquilino de Azure AD estarán vinculadas a él. 

Para más información sobre los grupos de administración, consulte el artículo [Organización de los recursos con grupos de administración de Azure](../governance/management-groups/overview.md).

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Visualización y creación de grupos de administración en Azure Portal

1. En [Azure Portal](https://portal.azure.com), use el cuadro de búsqueda de la barra superior para encontrar y abrir **Grupos de administración**.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Acceso a los grupos de administración":::

    Aparece la lista de grupos de administración.

1. Para crear un grupo de administración, seleccione **Agregar grupo de administración**, escriba los detalles pertinentes y elija **Guardar**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Incorporación de un grupo de administración a Azure":::

    - El **identificador de grupo de administración** es el identificador único de directorio que se usa para enviar comandos en este grupo de administración. Este identificador no es editable una vez creado, dado que se usa en todo el sistema de Azure para identificar este grupo. 
    - El campo de nombre para mostrar es el nombre que se muestra en Azure Portal. Un nombre para mostrar independiente es un campo opcional al crear el grupo de administración y se puede cambiar en cualquier momento.  


### <a name="add-subscriptions-to-a-management-group"></a>Incorporación de suscripciones a un grupo de administración
Puede agregar suscripciones al grupo de administración que ha creado.

1. En Azure Portal, abra **Grupos de administración** y seleccione el grupo de administración para la suscripción.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Selección de un grupo de administración de la suscripción":::

1. Cuando se abra la página del grupo, seleccione **Suscripciones**.

1. En la página de las suscripciones, seleccione **Agregar**, seleccione las suscripciones y elija **Guardar**. Repita estos pasos hasta que haya agregado todas las suscripciones del ámbito.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Incorporación de una suscripción a un grupo de administración":::

   > [!IMPORTANT]
   > Los grupos de administración pueden contener tanto suscripciones como grupos de administración secundarios. Cuando asigna un rol de Azure a un usuario en el grupo de administración primario, las suscripciones del grupo de administración secundario heredan el acceso. Las directivas establecidas en el grupo de administración primario también son heredadas por los secundarios. 



## <a name="assign-azure-roles-to-other-users"></a>Asignación de roles de Azure a otros usuarios

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Asignación de roles de Azure a los usuarios mediante Azure Portal: 

1. En [Azure Portal](https://portal.azure.com), use el cuadro de búsqueda de la barra superior para encontrar y abrir **Grupos de administración**.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Acceso a los grupos de administración":::

    Aparece la lista de grupos de administración.

1.  Seleccione el grupo de administración correspondiente.

1. Seleccione **Control de acceso (IAM)** , abra la pestaña **Asignaciones de roles** y seleccione **Agregar** > **Agregar asignación de roles**.

    :::image type="content" source="./media/security-center-management-groups/add-user.png" alt-text="Agregar un usuario a un grupo de administración":::

1. En la página **Agregar asignación de roles**, seleccione el rol correspondiente.

    :::image type="content" source="./media/security-center-management-groups/add-role-assignment-page.png" alt-text="Página Agregar asignación de roles":::

1. En la pestaña **Miembros**, seleccione **+ Seleccionar miembros** y asigne el rol a los miembros correspondientes.

1. En la pestaña **Revisión y asignación**, seleccione **Revisión y asignación** para asignar el rol.


### <a name="assign-azure-roles-to-users-with-powershell"></a>Asignación de roles de Azure a los usuarios con PowerShell: 

1. Instale [Azure PowerShell](/powershell/azure/install-az-ps).
2. Ejecute los comandos siguientes: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Cuando se le solicite, inicie sesión con credenciales de administrador global. 

    ![Captura de pantalla de la solicitud de inicio de sesión](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Conceda permisos de rol de lector mediante la ejecución del comando siguiente:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Para eliminar el rol, use el siguiente comando: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Eliminación de privilegios de acceso elevados 

Después de asignar los roles de Azure a los usuarios, el administrador de inquilino debería eliminarse a sí mismo del rol de administrador de acceso de usuarios.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com) o en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com).

2. En la lista de navegación, seleccione **Azure Active Directory** y, luego, **Propiedades**.

3. En **Administración de acceso a recursos de Azure**, establezca el modificador en **No**.

4. Para guardar la configuración, seleccione **Guardar**.



## <a name="next-steps"></a>Pasos siguientes

En esta página, aprendió a organizar las suscripciones en grupos de administración y a asignar roles a los usuarios. Para obtener información relacionada, consulte:

- [Permisos en Azure Security Center](security-center-permissions.md)