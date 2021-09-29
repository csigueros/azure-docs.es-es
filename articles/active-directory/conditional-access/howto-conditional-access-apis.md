---
title: 'API de acceso condicional y PowerShell: Azure Active Directory'
description: Uso de las API de acceso condicional de Azure AD y PowerShell para administrar directivas como código
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4c6e36defc8b3e97f73133d2fb766e64a872fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618984"
---
# <a name="conditional-access-programmatic-access"></a>Acceso condicional: Acceso mediante programación

Muchas organizaciones han expresado su necesidad de administrar sus entornos como código tanto como sea posible. Con Microsoft Graph puede tratar las directivas de acceso condicional como cualquier otro elemento de código del entorno.

Microsoft Graph proporciona un modelo de programación unificado que las organizaciones pueden usar para interactuar con los datos de Microsoft 365, Windows 10 y Enterprise Mobility + Security. Para más información sobre Microsoft Graph, consulte el artículo [Introducción a Microsoft Graph](/graph/overview).

![Imagen que muestra los recursos principales y las relaciones que forman parte del grafo](./media/howto-conditional-access-apis/microsoft-graph.png)

Los ejemplos siguientes se proporcionan tal cual sin soporte técnico. Puede usar estos ejemplos como base para las herramientas de su organización. 

Muchos de los ejemplos siguientes usan herramientas como [Identidades administradas](../managed-identities-azure-resources/overview.md), [Logic Apps](../../logic-apps/logic-apps-overview.md), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/) y [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="configure"></a>Configuración

### <a name="powershell"></a>PowerShell

Para muchos administradores, PowerShell ya es una herramienta de scripting conocida. En el ejemplo siguiente se muestra cómo usar el [módulo de PowerShell de Azure AD](https://www.powershellgallery.com/packages/AzureAD) para administrar directivas de acceso condicional.

- [Configuración de directivas de acceso condicional con comandos de PowerShell de Azure AD](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>Graph API

En este ejemplo se muestran las opciones básicas de creación, lectura, actualización y eliminación (CRUD) disponibles en las Graph API de acceso condicional. En el ejemplo también se incluyen algunas plantillas JSON que puede usar para crear algunas directivas de ejemplo.

- [Configuración de directivas de acceso condicional con llamadas a Microsoft Graph API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Configuración mediante plantillas

Use las API de acceso condicional para implementar directivas de acceso condicional en el entorno de preproducción mediante una plantilla.

- [Configuración de directivas de acceso condicional con plantillas de Microsoft Graph API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Prueba

Este ejemplo modela prácticas de implementación más seguras con flujos de trabajo de aprobación que pueden copiar directivas de acceso condicional desde un entorno, como el de preproducción, a otro, como el entorno de producción.

- [Promoción de directivas de acceso condicional desde entornos de prueba](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Implementación

En este ejemplo se proporciona un mecanismo para realizar una implementación de ensayo de las directivas de acceso condicional gradualmente a la población de usuarios, lo que le permite administrar el impacto en el soporte técnico y detectar los problemas puntualmente.

- [Implementación de directivas de acceso condicional en entornos de producción con flujos de trabajo de aprobación](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Monitor

Este ejemplo proporciona un mecanismo para supervisar los cambios de las directivas de acceso condicional a lo largo del tiempo y puede desencadenar alertas cuando se modifican las directivas principales.

- [Supervisión de cambios en las directivas de acceso condicional implementadas y activación de alertas](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Administrar

### <a name="backup-and-restore"></a>Copia de seguridad y restauración

Automatice la copia de seguridad y la restauración de las directivas de acceso condicional mediante aprobaciones de Teams con este ejemplo.

- [Administración del proceso de copia de seguridad y restauración de las directivas de acceso condicional mediante llamadas a Microsoft Graph API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Cuentas de acceso de emergencia

Varios administradores pueden crear directivas de acceso condicional y es posible que olviden agregar las [cuentas de acceso de emergencia](../roles/security-emergency-access.md) como una exclusión de esas directivas. Este ejemplo garantiza que todas las directivas se actualizan para incluir las cuentas de acceso de emergencia designadas.

- [Administración de la asignación de cuentas de acceso de emergencia a las directivas de acceso condicional mediante llamadas a Microsoft Graph API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Planes de contingencia

Las cosas no siempre funcionan de la manera que se desea y, cuando eso suceda, necesita una manera de volver a un estado en el que el trabajo pueda continuar. En el ejemplo siguiente se proporciona una manera de revertir las directivas a un plan de contingencia correcto conocido y deshabilitar otras directivas de acceso condicional.

- [Administración de la activación de directivas de contingencia de acceso condicional mediante llamadas a Microsoft Graph API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Contribuciones de la comunidad

Estos ejemplos están disponibles en nuestro [repositorio de GitHub](https://github.com/Azure-Samples/azure-ad-conditional-access-apis). Nos complace apoyar las contribuciones de la comunidad mediante el uso de los problemas de GitHub y las solicitudes de incorporación de cambios.

## <a name="next-steps"></a>Pasos siguientes

- [Overview of Microsoft Graph (Información general de Microsoft Graph)](/graph/overview)

- [API de acceso condicional](/graph/api/resources/conditionalaccesspolicy)

- [API de ubicación con nombre](/graph/api/resources/namedlocation)
