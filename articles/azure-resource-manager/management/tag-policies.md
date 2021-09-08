---
title: Definiciones de directiva para el etiquetado de recursos
description: Describe las definiciones de Azure Policy que puede asignar para garantizar el cumplimiento de la etiqueta.
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: f7af799423b0aceac66dfa97e92c92d25c3916db
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456263"
---
# <a name="assign-policy-definitions-for-tag-compliance"></a>Asignación de definiciones de directiva para el cumplimiento de etiquetas

Puede usar [Azure Policy](../../governance/policy/overview.md) para aplicar reglas de etiquetado y convenciones. Mediante la creación de una directiva, evitará el escenario de recursos que se vayan a implementar en su suscripción que no cuentan con las etiquetas esperadas para su organización. En lugar de aplicar manualmente las etiquetas o buscar recursos que no son compatibles, cree una directiva que aplica automáticamente las etiquetas necesarias durante la implementación. También se pueden aplicar etiquetas a los recursos existentes con el nuevo efecto de [modificación](../../governance/policy/concepts/effects.md#modify) y una [tarea de corrección](../../governance/policy/how-to/remediate-resources.md). La sección siguiente muestra definiciones de directiva de ejemplo para las etiquetas.

## <a name="policy-definitions"></a>Definiciones de directiva

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>Pasos siguientes

* Para información sobre el etiquetado de recursos, consulte [Uso de etiquetas para organizar los recursos](tag-resources.md).
* No todos los tipos de recursos admiten etiquetas. Para determinar si se puede aplicar una etiqueta a un tipo de recurso determinado, consulte [Tag support for Azure resources](tag-support.md) (Compatibilidad con etiquetas para los recursos de Azure).
