---
title: Acceso a los registros de Azure AD con Microsoft Graph API | Microsoft Docs
description: En este inicio rápido, aprenderá a acceder al registro de inicios de sesión mediante Graph API.
services: active-directory
ms.service: active-directory
ms.subservice: report-monitor
ms.topic: quickstart
ms.date: 06/03/2021
ms.author: markvi
author: MarkusVi
manager: mtillman
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: dad33236812329014851a7397488025a4df558cd
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2021
ms.locfileid: "111528793"
---
# <a name="quickstart-access-azure-ad-logs-with-the-microsoft-graph-api"></a>Inicio rápido: Acceso a los registros de Azure AD con Microsoft Graph API 

Con la información del registro de inicios de sesión de Azure AD puede averiguar qué ha ocurrido si se produjo un error en el inicio de sesión de un usuario. En este inicio se muestra cómo acceder al registro de inicios de sesión mediante Graph API.


## <a name="prerequisites"></a>Requisitos previos

Para completar el escenario en este inicio rápido, necesita:

- **Acceso a un inquilino de Azure AD**: si no tiene acceso a uno, consulte [Cree su cuenta de Azure gratuita hoy mismo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- **Una cuenta de prueba denominada Isabella Simonsen**: si no sabe cómo crear una cuenta de prueba, consulte [Agregar usuarios basados en la nube](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="perform-a-failed-sign-in"></a>Creación de un inicio de sesión con errores

El objetivo de este paso es crear un registro de un inicio de sesión con error en el registro de inicios de sesión de Azure AD.

**Para este paso:**

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como Isabella Simonsen con una contraseña incorrecta.

2. Espere 5 minutos para asegurarse de que la entrada aparece en el registro de inicios de sesión. Para más información, consulte [Informes de actividad](reference-reports-latencies.md#activity-reports).



## <a name="find-the-failed-sign-in"></a>Identificación del inicio de sesión con errores

En esta sección se proporcionan los pasos necesarios para obtener información sobre el inicio de sesión mediante Graph API.

 ![Consulta del Probador de Graph](./media/quickstart-access-log-with-graph-api/graph-explorer-query.png)   

**Para revisar el inicio de sesión con errores:**

1. Vaya al [Probador de Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer).

2. Inicie sesión en el inquilino como administrador global.

    ![Autenticación en el Probador de Microsoft Graph](./media/quickstart-access-log-with-graph-api/graph-explorer-authentication.png)   

3. En la **lista desplegable Verbo HTTP**, seleccione **GET**.

4. En la **lista desplegable Versión de API**, seleccione **beta**.

5. En la **barra de direcciones Consultar solicitud**, escriba `https://graph.microsoft.com/beta/auditLogs/signIns?$top=100&$filter=userDisplayName eq 'Isabella Simonsen'`.
 
6. Haga clic en **Ejecutar consulta**.

Revise el resultado de la consulta.

 ![Vista previa de la respuesta en el Probador de Microsoft Graph](./media/quickstart-access-log-with-graph-api/response-preview.png)   


## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, elimine el usuario de prueba. Si no sabe cómo eliminar un usuario de Azure AD, consulte [Eliminación de usuarios desde Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [¿Qué son los informes de Azure Active Directory?](overview-reports.md)
