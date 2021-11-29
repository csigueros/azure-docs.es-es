---
title: 'Uso de la configuración administrada de Microsoft para la directiva de métodos de autenticación : Azure Active Directory'
description: Aprenda a usar la configuración administrada de Microsoft para Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d63cd4795bfa94e827125c630ae7bb8c49e072ff
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707841"
---
# <a name="how-to-use-microsoft-managed-settings---authentication-methods-policy"></a>Uso de la configuración administrada de Microsoft: directiva de métodos de autenticación

<!---what API--->

Además de configurar la directiva de métodos de autenticación para que esté **habilitada** o **deshabilitada**, los administradores de TI pueden configurar algunas opciones para que sean **administradas por Microsoft**. Una configuración que **administra Microsoft** permite que Azure AD habilite o deshabilite esa configuración. 

La opción de permitir que Azure AD administre la configuración es una manera cómoda para que una organización permita que Microsoft habilite o deshabilite una característica de forma predeterminada. Las organizaciones pueden mejorar fácilmente su posición de seguridad si confían en Microsoft para administrar cuándo se debe habilitar una característica de forma predeterminada. Al configurar una configuración como **Administrada por Microsoft** (denominada *predeterminada* en Graph API), los administradores de TI pueden confiar en Microsoft para habilitar una característica de seguridad que no han deshabilitado explícitamente. 

## <a name="settings-that-can-be-microsoft-managed"></a>Configuraciones que puede administrar Microsoft

En la tabla siguiente se enumeran las opciones de configuración que puede administrar Microsoft, y si están habilitados o deshabilitados. 

| Configuración                                                                                                                         | Configuración |
|---------------------------------------------------------------------------------------------------------------------------------|---------------|
| [Campaña de registro](how-to-mfa-registration-campaign.md)                                                      | Disabled      |
| [Coincidencia de número](how-to-mfa-number-match.md)                                             | Disabled      |
| [Contexto adicional en notificaciones de Microsoft Authenticator](how-to-mfa-additional-context.md) | Disabled      |

## <a name="next-steps"></a>Pasos siguientes

[Métodos de autenticación en Azure Active Directory: aplicación Microsoft Authenticator](concept-authentication-authenticator-app.md)
