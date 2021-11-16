---
title: 'Inicio rápido: Creación de una cuenta de Azure Automation mediante el portal'
description: Este inicio rápido le ayuda a empezar a crear una cuenta de Azure Automation mediante el portal.
services: automation
ms.date: 10/26/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7497362ccd71e6efabc873015796855bd989d33c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470700"
---
# <a name="quickstart-create-an-automation-account-using-the-azure-portal"></a>Inicio rápido: Creación de una cuenta de Automation mediante Azure Portal

Puede crear una [cuenta de Azure Automation](../automation-security-overview.md) mediante Azure Portal, una interfaz de usuario basada en explorador que permite el acceso a varios recursos. Una cuenta de Automation puede administrar los recursos de todas las regiones y las suscripciones de un inquilino determinado. Este inicio rápido le guía en la creación de una cuenta de Automation.

## <a name="prerequisites"></a>Prerrequisitos

Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-automation-account"></a>Creación de una cuenta de Automation

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el menú superior, seleccione **+ Crear un recurso**.

1. En **Categorías**, seleccione **Herramientas de administración y TI** y, después, **Automation**.

   :::image type="content" source="./media/create-account-portal/automation-account-portal.png" alt-text="Localización de cuentas de Automation en el portal.":::

Las opciones de la nueva cuenta de almacenamiento se organizan en pestañas en la página **Creación de una cuenta de Automation**. En las secciones siguientes se describe cada una de las pestañas y sus opciones.

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

:::image type="content" source="./media/create-account-portal/create-account-basics.png" alt-text="Campos obligatorios para crear la cuenta de Automation en la pestaña Aspectos básicos":::

### <a name="advanced"></a>Avanzado

En la pestaña **Avanzada**, puede configurar la opción de identidad administrada para la nueva cuenta de Automation. La opción de identidad administrada asignada por el usuario también se puede configurar después de crear la cuenta de Automation.

Para obtener instrucciones sobre cómo crear una identidad administrada asignada por el usuario, consulte [Creación de una identidad administrada asignada por el usuario](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

En la siguiente tabla se describen los campos de la ficha **Opciones avanzadas**.

| **Campo** | **Obligatorio**<br> **or**<br> **opcional** |**Descripción** |
|---|---|---|
|Asignada por el sistema |Opcionales |Una identidad de Azure Active Directory que está asociada al ciclo de vida de la cuenta de Automation. |
|Asignada por el usuario |Opcionales |Una identidad administrada representada como un recurso de Azure independiente que se administra por separado de los recursos que la usan.|

Puede optar por habilitar las identidades administradas más adelante, y la cuenta de Automation se crea sin ninguna. Para habilitar una identidad administrada después de crear la cuenta, consulte [Habilitación de una identidad administrada](enable-managed-identity.md). Si selecciona ambas opciones, para la identidad asignada por el usuario, seleccione la opción **Agregar identidades asignadas por el usuario**. En la página **Seleccionar una identidad administrada asignada por el usuario**, seleccione una suscripción y agregue una o varias identidades asignadas por el usuario creadas en esa suscripción para asignarlas a la cuenta de Automation.

En la imagen siguiente se muestra una configuración estándar para una nueva cuenta de Automation.

:::image type="content" source="./media/create-account-portal/create-account-advanced.png" alt-text="Campos obligatorios para crear la cuenta de Automation en la pestaña Avanzada":::

### <a name="tags-tab"></a>Pestaña Etiquetas

En la pestaña **Etiquetas**, puede especificar etiquetas de Resource Manager para ayudar a organizar los recursos de Azure. Para obtener más información, consulte [Etiquetado de recursos, grupos de recursos y suscripciones para una organización lógica](../../azure-resource-manager/management/tag-resources.md).

### <a name="review--create-tab"></a>Pestaña Revisar y crear

Al ir a la pestaña **Revisar y crear**, Azure ejecuta la validación en la configuración de la cuenta de Automation que ha elegido. Si se supera la validación, puede continuar con la creación de la cuenta de Automation.

En cambio, si se produce un error en la validación, el portal indica qué configuración debe modificarse.

Revise la nueva cuenta de Automation.

:::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="Página de información general sobre la cuenta de Automation":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando la cuenta de Automation, seleccione **Eliminar** en la página **Información general** y, a continuación, seleccione **Sí** cuando se le solicite.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado una cuenta de Automation. Para usar identidades administradas con la cuenta de Automation, continúe con el siguiente inicio rápido:

> [!div class="nextstepaction"]
> [Tutorial: Creación de un runbook de PowerShell de Automation mediante una identidad administrada](../learn/powershell-runbook-managed-identity.md)