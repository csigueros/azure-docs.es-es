---
title: 'Inicio rápido: Habilitación de identidades administradas para la cuenta de Automation mediante Azure Portal'
description: Este inicio rápido le ayuda a habilitar identidades administradas para la cuenta de Automation mediante Azure Portal
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.openlocfilehash: 14fc0af0b75639ae79c417645912a868a0df34f6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544760"
---
# <a name="quickstart-enable-managed-identities-for-your-automation-account-using-the-azure-portal"></a>Inicio rápido: Habilitación de identidades administradas para la cuenta de Automation mediante Azure Portal

En este inicio rápido se muestra cómo habilitar las identidades administradas en una cuenta de Azure Automation. Para más información sobre cómo funcionan las identidades administradas con Azure Automation, consulte [Identidades administradas (versión preliminar)](../automation-security-overview.md#managed-identities-preview).

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Una cuenta de Azure Automation Para obtener instrucciones, consulte [Creación de una cuenta de Automation](create-account-portal.md).

- Una identidad administrada asignada por el usuario. Para obtener instrucciones, consulte [Crear una identidad administrada asignada por el usuario](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). La identidad administrada asignada por el usuario y los recursos de Azure de destino que administra el runbook con esa identidad deben estar en la misma suscripción de Azure.

## <a name="enable-system-assigned-managed-identity"></a>Habilitación de identidad administrada asignada por el sistema

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a su cuenta de Automation.

1. En  **Configuración de la cuenta**, seleccione  **Identidad (versión preliminar)** .

   :::image type="content" source="media/enable-managed-identity/managed-identity-portal.png" alt-text="Desplazamiento a Identidad (versión preliminar) en Azure Portal.":::

1. En la opción **Estado** asignadas por el sistema, seleccione **Activado** y presione **Guardar**. Cuando se le solicite confirmación, seleccione **Sí**.

   Ahora la cuenta de Automation podrá usar la identidad asignada por el sistema, registrada con Azure Active Directory (Azure AD) y representada mediante un identificador de objeto.

   :::image type="content" source="media/enable-managed-identity/system-assigned-object-id.png" alt-text="Identificador de objeto de la identidad administrada.":::

## <a name="add-user-assigned-managed-identity"></a>Agregar una identidad administrada asignada por el usuario

Esta sección continúa donde finalizó la última sección.

1. Seleccione **Usuario asignado** y seleccione **+ Agregar** o **Agregar identidad administrada asignada por el usuario** para abrir la página **Agregar identidad administrada asignada por el usuario**.

   :::image type="content" source="media/enable-managed-identity/user-assigned-portal.png" alt-text="Pestaña Usuario asignado en Azure Portal":::.

1. En la lista desplegable **Suscripción**, seleccione la suscripción de su identidad administrada asignada por el usuario.

   :::image type="content" source="media/enable-managed-identity/add-user-assigned.png" alt-text="Página Agregar usuario asignado en el portal.":::

1. En **Identidades administradas asignadas por el usuario**, seleccione la identidad administrada asignada por el usuario existente y, después, seleccione **Agregar**. A continuación, volverá a la pestaña **Usuario asignado**.

   :::image type="content" source="media/enable-managed-identity/added-user-identity-portal.png" alt-text="Usuario asignado agregado en el portal.":::


## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita la identidad administrada asignada por el usuario asociada a su cuenta de Automation, siga estos pasos:

1. En la pestaña **User-Assigned** (Asignado por el usuario), seleccione su identidad administrada asignada por el usuario.

1. En el menú superior, seleccione **Quitar** y, después, seleccione **Sí** cuando se le pida confirmación.

Si ya no necesita que la identidad administrada asignada por el sistema esté habilitada en su cuenta de Automation, siga estos pasos:

1. En la pestaña **Asignado por el sistema**, en **Estado**, seleccione **Desactivado**.

1. En el menú superior, seleccione **Guardar** y, después, seleccione **Sí** cuando se le pida confirmación.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha habilitado las identidades administradas para una cuenta de Azure Automation. Para usar la cuenta de Automation con identidades administradas para ejecutar un runbook, consulte.

> [!div class="nextstepaction"]
> [Tutorial: Creación de un runbook de PowerShell de Automation mediante una identidad administrada](../learn/powershell-runbook-managed-identity.md)