---
title: 'Azure AD Connect: Funcionamiento del inicio de sesión único de conexión directa | Microsoft Docs'
description: En este artículo se describe el funcionamiento de la característica Inicio de sesión único de conexión directa de Azure Active Directory.
services: active-directory
keywords: qué es Azure AD Connect, instalar Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 883bef7f38765d45bab0f505e4ce0184dfa59356
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113110464"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Inicio de sesión único de conexión directa de Azure Active Directory: Inmersión técnica profunda

En este artículo se proporcionan los detalles técnicos sobre cómo funciona la característica Inicio de sesión único de conexión directa (SSO de conexión directa) de Azure Active Directory.

## <a name="how-does-seamless-sso-work"></a>¿Cómo funciona SSO de conexión directa?

Esta sección tiene tres partes:

1. La configuración de la característica SSO de conexión directa.
2. El funcionamiento de una transacción de inicio de sesión de usuario único en un explorador web con SSO de conexión directa.
3. El funcionamiento de una transacción de inicio de sesión de usuario único en un cliente nativo con SSO de conexión directa.

### <a name="how-does-set-up-work"></a>¿Cómo funciona la configuración?

SSO de conexión directa se habilita a través de Azure AD Connect tal como se muestra [aquí](how-to-connect-sso-quick-start.md). Cuando se habilita la característica, se producen los pasos siguientes:

- Se crea una cuenta de equipo (`AZUREADSSOACC`) en la instancia local de Active Directory (AD) en cada bosque de AD que sincroniza en Azure AD (mediante Azure AD Connect).
- Además, se crean varios nombres de entidad de seguridad de servicio (SPN) de Kerberos que se van a usar durante el proceso de inicio de sesión de Azure AD.
- La clave de descifrado de Kerberos de la cuenta de equipo se comparte de manera segura con Azure AD. Si hay varios bosques de AD, cada cuenta de equipo tendrá su propia clave de descifrado de Kerberos única.

>[!IMPORTANT]
> La cuenta de equipo `AZUREADSSOACC` precisa de una gran protección por motivos de seguridad. Solo los administradores de dominio deben poder administrar la cuenta de equipo. Asegúrese de que la delegación de Kerberos de la cuenta de equipo está deshabilitada y de que ninguna otra cuenta de Active Directory tiene permisos de delegación en la cuenta de equipo `AZUREADSSOACC`. Almacene la cuenta de equipo en una unidad organizativa (OU) donde esté a salvo de eliminaciones accidentales y donde solo los administradores de dominio tengan acceso. La clave de descifrado de Kerberos de la cuenta de equipo también debe considerarse como confidencial. Se recomienda encarecidamente [sustituir la clave de descifrado de Kerberos](how-to-connect-sso-faq.yml) de la cuenta de equipo de `AZUREADSSOACC` al menos cada 30 días.

>[!IMPORTANT]
> El inicio de sesión único de conexión directa admite los tipos de cifrado `AES256_HMAC_SHA1`, `AES128_HMAC_SHA1` y `RC4_HMAC_MD5` para Kerberos. Se recomienda que el tipo de cifrado de la cuenta `AzureADSSOAcc$` se establezca en `AES256_HMAC_SHA1`, o uno de los tipos AES en lugar de RC4, para mayor seguridad. El tipo de cifrado se almacena en el atributo `msDS-SupportedEncryptionTypes` de la cuenta en Active Directory.  Si el tipo de cifrado de la cuenta `AzureADSSOAcc$` está establecido en `RC4_HMAC_MD5` y quiere cambiarlo por uno de los tipos de cifrado AES, asegúrese de revertir primero la clave de descifrado de Kerberos de la cuenta `AzureADSSOAcc$`, como se explica en la pregunta correspondiente del [documento de preguntas más frecuentes](how-to-connect-sso-faq.yml); de lo contrario, no se producirá el inicio de sesión único de conexión directa.

Una vez que se completa la instalación, SSO de conexión directa funcionan del mismo modo que cualquier otro inicio de sesión que usa la autenticación integrada de Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>¿Cómo funciona el inicio de sesión en un explorador web con SSO de conexión directa?

El flujo de inicio de sesión en un explorador web es el siguiente:

1. El usuario intenta acceder a una aplicación web (por ejemplo, la aplicación web Outlook - https://outlook.office365.com/owa/) ) desde un dispositivo corporativo unido a un dominio dentro de la red corporativa.
2. Si el usuario todavía no inicia sesión, se le redirige a la página de inicio de sesión de Azure AD.
3. El usuario escribe su nombre de usuario en la página de inicio de sesión de Azure AD.

   >[!NOTE]
   >Para [ciertas aplicaciones](./how-to-connect-sso-faq.yml), se omiten los pasos 2 y 3.

4. Con JavaScript en segundo plano, Azure AD desafía al explorador, a través de una respuesta 401 No autorizado, a que proporcione un vale de Kerberos.
5. A su vez, el explorador solicita un vale desde Active Directory para la cuenta de equipo `AZUREADSSOACC` (que representa a Azure AD).
6. Active Directory busca la cuenta de equipo y devuelve al explorador un vale Kerberos cifrado con el secreto de la cuenta de equipo.
7. El explorador reenvía el vale de Kerberos que adquirió desde Active Directory a Azure AD.
8. Azure AD descifra el vale de Kerberos, que incluye la identidad del usuario que inició sesión en el dispositivo corporativo, mediante la clave compartida previamente.
9. Después de la evaluación, Azure AD devuelve un token a la aplicación o le pide al usuario que realice pruebas adicionales, como la autenticación multifactor.
10. Si el inicio de sesión del usuario se realiza correctamente, el usuario puede acceder a la aplicación.

En el diagrama siguiente se ilustran todos los componentes y los pasos implicados.

![Inicio de sesión único de conexión directa: flujo de aplicación web](./media/how-to-connect-sso-how-it-works/sso2.png)

SSO de conexión directa es una característica oportunista, lo que significa que, si se genera un error, la experiencia de inicio de sesión se revierte a su comportamiento habitual, es decir, el usuario deberá escribir su contraseña para iniciar sesión.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>¿Cómo funciona el inicio de sesión en un cliente nativo con SSO de conexión directa?

El flujo de inicio de sesión en un cliente nativo es el siguiente:

1. El usuario intenta acceder a una aplicación nativa (por ejemplo, el cliente Outlook) desde un dispositivo corporativo unido a un dominio dentro de la red corporativa.
2. Si el usuario todavía no inicia sesión, la aplicación nativa recupera el nombre del usuario de la sesión de Windows del dispositivo.
3. La aplicación envía el nombre de usuario a Azure AD y recupera el punto de conexión MEX de WS-Trust del inquilino. Este punto de conexión de WS-Trust lo utiliza exclusivamente la característica SSO de conexión directa y no es una implementación general del protocolo WS-Trust en Azure AD.
4. La aplicación entonces consulta el punto de conexión MEX de WS-Trust para ver si está disponible el punto de conexión de autenticación integrada. El punto de conexión de autenticación integrada lo usa exclusivamente la característica SSO de conexión directa.
5. Si el paso 4 se completa correctamente, se emite un desafío de Kerberos.
6. Si la aplicación puede recuperar el vale de Kerberos, lo reenvía al punto de conexión de autenticación integrada de Azure AD.
7. Azure AD descifra el vale de Kerberos y lo valida.
8. Azure AD inicia la sesión del usuario y emite un token SAML a la aplicación.
9. Luego, la aplicación envía el token SAML al punto de conexión del token de OAuth2 de Azure AD.
10. Azure AD valida el token SAML y emite a la aplicación un token de acceso y un token de actualización para el recurso especificado y un token de identificador.
11. El usuario obtiene acceso al recurso de la aplicación.

En el diagrama siguiente se ilustran todos los componentes y los pasos implicados.

![Inicio de sesión único de conexión directa: flujo de aplicación nativa](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Pasos siguientes

- [**Inicio rápido**](how-to-connect-sso-quick-start.md): desarrollo y ejecución de SSO de conexión directa de Azure AD.
- [**Preguntas más frecuentes**](how-to-connect-sso-faq.yml): obtenga respuestas a las preguntas más frecuentes.
- [**Solución de problemas**](tshoot-connect-sso.md): información para resolver problemas habituales de esta característica.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para la tramitación de solicitudes de nuevas características.
