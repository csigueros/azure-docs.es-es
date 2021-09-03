---
title: Cumplimiento mediante Azure Policy
description: Asignación de directivas integradas en Azure Policy para auditar el cumplimiento de los registros de contenedor de Azure
ms.topic: article
ms.date: 08/10/2021
ms.openlocfilehash: d8f986a3a857f622248daa75d0402f1abfbf8a7b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722485"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Auditoría del cumplimiento de los registros de contenedor de Azure mediante Azure Policy

[Azure Policy](../governance/policy/overview.md) es un servicio de Azure que se usa para crear, asignar y administrar las definiciones de directivas. Las definiciones de directivas aplican distintas reglas y efectos sobre los recursos, para que estos sigan siendo conformes con los estándares corporativos y los acuerdos de nivel de servicio.

En este artículo se presentan las definiciones de directivas integradas para Azure Container Registry. Use estas definiciones para auditar los registros nuevos y existentes a fin de determinar su cumplimiento.

No se aplican cargos por usar Azure Policy.

## <a name="built-in-policy-definitions"></a>Definiciones de directiva integradas

Las definiciones de directivas integradas siguientes son específicas de Azure Container Registry:

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="create-policy-assignments"></a>Creación de asignaciones de directivas

* Cree asignaciones de directivas mediante [Azure Portal](../governance/policy/assign-policy-portal.md), la [CLI de Azure](../governance/policy/assign-policy-azurecli.md), una [plantilla de Resource Manager](../governance/policy/assign-policy-template.md) o los SDK de Azure Policy.
* Limite el ámbito de una asignación de directiva a un grupo de recursos, una suscripción o un [grupo de administración de Azure](../governance/management-groups/overview.md). Las asignaciones de directivas del registro de contenedor se aplican a los registros de contenedor nuevos y existentes dentro del ámbito.
* Habilite o deshabilite la [aplicación de directivas](../governance/policy/concepts/assignment-structure.md#enforcement-mode) en cualquier momento.

> [!NOTE]
> Después de crear o actualizar una asignación de directiva, la asignación tarda algún tiempo en evaluar a los recursos del ámbito definido. Consulte información sobre [desencadenadores de evaluación de directivas](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Revisión del cumplimiento de directivas

Acceda a la información de cumplimiento generada por las asignaciones de directivas mediante Azure Portal, las herramientas de la línea de comandos de Azure o los SDK de Azure Policy. Para detalles, consulte [Obtención de datos de cumplimiento de los recursos de Azure](../governance/policy/how-to/get-compliance-data.md).

Cuando un recurso no es compatible, hay muchos motivos posibles para ello. Para determinar el motivo o encontrar al responsable del cambio, consulte [Determinación del incumplimiento](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Cumplimiento de directivas en el portal:

1. Seleccione **Todos los servicios** y busque **Directiva**.
1. Seleccione **Cumplimiento**.
1. Use los filtros para limitar los estados de cumplimiento o para buscar las directivas.

    ![Cumplimiento de directivas en el portal](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Seleccione una directiva para revisar los eventos y los detalles de cumplimiento de los agregados. Si lo desea, seleccione un registro específico para el cumplimiento de los recursos.

### <a name="policy-compliance-in-the-azure-cli"></a>Cumplimiento de directivas en la CLI de Azure

También puede usar la CLI de Azure para obtener datos de cumplimiento. Por ejemplo, use el comando [az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) en la CLI para obtener los identificadores de directiva de las directivas de Azure Container Registry que se aplican:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Salida del ejemplo:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Luego, ejecute [az policy state list](/cli/azure/policy/state#az_policy_state_list) para devolver el estado de cumplimiento con formato JSON para todos los recursos de un identificador de directiva específico:

```azurecli
az policy state list \
  --resource <policyID>
```

O bien ejecute [az policy state list](/cli/azure/policy/state#az_policy_state_list) para devolver el estado de cumplimiento con formato JSON de un recurso de registro específico, como *myregistry*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre las [definiciones](../governance/policy/concepts/definition-structure.md) y [efectos](../governance/policy/concepts/effects.md) de Azure Policy.

* Creación de una [definición de directiva personalizada](../governance/policy/tutorials/create-custom-policy-definition.md).

* Más información sobre las [funcionalidades de gobernanza](../governance/index.yml) en Azure.
