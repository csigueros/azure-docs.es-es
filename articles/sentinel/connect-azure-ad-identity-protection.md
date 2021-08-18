---
title: Conexión de datos de Azure AD Identity Protection a Azure Sentinel.
description: Aprenda a transmitir registros y alertas de Azure AD Identity Protection a Azure Sentinel para ver paneles, crear alertas personalizadas y mejorar la investigación.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: 2f7d947afcbd3017df1c2183da6c88ec92656bc5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780375"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Conexión de datos desde Azure Active Directory (Azure AD) Identity Protection

Puede transmitir registros de [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) a Azure Sentinel para transmitir alertas a Azure Sentinel que permitan ver paneles, crear alertas personalizadas y mejorar la investigación. Azure Active Directory Identity Protection proporciona una vista consolidada de los usuarios en riesgo, las detecciones de riesgo y las vulnerabilidades, con la posibilidad de corregir el riesgo de inmediato y de establecer directivas para resolver eventos futuros de forma automática. Este servicio se basa en la experiencia de Microsoft a la hora de proteger las identidades de los consumidores, y logra una precisión increíble a partir de más 13 mil millones de inicios de sesión día.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Requisitos previos

- Debe tener una suscripción de [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).
- Debe tener un usuario con permisos de administrador global o de administrador de seguridad.

## <a name="connect-to-azure-ad-identity-protection"></a>Conexión a Azure AD Identity Protection

Si tiene una suscripción a Azure AD Premium P2, se incluye Azure AD Identity Protection. Si se [habilitan las directivas](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) y la generación de alertas, los datos de alerta se pueden transmitir fácilmente a Azure Sentinel.

1. En Azure Sentinel, seleccione **Data connectors** (Conectores de datos) y, después, haga clic en el icono de **Azure AD Identity Protection**.

1. Haga clic en **Connect** (Conectar) para iniciar la transmisión de eventos de Azure AD Identity Protection a Azure Sentinel.

1. Para usar el esquema correspondiente en Log Analytics para las alertas de Azure AD Identity Protection, busque **SecurityAlert**.

Si desea probar el conector, puede [simular detecciones](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) para generar alertas de ejemplo que se transmitirán a Azure Sentinel.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Azure AD Identity Protection a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
