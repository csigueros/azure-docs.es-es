---
title: ¿Qué es la identidad de dispositivos de Azure Active Directory?
description: Identidades de dispositivo y sus casos de uso
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo, ravenn, spunukol, jogro, jploegert
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ccb47e7600b50223ea0247c678a73ced4c86be3
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/06/2021
ms.locfileid: "113302990"
---
# <a name="what-is-a-device-identity"></a>¿Qué es una identidad de dispositivo?

Una [identidad de dispositivo](/graph/api/resources/device?view=graph-rest-1.0) es un objeto en Azure Active Directory (Azure AD). Este objeto de dispositivo es similar a usuarios, grupos o aplicaciones. Una identidad de dispositivo proporciona a los administradores información que pueden usar al tomar decisiones de acceso o configuración.

![Dispositivos que se muestran en la hoja de dispositivos de Azure AD](./media/overview/azure-active-directory-devices-all-devices.png)

Hay tres maneras de obtener una identidad de dispositivo:

- Registro de Azure AD
- Unión a Azure AD
- Unión a Azure AD híbrido

Las identidades de dispositivo son un requisito previo para escenarios como las [directivas de acceso condicional basado en dispositivos](../conditional-access/require-managed-devices.md) y la [Administración de dispositivos móviles con Microsoft Endpoint Manager](/mem/endpoint-manager-overview).

## <a name="modern-device-scenario"></a>Escenario de dispositivo moderno

El escenario de dispositivo moderno se centra en dos de estos métodos: 

- [Registro de Azure AD](concept-azure-ad-register.md) 
   - Bring your own device (BYOD)
   - Dispositivo móvil (teléfono móvil y tableta)
- [Unión a Azure AD](concept-azure-ad-register.md)
   - Dispositivos Windows 10 propiedad de la organización
   - [Windows Server 2019 y servidores más recientes de la organización que se ejecutan como máquinas virtuales en Azure](howto-vm-sign-in-azure-ad-windows.md)

[Unión a Azure AD híbrido](concept-azure-ad-join-hybrid.md) se ve como un paso provisional en el recorrido hacia la unión a Azure AD. Unión a Azure AD híbrido proporciona a las organizaciones compatibilidad con versiones de Windows de nivel inferior a Windows 7 y Server 2008. Los tres escenarios pueden coexistir en una sola organización.

## <a name="resource-access"></a>Acceso a los recursos

El registro y la unión de dispositivos a Azure AD proporciona a los usuarios un inicio de sesión único de conexión directa (SSO) a los recursos en la nube.

Los dispositivos unidos a Azure AD se benefician del [inicio de sesión único a los recursos locales de la organización](azuread-join-sso.md).

## <a name="provisioning"></a>Aprovisionamiento

Los dispositivos se pueden agregar a Azure AD como autoservicio o mediante un proceso controlado por los administradores.

## <a name="license-requirements"></a>Requisitos de licencia

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre los [dispositivos registrados en Azure AD](concept-azure-ad-register.md).
- Obtenga más información sobre los [dispositivos unidos a Azure AD](concept-azure-ad-join.md).
- Obtenga más información sobre los [dispositivos híbridos unidos a Azure AD](concept-azure-ad-join-hybrid.md).
- Para obtener información general sobre cómo administrar identidades de dispositivos en Azure Portal, consulte [Administración de identidades de dispositivos mediante Azure Portal](device-management-azure-portal.md).
- Para obtener más información sobre el acceso condicional basado en dispositivo, consulte [Configuración de directivas de acceso condicional basadas en dispositivo de Azure Active Directory](../conditional-access/require-managed-devices.md).
