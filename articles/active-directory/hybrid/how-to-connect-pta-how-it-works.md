---
title: 'Azure AD Connect: Funcionamiento de la autenticación de paso a través | Microsoft Docs'
description: En este artículo se describe el funcionamiento de la autenticación de paso a través de Azure Active Directory.
services: active-directory
keywords: Autenticación de paso a través de Azure AD Connect, instalación de Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c159842ca1693803286a164541fa631ee0537185
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105604"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticación de paso a través de Azure Active Directory: Inmersión técnica profunda
En este artículo se ofrece información general sobre el funcionamiento de la autenticación de paso a través de Azure Active Directory (Azure AD). Si desea información técnica y de seguridad detallada, consulte el artículo [Análisis a fondo de la seguridad](how-to-connect-pta-security-deep-dive.md).

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Funcionamiento de la autenticación de paso a través de Azure Active Directory

>[!NOTE]
>Como requisito previo para el funcionamiento de la autenticación de paso a través, los usuarios deben aprovisionarse en Azure AD desde Active Directory local mediante Azure AD Connect. La autenticación de paso a través no se aplica a los usuarios que están solo en la nube.

Cuando un usuario intenta iniciar sesión en una aplicación protegida mediante Azure AD, y si la autenticación de paso a través está habilitada en el inquilino, se producen los pasos siguientes:

1. Un usuario intenta acceder a una aplicación (por ejemplo, [Outlook Web App](https://outlook.office365.com/owa/)).
2. Si el usuario todavía no ha iniciado sesión, se le redirige a la página **Inicio de sesión de usuario** de Azure AD.
3. El usuario escribe su nombre de usuario en la página de inicio de sesión de Azure AD y después selecciona el botón **Siguiente**.
4. El usuario escribe su contraseña en la página de inicio de sesión de Azure AD y después selecciona el botón **Inicio de sesión**.
5. Cuando Azure AD recibe la solicitud de inicio de sesión, pone el nombre de usuario y la contraseña (cifrada mediante la clave pública de los agentes de autenticación) en una cola.
6. Un agente de autenticación local recupera el nombre de usuario y la contraseña cifrada de la cola. Tenga en cuenta que el agente no sondea con frecuencia las solicitudes de la cola, sino que recupera las solicitudes a través de una conexión persistente establecida previamente.
7. El agente descifra la contraseña con su clave privada.
8. El agente valida el nombre de usuario y la contraseña en Active Directory mediante las API de Windows, que es un mecanismo similar al que se usa en Servicios de federación de Active Directory (AD FS). El nombre de usuario puede ser el nombre de usuario predeterminado local (normalmente, `userPrincipalName`) u otro atributo (conocido como `Alternate ID`) configurado en Azure AD Connect.
9. El controlador de dominio (DC) de Active Directory local evalúa la solicitud y devuelve la respuesta adecuada (correcto, error, contraseña expirada o bloqueo de usuario) al agente.
10. El agente de autenticación, a su vez, devuelve esta respuesta a Azure AD.
11. Azure AD evalúa la respuesta y responde al usuario según corresponda. Por ejemplo, Azure AD inicia la sesión del usuario inmediatamente o envía una solicitud a Azure AD Multi-Factor Authentication.
12. Si el inicio de sesión del usuario se realiza correctamente, el usuario puede acceder a la aplicación.

En el diagrama siguiente se ilustran todos los componentes y los pasos implicados:

![Autenticación de paso a través](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Pasos siguientes
- [Limitaciones actuales](how-to-connect-pta-current-limitations.md): Obtenga información sobre los escenarios que se admiten y los que no.
- [Inicio rápido](how-to-connect-pta-quick-start.md): ponga en marcha la autenticación de paso a través de Azure AD.
- [Migración de AD FS a la autenticación de paso a través](https://aka.ms/adfstoPTADP): una guía detallada para migrar desde AD FS (u cualquier otra tecnología de federación) a la autenticación de paso a través.
- [Bloqueo inteligente](../authentication/howto-password-smart-lockout.md): configure la funcionalidad de bloqueo inteligente en el inquilino para proteger las cuentas de usuario.
- [Preguntas más frecuentes](how-to-connect-pta-faq.yml): Obtenga respuestas a las preguntas más frecuentes.
- [Solución de problemas](tshoot-connect-pass-through-authentication.md): Obtenga información sobre cómo resolver problemas comunes relacionados con la característica de autenticación de paso a través.
- [Análisis a fondo de la seguridad](how-to-connect-pta-security-deep-dive.md): Obtenga información técnica detallada sobre la característica de autenticación de paso a través.
- [SSO de conexión directa de Azure AD](how-to-connect-sso.md): Más información sobre esta característica complementaria.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Use el foro de Azure Active Directory para solicitar nuevas características.

