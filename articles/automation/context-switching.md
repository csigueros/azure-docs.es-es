---
title: Cambio de contexto en Azure Automation
description: En este artículo se explica el cambio de contexto y cómo evitar problemas de los runbook.
services: automation
ms.subservice: process-automation
ms.date: 09/27/2021
ms.topic: conceptual
ms.openlocfilehash: 121e302708fecd20934f6ba57bd08590e45a7429
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233207"
---
# <a name="context-switching-in-azure-automation"></a>Cambio de contexto en Azure Automation

Un cambio de contexto se produce cuando el contexto de un proceso cambia al contexto de un proceso diferente. Un contexto Azure es un conjunto de información que define el destino de los cmdlets de Azure PowerShell. El contexto consta de las propiedades siguientes:

|Propiedad | Descripción |
|---|---|
|Nombre | Nombre del contexto.|
|Cuenta | El nombre de usuario o la entidad de servicio que se utiliza para autenticar las comunicaciones con Azure.|
|Entorno | Representa la nube de Azure global o una de las nubes nacionales de Azure, como Azure Government. También puede especificar una plataforma de nube híbrida, como Azure Stack.|
|Subscription | Representa la suscripción de Azure que contiene los recursos que quiere administrar.|
|Inquilino | Instancia dedicada y de confianza de Azure Active Directory que representa una organización individual.|
|Credenciales | La información que Azure utiliza para comprobar su identidad y confirmar su autorización para acceder a los recursos de Azure.|

Cuando una cuenta inicia una sesión que puede acceder a varias suscripciones, cualquiera de esas suscripciones puede agregarse al contexto del usuario. Para garantizar el uso de la suscripción correcta, debe declararla al conectarse. Por ejemplo, use `Add-AzAccount -Credential $Cred -subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749'`. Sin embargo, pueden surgir problemas cuando los runbooks que administran una suscripción se ejecutan en el mismo proceso de espacio aislado que los demás runbooks que administran los recursos de otra suscripción desde la misma cuenta de Automation. Los cambios del contexto que realice un runbook pueden afectar al resto de runbooks que usan el contexto predeterminado. Dado que el contexto incluye información, como las credenciales que se usan y la suscripción de destino, los cmdlets podrían destinarse a la suscripción incorrecta, lo que provocaría errores de permisos o de tipo `not found`. Este problema se conoce como **cambio de contexto.**

## <a name="manage-azure-contexts"></a>Administración de contextos de Azure

Para evitar que los runbooks se ejecuten en recursos de una suscripción incorrecta, revise las recomendaciones siguientes:

1. Deshabilite el almacenamiento del contexto de espacio aislado en el runbook de Automation mediante el comando siguiente al principio de cada runbook: `Disable-AzContextAutosave -Scope Process`.
1. Los cmdlets Azure PowerShell admiten el parámetro `-DefaultProfile`. Este parámetro se ha agregado a todos los cmdlets de Az y Azure Resource Manager (AzureRM) para admitir la ejecución de varios scripts en el mismo proceso, lo que permite especificar el contexto que se va a usar para cada cmdlet. Guarde el objeto de contexto en el runbook cuando se cree y cada vez que se cambie. Después, haga referencia a este en cada llamada que se realice con el cmdlet de Az o AzureRM. Por ejemplo, `$AzureContext = Set-AzContext -SubscriptionId $subID`.
1. Pase el objeto de contexto al cmdlet de PowerShell, por ejemplo, `Get-AzVM -ResourceGroupName "myGroup" -DefaultProfile $AzureContext`.

Este es un fragmento de código de runbook de PowerShell que usa una identidad administrada asignada por el sistema de acuerdo con las recomendaciones para evitar el cambio de contexto.

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

# Pass context object - even though the context had just been set
# This is the step that guarantees the context will not be switched.
Get-AzVM -ResourceGroupName "resourceGroupName" -DefaultProfile $AzureContext | Select Name
```

## <a name="possible-symptoms"></a>Posibles síntomas

Puede que no surja ningún problema aunque no siga estas recomendaciones, pero también es posible que lo haya. El problema subyacente de esta situación es la sincronización; es decir, depende de la acción que esté realizando cada runbook en el momento en que el otro runbook cambia su contexto. A continuación, se muestran algunos mensajes de error posibles. Sin embargo, estos pueden deberse a otras condiciones no relacionadas con el cambio de contexto.

`The subscription named <subscription name> cannot be found.`

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

```error
Get-AzureRmResource : Resource group "SomeResourceGroupName" could not be found.
... resources = Get-AzResource -ResourceGroupName $group.ResourceGro ...
                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Get-AzResource], CloudException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.ResourceManager.Cmdlets.Implementation.GetAzureResourceCmdlet
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a la autenticación de cuentas de Azure Automation](automation-security-overview.md)
- [Ejecución de un runbook en Azure Automation](automation-runbook-execution.md)
- [Administración de módulos en Azure Automation](./shared-resources/modules.md)

