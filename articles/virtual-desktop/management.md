---
title: Microsoft Endpoint Configuration Manager para Azure Virtual Desktop
description: Formas recomendadas de administrar el entorno de Azure Virtual Desktop.
author: heidilohr
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1c44b679daa000602aad83d98a04894c5cc267b3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359103"
---
# <a name="microsoft-endpoint-manager-and-intune-for-azure-virtual-desktop"></a>Microsoft Endpoint Manager e Intune para Azure Virtual Desktop

Se recomienda usar [Microsoft Endpoint Manager](https://www.microsoft.com/endpointmanager) para administrar el entorno de Azure Virtual Desktop después de la implementación. Microsoft Endpoint Manager es una plataforma de administración unificada que incluye Microsoft Endpoint Configuration Manager y Microsoft Intune.

## <a name="microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager

Las versiones 1906 y posteriores de Microsoft Endpoint Configuration Manager permiten administrar los dispositivos de Azure Virtual Desktop. Para obtener más información, vea [Versiones de SO admitidas para clientes y dispositivos de Configuration Manager](/mem/configmgr/core/plan-design/configs/supported-operating-systems-for-clients-and-devices#windows-virtual-desktop).

## <a name="microsoft-intune"></a>Microsoft Intune

Intune admite máquinas virtuales de Windows 10 Enterprise para Azure Virtual Desktop. Para obtener más información sobre la compatibilidad, vea [Uso de Windows 10 Enterprise con Intune](/mem/intune/fundamentals/windows-virtual-desktop).

La compatibilidad de Intune con máquinas virtuales multisesión de Windows 10 Enterprise en Azure Virtual Desktop se encuentra actualmente en versión preliminar pública. Para ver lo que admite actualmente la versión preliminar pública, consulte [Uso de sesiones múltiples de Windows 10 Enterprise con Intune](/mem/intune/fundamentals/windows-virtual-desktop-multi-session).

## <a name="licensing"></a>Licencias

Con la mayoría de las suscripciones a Microsoft 365 se incluyen [licencias de Microsoft Endpoint Configuration Manager y Microsoft Intune](https://microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing). 

Obtenga más información sobre los requisitos de licencia en los siguientes recursos:

- [Preguntas más frecuentes sobre las licencias y ramas de Configuration Manager](/mem/configmgr/core/understand/product-and-licensing-faq#bkmk_equiv-sub) 
- [Licencias de Microsoft Intune](/mem/intune/fundamentals/licenses)
