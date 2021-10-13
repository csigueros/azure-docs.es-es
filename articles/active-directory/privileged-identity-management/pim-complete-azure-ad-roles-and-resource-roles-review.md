---
title: 'Realización de una revisión de acceso de recursos de Azure y roles de Azure AD en PIM: Azure AD | Microsoft Docs'
description: Aprenda a realizar una revisión de acceso de recursos de Azure y roles de Azure AD en Privileged Identity Management de Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7148284bef15b2e3f40a39652d0cb222ed5793c0
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669993"
---
# <a name="complete-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>Realización de una revisión de acceso de recursos de Azure y roles de Azure AD en PIM

Los administradores de roles con privilegios pueden revisar el acceso con privilegios una vez que se [ha iniciado una revisión del acceso](pim-create-azure-ad-roles-and-resource-roles-review.md). Privileged Identity Management (PIM) de Azure Active Directory (Azure AD) envía automáticamente un correo en el que se solicita a los usuarios que revisen su acceso. Si un usuario no recibe un correo electrónico, puede enviarle las instrucciones necesarias para [realizar una revisión del acceso](pim-perform-azure-ad-roles-and-resource-roles-review.md).

Una vez creada la revisión, siga los pasos de este artículo para llevar a cabo la revisión y ver los resultados.

## <a name="complete-access-reviews"></a>Realización de revisiones de acceso

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/). En el caso de **recursos de Azure**, vaya a **Privileged Identity Management** y seleccione **Recursos de Azure** en **Administrar** desde el panel. En el caso de **roles de Azure AD**, seleccione **Roles de Azure AD** desde el mismo panel.

2. En el caso de **recursos de Azure**, seleccione el recurso en **Recursos de Azure** y luego **Revisiones de acceso** desde el panel. En el caso de **roles de Azure AD**, vaya directamente a **Revisiones de acceso** en el panel.

3. Seleccione la revisión de acceso que quiere administrar. A continuación se muestra una captura de pantalla de ejemplo de la información general de **Revisiones de acceso** de **recursos de Azure** y **roles de Azure AD**.

    :::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png" alt-text="Captura de pantalla de la lista de revisiones de acceso que muestra el rol, el propietario, la fecha de inicio, la fecha de finalización y el estado." lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png":::

En la página de detalles están disponibles las siguientes opciones para administrar la revisión de **recursos de Azure** y **roles de Azure AD**:

![Captura de pantalla de las opciones para administrar una revisión de recursos de Azure: Detener, Restablecer, Aplicar, Eliminar.](media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-menu.png)

### <a name="stop-an-access-review"></a>Detención de una revisión de acceso

Todas las revisiones de acceso tienen una fecha de finalización, pero puede usar el botón **Detener** para terminarlas antes. El botón **Detener** solo se puede seleccionar cuando la instancia de revisión está activa. No se puede reiniciar una revisión después de que se ha detenido.

### <a name="reset-an-access-review"></a>Restablecimiento de una revisión de acceso

Cuando la instancia de revisión está activa y los revisores han tomado al menos una decisión, puede restablecer la revisión de acceso si selecciona el botón **Restablecer** para quitar todas las decisiones tomadas sobre ella. Después de restablecer una revisión de acceso, todos los usuarios se marcan como no revisados de nuevo.

### <a name="apply-an-access-review"></a>Aplicación de una revisión de acceso

Después de que finalice una revisión de acceso, bien porque se ha llegado a la fecha de finalización o porque se ha detenido manualmente, el botón **Aplicar** quita el acceso de los usuarios denegados al rol. Si se ha denegado el acceso de un usuario durante la revisión, este es el paso que quita su asignación de roles. Si la opción **Aplicar automáticamente** se configura al crear la revisión, este botón siempre está deshabilitado, porque la revisión se aplica automáticamente en lugar de manualmente.

### <a name="delete-an-access-review"></a>Eliminación de una revisión de acceso

Si ya no le interesa más la revisión, elimínela. Para quitar la revisión de acceso del servicio Privileged Identity Management, seleccione el botón **Eliminar**.

> [!IMPORTANT]
> No se le pedirá que confirme este cambio destructivo, por lo que debe comprobar que desea eliminar esa revisión.

## <a name="results"></a>Results

En la página **Resultados**, puede ver y descargar una lista de los resultados de la revisión.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png" alt-text="Captura de pantalla de la página de resultados que muestra los usuarios, el resultado, el motivo, quién ha realizado la revisión, quién la ha aplicado y la aplicación del resultado de los roles de Azure AD." lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png":::

> [!Note]
> Los **roles de Azure AD** tienen un concepto de grupos a los que se pueden asignar roles, donde se puede asignar un grupo al rol. Cuando esto sucede, el grupo se muestra en la revisión en lugar de expandir sus miembros y un revisor aprueba o deniega a todo el grupo.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png" alt-text="Captura de pantalla de la página de resultados que muestra los usuarios, el resultado, el motivo, quién ha realizado la revisión, quién la ha aplicado y la aplicación del resultado de los roles de recursos de Azure." lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png":::

> [!Note]
>Si se asigna un grupo a **roles de recursos de Azure**, el revisor del rol de recursos de Azure ve la lista expandida de los usuarios de un grupo anidado. Si un revisor deniega un miembro de un grupo anidado, ese resultado de denegación no se aplica correctamente, porque el usuario no se quita del grupo anidado.

## <a name="reviewers"></a>Revisores

En la página **Revisores** puede ver y agregar revisores a la revisión de acceso existente. Aquí también puede recordar a los revisores que completen sus revisiones.

> [!Note]
> Si el tipo de revisor seleccionado es usuario o grupo, puede agregar más usuarios o grupos como revisores principales en cualquier momento. También puede quitar revisores principales en cualquier momento. Si el tipo de revisor es administrador, puede agregar usuarios o grupos como revisores de reserva para completar las revisiones de los usuarios que no tienen administradores. Los revisores de reserva no se pueden quitar.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png" alt-text="Captura de pantalla de la página de revisores que muestra el nombre principal de usuario de los roles de recursos de Azure." lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png":::

## <a name="next-steps"></a>Pasos siguientes

- [Cree una revisión de acceso de recursos de Azure y roles de Azure AD en PIM](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [Realice una revisión de acceso de recursos de Azure y roles de Azure AD en PIM](pim-perform-azure-ad-roles-and-resource-roles-review.md)
