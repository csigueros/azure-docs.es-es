---
title: 'Inicio rápido: Creación de una cuenta de Azure Automation mediante el portal'
description: Este inicio rápido le ayuda a empezar a crear una cuenta de Azure Automation mediante el portal.
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: f0f93a43fd74ba38b8be17636b695e287354ed5a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218282"
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

1. En la página **Agregar cuenta de Automation**, proporcione la siguiente información:

   | Propiedad | Descripción |
   |---|---|
   |Nombre| Escriba un nombre único para su ubicación y grupo de recursos. Es posible que los nombres de las cuentas de Automation que se hayan eliminado no estén disponibles de inmediato. No se puede cambiar el nombre de la cuenta una vez que se ha escrito en la interfaz de usuario. |
   |Suscripción| En la lista desplegable, seleccione la suscripción de Azure para la cuenta.|
   |Resource group|En la lista desplegable, seleccione el grupo de recursos existente o seleccione **Crear**.|
   |Location| En la lista desplegable, seleccione una ubicación para la cuenta. Para obtener una lista actualizada de ubicaciones en las que se puede implementar una cuenta de Automation, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).|
   |Crear cuenta de ejecución de Azure| así que seleccione **No**.  Una cuenta de ejecución de Azure en la cuenta de Automation es útil para autenticarse con Azure. sin embargo, las identidades administradas en Automation ya están disponibles. Las [identidades administradas](../../active-directory/managed-identities-azure-resources/overview.md) proporcionan una identidad que usan las aplicaciones al conectarse a recursos que admiten la autenticación de Azure Active Directory (Azure AD). |

   :::image type="content" source="./media/create-account-portal/add-automation-account-portal.png" alt-text="Campos obligatorios para agregar la cuenta de Automation":::

1. Seleccione **Crear** para iniciar la implementación de la cuenta de Automation. La creación se completa aproximadamente en un minuto.

1. Recibirá una notificación cuando la implementación haya finalizado. Seleccione **Ir al recurso** en la notificación para abrir la página **Cuenta de Automation**.

1. Revise la nueva cuenta de Automation.

   :::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="Página de información general sobre la cuenta de Automation":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando la cuenta de Automation, seleccione **Eliminar** en la página **Información general** y, a continuación, seleccione **Sí** cuando se le solicite.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado una cuenta de Automation. Para usar identidades administradas con la cuenta de Automation, continúe con el siguiente inicio rápido:

> [!div class="nextstepaction"]
> [Inicio rápido: Habilitación de identidades administradas](enable-managed-identity.md)

