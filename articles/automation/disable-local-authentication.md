---
title: Deshabilitación de la autenticación local en Azure Automation
description: En este artículo se describe cómo deshabilitar la autenticación local en Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: 29caef661b7078844991c3ca3867a1f03eb0114e
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293533"
---
# <a name="disable-local-authentication-in-automation"></a>Deshabilitación de la autenticación local en Automation

Azure Automation proporciona soporte a la autenticación en Microsoft Azure Active Directory (Azure AD) en todos los puntos de conexión públicos del servicio Automation. Esta mejora de seguridad crítica quita las dependencias de certificado y proporciona a las organizaciones el control para deshabilitar los métodos de autenticación local. Esta característica proporciona una integración fluida cuando se requiere un control centralizado y la administración de identidades y credenciales de recursos mediante Azure AD.

Azure Automation proporciona una característica opcional para "Deshabilitar la autenticación local" en el nivel de cuenta de Automation mediante la directiva de Azure [Configurar la cuenta de Azure Automation para deshabilitar la autenticación local](../automation/policy-reference.md#azure-automation). De forma predeterminada, esta marca se establece en false en la cuenta, por lo que puede usar la autenticación local y la autenticación de Azure AD. Si decide deshabilitar la autenticación local, el servicio Automation solo acepta la autenticación basada en Azure AD.

En Azure Portal, puede recibir un mensaje de advertencia en la página de aterrizaje de la cuenta de Automation seleccionada si la autenticación está deshabilitada.Para confirmar si la directiva de autenticación local está habilitada, use el cmdlet de PowerShell [Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount) y compruebe la propiedad `DisableLocalAuth`. Un valor de `true` significa que la autenticación local está deshabilitada.

La deshabilitación de la autenticación local no tiene efecto inmediato. Espere unos minutos para que el servicio bloquee las solicitudes de autenticación futuras.
 
## <a name="re-enable-local-authentication"></a>Nueva habilitación de la autenticación local

Para volver a habilitar la autenticación local, ejecute el cmdlet `Set-AzAutomationAccount` de PowerShell con el parámetro `-DisableLocalAuth false`.Espere unos minutos para que el servicio acepte el cambio para permitir solicitudes de autenticación local. 

## <a name="compatibility"></a>Compatibilidad

En la tabla siguiente se describen los comportamientos o características cuyo funcionamiento se impide al deshabilitar la autenticación local.

|Escenario | Alternativa |
|---|---|
|Llamada a un runbook con un webhook. | Inicie un trabajo de runbook mediante una plantilla de Azure Resource Manager, que usa autenticación de Azure AD. |
|Uso de Desired State Configuration de Automation.| Use la [configuración de invitado de Azure Policy](/governance/policy/concepts/guest-configuration).  |
|Uso de instancias de Hybrid Runbook Worker basadas en agente.| Use [instancias de Hybrid Runbook Worker basadas en extensiones (versión preliminar)](./extension-based-hybrid-runbook-worker-install.md).|

## <a name="limitations"></a>Limitaciones

La aplicación de revisiones de Update Management no funcionará cuando se deshabilite la autenticación local.


## <a name="next-steps"></a>Pasos siguientes
- [Introducción a la autenticación de cuentas de Azure Automation](./automation-security-overview.md)