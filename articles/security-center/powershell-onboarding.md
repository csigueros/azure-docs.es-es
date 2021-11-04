---
title: Incorporación a Microsoft Defender for Cloud con PowerShell
description: En este documento encontrará instrucciones sobre el proceso de habilitación de Microsoft Defender for Cloud con cmdlets de PowerShell.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/20/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c8fb5a75cd9254023555e7765cf4678c7431bec5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091244"
---
# <a name="automate-onboarding-of-microsoft-defender-for-cloud-using-powershell"></a>Automatización de la incorporación de Microsoft Defender for Cloud mediante PowerShell

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Puede proteger las cargas de trabajo de Azure a través de programación mediante el módulo PowerShell de Microsoft Defender for Cloud.
El uso de PowerShell le permite automatizar las tareas y evitar los errores humanos inherentes a las tareas manuales. Esto es especialmente útil en implementaciones a gran escala en las que intervienen docenas de suscripciones con cientos de miles de recursos, todos los cuales deben protegerse desde el principio.

La incorporación de Microsoft Defender for Cloud mediante PowerShell permite automatizar mediante programación la incorporación y la administración de los recursos de Azure y agregar los controles de seguridad necesarios.

En este artículo se proporciona un script de PowerShell de ejemplo que se puede modificar y usar en su entorno para implementar Defender for Cloud en sus suscripciones. 

En este ejemplo, se habilitará Defender for Cloud en una suscripción con el identificador: d07c0080-170c-4c24-861d-9c817742786c y se aplicará la configuración recomendada que proporciona un alto nivel de protección habilitando las características de seguridad mejoradas de Microsoft Defender for Cloud, que ofrece funcionalidades avanzadas de detección y protección contra amenazas:

1. Habilite [la seguridad mejorada en Microsoft Defender](enable-enhanced-security.md).
 
2. Establezca el área de trabajo de Log Analytics a la que el agente de Log Analytics enviará los datos que recopila de las máquinas virtuales asociadas con la suscripción; en este ejemplo, un área de trabajo definida por el usuario existente (myWorkspace).

3. Active el aprovisionamiento automático del agente de Defender for Cloud que [implementa el agente de Log Analytics](enable-data-collection.md#auto-provision-mma).

5. Establezca el [CISO de la organización como contacto de seguridad de las alertas y eventos destacados de Defender for Cloud](configure-email-notifications.md).

6. Asigne las [directivas de seguridad predeterminadas](tutorial-security-policy.md) de Defender for Cloud.

## <a name="prerequisites"></a>Prerrequisitos

Estos pasos deben realizarse antes de ejecutar los cmdlets de Defender for Cloud:

1. Ejecute PowerShell como administrador.

1. Ejecute los siguientes comandos en PowerShell:
      
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy AllSigned
    ```

    ```powershell
    Install-Module -Name Az.Security -Force
    ```

## <a name="onboard-defender-for-cloud-using-powershell"></a>Incorporación de Defender for Cloud mediante PowerShell

1. Registre las suscripciones en el proveedor de recursos de Defender for Cloud:

    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```

    ```powershell
    Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'
    ```

1. Opcional: establezca el nivel de cobertura (características de seguridad mejoradas de Microsoft Defender for Cloud activadas/desactivadas) de las suscripciones. Si no están definidas, estas características están desactivadas:

    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```

    ```powershell
    Set-AzSecurityPricing -Name "default" -PricingTier "Standard"
    ```

1. Configure el área de trabajo de Log Analytics a la que los agentes enviarán notificaciones. Debe tener un área de trabajo de Log Analytics ya creada a la que las máquinas virtuales de la suscripción enviarán notificaciones. Puede definir varias suscripciones para enviar notificaciones a la misma área de trabajo. Si no está definida, se usará el área de trabajo predeterminada.

    ```powershell
    Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"
    ```

1. Instalación de aprovisionamiento automático del agente de Log Analytics en las máquinas virtuales de Azure:
    
    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```
    
    ```powershell
    Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision
    ```

    > [!NOTE]
    > Recomendamos que habilite el aprovisionamiento automático para asegurarse de que las máquinas virtuales de Azure estén protegidas automáticamente por Microsoft Defender for Cloud.
    >

1. Opcional: se recomienda encarecidamente [definir los detalles de contacto de seguridad](configure-email-notifications.md) para las suscripciones que incorpore, que se usarán como destinatarios de las notificaciones y alertas generadas por Defender for Cloud:

    ```powershell
    Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -AlertAdmin -NotifyOnAlert
    ```

1. Asigne la iniciativa de directiva predeterminada de Defender for Cloud:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
    ```

    ```powershell
    $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Defender for Cloud Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'
    ```

Ha incorporado correctamente Microsoft Defender for Cloud con PowerShell.

Ya puede usar estos cmdlets de PowerShell con scripts de automatización para recorrer en iteración mediante programación suscripciones y recursos. Esto ahorra tiempo y reduce la probabilidad de error humano. Puede usar este [script de ejemplo](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) como referencia.




## <a name="see-also"></a>Consulte también
Para más información sobre cómo puede usar PowerShell para automatizar la incorporación a Defender for Cloud, consulte el artículo siguiente:

* [Az.Security](/powershell/module/az.security)

Para más información sobre Defender for Cloud, consulte los artículos siguientes:

* [Establecimiento de directivas de seguridad en Microsoft Defender for Cloud](tutorial-security-policy.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Microsoft Defender for Cloud](managing-and-responding-alerts.md): aprenda a administrar y responder a alertas de seguridad.
