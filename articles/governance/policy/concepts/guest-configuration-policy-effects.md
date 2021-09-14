---
title: Opciones de corrección para la configuración de invitados
description: La característica de configuración de invitado de Azure Policy ofrece opciones para la corrección o el control continuos mediante tareas de corrección.
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 6c16f4a2b20ea753b1ded4e8d389babc613b4b36
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868560"
---
# <a name="remediation-options-for-guest-configuration"></a>Opciones de corrección para la configuración de invitados

Antes de comenzar, es una buena idea leer la página de información general de la [configuración de invitado](../concepts/guest-configuration.md).

> [!IMPORTANT]
> Se requiere la extensión de configuración de invitado para Azure Virtual Machines. Para implementar la extensión a gran escala en todas las máquinas, asigne la iniciativa de directiva siguiente: `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
> 
> Para usar paquetes de configuración de invitado que aplican configuraciones, se requiere la versión de la extensión de configuración de invitado de máquina virtual de Azure **1.29.24** o posterior, o el agente de Arc **1.10.0** o posterior.
>
> Las definiciones de directivas de configuración de invitado personalizadas que usan **AuditIfNotExists** están disponibles con carácter general, pero las definiciones que usan **DeployIfNotExists** con la configuración de invitado están en **versión preliminar**.

## <a name="how-remediation-set-is-managed-by-guest-configuration"></a>Administración de la corrección (Set) mediante la configuración de invitado

La configuración de invitado usa el efecto de directiva [DeployIfNotExists](../concepts/effects.md#deployifnotexists) en las definiciones que proporcionan cambios dentro de las máquinas.
Establezca las propiedades de una asignación de directiva para controlar cómo la [evaluación](../concepts/effects.md#deployifnotexists-evaluation) proporciona configuraciones automáticamente o a petición.

[Hay disponible un tutorial de vídeo de este documento](https://youtu.be/rjAk1eNmDLk).

### <a name="guest-configuration-assignment-types"></a>Tipos de asignación de configuración de invitado

Hay tres tipos de asignación disponibles cuando se crean asignaciones de invitado.
La propiedad está disponible como parámetro de definiciones de configuración de invitado que admiten **DeployIfNotExists**.

| Tipo de asignación | Comportamiento |
|-|-|
| Auditoría | Informe sobre el estado de la máquina, pero no realice cambios. |
| ApplyandMonitor | Se aplica a la máquina una vez y, luego, se supervisan los cambios. Si la configuración se desvía y se convierte en "NonCompliant", no se corregirá automáticamente a menos que se active la corrección. |
| ApplyandAutoCorrect | Se aplica a la máquina. Si se desvía, el servicio local dentro de la máquina realiza una corrección en la siguiente evaluación. |

En cada uno de los tres tipos de asignación, cuando se asigna una nueva directiva a una máquina existente, se crea automáticamente una asignación de invitado para auditar primero el estado de la configuración y así tener información que permita tomar decisiones sobre qué máquinas necesitan corrección.

## <a name="remediation-on-demand-applyandmonitor"></a>Corrección a petición (ApplyAndMonitor)

De forma predeterminada, las asignaciones de configuración de invitado funcionan en un escenario de "corrección a petición". La configuración se aplica y, luego, se permite que se desvíe de los requisitos de cumplimiento. El estado de cumplimiento de la asignación de invitado es "Compliant" a menos que se produzca un error al aplicar la configuración o si durante la siguiente evaluación la máquina ya no está en el estado deseado. El agente notifica el estado "NonCompliant" y no se corrige automáticamente.

Para habilitar este comportamiento, establezca la [propiedad assignmentType](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype) de la asignación de configuración de invitado en "ApplyandMonitor". Cada vez que se procesa la asignación dentro de la máquina, para cada recurso, el método [Test](/powershell/scripting/dsc/resources/get-test-set#test) devuelve "true", y el agente notifica "Compliant" o, si el método devuelve "false", el agente notifica "NonCompliant".

## <a name="continuous-remediation-autocorrect"></a>Corrección continua (AutoCorrect)

La configuración de invitado admite el concepto de "corrección continua". Si la máquina se desvía de los requisitos de cumplimiento de una configuración, la próxima vez que se evalúe la configuración se corregirá automáticamente. A menos que se produzca un error, la máquina siempre notifica el estado "Compliant" para la configuración. No hay ninguna manera de notificar cuándo se corrigió automáticamente un desvío al usar la corrección continua.

Para habilitar este comportamiento, establezca la [propiedad assignmentType](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype) de la asignación de configuración de invitado en "ApplyandAutoCorrect". Cada vez que se procesa la asignación dentro de la máquina, para cada recurso, el método [Test](/powershell/scripting/dsc/resources/get-test-set#test) devuelve "false" y el método [Set](/powershell/scripting/dsc/resources/get-test-set#set) se ejecuta automáticamente.

## <a name="disable-remediation"></a>Deshabilitación de la corrección

Cuando la propiedad `assignmentType` se establece en "Audit", el agente solo realiza una auditoría de la máquina y no intenta corregir la configuración si no es compatible.

### <a name="disable-remediation-of-custom-content"></a>Deshabilitación de la corrección del contenido personalizado

Puede invalidar la propiedad de tipo de asignación en los paquetes de contenido personalizado; para ello, agregue una etiqueta a la máquina con el nombre **CustomGuestConfigurationSetPolicy** y el valor **disable**. Al agregar la etiqueta se deshabilita la corrección solo para paquetes de contenido personalizado, no para el contenido integrado proporcionado por Microsoft.

## <a name="azure-policy-enforcement"></a>Cumplimiento de Azure Policy

Las asignaciones de Azure Policy incluyen una propiedad obligatoria [Enforcement Mode](../concepts/assignment-structure.md#enforcement-mode) (Modo de cumplimiento) que determina el comportamiento de los recursos nuevos y existentes.
Use esta propiedad para controlar si las configuraciones se aplican automáticamente a las máquinas.

**De forma predeterminada, el cumplimiento está habilitado**. Cuando se implementa una nueva máquina **o se actualizan las propiedades de una máquina**, si la máquina está en el ámbito de una asignación de Azure Policy con una definición de directiva en la categoría "Configuración de invitado", Azure Policy aplica automáticamente la configuración. **Las operaciones de actualización incluyen acciones que se producen en Azure Resource Manager**, como agregar o cambiar una etiqueta, y para las máquinas virtuales, cambios como cambiar el tamaño de un disco o conectarlo. Deje habilitado el cumplimiento si se debe corregir la configuración cuando se produzcan cambios en el recurso de máquina de Azure. Los cambios que se realicen dentro de la máquina no desencadenan la corrección automática, siempre y cuando no cambien el recurso de la máquina en Azure Resource Manager.

Si el cumplimiento está deshabilitado, la asignación de configuración audita el estado de la máquina hasta que una [tarea de corrección](../how-to/remediate-resources.md) cambie el comportamiento. De forma predeterminada, las definiciones de configuración de invitado actualizan la [propiedad assignmentType](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype) de "Audit" a "ApplyandMonitor" para que la configuración se aplique una vez y, luego, no se volverá a aplicar hasta que se active una corrección.

## <a name="optional-remediate-all-existing-machines"></a>OPCIONAL: Corrección de todas las máquinas existentes

Si se crea una asignación de Azure Policy desde Azure Portal, en la pestaña "Corrección" hay una casilla "Crear una tarea de corrección". Cuando la casilla está activada, después de que se crea la asignación de directiva, las tareas de corrección corregirán automáticamente los recursos que se evalúen como "NonCompliant".

El efecto de esta configuración para la configuración de invitado es que puede implementar una configuración en muchas máquinas simplemente con solo asignar una directiva. Tampoco tendrá que ejecutar manualmente la tarea de corrección en las máquinas que no sean compatibles.

## <a name="manually-trigger-remediation-outside-of-azure-policy"></a>Activación manual de la corrección fuera de Azure Policy

También es posible organizar la corrección fuera de la experiencia de Azure Policy mediante la actualización de un recurso de asignación de invitado, incluso si la actualización no realiza cambios en las propiedades del recurso.

Cuando se crea una asignación de configuración de invitado, la [propiedad complianceStatus](/rest/api/guestconfiguration/guest-configuration-assignments/get#compliancestatus) se establece en "Pending".
El servicio de configuración de invitado dentro de la máquina (suministrado a las máquinas virtuales de Azure mediante la [extensión de configuración de invitado](../../../virtual-machines/extensions/guest-configuration.md) e incluido con servidores habilitados para Arc) solicita una lista de asignaciones cada 5 minutos.
Si la asignación de configuración de invitado tiene ambos requisitos, un valor de `complianceStatus` de "Pending" y un valor de `configurationMode` de "ApplyandMonitor" o "ApplyandAutoCorrect", el servicio de la máquina aplica la configuración. Después de aplicar la configuración, en el [siguiente intervalo](./guest-configuration.md#validation-frequency), el modo de configuración determina si el comportamiento es notificar solo el estado de cumplimiento y permitir el desvío o corregir automáticamente.

## <a name="understanding-combinations-of-settings"></a>Descripción de las combinaciones de configuraciones

|~| Auditoría | ApplyandMonitor | ApplyandAutoCorrect |
|-|-|-|-|
| Cumplimiento habilitado | Solo notifica el estado | Configuración aplicada en VM Create **y aplicada de nuevo en Update**; de lo contrario, se permite el desvío | Configuración aplicada en VM Create y aplicada de nuevo en Update y corregida en el siguiente intervalo si se produce un desvío |
| Cumplimiento deshabilitado | Solo notifica el estado | Configuración aplicada, pero se permite el desvío | Configuración aplicada en VM Create o Update y corregida en el siguiente intervalo si se produce un desvío |

## <a name="next-steps"></a>Pasos siguientes

- Lea la [introducción a la configuración de invitado](./guest-configuration.md).
- Configure un [entorno de desarrollo](../how-to/guest-configuration-create-setup.md) personalizado de paquetes de configuración de invitado.
- [Cree un artefacto de paquete](../how-to/guest-configuration-create.md) para la configuración de invitado.
- [Pruebe el artefacto de paquete](../how-to/guest-configuration-create-test.md) desde el entorno de desarrollo.
- Use el módulo `GuestConfiguration` a fin de [crear una definición de Azure Policy](../how-to/guest-configuration-create-definition.md) para la administración a gran escala del entorno.
- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Aprenda a ver los [detalles de cumplimiento para las asignaciones de directivas de configuración de invitado](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration).
