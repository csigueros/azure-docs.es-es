---
title: Determinación de las causas de incumplimiento
description: Cuando un recurso no es compatible, hay muchos motivos posibles para ello. Descubra qué es lo que provoca que no sea compatible.
ms.date: 09/01/2021
ms.topic: how-to
ms.openlocfilehash: 3c4076673adfe3253a418cc648592e72a8979b5a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433818"
---
# <a name="determine-causes-of-non-compliance"></a>Determinación de las causas de incumplimiento

Cuando se determina que un recurso de Azure no cumple con una regla de directiva, resulta útil entender con qué parte de la regla no es compatible el recurso. También resulta útil para comprender cuál es el cambio que modificó un recurso compatible anteriormente y lo transformó en no compatible. Hay dos maneras de obtener esta información:

- [Detalles de cumplimiento](#compliance-details)
- [Historial de cambios (versión preliminar)](#change-history)

## <a name="compliance-details"></a>Detalles de cumplimiento

Cuando un recurso no es compatible, los detalles de cumplimiento para ese recurso estarán disponibles en la página **Cumplimiento de directiva**. El panel de detalles de cumplimiento incluye la siguiente información:

- Detalles de recursos como el nombre, el tipo, la ubicación y el identificador del recurso.
- Estado de compatibilidad y marca de tiempo de la última evaluación para la asignación de la directiva actual.
- Una lista de _razones_ por las que el recurso no es compatible.

> [!IMPORTANT]
> Puesto que los detalles de cumplimiento para un recurso _no compatible_ muestran el valor correcto de las propiedades en ese recurso, el usuario debe realizar la operación de **lectura** en el **tipo** de recurso. Por ejemplo, si el recurso _no compatible_ es **Microsoft.Compute/virtualMachines**, el usuario debe realizar la operación **Microsoft.Compute/virtualMachines/read**. Si el usuario no realiza la operación necesaria, se muestra un error de acceso.

Para ver los detalles de cumplimiento, siga estos pasos:

1. Inicie el servicio Azure Policy en Azure Portal. Para ello, seleccione **Todos los servicios** y, a continuación, busque y seleccione **Directiva**.

1. En la página **Información general** o **Cumplimiento**, seleccione una directiva que sea con un **estado de compatibilidad** que sea _No compatible_.

1. En la pestaña **Compatibilidad de recursos** de **Cumplimiento de directiva**, seleccione y mantenga (o haga clic con el botón derecho), o seleccione los puntos suspensivos de un recurso en un **estado de compatibilidad** que sea _No compatible_. A continuación, seleccione **Ver detalles de cumplimiento**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Captura de pantalla del vínculo &quot;Ver detalles de cumplimiento&quot; en la pestaña Cumplimiento de recursos." border="false":::

1. La página **Detalles de cumplimiento** muestra información de la última evaluación del recurso en la asignación de directiva actual. En este ejemplo, el campo **Microsoft.Sql/servers/version** tiene que ser _12.0_ y se espera que la definición de política sea _14.0_. Si el recurso no es compatible por varias razones, estas se muestran en este panel.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Captura de pantalla del panel Detalles de cumplimiento y el motivos por el que no hay cumplimiento es que el valor actual es doce y el valor de destino es catorce." border="false":::

   Para una definición de directiva **auditIfNotExists** o **deployIfNotExists**, los detalles incluyen la propiedad **details.type** y cualquier propiedad opcional. Para obtener una lista, consulte [auditIfNotExists propiedades](../concepts/effects.md#auditifnotexists-properties) y [deployIfNotExists propiedades](../concepts/effects.md#deployifnotexists-properties). **Último recurso evaluado** es un recurso relacionado de la sección de **detalles** de la definición.

   Definición **deployIfNotExists** parcial de ejemplo:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Captura de pantalla del panel Detalles de cumplimiento para ifNotExists, incluido el recuento de recursos evaluados." border="false":::

> [!NOTE]
> Para proteger los datos, cuando un valor de propiedad es _secreto_, el valor actual muestra asteriscos.

Estos detalles explican por qué un recurso no es compatible actualmente, pero no muestran cuándo se realizó el cambio en el recurso que provocó que se transformara en no compatible. Para obtener esa información, consulte [Historial de cambios (versión preliminar)](#change-history) a continuación.

### <a name="compliance-reasons"></a>Razones de cumplimiento

Los [modos de Resource Manager](../concepts/definition-structure.md#resource-manager-modes) y los [modos del proveedor de recursos](../concepts/definition-structure.md#resource-provider-modes) tienen _razones_ diferentes para el no cumplimiento.

#### <a name="general-resource-manager-mode-compliance-reasons"></a>Razones de cumplimiento del modo general de Resource Manager

La siguiente tabla asigna cada _razón_ del [modo de Resource Manager](../concepts/definition-structure.md#resource-manager-modes) posible a la [condición](../concepts/definition-structure.md#conditions) responsable en la definición de la directiva:

|Motivo |Condición |
|-|-|
|El valor actual debe contener el valor de destino como una clave. |containsKey o **no** notContainsKey |
|El valor actual debe contener el valor de destino. |contains o **no** notContains |
|El valor actual debe ser igual al valor de destino. |equals o **no** notEquals |
|El valor actual debe ser inferior al valor de destino. |less o **no** greaterOrEquals |
|El valor actual debe ser superior o igual al valor de destino. |greaterOrEquals o **no** less |
|El valor actual debe ser superior al valor de destino. |greater o **no** lessOrEquals |
|El valor actual debe ser inferior o igual al valor de destino. |lessOrEquals o **no** greater |
|El valor actual debe existir. |exists |
|El valor actual debe estar en el valor de destino. |in o **no** notIn |
|El valor actual debe ser como el valor de destino. |like o **no** notLike |
|El valor actual debe coincidir con distinción de mayúsculas y minúsculas con el valor de destino. |match o **no** notMatch |
|El valor actual debe coincidir sin distinción de mayúsculas y minúsculas con el valor de destino. |matchInsensitively o **no** notMatchInsensitively |
|El valor actual no debe contener el valor de destino como clave. |notContainsKey o **no** containsKey|
|El valor actual no debe contener el valor de destino. |notContains o **no** contains |
|El valor actual no debe ser igual que el valor de destino. |notEquals o **no** equals |
|El valor actual no debe existir. |**no** exists  |
|El valor actual no debe estar en el valor de destino. |notIn o **no** in |
|El valor actual no debe ser como el valor de destino. |notLike o **no** like |
|El valor actual no debe coincidir con distinción de mayúsculas y minúsculas con el valor de destino. |notMatch o **no** match |
|El valor actual no debe coincidir sin distinción de mayúsculas y minúsculas con el valor de destino. |notMatchInsensitively o **no** matchInsensitively |
|Ninguno de los recursos relacionados coincide con los detalles de vigencia de la definición de directiva. |Un recurso del tipo definido en **then.details.type** y relacionado con el recurso definido en la porción **if** de la regla de directiva no existe. |

#### <a name="aks-resource-provider-mode-compliance-reasons"></a>Razones de cumplimiento del modo del proveedor de recursos de AKS

En la tabla siguiente se asigna cada _razón_ del `Microsoft.Kubernetes.Data`
[modo del proveedor de recursos](../concepts/definition-structure.md#resource-provider-modes) al estado responsable de la [plantilla de restricción](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates) en la definición de directiva:

|Motivo |Descripción de la razón de la plantilla de restricción |
|-|-|
|Constraint/TemplateCreateFailed |No se pudo crear el recurso para una definición de directiva con una restricción o plantilla que no coincide con una restricción o plantilla existentes en el clúster por nombre de metadatos de recursos. |
|Constraint/TemplateUpdateFailed |No se pudo actualizar la restricción o plantilla para una definición de directiva con una restricción o plantilla que coincide con una restricción o plantilla existentes en el clúster por nombre de metadatos de recursos. |
|Constraint/TemplateInstallFailed |No se pudo crear la restricción o plantilla y no se pudo instalar en el clúster para la operación de creación o actualización. |
|ConstraintTemplateConflicts |La plantilla tiene un conflicto con una o varias definiciones de directiva que usan el mismo nombre de plantilla con otro origen. |
|ConstraintStatusStale |Hay un estado "Auditoría" existente, pero Gatekeeper no ha realizado ninguna auditoría en la última hora. |
|ConstraintNotProcessed |No hay ningún estado y Gatekeeper ha realizado una auditoría en la última hora. |
|InvalidConstraint/Template |El servidor de API ha rechazado el recurso debido a un YAML incorrecto. Este motivo también puede deberse a una discrepancia en el tipo de parámetro (ejemplo: se proporciona una cadena en lugar de un entero).

> [!NOTE]
> En el caso de las asignaciones de directivas existentes y las plantillas de restricción que ya están en el clúster, si se produce un error en esa restricción o plantilla, el clúster se protege manteniendo la restricción o plantilla existentes. Se notifica el no cumplimiento del clúster hasta que el error se resuelva en la asignación de directivas o el complemento se recupere por sí mismo. Para más información sobre cómo controlar conflictos, consulte [Conflictos de plantilla de restricciones](../concepts/policy-for-kubernetes.md#constraint-template-conflicts).

## <a name="component-details-for-resource-provider-modes"></a>Detalles de componente de los modos de proveedor de recursos

En las asignaciones con un [modo de proveedor de recursos](../concepts/definition-structure.md#resource-provider-modes), seleccione el recurso _No compatible_ para abrir una vista más profunda. En la pestaña **Compatibilidad de componentes** hay información adicional específica del modo de proveedor de recursos en la directiva asignada que muestra _No compatible_, **Componente** e **Id. de componente**.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Captura de pantalla de la pestaña Compatibilidad de componentes y detalles de cumplimiento de una asignación de modo de proveedor de recursos." border="false":::

## <a name="compliance-details-for-guest-configuration"></a>Detalles de cumplimiento de la configuración de invitado

En las definiciones de directiva de la categoría _Configuración de invitados_, podría haber varias configuraciones evaluadas dentro de la máquina virtual y es necesario ver los detalles por configuración. Por ejemplo, si va a realizar una auditoría de una lista de opciones de seguridad y solo una de ellas tiene el estado _No conforme_, debe saber qué directivas de contraseñas específicas no cumplen los requisitos y por qué.

También es posible que no disponga de acceso para iniciar sesión en la máquina virtual directamente, pero tenga que comunicar el motivo por el que la máquina virtual es _No compatible_.

### <a name="azure-portal"></a>Azure portal

Para empezar, siga los mismos pasos de la sección anterior para ver los detalles de cumplimiento de directivas.

En la vista del panel Detalles de cumplimiento, seleccione el vínculo **Último recurso evaluado**.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Captura de pantalla de la visualización de los detalles de cumplimiento de la definición de auditIfNotExists." border="false":::

La página **Asignación de invitado** muestra todos los detalles de cumplimiento disponibles. Cada fila de la vista representa una evaluación que se realizó dentro de la máquina. En la columna **Motivo**, se muestra una frase que describe el motivo por el que Asignación de invitado es _No compatible_. Por ejemplo, si audita directivas de contraseñas, la columna **Motivo** mostraría un texto que incluye el valor actual de cada configuración.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Captura de pantalla de los detalles de cumplimiento de la asignación de invitados." border="false":::

### <a name="view-configuration-assignment-details-at-scale"></a>Visualización de los detalles de la asignación de configuración a escala

La característica de configuración de invitado se puede usar fuera de las asignaciones de Azure Policy.
Por ejemplo, [Azure AutoManage](../../../automanage/automanage-virtual-machines.md) crea asignaciones de configuración de invitado, o puede asignar [configuraciones cuando implemente máquinas](guest-configuration-create-assignment.md).

Para ver todas las asignaciones de configuración de invitado del inquilino, en Azure Portal abra la página **Asignaciones de invitado**. Para ver información detallada sobre el cumplimiento, seleccione cada asignación mediante el vínculo de la columna "Nombre".

:::image type="content" source="../media/determine-non-compliance/guest-config-assignment-view.png" alt-text="Captura de pantalla de la página Asignación de invitados." border="true":::

## <a name="change-history-preview"></a><a name="change-history"></a>Historial de cambios (versión preliminar)

Como parte de una nueva **versión preliminar pública**, los últimos 14 días del historial de cambios están disponibles para todos los recursos de Azure que admiten la [eliminación de modo completa](../../../azure-resource-manager/templates/deployment-complete-mode-deletion.md). El historial de cambios proporciona información acerca de cuándo se detectó un cambio y una _diferencia visual_ para cada cambio. Se desencadena una detección de cambios cuando se agregan, eliminan o modifican las propiedades de Azure Resource Manager.

1. Inicie el servicio Azure Policy en Azure Portal. Para ello, seleccione **Todos los servicios** y, a continuación, busque y seleccione **Directiva**.

1. En la página **Información general** o **Cumplimiento**, seleccione una directiva con cualquier **estado de compatibilidad**.

1. En la pestaña **Compatibilidad de recursos**, de la página **Cumplimiento de directiva**, seleccione un recurso.

1. Seleccione la pestaña **Historial de cambios (versión preliminar)** en la página **Compatibilidad de recursos**. Se muestra una lista de cambios detectados, si existe alguna.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Captura de pantalla de la pestaña Historial de cambios y las horas de modificación detectadas en la página Cumplimiento de recursos." border="false":::

1. Seleccione uno de los cambios detectados. Las _diferencias visuales_ para el recurso se presentan en la página **Historial de cambios**.

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Captura de pantalla de las diferencias visuales del historial de cambios en el estado anterior y posterior de las propiedades en la página Historial de cambios." border="false":::

Las _diferencias visuales_ ayudan a identificar los cambios de un recurso. Los cambios detectados pueden no estar relacionados con el estado de compatibilidad actual del recurso.

[Azure Resource Graph](../../resource-graph/overview.md) proporciona los datos del historial de cambios. Para consultar esta información fuera de Azure Portal, consulte [Obtención de cambios de recursos](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Pasos siguientes

- Puede consultar ejemplos en [Ejemplos de Azure Policy](../samples/index.md).
- Revise la [estructura de definición de Azure Policy](../concepts/definition-structure.md).
- Vea la [Descripción de los efectos de directivas](../concepts/effects.md).
- Obtenga información acerca de cómo se pueden [crear directivas mediante programación](programmatically-create.md).
- Obtenga información sobre cómo [obtener datos de cumplimiento](get-compliance-data.md).
- Obtenga información sobre cómo [corregir recursos no compatibles](remediate-resources.md).
- En [Organización de los recursos con grupos de administración de Azure](../../management-groups/overview.md), obtendrá información sobre lo que es un grupo de administración.