---
title: Detección e información (versión preliminar) para roles de Azure AD en Privileged Identity Management (anteriormente, Asistente de seguridad de Azure Active Directory)
description: Detección e información (anteriormente, Asistente de seguridad) ayudan a convertir las asignaciones de roles permanentes de Azure AD en asignaciones Just-in-Time con Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21331670a04dc94905af0f1535a5eb06e7f083ae
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669613"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Detección e información (versión preliminar) para roles de Azure AD (anteriormente, Asistente para seguridad)

Si es la primera vez que usa Privileged Identity Management (PIM) en la organización de Azure Active Directory (Azure AD), podrá usar la página **Detección e información (versión preliminar**) para empezar. Esta característica muestra quién tiene asignado roles con privilegios en su organización y cómo usar PIM para cambiar rápidamente las asignaciones de roles permanentes en asignaciones Just-in-Time. Puede ver o realizar cambios en las asignaciones de roles con privilegios permanentes en **Detección e información (versión preliminar)** . Se trata de una herramienta de análisis y una herramienta de acción.

## <a name="discovery-and-insights-preview"></a>Detección e información (versión preliminar)

Antes de que la organización empiece a usar Privileged Identity Management, todas las asignaciones de roles son permanentes. Los usuarios siempre tienen sus roles asignados aunque no necesiten sus privilegios. Detección e información (versión preliminar), que reemplaza al antiguo Asistente de seguridad, muestra una lista de roles con privilegios y el número de usuarios que tienen actualmente esos roles. Puede obtener una lista de las asignaciones de un rol para obtener más información sobre los usuarios asignados si uno o varios de ellos no están familiarizados.

:heavy_check_mark: **Microsoft recomienda** que mantenga dos cuentas de emergencia que estén asignadas permanentemente al rol de administrador global. Asegúrese de que estas cuentas no requieren el mismo mecanismo de autenticación multifactor que las cuentas administrativas normales para iniciar sesión, como se describe en [Administración de cuentas de acceso de emergencia en Azure AD](../roles/security-emergency-access.md).

Además, mantenga las asignaciones de roles permanentes si un usuario tiene una cuenta de Microsoft (es decir, una cuenta que usan para iniciar sesión en servicios de Microsoft como Skype u Outlook.com). Si necesita autenticación multifactor para un usuario con un cuenta Microsoft para activar una asignación de roles, el usuario se bloqueará.

## <a name="open-discovery-and-insights-preview"></a>Apertura de Detección e información (versión preliminar)

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Roles de Azure AD** y, a continuación, seleccione **Detección e información (versión preliminar)** . Al abrir la página, se inicia el proceso de detección para buscar las asignaciones de roles pertinentes.

    ![La página Roles de Azure AD - Detección e información que muestra las 3 opciones](./media/pim-security-wizard/new-preview-link.png)

1. Seleccione **Reducir administradores globales**.

    ![Captura de pantalla que muestra la opción "Detección e información (versión preliminar)" con la acción "Reducir administradores globales" seleccionada.](./media/pim-security-wizard/new-preview-page.png)

1. Revise la lista de asignaciones de roles de administradores globales.

    ![Reducir administradores globales: panel de roles que muestra todos los administradores global](./media/pim-security-wizard/new-global-administrator-list.png)

1. Seleccione **Siguiente** para seleccionar los usuarios o grupos que desea que sean válidos y, a continuación, seleccione **Establecer como apto** o **Quitar asignación**.

    ![Página de conversión de los miembros en aptos con opciones para seleccionar los miembros que quiere que sean aptos para los roles](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. También puede requerir que todos los administradores globales revisen su propio acceso.

    ![Página de administradores globales que muestra la sección de revisiones de acceso](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. Después de seleccionar cualquiera de estos cambios, verá una notificación de Azure.

1. Después, puede seleccionar **Eliminar acceso permanente** o **Revisar entidades de servicio** para repetir los pasos anteriores en otros roles con privilegios y en las asignaciones de roles de entidades de servicio. En el caso de las asignaciones de roles de entidades de servicio, solo se pueden quitar asignaciones de roles.

    ![Opciones de información adicionales para eliminar el acceso permanente y revisar las entidades de servicio ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>Pasos siguientes

- [Asignación de roles de Azure AD en Privileged Identity Management](pim-how-to-add-role-to-user.md)
