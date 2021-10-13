---
title: 'Paneles de recursos para las revisiones de acceso en PIM: Azure AD | Microsoft Docs'
description: Describe cómo usar un panel de recursos para realizar una revisión de acceso en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d87ff7dc27dea5b25995dcfbd7f969a6d01fec56
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669271"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Uso de un panel de recursos para realizar una revisión de acceso en Privileged Identity Management (PIM)

Puede usar un panel de recursos para realizar una revisión de acceso en Privileged Identity Management (PIM). El panel de vista de administración en Azure Active Directory (Azure AD) tiene tres componentes principales:

- Una representación gráfica de las activaciones de rol de recursos.
- Gráficos que muestran la distribución de las asignaciones de roles por tipo de asignación.
- Un área de datos que contiene información sobre las nuevas asignaciones de roles

![Captura de pantalla del panel de la vista de administrador, que muestra gráficos y tablas](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Captura de pantalla del panel de la vista de administrador, que muestra listas de datos](media/pim-resource-roles-overview-dashboards/role-settings.png)

La representación gráfica de las activaciones de rol de recursos de los últimos siete días. Estos datos se limitan al recurso seleccionado y muestran las activaciones de los roles más comunes (propietario, colaborador, administrador de acceso de usuario) y de la combinación de todos los roles.

A un lado del grafo de activaciones, dos gráficos muestran la distribución de las asignaciones de roles por tipo de asignación, tanto de usuario como de grupo. Para cambiar el valor a un porcentaje (o viceversa), seleccione un segmento del gráfico.

Debajo de los gráficos aparece el número de usuarios y grupos con nuevas asignaciones de roles de los últimos 30 días y una lista de los roles ordenada por asignaciones totales (en orden descendente).

## <a name="next-steps"></a>Pasos siguientes

- [Inicio de una revisión de acceso para los roles de recursos de Azure en Privileged Identity Management](./pim-create-azure-ad-roles-and-resource-roles-review.md)