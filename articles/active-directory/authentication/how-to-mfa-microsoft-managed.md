---
title: 'Uso de la configuración administrada de Microsoft para la directiva de métodos de autenticación : Azure Active Directory'
description: Aprenda a usar la configuración administrada de Microsoft para Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/03/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4134998bd3c7967d5ec151b900d67364373a2659
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566036"
---
# <a name="how-to-use-microsoft-managed-settings---authentication-methods-policy"></a>Uso de la configuración administrada de Microsoft: directiva de métodos de autenticación

Además de configurar la directiva de métodos de autenticación para que esté **habilitada** o **deshabilitada**, los administradores de TI pueden configurar algunas opciones para que sean **administradas por Microsoft**. Una configuración que **administra Microsoft** permite que Azure AD habilite o deshabilite esa configuración. 

## <a name="settings-that-can-be-microsoft-managed"></a>Configuraciones que puede administrar Microsoft

En la tabla siguiente se enumeran las opciones de configuración que puede administrar Microsoft, y si están habilitados o deshabilitados. 

| Configuración         | Configuración |
|-----------------|---------------|
| [Campaña de registro](how-to-nudge-authenticator-app.md)  | Disabled      |
| Coincidencia de número        | Disabled      |
| Contexto adicional  | Disabled      |

## <a name="next-steps"></a>Pasos siguientes

[Métodos de autenticación en Azure Active Directory: aplicación Microsoft Authenticator](concept-authentication-authenticator-app.md)
