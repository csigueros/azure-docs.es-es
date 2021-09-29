---
title: Configuración de directiva de grupo y MDM para ESR - Azure Active Directory
description: Configuración de administración para Enterprise State Roaming
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed9ad565a09c95d566876710dea94a7e6a1a7983
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619844"
---
# <a name="group-policy-and-mdm-settings"></a>Configuración de MDM y directivas de grupo

Use esta configuración de directiva de grupo y de dispositivos móviles (MDM) de solo en dispositivos de empresa, dado que estas directivas se aplican en todo el dispositivo del usuario. Aplicar una directiva MDM para deshabilitar la sincronización de configuración para un dispositivo de usuario personal ejercerá un impacto negativo en el uso de ese dispositivo. Además, otras cuentas de usuario en el dispositivo también se verán afectadas por la directiva.

Las empresas que desean administrar la movilidad para sus dispositivos personales (no administrados) pueden usar el Portal de Azure para habilitar o deshabilitar la movilidad, en lugar de usar la directiva de grupo o MDM.
Las tablas siguientes describen la configuración de la directiva disponible.

> [!NOTE]
> Este artículo se aplica al explorador basado en HTML heredado de Microsoft Edge que se publicó con Windows 10 en julio de 2015. No se aplica al nuevo explorador Microsoft Edge basado en Chromium, publicado el 15 de enero de 2020. Para más información sobre el comportamiento de sincronización del nuevo Microsoft Edge, consulte el artículo [Sincronización de Microsoft Edge](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>Configuración de MDM

La configuración de directiva MDM se aplica a Windows 10 y Windows 10 Mobile.  El soporte para Windows 10 Mobile existe solo para la itinerancia basada en cuentas de Microsoft a través de la cuenta OneDrive del usuario. Consulte la sección [Dispositivos y puntos de conexión](enterprise-state-roaming-windows-settings-reference.md) para información detallada sobre qué dispositivos se admiten para la sincronización basada en Azure AD.

| Nombre | Descripción |
| --- | --- |
| Permitir la conexión con una cuenta de Microsoft |Permite a los usuarios autenticarse con una cuenta de Microsoft en el dispositivo. |
| Permitir la sincronización de mi configuración |Permite a los usuarios usar un perfil itinerante para la configuración de Windows y los datos de la aplicación; si deshabilita esta directiva se deshabilitará la sincronización, así como las copias de seguridad en dispositivos móviles |

## <a name="group-policy-settings"></a>Configuración de directiva de grupo

La configuración de directiva de grupo se aplica a dispositivos de Windows 10 que están unidos a un dominio de Active Directory. La tabla incluye también la configuración heredada que aparecerá para administrar la configuración de sincronización, pero que no funciona para Enterprise State Roaming para Windows 10. Esto se indica con "No utilizar" en la descripción.

Esta configuración se encuentran en: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Nombre | Descripción |
| --- | --- |
| Cuentas: Bloquear cuentas Microsoft |Esta configuración de directiva impide a los usuarios agregar nuevas cuentas de Microsoft en este equipo. |
| No sincronizar |Impide a los usuarios usar un perfil itinerante para los datos de la aplicación y la configuración de Windows. |
| No sincronizar personalización |Deshabilita la sincronización del grupo Temas. |
| No sincronizar la configuración del explorador |Deshabilita la sincronización del grupo Internet Explorer. |
| No sincronizar contraseñas |Deshabilita la sincronización del grupo Contraseñas. |
| No sincronizar otra configuración de Windows |Deshabilita la sincronización del grupo Otra configuración de Windows. |
| No sincronizar la personalización del escritorio |No se usa; no tiene efecto. |
| No sincronizar con conexiones de uso medido |Deshabilita la movilidad en conexiones limitadas, como 3G móvil. |
| No sincronizar aplicaciones |No se usa; no tiene efecto. |
| No sincronizar la configuración de aplicaciones |Deshabilita la movilidad de datos de la aplicación. |
| No sincronizar la configuración de inicio |No se usa; no tiene efecto. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general, consulte [Introducción a Enterprise State Roaming](enterprise-state-roaming-overview.md).
