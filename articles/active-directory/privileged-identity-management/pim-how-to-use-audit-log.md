---
title: Visualización del informe del registro de auditoría para roles de Azure AD en PIM de Azure AD | Microsoft Docs
description: Aprenda a visualizar el historial de registro de auditoría de los roles de Azure AD en Privileged Identity Management (PIM) de Azure AD.
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
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 408ae958cb82557d6dabd92cd92e3967b9a8b40e
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667979"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Visualización del historial de auditoría para los roles de Azure AD en Privileged Identity Management

Puede utilizar el historial de auditoría de Privileged Identity Management (PIM) para ver todas las asignaciones de roles y las activaciones de los últimos 30 días para todos los roles con privilegios. Si quiere conservar los datos de auditoría durante más tiempo que el período de retención predeterminado, puede usar Azure Monitor para enrutarlos a una cuenta de almacenamiento de Azure. Para obtener más información, consulte [Archivado de registros de Azure AD en una cuenta de Azure Storage](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md). Si quiere ver el historial de auditoría completo de la actividad de la organización de Azure Active Directory (Azure AD), incluidos el administrador, el usuario final y la actividad de sincronización, puede usar los [informes de actividad y seguridad de Azure Active Directory](../reports-monitoring/overview-reports.md).

Siga estos pasos para ver el historial de auditoría de los roles de Azure AD.

## <a name="view-resource-audit-history"></a>Visualización del historial de auditoría de recursos

La auditoría de recursos proporciona una vista de toda la actividad asociada a los roles de Azure AD.

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Roles de Azure AD**.

1. Seleccione **Auditoría de recursos**.

1. Filtre el historial con una fecha predefinida o un intervalo personalizado.

    ![Lista de auditoría de roles de Azure AD con filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Visualización de mi auditoría

Mi auditoría permite ver la actividad del rol personal.

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Roles de Azure AD**.

1. Seleccione el recurso del que desea ver el historial de auditoría.

1. Seleccione **Mi auditoría**.

1. Filtre el historial con una fecha predefinida o un intervalo personalizado.

    ![Lista de auditoría para el usuario actual](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Pasos siguientes

- [Visualización de la actividad y del historial de auditoría para los roles de recursos de Azure en Privileged Identity Management](azure-pim-resource-rbac.md)
