---
title: ¿Qué son los dispositivos registrados en Azure AD?
description: Aprenda cómo los dispositivos registrados en Azure AD proporcionan a los usuarios la compatibilidad con escenarios de dispositivos móviles o de Bring Your Own Device (BYOD).
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 217663ae8dc1f2a0cba7bcd001ee12a20f1b6826
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618046"
---
# <a name="azure-ad-registered-devices"></a>Dispositivos registrados en Azure AD

El objetivo de los dispositivos registrados en Azure AD es proporcionar a los usuarios la compatibilidad con escenarios de dispositivos móviles o de Bring Your Own Device (BYOD). En estos escenarios, el usuario puede acceder a los recursos de la organización con un dispositivo personal.

| Registrados en Azure AD | Descripción |
| --- | --- |
| **Definición** | Dispositivos registrados en Azure AD sin necesitar una cuenta de la organización para iniciar sesión en el dispositivo |
| **Público principal** | Se aplica a todos los usuarios con estos criterios: |
|   | Bring Your Own Device |
|   | Dispositivos móviles |
| **Propiedad del dispositivo** | Usuario u organización |
| **Sistemas operativos** | Windows 10, iOS, Android y macOS |
| **Aprovisionamiento** | Windows 10: configuración |
|   | iOS/Android: aplicación Portal de empresa o Microsoft Authenticator |
|   | macOS: Portal de empresa |
| **Opciones de inicio de sesión en el dispositivo** | Credenciales locales del usuario final |
|   | Contraseña |
|   | Windows Hello |
|   | PIN |
|   | Biométrica o patrón para otros dispositivos |
| **Administración de dispositivos** | Administración de dispositivos móviles (por ejemplo, Microsoft Intune) |
|   | Administración de aplicaciones móviles |
| **Principales funcionalidades** | SSO a los recursos en la nube |
|   | Acceso condicional cuando los dispositivos están inscritos en Intune |
|   | Acceso condicional a través de una directiva de protección de aplicaciones |
|   | Permite el inicio de sesión telefónico con la aplicación Microsoft Authenticator |

![Dispositivos registrados en Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Los dispositivos registrados en Azure AD inician sesión con una cuenta local, por ejemplo, una cuenta Microsoft en un dispositivo Windows 10. Estos dispositivos tienen una cuenta de Azure AD para el acceso a los recursos de la organización. Es posible limitar el acceso a los recursos de la organización en función de esa cuenta de Azure AD y las directivas de acceso condicional aplicadas a la identidad del dispositivo.

Los administradores pueden proteger y controlar aún más estos dispositivos registrados en Azure AD con herramientas para la Administración de dispositivos móviles (MDM), como Microsoft Intune. MDM proporciona una manera de aplicar las configuraciones que requiere la organización, como el cifrado del almacenamiento, la complejidad de las contraseñas y que el software de seguridad siempre esté actualizado. 

El registro en Azure AD se puede realizar al acceder por primera vez a una aplicación de trabajo o si se usa manualmente el menú Configuración de Windows 10. 

## <a name="scenarios"></a>Escenarios

Un usuario de la organización quiere acceder a la herramienta de inscripción de ventajas desde su equipo doméstico. Su organización requiere que el acceso a esta herramienta se realice desde un dispositivo compatible con Intune. El usuario registra su equipo doméstico con Azure AD y se aplican las directivas necesarias de Intune, lo que permite que el usuario acceda a los recursos.

Otro usuario quiere acceder a su correo electrónico de la organización en su teléfono Android personal liberado. Su empresa exige un dispositivo compatible y ha creado una directiva de cumplimiento de Intune para bloquear los dispositivos liberados. El empleado no puede acceder a los recursos de la organización con este dispositivo.

## <a name="next-steps"></a>Pasos siguientes

- [Administración de identidades de dispositivos con Azure Portal](device-management-azure-portal.md)
- [Administración de dispositivos obsoletos en Azure AD](manage-stale-devices.md)
