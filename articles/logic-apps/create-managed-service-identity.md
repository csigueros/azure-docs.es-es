---
title: Autenticación de conexiones con identidades administradas
description: Use una identidad administrada para autenticar las conexiones de flujo de trabajo en los recursos protegidos de Azure AD sin credenciales ni secretos en Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell, subject-rbac-steps, ignite-fall-2021
ms.openlocfilehash: c6e814f0b0b36408210cac7657c947f16a0076c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085364"
---
# <a name="authenticate-access-to-azure-resources-with-managed-identities-in-azure-logic-apps"></a>Autenticación del acceso a recursos de Azure con identidades administradas en Azure Logic Apps

Algunos desencadenadores y acciones de los flujos de trabajo de aplicaciones lógicas admiten el uso de una [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md), conocida anteriormente como *Identidad de servicio administrado (MSI)* , para autenticar conexiones a los recursos protegidos por Azure Active Directory (Azure AD). Cuando el recurso de aplicación lógica tiene habilitada una identidad administrada, no tiene que proporcionar credenciales, secretos ni tokens de Azure AD. Azure administra esta identidad y ayuda a proteger la información de autenticación, ya que no tiene que administrar esta información confidencial.

Azure Logic Apps admite las [*identidades administradas* asignadas por el sistema](../active-directory/managed-identities-azure-resources/overview.md), que puede usar con solo un recurso de aplicación lógica y las [*identidades administradas* asignadas por el usuario](../active-directory/managed-identities-azure-resources/overview.md), que puede compartir entre un grupo de recursos de aplicaciones lógicas, en función de dónde se ejecuten los flujos de trabajo de la aplicación lógica.

| Tipo de recurso de aplicación lógica | Entorno | Descripción |
|-------------------------|-------------|-------------|
| Consumo | - Azure Logic Apps multiinquilino <p><p>- Entorno del servicio de integración (ISE) | Puede habilitar y usar *la* identidad asignada por el sistema o una *única* identidad asignada por el usuario en el nivel de recurso y de conexión de la aplicación lógica. |
| Estándar | - Azure Logic Apps de un solo inquilino <p><p>- App Service Environment v3 (ASEv3) <p><p>- Logic Apps habilitado para Azure Arc | Actualmente, *solo* puede usar la identidad asignada por el sistema, que se habilita de forma automática. La identidad asignada por el usuario no está disponible actualmente. |
|||

Para más información sobre los límites de identidad administrada en Azure Logic Apps, consulte [Límites de identidades administradas para aplicaciones lógicas](logic-apps-limits-and-config.md#managed-identity). Para más información sobre los entornos y los tipos de recursos de aplicación lógica Estándar y Consumo, consulte la siguiente documentación:

* [¿Qué es Azure Logic Apps?](logic-apps-overview.md#resource-environment-differences)
* [Entorno de servicio de integración de un solo inquilino o de tipo multiinquilino](single-tenant-overview-compare.md)
* [Logic Apps habilitado para Azure Arc](azure-arc-enabled-logic-apps-overview.md)

<a name="triggers-actions-managed-identity"></a>
<a name="managed-connectors-managed-identity"></a>

## <a name="where-you-can-use-a-managed-identity"></a>Dónde puede usar una identidad administrada

Solo las operaciones de conectores integrados y administrados específicas que admiten la autenticación abierta de Azure AD (Azure AD OAuth) pueden usar una identidad administrada para la autenticación. En la tabla siguiente solo se proporciona una *selección de ejemplo*. Para obtener una lista más completa, consulte [Tipos de autenticación para desencadenadores y acciones que admiten la autenticación](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) y [Servicios de Azure que admiten la autenticación de Azure AD con identidades administradas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="consumption"></a>[Consumo](#tab/consumption)

En la tabla siguiente se enumeran las operaciones en las que puede usar la identidad administrada asignada por el sistema o la identidad administrada asignada por el usuario en el tipo de recurso **Aplicación lógica (Consumo)** :

| Tipo de operación | Operaciones compatibles |
|----------------|----------------------|
| Integrada | - Azure API Management <br>- Azure App Services <br>- Azure Functions <br>- HTTP <br>- HTTP + Webhook <p>**Nota**: Las operaciones HTTP pueden autenticar las conexiones a Azure Storage detrás de firewalls de Azure con la identidad asignada por el sistema. Sin embargo, no admiten la identidad administrada asignada por el usuario para autenticar las mismas conexiones. |
| Conector administrado (**versión preliminar**) | Autenticación única: <br>- Azure Automation <br>- Azure Event Grid <br>- Azure Key Vault <br>- Azure Resource Manager <br>- HTTP con Azure AD <p>Autenticación múltiple: <br> : Blob Storage de Azure <br>- SQL Server |
|||

### <a name="standard"></a>[Estándar](#tab/standard)

En la tabla siguiente se enumeran las operaciones en las que puede usar la identidad administrada asignada por el sistema en el tipo de recurso **Aplicación lógica (estándar)** :

| Tipo de operación | Operaciones compatibles |
|----------------|----------------------|
| Integrada | - HTTP <br>- HTTP + Webhook <p>**Nota**: Las operaciones HTTP pueden autenticar las conexiones a Azure Storage detrás de firewalls de Azure con la identidad asignada por el sistema. |
| Conector administrado (**versión preliminar**) | Autenticación única: <br>- Azure Automation <br>- Azure Event Grid <br>- Azure Key Vault <br>- Azure Resource Manager <br>- HTTP con Azure AD <p>Autenticación múltiple: <br> : Blob Storage de Azure <br>- SQL Server |
|||

---

En este artículo se muestra cómo habilitar y configurar la identidad asignada por el sistema o la identidad asignada por el usuario, en función de si usa el tipo de recurso **Aplicación lógica (Consumo)** o **Aplicación lógica (estándar)** . A diferencia de la identidad asignada por el sistema, que no tiene que crear manualmente, *tiene* que crear manualmente la identidad asignada por el usuario para el tipo de recurso **Aplicación lógica (Consumo)** . En este artículo se incluyen los pasos para crear la identidad asignada por el usuario mediante Azure Portal y la plantilla de Azure Resource Manager (plantilla de ARM). Para Azure PowerShell, CLI de Azure y API REST de Azure, consulte la siguiente documentación:

| Herramienta | Documentación |
|------|---------------|
| Azure PowerShell | [Crear una identidad asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) |
| Azure CLI | [Crear una identidad asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |
| API REST de Azure | [Crear una identidad asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) |
|||

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta y una suscripción de Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/). La identidad administrada y el recurso de Azure de destino al que necesita acceder deben usar la misma suscripción a Azure.

* Para conceder a una identidad administrada acceso a un recurso de Azure, debe agregar un rol al recurso de destino para esa identidad. Para agregar roles, necesita [permisos de administrador de Azure AD](../active-directory/roles/permissions-reference.md) que puedan asignar roles a identidades en el inquilino de Azure AD correspondiente.

* El recurso de Azure de destino al que quiere acceder. En este recurso, agregará un rol para la identidad administrada, lo que ayuda al recurso de aplicación lógica o a la conexión a autenticar el acceso al recurso de destino.

* El recurso de aplicación lógica en la que desea usar el [desencadenador o las acciones que admiten identidades administradas](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

  | Tipo de recurso de aplicación lógica | Compatibilidad con identidad administrada |
  |-------------------------|--------------------------|
  | Consumo | Identidad asignada por el sistema o asignada por el usuario |
  | Estándar | Identidad asignada por el sistema (habilitada automáticamente) |
  |||

<a name="system-assigned-azure-portal"></a>
<a name="azure-portal-system-logic-app"></a>

## <a name="enable-system-assigned-identity-in-azure-portal"></a>Activar una identidad asignada por el sistema en Azure Portal

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de aplicación lógica.

1. En el menú de la aplicación lógica, en **Configuración**, seleccione **Identidad**.

1. En el panel **Identidad** > en **Asignado por el sistema** m seleccione **Activado!**  > **Guardar**. Cuando Azure le pida confirmación, seleccione **Sí**.

   ![Captura de pantalla que muestra Azure Portal con el panel "Identidad" de la aplicación lógica Consumo y la pestaña "Asignado por el sistema" con las opciones "Activada" y "Guardar" seleccionadas.](./media/create-managed-service-identity/enable-system-assigned-identity-consumption.png)

   > [!NOTE]
   > Si recibe un error que le indica que solo puede tener una identidad administrada, el recurso de la aplicación lógica ya está asociado a la identidad asignada por el usuario. Para poder agregar la identidad asignada por el sistema, primero debe *quitar* la identidad asignada por el usuario del recurso de la aplicación lógica.

   El recurso de aplicación lógica ahora puede usar la identidad asignada por el sistema, que se registra con Azure AD y se representa mediante un identificador de objeto.

   ![Captura de pantalla que muestra el panel "Identidad" de la aplicación lógica Consumo con el identificador de objeto para la identidad asignada por el sistema.](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Propiedad | Valor | Descripción |
   |----------|-------|-------------|
   | **Id. de objeto (entidad de seguridad)** | <*identity-resource-ID*> | Identificador único global (GUID) que representa la identidad asignada por el sistema de la aplicación lógica en un inquilino de Azure AD. |
   ||||

1. Ahora siga los [pasos que proporcionan a la identidad acceso al recurso](#access-other-resources), especificados más adelante en este tema.

### <a name="standard"></a>[Estándar](#tab/standard)

En un tipo de recurso **Aplicación lógica (estándar)** , la identidad asignada por el sistema se habilita de forma automática.

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. En el menú de la aplicación lógica, en **Configuración**, seleccione **Identidad**.

1. En el panel **Identidad** > en **Asignado por el sistema** m seleccione **Activado!**  > **Guardar**. Cuando Azure le pida confirmación, seleccione **Sí**.

   ![Captura de pantalla que muestra Azure Portal con el panel "Identidad" de la aplicación lógica estándar y la pestaña "Asignado por el sistema" con las opciones "Activada" y "Guardar" seleccionadas.](./media/create-managed-service-identity/enable-system-assigned-identity-standard.png)

   El recurso de aplicación lógica ahora puede usar la identidad asignada por el sistema, que se registra con Azure AD y se representa mediante un identificador de objeto.

   ![Captura de pantalla que muestra el panel "Identidad" de la aplicación lógica estándar con el identificador de objeto para la identidad asignada por el sistema.](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Propiedad | Valor | Descripción |
   |----------|-------|-------------|
   | **Id. de objeto (entidad de seguridad)** | <*identity-resource-ID*> | Identificador único global (GUID) que representa la identidad asignada por el sistema de la aplicación lógica en un inquilino de Azure AD. |
   ||||

1. Ahora siga los [pasos que proporcionan a la identidad acceso al recurso](#access-other-resources), especificados más adelante en este tema.

---

<a name="system-assigned-template"></a>
<a name="template-system-logic-app"></a>

## <a name="enable-system-assigned-identity-in-an-arm-template"></a>Habilitación de la identidad asignada por el sistema en una plantilla de ARM

Para automatizar la creación e implementación de los recursos de Azure, como las aplicaciones lógicas, puede usar una [plantilla de ARM](logic-apps-azure-resource-manager-templates-overview.md). Para habilitar la identidad administrada asignada por el sistema para el recurso de la aplicación lógica en la plantilla, agregue el objeto `identity` y la propiedad secundaria `type` a la definición de recursos de la aplicación lógica en la plantilla, por ejemplo:

### <a name="consumption"></a>[Consumo](#tab/consumption)

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {},
   <...>
}
```

### <a name="standard"></a>[Estándar](#tab/standard)

```json
{
   "apiVersion": "2021-01-15",
   "type": "Microsoft.Web/sites",
   "name": "[variables('sites_<logic-app-resource-name>_name')]",
   "location": "[resourceGroup().location]",
   "kind": "functionapp,workflowapp",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {},
   <...>
}
```

---

Cuando Azure crea la definición de recurso de la aplicación lógica, el objeto `identity` obtiene estas otras propiedades:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propiedad (JSON) | Value | Descripción |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | El identificador único global (GUID) del objeto de entidad de servicio para la identidad administrada que representa la aplicación lógica en el inquilino de Azure AD. Este GUID aparece a veces como "Id. de objeto" o `objectID`. |
| `tenantId` | <*Azure-AD-tenant-ID*> | El identificador único global (GUID) que representa un inquilino de Azure AD del que la aplicación lógica es ahora miembro. En el inquilino de Azure AD, la entidad de servicio tiene el mismo nombre que la instancia de aplicación lógica. |
||||

<a name="azure-portal-user-identity"></a>
<a name="user-assigned-azure-portal"></a>

## <a name="create-user-assigned-identity-in-the-azure-portal-consumption-only"></a>Creación de una identidad asignada por el usuario en Azure Portal (solo Consumo)

Para poder habilitar la identidad asignada por el usuario en el recurso de **aplicación lógica (Consumo)** , primero debe crear esa identidad como un recurso de Azure independiente.

1. En el cuadro de búsqueda de [Azure Portal](https://portal.azure.com), escriba `managed identities`. Seleccione **Identidades administradas**.

   ![Captura de pantalla que muestra Azure Portal con la opción "Identidades administradas" seleccionada.](./media/create-managed-service-identity/find-select-managed-identities.png)

1. En el panel **Identidades administradas**, seleccione **Crear**.

   ![Captura de pantalla que muestra el panel "Identidades administradas" y la opción "Crear" seleccionada.](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Proporcione información sobre su identidad administrada y, a continuación, seleccione **Revisar y crear**; por ejemplo:

   ![Captura de pantalla que muestra el panel "Crear identidad administrada asignada por el usuario" con detalles de identidad administrada.](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Propiedad | Obligatorio | Value | Descripción |
   |----------|----------|-------|-------------|
   | **Suscripción** | Sí | <*Azure-subscription-name*> | Nombre de la suscripción a Azure que se va a usar |
   | **Grupos de recursos** | Sí | <*nombre del grupo de recursos de Azure*> | Nombre del grupo de recursos de Azure que se va a usar. Cree un nuevo grupo o seleccione uno existente. En este ejemplo se crea un grupo denominado `fabrikam-managed-identities-RG`. |
   | **Región** | Sí | <*Azure-region*> | Región de Azure en la que se va a almacenar la información sobre el recurso. En este ejemplo se usa **West US**. |
   | **Nombre** | Sí | <*user-assigned-identity-name*> | Nombre que se va a asignar a la identidad asignada por el usuario. En este ejemplo se usa `Fabrikam-user-assigned-identity`. |
   |||||

   Después de validar la información, Azure crea la identidad administrada. Ahora puede agregar la identidad asignada por el usuario al recurso de aplicación lógica, que solo puede tener una identidad asignada por el usuario.

1. En Azure Portal, abra el recurso de aplicación lógica.

1. En el menú de la aplicación lógica, en **Configuración**, seleccione **Identidad**.

1. En el panel **Identidad**, seleccione **Asignada por el usuario** > **Agregar**.

   ![Captura de pantalla que muestra el panel "Identidad" con la opción "Agregar" seleccionada.](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. En el panel **Agregar identidad administrada asignada por el usuario**, siga estos pasos:

   1. En la lista **Suscripción**, seleccione su suscripción a Azure si aún no está seleccionada.

   1. En la lista con *todas* las identidades administradas de esa suscripción, seleccione la identidad asignada por el usuario que desee. Para filtrar la lista, en el cuadro de búsqueda **Identidades administradas asignadas por el usuario**, escriba el nombre de la identidad o el grupo de recursos.

      ![Captura de pantalla que muestra la identidad asignada por el usuario seleccionada.](./media/create-managed-service-identity/select-user-assigned-identity.png)

   1. Cuando finalice, seleccione **Agregar**.

      > [!NOTE]
      > Si recibe un error que le indica que solo puede tener una identidad administrada, la aplicación lógica ya está asociada a la identidad asignada por el sistema. Para poder agregar la identidad asignada por el usuario, primero debe deshabilitar la identidad asignada por el sistema.

   La aplicación lógica ahora está asociada a la identidad administrada asignada por el usuario.

   ![Captura de pantalla que muestra la asociación entre la identidad asignada por el usuario y el recurso de aplicación lógica.](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Ahora siga los [pasos que proporcionan a la identidad acceso al recurso](#access-other-resources), especificados más adelante en este tema.

<a name="template-user-identity"></a>

## <a name="create-user-assigned-identity-in-an-arm-template-consumption-only"></a>Creación de una identidad asignada por el usuario en una plantilla de ARM (solo Consumo)

Para automatizar la creación e implementación de los recursos de Azure, tales como las aplicaciones lógicas, puede usar una [plantilla de ARM](logic-apps-azure-resource-manager-templates-overview.md), que admite [identidades asignadas por el usuario para la autenticación](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). En la sección `resources` de la plantilla, la definición de recursos de la aplicación lógica requiere estos elementos:

* Un objeto `identity` con la propiedad `type` establecida en `UserAssigned`.

* Objeto secundario `userAssignedIdentities` que especifica el nombre y el recurso asignados por el usuario.

En este ejemplo se muestra una definición de recurso de aplicación lógica para una solicitud HTTP PUT y se incluye un objeto `identity` no parametrizado. La respuesta a la solicitud PUT y la operación GET subsiguiente también tienen este objeto `identity`:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

Si la plantilla también incluye la definición de recursos de la identidad administrada, puede parametrizar el objeto `identity`. En este ejemplo se muestra cómo el objeto secundario `userAssignedIdentities` hace referencia a una variable `userAssignedIdentity` que se define en la sección `variables` de la plantilla. Esta variable hace referencia al identificador de recurso de la identidad asignada por el usuario.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Conceder acceso de identidad a los recursos

Para poder usar la identidad administrada de la aplicación lógica para la autenticación, en el recurso de Azure donde desea usar la identidad, debe configurar el acceso para su identidad mediante el control de acceso basado en rol de Azure (Azure RBAC). En los pasos de esta sección se explica cómo asignar el rol adecuado a esa identidad en el recurso de Azure mediante [Azure Portal](#azure-portal-assign-access) y la [plantilla de Azure Resource Manager (plantilla de ARM)](../role-based-access-control/role-assignments-template.md). Para Azure PowerShell, CLI de Azure y API REST de Azure, consulte la siguiente documentación:

| Herramienta | Documentación |
|------|---------------|
| Azure PowerShell | [Agregar asignación de roles](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) |
| Azure CLI | [Agregar asignación de roles](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md) |
| API REST de Azure | [Agregar asignación de roles](../role-based-access-control/role-assignments-rest.md) |
|||

<a name="azure-portal-assign-access"></a>

### <a name="assign-managed-identity-role-based-access-in-the-azure-portal"></a>Asignación del acceso basado en rol de la identidad administrada en Azure Portal

En el recurso de Azure donde desea usar la identidad administrada para la autenticación, debe asignar esa identidad a un rol que pueda acceder a ese recurso de destino. Para más información general sobre esta tarea, consulte [Asignación de un acceso de identidad administrada a otro recurso mediante Azure RBAC](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

> [!NOTE]
> Cuando una identidad administrada tiene acceso a un recurso de Azure en la misma suscripción, la identidad solo puede acceder a ese recurso. Sin embargo, en algunos desencadenadores y acciones que admiten identidades administradas, primero debe seleccionar el grupo de recursos de Azure que contiene el recurso de destino. Si la identidad no tiene acceso en el nivel de grupo de recursos, no se muestra ningún recurso de ese grupo, a pesar de tener acceso al recurso de destino.
>
> Para controlar este comportamiento, también debe proporcionar a la identidad acceso al grupo de recursos, no solo al recurso. Del mismo modo, si tiene que seleccionar la suscripción para poder seleccionar el recurso de destino, debe proporcionar a la identidad acceso a la suscripción.

1. En [Azure Portal](https://portal.azure.com), abra el recurso donde desea usar la identidad.

1. En el menú del recurso, seleccione **Control de acceso (IAM)**  > **Agregar** > **Adición de la asignación de roles**.

   > [!NOTE]
   > Si la opción **Adición de la asignación de roles** está deshabilitada, no tiene permisos para asignar roles. Para más información, consulte [Roles integrados de Azure AD](../active-directory/roles/permissions-reference.md).

1. Ahora, asigne el rol necesario a la identidad administrada. En la pestaña **Rol**, asigne un rol que dé a la identidad el acceso necesario al recurso actual.

   En este ejemplo, asigne el rol denominado **Colaborador de datos de Storage Blob**, que incluye acceso de escritura para blobs en un contenedor de Azure Storage. Para más información sobre roles de contenedor de almacenamiento específicos, consulte [Roles que pueden acceder a blobs en un contenedor de Azure Storage](../storage/blobs/authorize-access-azure-active-directory.md#assign-azure-roles-for-access-rights).

1. A continuación, elija la identidad administrada donde desea asignar el rol. En **Asignar acceso a**, seleccione **Identidad administrada** > **Agregar miembros**.

1. En función del tipo de identidad administrada, seleccione o proporcione los valores siguientes:

   | Tipo | Instancia de servicio de Azure | Suscripción | Miembro |
   |------|------------------------|--------------|--------|
   | **Asignada por el sistema** | **Aplicación lógica** | <*Azure-subscription-name*> | <*your-logic-app-name*> |
   | **Asignado al usuario** (solo Consumo) | No aplicable | <*Azure-subscription-name*> | <*your-user-assigned-identity-name*> |
   |||||

   Para más información sobre la asignación de roles, consulte la documentación [Asignación de roles mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).

1. Después de terminar de configurar el acceso para la identidad, puede usar la identidad para [autenticar el acceso para desencadenadores y acciones que admiten identidades administradas](#authenticate-access-with-identity).

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autenticación del acceso con una identidad administrada

Después de [habilitar la identidad administrada para el recurso de aplicación lógica](#azure-portal-system-logic-app) y [conceder a esa identidad acceso al recurso o a la entidad de destino](#access-other-resources), puede usar esa identidad en [desencadenadores y acciones que admiten identidades administradas](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

> [!IMPORTANT]
> Antes de que una función de Azure pueda usar la identidad asignada por el sistema, primero [habilite la autenticación de Azure Functions](logic-apps-azure-functions.md#enable-authentication-for-functions).

En estos pasos se muestra cómo usar la identidad administrada con un desencadenador o una acción a través del Azure Portal. Para especificar la identidad administrada en la definición de JSON subyacente de un desencadenador o una acción, consulte [Autenticación de identidad administrada](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de aplicación lógica.

1. Si todavía no lo ha hecho, agregue el [desencadenador o la acción que admita identidades administradas](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > No todos los desencadenadores y las acciones permiten agregar un tipo de autenticación. Para más información, consulte [Tipos de autenticación para desencadenadores y acciones que admiten la autenticación](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Siga estos pasos en el desencadenador o en la acción que agregó:

   * **Operaciones integradas que admiten la autenticación de identidad administrada**

     1. En la lista **Agregar nuevo parámetro**, agregue la propiedad **Autenticación** si todavía no aparece.

        ![Captura de pantalla que muestra una acción integrada de ejemplo con la lista "Agregar nuevo parámetro" abierta y la opción "Autenticación" seleccionada en Consumo.](./media/create-managed-service-identity/built-in-authentication-consumption.png)

     1. En la lista **Tipo de autenticación**, seleccione **Identidad administrada**.

        ![Captura de pantalla que muestra una acción integrada de ejemplo con la lista "Tipo de autenticación" abierta y la opción "Identidad administrada" seleccionada en Consumo.](./media/create-managed-service-identity/built-in-managed-identity-consumption.png)

     Para más información, consulte este [ejemplo sobre cómo autenticar una acción o un desencadenador integrado con una identidad administrada](#authenticate-built-in-managed-identity).

   * **Operaciones de conector administrado que admiten la autenticación de identidad administrada** (versión preliminar)

     1. En la página de selección de inquilinos, seleccione **Conexión con identidad administrada (versión preliminar)** , por ejemplo:

        ![Captura de pantalla que muestra la acción de Azure Resource Manager y "Conectar con identidad administrada" seleccionada en Consumo.](./media/create-managed-service-identity/select-connect-managed-identity-consumption.png)

     1. En la página siguiente, en el **nombre de la conexión**, escriba un nombre para la conexión.

     1. Para el tipo de autenticación, elija una de las siguientes opciones en función del conector administrado:

        * **Autenticación única**: estos conectores solo admiten un tipo de autenticación. En la lista **Identidad administrada**, seleccione la identidad administrada habilitada actualmente, si aún no está seleccionada, y, a continuación, elija **Crear**, por ejemplo:

          ![Captura de pantalla que muestra la página de nombre de conexión y la identidad administrada única seleccionada en Consumo.](./media/create-managed-service-identity/single-system-identity-consumption.png)

        * **Autenticación múltiple**: estos conectores admiten más de un tipo de autenticación. En la lista **Tipo de autenticación**, seleccione **Identidad administrada de aplicaciones lógicas** > **Crear**, por ejemplo:

          ![Captura de pantalla que muestra la página de nombre de conexión e "Identidad administrada de aplicaciones lógicas" seleccionada en Consumo.](./media/create-managed-service-identity/multi-system-identity-consumption.png)

        Para más información, consulte este [ejemplo sobre cómo autenticar una acción o un desencadenador de conector administrado con una identidad administrada](#authenticate-managed-connector-managed-identity).

### <a name="standard"></a>[Estándar](#tab/standard)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de aplicación lógica.

1. Si todavía no lo ha hecho, agregue el [desencadenador o la acción que admita identidades administradas](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > No todos los desencadenadores y las acciones permiten agregar un tipo de autenticación. Para más información, consulte [Tipos de autenticación para desencadenadores y acciones que admiten la autenticación](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Siga estos pasos en el desencadenador o en la acción que agregó:

   * **Operaciones integradas que admiten la autenticación de identidad administrada**

     1. En la lista **Agregar nuevo parámetro**, agregue la propiedad **Autenticación** si todavía no aparece.

        ![Captura de pantalla que muestra una acción integrada de ejemplo con la lista "Agregar nuevo parámetro" abierta y la opción "Autenticación" seleccionada en Estándar.](./media/create-managed-service-identity/built-in-authentication-standard.png)

     1. En la lista **Tipo de autenticación**, seleccione **Identidad administrada**.

        ![Captura de pantalla que muestra una acción integrada de ejemplo con la lista "Tipo de autenticación" abierta y la opción "Identidad administrada" seleccionada en Estándar.](./media/create-managed-service-identity/built-in-managed-identity-standard.png)

     Para más información, consulte este [ejemplo sobre cómo autenticar una acción o un desencadenador integrado con una identidad administrada](#authenticate-built-in-managed-identity).

   * **Operaciones de conector administrado que admiten la autenticación de identidad administrada** (versión preliminar)

     1. En la página de selección de inquilinos, seleccione **Conexión con identidad administrada (versión preliminar)** , por ejemplo:

        ![Captura de pantalla que muestra la acción de Azure Resource Manager y "Conectar con identidad administrada" seleccionada en Estándar.](./media/create-managed-service-identity/select-connect-managed-identity-standard.png)

     1. En la página siguiente, en el **nombre de la conexión**, escriba un nombre para la conexión.

     1. Para el tipo de autenticación, elija una de las siguientes opciones en función del conector administrado:

        * **Autenticación única**: estos conectores solo admiten un tipo de autenticación. En la lista **Identidad administrada**, seleccione la identidad administrada habilitada actualmente, si aún no está seleccionada, y, a continuación, elija **Crear**, por ejemplo:

          ![Captura de pantalla que muestra la página de nombre de conexión y la identidad administrada única seleccionada en Estándar.](./media/create-managed-service-identity/single-system-identity-standard.png)

        * **Autenticación múltiple**: estos conectores admiten más de un tipo de autenticación. En la lista **Tipo de autenticación**, seleccione **Identidad administrada de aplicaciones lógicas** > **Crear**, por ejemplo:

          ![Captura de pantalla que muestra la página de nombre de la conexión y la identidad administrada de aplicaciones lógicas seleccionada en Estándar.](./media/create-managed-service-identity/multi-system-identity-standard.png)

        Para más información, consulte este [ejemplo sobre cómo autenticar una acción o un desencadenador de conector administrado con una identidad administrada](#authenticate-managed-connector-managed-identity).

---

<a name="authenticate-built-in-managed-identity"></a>

## <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Ejemplo: Autentique una acción o un desencadenador integrado con una identidad administrada

El desencadenador o la acción HTTP integrados pueden usar la identidad asignada por el sistema que se habilita en el recurso de la aplicación lógica. En general, el desencadenador o la acción HTTP usa las siguientes propiedades para especificar el recurso o la entidad a la que desea acceder:

| Propiedad | Obligatorio | Descripción |
|----------|----------|-------------|
| **Método** | Sí | El método HTTP usado por la operación que desea ejecutar |
| **URI** | Sí | La dirección URL del punto de conexión para acceder a la entidad o recurso de Azure de destino. La sintaxis del URI normalmente incluye el [Id. de recurso](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para el recurso o servicio de Azure. |
| **Encabezados** | No | Los valores de encabezado que necesita o desea incluir en la solicitud saliente, como el tipo de contenido |
| **Consultas** | No | Los parámetros de consulta que necesita o desea incluir en la solicitud, como el parámetro para una operación específica o la versión de la API para la operación que desea ejecutar |
| **Autenticación** | Sí | El tipo de autenticación que se va a usar para autenticar el acceso al recurso o entidad de destino |
||||

Como ejemplo específico, supongamos que desea ejecutar la [Operación instantánea de blob](/rest/api/storageservices/snapshot-blob) en un BLOB de la cuenta Azure Storage en la que previamente configuró el acceso para su identidad. Sin embargo, el [conector de Azure Blob Storage](/connectors/azureblob/) no ofrece actualmente esta operación. En su lugar, puede ejecutar esta operación mediante la [acción HTTP](logic-apps-workflow-actions-triggers.md#http-action) u otra [operación de la API REST de servicios blob](/rest/api/storageservices/operations-on-blobs).

> [!IMPORTANT]
> Para acceder a cuentas de Azure Storage detrás de firewalls mediante solicitudes HTTP e identidades administradas, asegúrese de que también configura la cuenta de almacenamiento con la [excepción que permite el acceso a los servicios de Microsoft de confianza](../connectors/connectors-create-api-azureblobstorage.md#access-blob-storage-with-managed-identities).

Para ejecutar la [operación blob de instantáneas](/rest/api/storageservices/snapshot-blob), la acción HTTP especifica estas propiedades:

| Propiedad | Obligatorio | Valor de ejemplo | Descripción |
|----------|----------|---------------|-------------|
| **Método** | Sí | `PUT`| El método HTTP que utiliza la operación blob de instantáneas |
| **URI** | Sí | `https://<storage-account-name>/<folder-name>/{name}` | El identificador de recurso de un archivo Azure Blob Storage en el entorno de Azure Global (público), que usa esta sintaxis |
| **Encabezados** | Para Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | Los valores de encabezado `x-ms-blob-type`, `x-ms-version` y `x-ms-date` son necesarios para las operaciones de Azure Storage. <p><p>**Importante**: En el desencadenador HTTP saliente y en las solicitudes de acción de Azure Storage, el encabezado requiere la propiedad `x-ms-version` y la versión de la API para la operación que se desea ejecutar. El valor `x-ms-date` debe ser la fecha actual. En caso contrario, se produce un error de tipo `403 FORBIDDEN` en la aplicación lógica. Para obtener la fecha actual en el formato requerido, puede usar la expresión del valor de ejemplo. <p>Para obtener más información, consulte estos temas: <p><p>- [Encabezados de solicitud - Blob de instantáneas](/rest/api/storageservices/snapshot-blob#request) <br>- [Control de versiones para servicios de Azure Storage](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Consultas** | Solo para la operación del blob de instantáneas | `comp` = `snapshot` | El nombre y el valor del parámetro de consulta para la operación. |
|||||

### <a name="consumption"></a>[Consumo](#tab/consumption)

En el ejemplo siguiente se muestra una acción HTTP de ejemplo con todos los valores de propiedad descritos anteriormente que se usarán para la operación de blob de instantáneas:

![Captura de pantalla Azure Portal con el flujo de trabajo de la aplicación lógica Consumo y la acción HTTP configuradas para acceder al recurso.](./media/create-managed-service-identity/http-action-example.png)

1. Después de agregar la acción HTTP, agregue la propiedad **Autenticación** a la acción HTTP. En la lista **Agregar nuevo parámetro**, seleccione **Autenticación**.

   ![Captura de pantalla que muestra el flujo de trabajo Consumo con la acción HTTP y la lista "Agregar nuevo parámetro" abierta con la propiedad "Autenticación" seleccionada.](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > No todos los desencadenadores y las acciones permiten agregar un tipo de autenticación. Para más información, consulte [Tipos de autenticación para desencadenadores y acciones que admiten la autenticación](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. En la lista **Tipo de autenticación**, seleccione **Identidad administrada**.

   ![Captura de pantalla que muestra el flujo de trabajo Consumo con acción HTTP y la propiedad "Autenticación" con el valor "Identidad administrada" seleccionado.](./media/create-managed-service-identity/select-managed-identity.png)

1. En la lista identidades administradas, seleccione entre las opciones disponibles en función de su escenario.

   * Si configura la identidad asignada por el sistema, seleccione **Identidad administrada asignada por el sistema** si aún no está seleccionada.

     ![Captura de pantalla que muestra el flujo de trabajo Consumo con la acción HTTP y la propiedad "Identidad administrada" con el valor "Identidad administrada asignada por el sistema" seleccionado.](./media/create-managed-service-identity/select-system-assigned-identity.png)

   * Si configura una identidad asignada por el sistema, seleccione esa identidad si aún no lo está.

     ![Captura de pantalla que muestra el flujo de trabajo Consumo con la acción HTTP y la propiedad "Identidad administrada" con la identidad asignada por el usuario seleccionada.](./media/create-managed-service-identity/select-user-assigned-identity-action.png)

   Este ejemplo continúa con la **Identidad administrada asignada por el sistema**.

1. En algunos desencadenadores y acciones, la propiedad **Audiencia** también aparece para que pueda establecer el identificador de recurso de destino. Establezca la propiedad **Audiencia** en el [identificador de recurso para el recurso o servicio de destino](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). De lo contrario, de forma predeterminada, la propiedad **Audiencia** usa el identificador de recurso `https://management.azure.com/`, que es el identificador de recurso para Azure Resource Manager.
  
    Por ejemplo, si desea autenticar el acceso a un [recurso de Key Vault en la nube global de Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault), debe establecer la propiedad **Audiencia** en el siguiente identificador de recurso *exactamente*: `https://vault.azure.net`. Tenga en cuenta que este identificador de recurso específico *no* tiene barras diagonales finales. De hecho, incluir una barra diagonal final podría producir un error `400 Bad Request` o un error `401 Unauthorized`.

   > [!IMPORTANT]
   > Asegúrese de que el identificador de este recurso de destino *coincide exactamente* con el valor esperado en Azure Active Directory (AD), incluida toda barra diagonal necesaria al final. Por ejemplo, el Id. de recurso para todas las cuentas de Azure Blob Storage requiere una barra diagonal final. Sin embargo, el Id. de recurso de una cuenta de almacenamiento específica no requiere una barra diagonal final. Verifique los [identificadores de recursos para los servicios de Azure que admiten Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   En este ejemplo se establece la propiedad **Audiencia** en `https://storage.azure.com/`, de modo que los tokens de acceso que se usan para la autenticación sean válidos para todas las cuentas de almacenamiento. Sin embargo, también puede especificar una dirección URL de servicio raíz, `https://<your-storage-account>.blob.core.windows.net`, para una cuenta de almacenamiento específica.

   ![Captura de pantalla que muestra el flujo de trabajo Consumo con acción HTTP y la propiedad "Audiencia" establecida en el identificador de recurso de destino.](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Para obtener más información sobre cómo autorizar el acceso con Azure AD para Azure Storage, revise la siguiente documentación:

   * [Autorización del acceso a blobs y colas de Azure con Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md)

   * [Autorización del acceso a Azure Storage con Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Siga creando el flujo de trabajo como desee.

### <a name="standard"></a>[Estándar](#tab/standard)

En el ejemplo siguiente se muestra una acción HTTP de ejemplo con todos los valores de propiedad descritos anteriormente que se usarán para la operación de blob de instantáneas:

![Captura de pantalla que muestra Azure Portal con el flujo de trabajo de la aplicación lógica Estándar y la acción HTTP configuradas para acceder al recurso.](./media/create-managed-service-identity/http-action-example-standard.png)

1. Después de agregar la acción HTTP, agregue la propiedad **Autenticación** a la acción HTTP. En la lista **Agregar nuevo parámetro**, seleccione **Autenticación**.

   ![Captura de pantalla que muestra el flujo de trabajo Estándar con la acción HTTP y la lista "Agregar nuevo parámetro" abierta con la propiedad "Autenticación" seleccionada.](./media/create-managed-service-identity/add-authentication-property-standard.png)

   > [!NOTE]
   > No todos los desencadenadores y las acciones permiten agregar un tipo de autenticación. Para más información, consulte [Tipos de autenticación para desencadenadores y acciones que admiten la autenticación](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. En la lista **Tipo de autenticación**, seleccione **Identidad administrada**.

   ![Captura de pantalla que muestra el flujo de trabajo Estándar con acción HTTP y la propiedad "Autenticación" con el valor "Identidad administrada" seleccionado.](./media/create-managed-service-identity/select-managed-identity-standard.png)

1. En la lista de identidades administradas, seleccione **Identidad administrada asignada por el sistema** si aún no está seleccionada.

     ![Captura de pantalla que muestra el flujo de trabajo Estándar con la acción HTTP y la lista "Identidad administrada" abierta con la opción "Identidad administrada asignada por el sistema" seleccionada.](./media/create-managed-service-identity/select-system-assigned-identity-standard.png)

1. En algunos desencadenadores y acciones, la propiedad **Audiencia** también aparece para que pueda establecer el identificador de recurso de destino. Establezca la propiedad **Audiencia** en el [identificador de recurso para el recurso o servicio de destino](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). De lo contrario, de forma predeterminada, la propiedad **Audiencia** usa el identificador de recurso `https://management.azure.com/`, que es el identificador de recurso para Azure Resource Manager.
  
    Por ejemplo, si desea autenticar el acceso a un [recurso de Key Vault en la nube global de Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault), debe establecer la propiedad **Audiencia** en el siguiente identificador de recurso *exactamente*: `https://vault.azure.net`. Tenga en cuenta que este identificador de recurso específico *no* tiene barras diagonales finales. De hecho, incluir una barra diagonal final podría producir un error `400 Bad Request` o un error `401 Unauthorized`.

   > [!IMPORTANT]
   > Asegúrese de que el identificador de este recurso de destino *coincide exactamente* con el valor esperado en Azure Active Directory (AD), incluida toda barra diagonal necesaria al final. Por ejemplo, el Id. de recurso para todas las cuentas de Azure Blob Storage requiere una barra diagonal final. Sin embargo, el Id. de recurso de una cuenta de almacenamiento específica no requiere una barra diagonal final. Verifique los [identificadores de recursos para los servicios de Azure que admiten Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   En este ejemplo se establece la propiedad **Audiencia** en `https://storage.azure.com/`, de modo que los tokens de acceso que se usan para la autenticación sean válidos para todas las cuentas de almacenamiento. Sin embargo, también puede especificar una dirección URL de servicio raíz, `https://<your-storage-account>.blob.core.windows.net`, para una cuenta de almacenamiento específica.

   ![Especificar la propiedad "Audiencia" en el identificador de recurso de destino](./media/create-managed-service-identity/specify-audience-url-target-resource-standard.png)

   Para obtener más información sobre cómo autorizar el acceso con Azure AD para Azure Storage, revise la siguiente documentación:

   * [Autorización del acceso a blobs y colas de Azure con Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md)
   * [Autorización del acceso a Azure Storage con Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Siga creando el flujo de trabajo como desee.

---

<a name="authenticate-managed-connector-managed-identity"></a>

## <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Ejemplo: Autentique una acción o un desencadenador de conector administrado con una identidad administrada

El conector administrado de Azure Resource Manager tiene una acción, **Leer un recurso**, que puede usar la identidad administrada que habilitó para el recurso de aplicación lógica. En este ejemplo se muestra cómo usar la identidad administrada asignada por el sistema.

### <a name="consumption"></a>[Consumo](#tab/consumption)

1. Después de agregar la acción al flujo de trabajo y de seleccionar el inquilino de Azure AD, seleccione **Conexión con identidad administrada (versión preliminar)** .

   ![Captura de pantalla que muestra la acción de Azure Resource Manager y "Conectar con identidad administrada" seleccionada.](./media/create-managed-service-identity/select-connect-managed-identity-consumption.png)

1. En la página del nombre de la conexión, dé un nombre a la conexión y seleccione la identidad administrada que desea usar.

   La acción de Azure Resource Manager es una acción de autenticación única, por lo que el panel de información de conexión muestra una lista de **identidades administradas** que selecciona automáticamente la identidad administrada que está habilitada actualmente en el recurso de aplicación lógica. Si ha habilitado una identidad administrada asignada por el sistema, la lista **Identidad administrada** selecciona **Identidad administrada asignada por el sistema**. Si en su lugar ha habilitado una identidad administrada asignada por el usuario, la lista selecciona esa identidad en su lugar.

   Si usa un desencadenador o una acción de autenticación múltiple, como Azure Blob Storage, el panel de información de la conexión muestra una lista de **tipos de autenticación** que incluye la opción de **identidad administrada de aplicaciones lógicas** entre otros tipos de autenticación.

   En este ejemplo, la **identidad administrada asignada por el sistema** es la única selección disponible.

   ![Captura de pantalla que muestra la acción de Azure Resource Manager con el nombre de conexión especificado y la opción "Identidad administrada asignada por el sistema" seleccionada.](./media/create-managed-service-identity/single-system-identity-consumption.png)

   > [!NOTE]
   > Si la identidad administrada no está habilitada al intentar crear la conexión, se ha cambiado o se quitó mientras aún existe una conexión habilitada para identidad administrada, aparece un error que indica que debe habilitar la identidad y conceder acceso al recurso de destino.

1. Cuando esté listo, seleccione **Crear**.

1. Después de crear correctamente la conexión, el diseñador puede usar la autenticación de identidad administrada para capturar cualquier esquema, contenido o valores dinámicos.

1. Siga creando el flujo de trabajo como desee.

### <a name="standard"></a>[Estándar](#tab/standard)

1. Después de agregar la acción al flujo de trabajo, en el panel **Crear conexión** de la acción, seleccione el inquilino de Azure AD y, a continuación, seleccione **Conectar con identidad administrada (versión preliminar)** .

   ![Captura de pantalla que muestra la acción de Azure Resource Manager y "Conectar con identidad administrada" seleccionada.](./media/create-managed-service-identity/select-connect-managed-identity-standard.png)

1. En la página de nombre de la conexión, escriba un nombre para la conexión.

   La acción de Azure Resource Manager es una acción de autenticación única, por lo que el panel de información de conexión muestra una lista de **identidades administradas** que selecciona automáticamente la identidad administrada que está habilitada actualmente en el recurso de aplicación lógica. Si ha habilitado una identidad administrada asignada por el sistema, la lista **Identidad administrada** selecciona **Identidad administrada asignada por el sistema**. Si en su lugar ha habilitado una identidad administrada asignada por el usuario, la lista selecciona esa identidad en su lugar.

   En este ejemplo, la **identidad administrada asignada por el sistema** es la única selección disponible.

   ![Captura de pantalla que muestra la acción de Azure Resource Manager con el nombre de conexión especificado y la opción "Identidad administrada asignada por el sistema" seleccionada.](./media/create-managed-service-identity/single-system-identity-standard.png)

   Si usa un desencadenador o una acción de autenticación múltiple, como Azure Blob Storage, el panel de información de conexión muestra una lista de **tipos de autenticación** que incluye la opción **Identidad administrada** entre otros tipos de autenticación.

   > [!NOTE]
   > Si la identidad administrada no está habilitada al intentar crear la conexión, se ha cambiado o se quitó mientras aún existe una conexión habilitada para identidad administrada, aparece un error que indica que debe habilitar la identidad y conceder acceso al recurso de destino.

1. Cuando esté listo, seleccione **Crear**.

1. Después de crear correctamente la conexión, el diseñador puede usar la autenticación de identidad administrada para capturar cualquier esquema, contenido o valores dinámicos.

1. Siga creando el flujo de trabajo como desee.

---

<a name="logic-app-resource-definition-connection-managed-identity"></a>

## <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity-consumption"></a>Definición de recursos de aplicación lógica y conexiones que usan una identidad administrada (Consumo)

Una conexión que permite y usa una identidad administrada es un tipo de conexión especial que solo funciona con una identidad administrada. En tiempo de ejecución, la conexión usa la identidad administrada que está habilitada en el recurso de aplicación lógica. En tiempo de ejecución, el servicio Azure Logic Apps comprueba si las acciones o los desencadenadores de conector administrado del flujo de trabajo de la aplicación lógica están configurados para usar la identidad administrada y si todos los permisos necesarios están configurados para usar la identidad administrada a fin de acceder a los recursos de destino especificados por el desencadenador y las acciones. Si se realiza correctamente, Azure Logic Apps recupera el token de Azure AD asociado con la identidad administrada y usa esa identidad para autenticar el acceso al recurso de destino y realizar la operación configurada en desencadenadores y acciones.

### <a name="consumption"></a>[Consumo](#tab/consumption)

En un recurso de **Aplicación lógica (Consumo)** , la configuración de la conexión se guarda en el objeto `parameters` de la definición de recursos de la aplicación lógica, que contiene el objeto `$connections` que incluye punteros al identificador de recurso de la conexión junto con el identificador de recurso de la identidad, si la identidad asignada por el usuario está habilitada.

En este ejemplo se muestra el aspecto de la configuración cuando la aplicación lógica habilita la identidad administrada asignada por el sistema:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

En este ejemplo se muestra el aspecto de la configuración cuando la aplicación lógica habilita una identidad administrada asignada por el usuario:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

### <a name="standard"></a>[Estándar](#tab/standard)

En un recurso de **aplicación lógica (Estándar)** , la configuración de conexión se guarda en el recurso de la aplicación lógica o el archivo `connections.json` del proyecto, que contiene un objeto JSON `managedApiConnections` que incluye información de configuración de conexión para cada conector administrado que se usa en un flujo de trabajo. Por ejemplo, esta información de conexión incluye punteros al identificador de recurso de la conexión junto con las propiedades de identidad administrada, como el identificador de recurso, si la identidad asignada por el usuario está habilitada.

En este ejemplo se muestra el aspecto de la configuración cuando la aplicación lógica habilita la identidad administrada asignada por el usuario:

```json
{
    "managedApiConnections": {
        "<connector-name>": {
            "api": {
                "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{region}/managedApis/<connector-name>"
            },
            "connection": {
                "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/<connection-name>"
            },
            "connectionRuntimeUrl": <connection-URL>,
            "authentication": { // Authentication with APIHub
                "type": "ManagedServiceIdentity"
            },
            "connectionProperties": {
                "authentication": { //Authentication with the target resource
                    "type": "ManagedServiceIdentity",
                    "identity": "<user-assigned-identity>", // Optional
                    "audience": "<resource-URL>"
                }
            }
        }
    }
}
```

---

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities-consumption"></a>Plantilla de ARM para conexiones administradas e identidades administradas (Consumo)

Si automatiza la implementación con una plantilla de ARM y el flujo de trabajo de la aplicación lógica incluye un desencadenador o una acción de conector administrado que usa una identidad administrada, confirme que la definición de recurso de conexión subyacente incluye la propiedad `parameterValueType` con `Alternative` como valor de propiedad. De lo contrario, la implementación de ARM no configurará la conexión de uso de la identidad administrada para la autenticación y la conexión no funcionará en el flujo de trabajo de la aplicación lógica. Este requisito solo se aplica a [desencadenadores y acciones de conectores administrados específicos](#triggers-actions-managed-identity) en los que se ha seleccionado la [opción **Conectar con identidad administrada**](#authenticate-managed-connector-managed-identity).

Por ejemplo, esta es la definición de recurso de conexión subyacente para una acción Azure Automation que usa una identidad administrada en la que la definición incluye la propiedad `parameterValueType`, que se establece en `Alternative` como el valor de la propiedad:

```json
{
    "type": "Microsoft.Web/connections",
    "name": "[variables('automationAccountApiConnectionName')]",
    "apiVersion": "2016-06-01",
    "location": "[parameters('location')]",
    "kind": "V1",
    "properties": {
        "api": {
            "id": "[subscriptionResourceId('Microsoft.Web/locations/managedApis', parameters('location'), 'azureautomation')]"
        },
        "customParameterValues": {},
        "displayName": "[variables('automationAccountApiConnectionName')]",
        "parameterValueType": "Alternative"
    }
},
```

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Deshabilitar la identidad administrada

Para dejar de usar la identidad administrada para la autenticación, [quite primero el acceso de la identidad al recurso de destino](#disable-identity-target-resource). A continuación, en el recurso de la aplicación lógica, [desactive la identidad asignada por el sistema o quite la identidad asignada por el usuario](#disable-identity-logic-app).

Al deshabilitar la identidad administrada en el recurso de la aplicación lógica, se quita la capacidad de esa identidad para solicitar acceso a los recursos de Azure a los que la identidad tenía acceso.

> [!NOTE]
> Si deshabilita la identidad asignada por el sistema, todas las conexiones usadas por los flujos de trabajo en el flujo de trabajo de esa aplicación lógica no funcionarán en tiempo de ejecución, aunque vuelva a habilitarla inmediatamente. Este comportamiento se produce porque al deshabilitar la identidad se elimina el identificador de objeto. Cada vez que se habilita la identidad, Azure genera la identidad con un identificador de objeto diferente y único. Para resolver este problema, debe volver a crear las conexiones para que usen el identificador de objeto actual para la identidad asignada por el sistema actual.
>
> Intente evitar deshabilitar la identidad asignada por el sistema tanto como sea posible. Si desea quitar el acceso de la identidad a los recursos de Azure, quite la asignación de roles de la identidad del recurso de destino. Si elimina el recurso de la aplicación lógica, Azure quita automáticamente la identidad administrada de Azure AD.

En los pasos de esta sección se explica cómo usar [Azure Portal](#azure-portal-disable) y la [plantilla de Azure Resource Manager (plantilla de ARM)](#template-disable). Para Azure PowerShell, CLI de Azure y API REST de Azure, consulte la siguiente documentación:

| Herramienta | Documentación |
|------|---------------|
| Azure PowerShell | 1. [Eliminación de la asignación de roles](../role-based-access-control/role-assignments-powershell.md) <br>2. [Eliminación de la identidad asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) |
| Azure CLI | 1. [Eliminación de la asignación de roles](../role-based-access-control/role-assignments-cli.md) <br>2. [Eliminación de la identidad asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |
| API REST de Azure | 1. [Eliminación de la asignación de roles](../role-based-access-control/role-assignments-rest.md) <br>2. [Eliminación de la identidad asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) |
|||

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Deshabilitar la identidad administrada en Azure Portal

Para quitar el acceso de la identidad administrada, quite la asignación de roles de la identidad del recurso de destino y, a continuación, deshabilite la identidad administrada.

<a name="disable-identity-target-resource"></a>

#### <a name="remove-role-assignment"></a>Eliminar asignación de roles

En los pasos siguientes se quita el acceso al recurso de destino de la identidad administrada:

1. En [Azure Portal](https://portal.azure.com), vaya al recurso de Azure de destino donde quiere quitar el acceso de la identidad administrada.

1. En el menú del recurso de destino, seleccione **Control de acceso (IAM)** . En la barra de herramientas, seleccione **Asignación de roles**.

1. En la lista de roles, seleccione las identidades administradas que desea quitar. En la barra de herramientas, seleccione **Eliminar**.

   > [!TIP]
   > Si está deshabilitada la opción **Eliminar**, lo más probable es que no tenga los permisos correspondientes. Para más información acerca de los permisos con los que puede administrar roles para los recursos, consulte [Permisos de roles de administrador en Azure Active Directory](../active-directory/roles/permissions-reference.md).

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app-resource"></a>Deshabilitación de la identidad administrada en el recurso de aplicación lógica

1. En [Azure Portal](https://portal.azure.com), abra el recurso de aplicación lógica.

1. En el menú de navegación de la aplicación lógica, en **Configuración**, seleccione **Identidad** y, a continuación, siga los pasos adecuados para su identidad:

   * Seleccione **Asignado por el sistema** > **Activado** > **Guardar**. Cuando Azure le pida confirmación, seleccione **Sí**.

   * Seleccione **Usuario asignado** y la identidad administrada y, después, elija **Quitar**. Cuando Azure le pida confirmación, seleccione **Sí**.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-an-arm-template"></a>Deshabilitación de la identidad administrada en una plantilla de ARM

Si creó la identidad administrada de la aplicación lógica mediante una plantilla de ARM, establezca la propiedad secundaria `type` del objeto `identity` en `None`.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Proteger el acceso y los datos en Azure Logic Apps](logic-apps-securing-a-logic-app.md)
