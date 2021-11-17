---
title: Creación de una cuenta independiente de Azure Automation
description: En este artículo se indica cómo crear una cuenta de Azure Automation independiente.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: eedee6af181a0dc99635dc455dfda6a154fe5eb9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451083"
---
# <a name="create-a-standalone-azure-automation-account"></a>Creación de una cuenta independiente de Azure Automation

En este artículo, se muestra cómo crear una cuenta de Azure Automation con Azure Portal. Puede usar la cuenta de Automation del portal para evaluar y obtener información acerca de Automation sin usar características de administración o integración adicionales con los registros de Azure Monitor. Puede agregar características de administración o realizar la integración con los registros de Azure Monitor para disfrutar de una supervisión avanzada de los trabajos de runbook en cualquier momento en el futuro.

Con una cuenta de Automation, podrá autenticar runbooks mediante la administración de recursos en Azure Resource Manager o en el modelo de implementación clásica. Una cuenta de Automation puede administrar los recursos de todas las regiones y suscripciones de un inquilino determinado.

Con esta cuenta que se ha creado automáticamente, podrá generar e implementar rápidamente runbooks que contribuyan a satisfacer sus necesidades de automatización.

## <a name="permissions-required-to-create-an-automation-account"></a>Permisos necesarios para crear una cuenta de Automation

Para crear o actualizar una cuenta de Automation y completar las tareas que se describen en este artículo, debe tener los siguientes privilegios y permisos:

Para crear una cuenta de Automation, la cuenta de usuario de Azure AD debe agregarse a un rol con permisos equivalentes a los del rol del propietario de los recursos de `Microsoft.Automation`. Para más información, consulte [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md).

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Creación de una nueva cuenta de Automation en Azure Portal

Para crear una cuenta de Azure Automation en Azure Portal, complete los pasos siguientes:

1. Inicie sesión en Azure Portal con una cuenta que sea miembro del rol Administradores de la suscripción y coadministrador de la suscripción.
1. Seleccione **+ Crear un recurso**.
1. Busque **Automation**. En los resultados de la búsqueda, haga clic en **Automation**.

   :::image type="content" source="./media/automation-create-standalone-account/automation-account-portal.png" alt-text="Ubicación de las cuentas de Automation en el portal":::

Las opciones de la nueva cuenta de Automation se organizan en pestañas en la página **Creación de una cuenta de Automation**. En las secciones siguientes se describe cada una de las pestañas y sus opciones.

### <a name="basics"></a>Conceptos básicos

En la pestaña **Aspectos básicos**, proporcione la información esencial de la cuenta de Automation. Después de completar la pestaña **Aspectos básicos**, puede personalizar aún más la nueva cuenta de Automation; para ello, establezca las opciones en las otras pestañas, o bien seleccione **Revisar y crear** para aceptar las opciones predeterminadas y continuar con la validación y la creación de la cuenta.

> [!NOTE]
> De forma predeterminada, una identidad administrada asignada por el sistema está habilitada para la cuenta de Automation.

En la siguiente tabla se describen los campos de la pestaña **Aspectos básicos**.

| **Campo** | **Obligatorio**<br> **or**<br> **opcional** |**Descripción** |
|---|---|---|
|Subscription|Requerido |En la lista desplegable, seleccione la suscripción de Azure para la cuenta.|
|Resource group|Requerido |En la lista desplegable, seleccione el grupo de recursos existente o seleccione **Crear**.|
|Nombre de la cuenta de Automation|Requerido |Escriba un nombre único para su ubicación y grupo de recursos. Es posible que los nombres de las cuentas de Automation que se hayan eliminado no estén disponibles de inmediato. No se puede cambiar el nombre de la cuenta una vez que se ha escrito en la interfaz de usuario. |
|Region|Requerido |En la lista desplegable, seleccione una región para la cuenta. Para obtener una lista actualizada de ubicaciones en las que se puede implementar una cuenta de Automation, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).|

En la imagen siguiente se muestra una configuración estándar para una nueva cuenta de Automation.

:::image type="content" source="./media/automation-create-standalone-account/create-account-basics.png" alt-text="Campos obligatorios para crear la cuenta de Automation en la pestaña Aspectos básicos":::

### <a name="advanced"></a>Avanzado

En la pestaña **Opciones avanzadas**, puede configurar la opción de identidad administrada para la nueva cuenta de Automation. La opción de identidad administrada asignada por el usuario también se puede configurar después de crear la cuenta de Automation.

Para obtener instrucciones sobre cómo crear una identidad administrada asignada por el usuario, consulte [Creación de una identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

En la siguiente tabla se describen los campos de la ficha **Opciones avanzadas**.

| **Campo** | **Obligatorio**<br> **or**<br> **opcional** |**Descripción** |
|---|---|---|
|Asignada por el sistema |Opcionales |Una identidad de Azure Active Directory que está asociada al ciclo de vida de la cuenta de Automation. |
|Asignada por el usuario |Opcionales |Una identidad administrada representada como un recurso de Azure independiente que se administra por separado de los recursos que la usan.|

Puede optar por habilitar las identidades administradas más adelante, y la cuenta de Automation se crea sin ninguna. Para habilitar una identidad administrada después de crear la cuenta, consulte [Habilitación de la identidad administrada](enable-managed-identity-for-automation.md). Si selecciona ambas opciones, para la identidad asignada por el usuario, seleccione la opción **Agregar identidades asignadas por el usuario**. En la página **Seleccionar una identidad administrada asignada por el usuario**, seleccione una suscripción y agregue una o varias identidades asignadas por el usuario creadas en esa suscripción para asignarlas a la cuenta de Automation.

En la imagen siguiente se muestra una configuración estándar para una nueva cuenta de Automation.

:::image type="content" source="./media/automation-create-standalone-account/create-account-advanced.png" alt-text="Campos obligatorios para crear la cuenta de Automation en la pestaña Opciones avanzadas":::

### <a name="tags-tab"></a>Pestaña Etiquetas

En la pestaña **Etiquetas**, puede especificar etiquetas de Resource Manager para ayudar a organizar los recursos de Azure. Para obtener más información, consulte [Etiquetado de recursos, grupos de recursos y suscripciones para una organización lógica](../azure-resource-manager/management/tag-resources.md).

### <a name="review--create-tab"></a>Pestaña Revisar y crear

Al ir a la pestaña **Revisar y crear**, Azure ejecuta la validación en la configuración de la cuenta de Automation que ha elegido. Si se supera la validación, puede continuar con la creación de la cuenta de Automation.

En cambio, si se produce un error en la validación, el portal indica qué configuración debe modificarse.

Revise la nueva cuenta de Automation.

:::image type="content" source="./media/automation-create-standalone-account/automation-account-overview.png" alt-text="Página de información general sobre la cuenta de Automation":::

Una vez que se crea la cuenta de Automation, se también varios recursos automáticamente. Una vez creados, estos runbooks se pueden eliminar de forma segura, en caso de que no se desee conservarlos. Las identidades administradas pueden utilizarse para autenticarse con la cuenta en un runbook y deben conservarse a menos que se cree otra o que ya no sean necesarias. La siguiente tabla resume los recursos de la cuenta.

| Recursos | Descripción | |------|| ------| | Runbook AzureAutomationTutorial | Runbook gráfico de ejemplo que muestra cómo autenticarse mediante una cuenta de ejecución. El runbook obtiene todos los recursos de Resource Manager. | |AzureAutomationTutorialScript | Runbook de PowerShell de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución. El runbook obtiene todos los recursos de Resource Manager. | |AzureAutomationTutorialPython2Runbook | Runbook de Python de ejemplo que muestra cómo autenticarse mediante una cuenta de ejecución. El runbook enumera todos los grupos de recursos presentes en la suscripción.|

> [!NOTE]
> Los runbooks del tutorial no se han actualizado para autenticarse mediante una identidad administrada. Revise la sección [Uso de la identidad asignada por el sistema](enable-managed-identity-for-automation.md#give-access-to-azure-resources-by-obtaining-a-token) o [Uso de la identidad asignada por el usuario](add-user-assigned-identity.md#give-identity-access-to-azure-resources-by-obtaining-a-token) para obtener información sobre cómo conceder a la identidad administrada acceso a los recursos y configurar los runbooks para que se autentiquen mediante cualquier tipo de identidad administrada.

## <a name="next-steps"></a>Pasos siguientes

* Para empezar a trabajar con runbooks de PowerShell, vea [Tutorial: Creación de un runbook de PowerShell](./learn/powershell-runbook-managed-identity.md).
* Para empezar a trabajar con runbooks de flujo de trabajo de PowerShell, consulte [Tutorial: Creación de un runbook de flujo de trabajo de PowerShell](learn/automation-tutorial-runbook-textual.md).
* Para empezar a trabajar con runbooks de Python 3, vea [Tutorial: Creación de un runbook de Python 3](learn/automation-tutorial-runbook-textual-python-3.md).
* Para ver una referencia de los cmdlets de PowerShell, consulte [Az.Automation](/powershell/module/az.automation).