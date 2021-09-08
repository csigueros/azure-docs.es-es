---
title: Habilitación automática de la copia de seguridad al crear máquinas virtuales mediante Azure Policy
description: Un artículo en el que se describe cómo usar Azure Policy para habilitar automáticamente la copia de seguridad de todas las máquinas virtuales creadas en un ámbito determinado
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: fe70ba544bb75cee7faf3a4eb3aa2f5ef440909e
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/06/2021
ms.locfileid: "113300617"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Habilitación automática de la copia de seguridad al crear máquinas virtuales mediante Azure Policy

Una de las principales responsabilidades de un administrador de copia de seguridad o de cumplimiento en una organización es asegurarse de que se realiza una copia de seguridad de todas las máquinas críticas para la empresa con el período de retención adecuado.

En la actualidad, Azure Backup proporciona una variedad de directivas integradas (con [Azure Policy](../governance/policy/overview.md)) para ayudarle a garantizar automáticamente que las máquinas virtuales de Azure estén configuradas para la copia de seguridad. En función de cómo estén organizados los equipos y los recursos de copia de seguridad, puede usar cualquiera de las siguientes directivas:

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Directiva 1: Configuración de la copia de seguridad en las VM sin una etiqueta dada en un almacén de Recovery Services existente en la misma ubicación

Si su organización tiene un equipo central de copia de seguridad que administra las copias de seguridad en todos los equipos de la aplicación, puede usar esta directiva para configurar la copia de seguridad en un almacén de Recovery Services central existente en la misma suscripción y ubicación que las máquinas virtuales que se controlan. Puede optar por **excluir** máquinas virtuales que contengan una determinada etiqueta del ámbito de esta directiva.

## <a name="policy-2---configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Directiva 2: Configuración de la copia de seguridad en las VM con una etiqueta dada en un almacén de Recovery Services existente en la misma ubicación
Esta directiva funciona igual que la directiva 1 anterior, con la única diferencia de que puede usar esta directiva para **incluir** máquinas virtuales que contengan una etiqueta determinada en el ámbito de esta directiva. 

## <a name="policy-3---configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Directiva 3: Configuración de la copia de seguridad en las VM sin una etiqueta dada en un nuevo almacén de Recovery Services con una directiva predeterminada
Si organiza aplicaciones en grupos de recursos dedicados y desea que las copias de seguridad se realicen en el mismo almacén, esta directiva le permite administrar automáticamente esta acción. Puede optar por **excluir** máquinas virtuales que contengan una determinada etiqueta del ámbito de esta directiva.

## <a name="policy-4---configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Directiva 4: Configuración de la copia de seguridad en las VM con una etiqueta dada en un nuevo almacén de Recovery Services con una directiva predeterminada
Esta directiva funciona igual que la directiva 3 anterior, con la única diferencia de que puede usar esta directiva para **incluir** máquinas virtuales que contengan una etiqueta determinada en el ámbito de esta directiva. 

Además de lo anterior, Azure Backup también proporciona una directiva de [ solo auditoría](../governance/policy/concepts/effects.md#audit): **Azure Backup debe estar habilitado para máquinas virtuales**. Esta directiva identifica las máquinas virtuales que no tienen habilitada la copia de seguridad, pero no configura automáticamente las copias de seguridad de estas máquinas virtuales. Esto resulta útil cuando solo está pensando en evaluar el cumplimiento general de las máquinas virtuales, pero no desea tomar medidas inmediatamente.

## <a name="supported-scenarios"></a>Escenarios admitidos

* La directiva integrada solo se admite actualmente para máquinas virtuales de Azure. Los usuarios deben tener cuidado para asegurarse de que la directiva de retención especificada durante la asignación sea una directiva de retención de máquinas virtuales. Consulte [este documento](./backup-azure-policy-supported-skus.md) para ver todas las SKU de máquina virtual que admite esta directiva.

* Las directivas 1 y 2 se pueden asignar a una única ubicación y suscripción a la vez. Para habilitar la copia de seguridad de máquinas virtuales entre ubicaciones y suscripciones, es necesario crear varias instancias de la asignación de directiva, una para cada combinación de ubicación y suscripción.

* En el caso de las directivas 1 y 2, actualmente no se admite el ámbito del grupo de administración.

* Para las directivas 1 y 2, el almacén especificado y las máquinas virtuales configuradas para la copia de seguridad pueden pertenecer a distintos grupos de recursos.

* Las directivas 3 y 4 se pueden asignar a una sola suscripción a la vez (o a un grupo de recursos dentro de una suscripción).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>Uso de directivas integradas

En los pasos siguientes se describe el proceso de un extremo a otro de asignación de la directiva 1: **Configuración de la copia de seguridad en las VM sin una etiqueta dada en un almacén de Recovery Services existente en la misma ubicación** a un ámbito determinado. Se aplicarán instrucciones similares para las otras directivas. Una vez asignadas, las nuevas máquinas virtuales creadas en el ámbito se configuran automáticamente para la copia de seguridad.

1. Inicie sesión en Azure Portal y vaya al panel **Directiva**.
2. Seleccione **Definiciones** en el menú de la izquierda para obtener una lista de todas las directivas integradas en los recursos de Azure.
3. Filtre la lista por **Categoría=Copia de seguridad** y seleccione la directiva denominada "Configurar la copia de seguridad para máquinas virtuales de una ubicación en un almacén central existente de la misma ubicación".
![Panel de directivas](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Seleccione el nombre de la directiva. Se le redirigirá a la definición detallada de dicha directiva.
![Panel de definición de directiva](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Seleccione el botón **Asignar** en la parte superior del panel. Esta acción le redirigirá al panel **Asignar directiva**.
6. En **Conceptos básicos**, seleccione los tres puntos situados junto al campo **Ámbito**. Se abrirá el panel de contexto adecuado, donde podrá seleccionar la suscripción en la que se va a aplicar la directiva. También puede seleccionar un grupo de recursos, de modo que la directiva se aplique solo a las máquinas virtuales de un grupo de recursos determinado.
![Conceptos básicos de la asignación de directiva](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. En la pestaña **Parámetros**, elija una ubicación de la lista desplegable y seleccione el almacén y la directiva de copia de seguridad a la que se deben asociar las máquinas virtuales del ámbito. También puede especificar un nombre de etiqueta y una matriz de valores de etiqueta. Una máquina virtual que contenga cualquiera de los valores especificados para la etiqueta especificada se excluirá del ámbito de la asignación de directiva.
![Parámetros de la asignación de directiva](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Asegúrese de que **Efecto** esté establecido en deployIfNotExists.
9. Vaya a **Revisar + crear** y seleccione **Crear**.

> [!NOTE]
>
> También se puede usar Azure Policy en máquinas virtuales existentes, mediante la [corrección](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Se recomienda que esta directiva no se asigne a más de 200 máquinas virtuales a la vez. Si la directiva está asignada a más de 200 máquinas virtuales, puede ocurrir que la copia de seguridad se desencadene unas horas después de lo especificado en la programación.

## <a name="next-steps"></a>Pasos siguientes

[Más información acerca de Azure Policy](../governance/policy/overview.md)