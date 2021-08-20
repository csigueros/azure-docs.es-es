---
title: Diagnóstico de inicio de sesión para escenarios de Azure AD
description: Enumera los escenarios admitidos por los diagnósticos de inicio de sesión de Azure AD.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/08/2021
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: c327de515699ed0d0d8b74e0c393d206864fef85
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113553132"
---
# <a name="sign-in-diagnostics-for-azure-ad-scenarios"></a>Diagnóstico de inicio de sesión para escenarios de Azure AD

Puede usar el diagnóstico de inicio de sesión de Azure AD para analizar lo que ha sucedido durante un intento de inicio de sesión y obtener recomendaciones para solucionar problemas sin necesidad de que intervenga el servicio de soporte técnico de Microsoft.  

En este artículo se proporciona información general sobre los tipos de escenarios que puede identificar y resolver al usar esta herramienta.

## <a name="supported-scenarios"></a>Escenarios admitidos

El diagnóstico de inicio de sesión Azure AD proporciona compatibilidad con los escenarios siguientes:

- **Acceso condicional**

    - Bloqueado por acceso condicional  

    - Acceso condicional con error  

    - Autenticación multifactor (MFA) del acceso condicional  

    - Inicio de sesión B2B bloqueado por acceso condicional 

- **Autenticación multifactor (MFA)**  

    - Autenticación multifactor de otros requisitos  

    - Se requiere una prueba de autenticación multifactor  

    - Prueba de MFA requerida (ubicación de inicio de sesión de riesgo)  

- **Credenciales correctas e incorrectas**  

    - Inicio de sesión correcto  

    - Cuenta bloqueada  

    - Nombre de usuario o contraseña no válidos  

- **Aplicaciones empresariales**  

    - Proveedor de servicios de aplicaciones empresariales  

    - Configuración de aplicaciones empresariales  

- **Otros escenarios**   

    - Valores predeterminados de seguridad  
    
    - Información de código de error  

    - Autenticación heredada  

    - Bloque por directiva de riesgo 







## <a name="conditional-access"></a>Acceso condicional  


### <a name="blocked-by-conditional-access"></a>Bloqueado por acceso condicional 

En este escenario, una directiva de acceso condicional ha bloqueado un intento de inicio de sesión. 


![Captura de pantalla que muestra la configuración de acceso con Bloquear acceso seleccionado.](./media/concept-sign-in-diagnostics-scenarios/block-access.png)

La sección de diagnóstico de este escenario muestra detalles sobre el evento de inicio de sesión del usuario y las directivas aplicadas. 

 

### <a name="failed-conditional-access"></a>Acceso condicional con error 

Este escenario suele ser el resultado de un error de intento de inicio de sesión debido a que no se cumplieron los requisitos de una directiva de acceso condicional. Los ejemplos comunes son: 



![Captura de pantalla que muestra la configuración de acceso con ejemplos de directivas comunes y Conceder acceso seleccionado.](./media/concept-sign-in-diagnostics-scenarios/require-controls.png)

- Requerir un dispositivo unido a Azure AD híbrido 

- Requerir aplicación cliente aprobada 

- Requerir la directiva de protección de aplicaciones 

En la sección de diagnóstico de este escenario se muestran detalles sobre el intento de inicio de sesión del usuario y las directivas aplicadas. 

 

### <a name="mfa-from-conditional-access"></a>Autenticación multifactor de acceso condicional 

En este escenario, una directiva de acceso condicional tiene el requisito de iniciar sesión con el conjunto de autenticación multifactor. 



![Captura de pantalla que muestra la configuración de acceso con Require multifactor authentication (Exigir la autenticación multifactor) seleccionada.](./media/concept-sign-in-diagnostics-scenarios/require-mfa.png)

En la sección de diagnóstico de este escenario se muestran detalles sobre el intento de inicio de sesión del usuario y las directivas aplicadas. 

 

 

## <a name="multifactor-authentication"></a>Autenticación multifactor  

### <a name="mfa-from-other-requirements"></a>Autenticación multifactor de otros requisitos 

En este escenario, una directiva de acceso condicional no aplicó un requisito de autenticación multifactor. Por ejemplo, la autenticación multifactor a cada usuario. 



![Captura de pantalla que muestra la configuración de autenticación multifactor por usuario.](./media/concept-sign-in-diagnostics-scenarios/mfa-per-user.png) 

La intención de este escenario de diagnóstico es proporcionar más detalles sobre: 

- El origen de la interrupción de la autenticación multifactor. 

- El resultado de la interacción del cliente. 

También puede ver todos los detalles del intento de inicio de sesión del usuario. 

 

### <a name="mfa-proof-up-required"></a>Se requiere una prueba de autenticación multifactor 

En este escenario, las solicitudes para configurar la autenticación multifactor interrumpieron los intentos de inicio de sesión. Esta configuración se conoce también como prueba. 

 

La prueba de autenticación multifactor se produce cuando se exige a un usuario que utilice la autenticación multifactor, pero aún no se ha configurado, o un administrador ha solicitado al usuario que la configure. 

 

La intención de este escenario de diagnóstico es revelar que la interrupción de la autenticación multifactor se debe a la falta de configuración del usuario. La solución recomendada es que el usuario realice la prueba. 

 

### <a name="mfa-proof-up-required-risky-sign-in-location"></a>Prueba de MFA requerida (ubicación de inicio de sesión de riesgo) 

En este escenario, los intentos de inicio de sesión se interrumpieron mediante una solicitud para configurar la autenticación multifactor a partir de una ubicación de inicio de sesión de riesgo. 

 

La intención de este escenario de diagnóstico es revelar que la interrupción de la autenticación multifactor se debe a la falta de configuración del usuario. La solución recomendada es que el usuario realice la prueba, en concreto desde una ubicación de red que no parezca arriesgada. 

 

Un ejemplo de este escenario es cuando la directiva requiere que el usuario configure MFA solo desde ubicaciones de red de confianza, pero el usuario inicia sesión desde una ubicación de red que no es de confianza. 

 

## <a name="correct--incorrect-credential"></a>Credenciales correctas e incorrectas

### <a name="successful-sign-in"></a>Inicio de sesión correcto 

En este escenario, los eventos de inicio de sesión no se interrumpieron con el acceso condicional o la autenticación multifactor.  

 

En este escenario de diagnóstico se proporcionan detalles sobre los eventos de inicio de sesión de usuario que se prevé que se interrumpan debido a las directivas de acceso condicional o a la autenticación multifactor. 

 

### <a name="the-account-is-locked"></a>La cuenta está bloqueada. 

En este escenario, un usuario ha iniciado sesión con credenciales incorrectas demasiadas veces. Este escenario se produce cuando se han producido demasiados intentos de inicio de sesión basados en contraseña con credenciales incorrectas. El escenario de diagnóstico proporciona información para que el administrador determine de dónde vienen los intentos y si son intentos legítimos de inicio de sesión de usuario o no. 

 

En este escenario de diagnóstico se proporcionan detalles sobre las aplicaciones, el número de intentos, el dispositivo usado, el sistema operativo y la dirección IP. 

 

Puede encontrar más información sobre este tema en la documentación de Azure AD Smart Lockout. 

 

 

### <a name="invalid-username-or-password"></a>Nombre de usuario o contraseña no válidos 

En este escenario, un usuario ha intentado iniciar sesión con un nombre de usuario o contraseña no válidos. El diagnóstico está pensado para permitir que un administrador determine si el problema es un usuario que introduce credenciales incorrectas o un cliente/aplicación que han almacenado en caché una contraseña antigua y la están reenviando. 

 

En este escenario de diagnóstico se proporcionan detalles sobre las aplicaciones, el número de intentos, el dispositivo usado, el sistema operativo y la dirección IP. 

 

## <a name="enterprise-app"></a>Aplicación empresarial 

En las aplicaciones empresariales, hay dos puntos en los que pueden producirse problemas: 

- La configuración de la aplicación del proveedor de identidades (Azure AD) 
- El proveedor de servicios (servicio de aplicación, también conocido como aplicación SaaS)

 

Los diagnósticos de estos problemas abordan qué parte del problema se debe buscar para la resolución y qué se debe hacer. 

 

### <a name="enterprise-apps-service-provider"></a>Proveedor de servicios de aplicaciones empresariales 

En este escenario, un usuario ha intentado iniciar sesión en una aplicación. El inicio de sesión falla debido a un problema con la aplicación (también conocida como proveedor de servicios) del flujo de inicio de sesión. Los problemas detectados por este diagnóstico normalmente deben resolverse cambiando la configuración o corrigiendo problemas en el servicio de la aplicación.  

La resolución de este escenario implica iniciar sesión en el otro servicio y cambiar alguna configuración según las instrucciones de diagnóstico. 

 

### <a name="enterprise-apps-configuration"></a>Configuración de aplicaciones empresariales 

En este escenario, el inicio de sesión falla debido a un problema de configuración de Azure AD en la aplicación. 

 

Para resolver este escenario, es necesario revisar y actualizar la configuración de la aplicación en la entrada de la hoja Aplicaciones empresariales de la aplicación. 

 

## <a name="other-scenarios"></a>Otros escenarios 

### <a name="security-defaults"></a>Valores predeterminados de seguridad 

En este escenario se tratan los eventos de inicio de sesión en los que se interrumpió el inicio de sesión del usuario debido a la configuración de los valores predeterminados de seguridad. Los valores predeterminados de seguridad aplican la seguridad de los procedimientos recomendados para la organización y requieren que la autenticación multifactor (MFA) se configure y se utilice en muchos escenarios para evitar que se produzcan reproducciones de contraseñas, ataques de reproducción e intentos de suplantación de identidad. 

Para obtener más información, vea [¿Cuáles son los valores de seguridad predeterminados?](../fundamentals/concept-fundamentals-security-defaults.md) 

### <a name="error-code-insights"></a>Información de código de error 

Cuando un evento no tiene un análisis contextual en el diagnóstico de inicio de sesión, se puede mostrar una explicación actualizada del código de error y el contenido pertinente. La información del código de error contiene texto detallado sobre el escenario, cómo corregir el problema y cualquier contenido que deba leerse con respecto al problema. 

### <a name="legacy-authentication"></a>Autenticación heredada 

Este escenario de diagnóstico diagnostica un evento de inicio de sesión que se bloqueó o interrumpió porque el cliente estaba intentando usar la autenticación básica (también conocida como heredada). 

Se recomienda evitar el inicio de sesión de autenticación heredada como procedimiento recomendado para la seguridad. Los protocolos de autenticación heredados como POP, SMTP, IMAP y MAPI no pueden aplicar la autenticación multifactor (MFA), lo que hace que sean los puntos de entrada preferidos para que los adversarios ataquen su organización. 

Para más información, consulte [Bloqueo de autenticación heredada a Azure AD con acceso condicional](../conditional-access/block-legacy-authentication.md). 

### <a name="b2b-blocked-sign-in-due-to-conditional-access"></a>Inicio de sesión B2B bloqueado por acceso condicional 

Este escenario de diagnóstico detecta un inicio de sesión bloqueado o interrumpido debido a que el usuario es de otra organización (un inicio de sesión B2B donde una directiva de acceso condicional requiere que el dispositivo del cliente esté unido al inquilino del recurso). 

Para más información, consulte [Acceso condicional para usuarios de colaboración B2B](../external-identities/conditional-access.md). 

### <a name="blocked-by-risk-policy"></a>Bloque por directiva de riesgo 

En este escenario, la directiva de protección de identidad bloquea un intento de inicio de sesión debido a que este se identifica como evento de riesgo. 

Para obtener más información, consulte [Procedimiento de configuración y habilitación de directivas de riesgo](../identity-protection/howto-identity-protection-configure-risk-policies.md). 




## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es el diagnóstico de inicio de sesión en Azure AD?](overview-sign-in-diagnostics.md)
