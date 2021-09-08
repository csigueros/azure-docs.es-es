---
title: ¿Qué es un dispositivo híbrido unido a Azure AD?
description: Conozca cómo la administración de identidades de dispositivos puede ayudarle a administrar los dispositivos que acceden a los recursos de su entorno.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 083dcc0dc5d6ffdf25f6d6f631a8e6f5788ce8b1
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303047"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos unidos a Azure AD

Las organizaciones con implementaciones de Active Directory existentes pueden beneficiarse de algunas de las funcionalidades proporcionadas por Azure Active Directory (Azure AD) mediante la implementación de dispositivos unidos a Azure AD híbridos. Estos dispositivos se han unido al entorno local de Active Directory y se han registrado en Azure Active Directory.

Los dispositivos Azure AD híbridos requieren una línea de visión de red a los controladores de dominio locales periódicamente. Sin esta conexión, los dispositivos se vuelven inutilizables. Si este requisito es un problema, considere la posibilidad de [conectar Azure AD](concept-azure-ad-join.md) a sus dispositivos.

| Unión a Azure AD híbrido | Descripción |
| --- | --- |
| **Definición** | Dispositivos unidos a AD local y a Azure AD que requieren una cuenta de la organización para iniciar sesión en el dispositivo |
| **Público principal** | Adecuados para organizaciones híbridas con infraestructura de AD local existente |
|   | Se aplica a todos los usuarios de una organización. |
| **Propiedad del dispositivo** | Organización |
| **Sistemas operativos** | Windows 10, 8.1 y 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 y 2019 |
| **Aprovisionamiento** | Windows 10, Windows Server 2016/2019 |
|   | Unión a un dominio por TI y la unión automática a través de Azure AD Connect o de la configuración de ADFS |
|   | Unión a un dominio por Windows Autopilot y la unión automática a través de Azure AD Connect o de la configuración de ADFS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 y Windows Server 2008 R2. Requieren MSI |
| **Opciones de inicio de sesión en el dispositivo** | Cuentas organizativas que usan: |
|   | Contraseña |
|   | Windows Hello para empresas para Win10 |
| **Administración de dispositivos** | Directiva de grupo |
|   | Administración independiente o conjunta de Configuration Manager con Microsoft Intune |
| **Principales funcionalidades** | SSO tanto a los recursos en la nube como a los recursos locales |
|   | Acceso condicional a través de la unión a un dominio o mediante Intune, si se trata de una coadministración |
|   | Autoservicio de restablecimiento de contraseña y restablecimiento de PIN de Windows Hello |
|   | Enterprise State Roaming entre dispositivos |

![Dispositivos híbridos unidos a Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Escenarios

Use dispositivos híbridos unidos a Azure AD si:

- Admite dispositivo de menor nivel que ejecuten Windows 7 y 8.1.
- Quiere seguir usando la directiva de grupo para administrar la configuración del dispositivo.
- Quiere seguir usando las soluciones existentes de creación de imágenes para implementar y configurar dispositivos.
- Tiene aplicaciones Win32 implementadas en estos dispositivos que se basan en la autenticación de máquina de Active Directory.

## <a name="next-steps"></a>Pasos siguientes

- [Planeación de la implementación de la unión a Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Administración de identidades de dispositivos con Azure Portal](device-management-azure-portal.md)
- [Administración de dispositivos obsoletos en Azure AD](manage-stale-devices.md)
