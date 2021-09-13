---
title: Archivo de inclusión
description: archivo de inclusión
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 06/04/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 1197b15f313746c0a8542bfadeb4e0123818fca8
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575641"
---
| Recurso | Límite |
| --- | --- |
| Suscripciones [asociadas a un inquilino de Azure Active Directory](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Sin límite |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por suscripción |Sin límite |
| [Grupos de recursos](../articles/azure-resource-manager/management/overview.md) por suscripción |980 |
| Tamaño de solicitud de API de Azure Resource Manager |4 194 304 bytes |
| Etiquetas por suscripción<sup>1</sup> |50 |
| Cálculos de etiquetas únicas por suscripción<sup>1</sup> | 80 000 |
| [Implementaciones de nivel de suscripción](../articles/azure-resource-manager/templates/deploy-to-subscription.md) por ubicación | 800<sup>2</sup> |
| Ubicaciones de las [implementaciones en el nivel de suscripción](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 10 |

<sup>1</sup>Puede aplicar hasta 50 etiquetas directamente a una suscripción. Sin embargo, la suscripción puede contener un número ilimitado de etiquetas que se aplican a los grupos de recursos y recursos de la misma. El número de etiquetas por recurso o grupo de recursos se limita a 50. Resource Manager devuelve una [lista de valores y nombres de etiqueta únicos](/rest/api/resources/tags) en la suscripción solo cuando haya 80 000 etiquetas, o menos. Sin embargo, aunque haya más, es posible encontrar un recurso por etiqueta.

<sup>2</sup>Las implementaciones se eliminan automáticamente del historial a medida se aproxima al límite. Para obtener más información, consulte [Eliminaciones automáticas del historial de implementaciones](../articles/azure-resource-manager/templates/deployment-history-deletions.md).