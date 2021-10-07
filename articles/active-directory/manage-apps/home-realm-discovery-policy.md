---
title: Directiva de detección del dominio de inicio
titleSuffix: Azure AD
description: Obtenga más información sobre cómo controlar la aceleración automática de una aplicación mediante la directiva de detección del dominio de inicio.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.openlocfilehash: ce71512e46f1b78ece832ec1fb1bad938edfcbf8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058849"
---
# <a name="home-realm-discovery-for-an-application-in-azure-active-directory"></a>Detección del dominio de inicio de una aplicación en Azure Active Directory

La detección del dominio de inicio (HDR) es el proceso que permite a Azure AD determinar con qué proveedor de identidades ("IdP") debe autenticarse un usuario en el momento del inicio de sesión.  Al iniciar sesión en un inquilino de Azure AD para obtener acceso a un recurso o a la página de inicio de sesión común de Azure AD, el usuario debe escribir un nombre de usuario (UPN). Azure AD usa esa información para detectar dónde debe iniciar sesión el usuario.

Es posible que el usuario sea dirigido a uno de los siguientes proveedores de identidades para autenticarse:

- El inquilino principal del usuario (puede ser el mismo inquilino que el recurso al que el usuario está intentando obtener acceso).

- Una cuenta Microsoft.  El usuario es un invitado en el inquilino de recursos que usa una cuenta de consumidor para la autenticación.

- Un proveedor de identidades local, como Servicios de federación de Active Directory (AD FS).

- Otro proveedor de identidades federado con el inquilino de Azure AD.

## <a name="auto-acceleration"></a>Aceleración automática

Algunas organizaciones configuran dominios en su inquilino de Azure AD para federarlos con otro IdP, como AD FS, y así poder realizar la autenticación de usuario.

Cuando un usuario inicia sesión en una aplicación, en primer lugar se le muestra una página de inicio de sesión de Azure AD. Una vez que escribe su UPN, si está en un dominio federado, se le redirige a la página de inicio de sesión del IdP que sirve a ese dominio. En determinadas circunstancias, es posible que los administradores quieran dirigir a los usuarios a la página de inicio de sesión cuando inician sesión en aplicaciones específicas.

Como resultado, los usuarios pueden omitir la página inicial de Azure Active Directory. Este proceso se conoce como "aceleración automática de inicio de sesión".

En casos donde el inquilino está federado a otro IdP para el inicio de sesión, la aceleración automática simplifica más el inicio de sesión del usuario.  Puede configurar la aceleración automática en aplicaciones individuales.

> [!NOTE]
> Si configura una aplicación para la aceleración automática, los usuarios no podrán usar credenciales administradas (como FIDO) y los usuarios invitados no podrán iniciar sesión. Si lleva a un usuario directamente a un IdP federado para que se autentique, este no podrá volver a la página de inicio de sesión de Azure Active Directory. Los usuarios invitados, que es posible que deban dirigirse a otros inquilinos o a un IdP externo, como una cuenta de Microsoft, no pueden iniciar sesión en esa aplicación porque omiten el paso de detección del dominio de inicio.

Hay tres formas de controlar la aceleración automática en un IdP federado:

- Use una sugerencia de dominio en las solicitudes de autenticación de una aplicación.
- Configuración de una directiva de detección del dominio de inicio para [forzar la aceleración automática]() (configure-authentication-for-federated-users-portal.md)
- Configurar una directiva Detección de dominio principal para [omitir las sugerencias de dominio](prevent-domain-hints-with-home-realm-discovery.md) de aplicaciones específicas o para determinados dominios.

## <a name="domain-hints"></a>Sugerencias de dominio

Las sugerencias de dominio son directivas que se incluyen en la solicitud de autenticación de una aplicación. Se pueden usar para enviar el usuario a su página de inicio de sesión del IdP federado. O también las puede usar una aplicación de varios inquilinos para enviar al usuario directamente a la página de inicio de sesión de Azure AD del inquilino.

Por ejemplo, la aplicación "largeapp.com" puede permitir que sus clientes obtengan acceso a la aplicación mediante la dirección URL personalizada "contoso.largeapp.com". La aplicación también puede incluir una sugerencia de dominio contoso.com en la solicitud de autenticación.

La sintaxis de la sugerencia de dominio varía en función del protocolo que se usa y se suele configurar en la aplicación de las siguientes formas:

- En el caso de aplicaciones que usan **WS-Federation**: whr=contoso.com en la cadena de consulta.

- En el caso de aplicaciones que usan **SAML**: una solicitud de autenticación de SAML que contiene una sugerencia de dominio o una cadena de consulta whr=contoso.com.

- En el caso de aplicaciones que usan **Open ID Connect**: una cadena de consulta domain_hint=contoso.com.

De forma predeterminada, Azure AD intenta redirigir el inicio de sesión al IdP que está configurado para un dominio si se cumplen las **dos** condiciones siguientes:

- Se incluye una sugerencia de dominio en la solicitud de autenticación de la aplicación **y**
- El inquilino está federado con ese dominio.

Si la sugerencia de dominio no hace referencia a un dominio federado verificado, se omite.

> [!NOTE]
> Si se incluye una sugerencia de dominio en una solicitud de autenticación y [debe respetarse](#home-realm-discovery-policy-to-prevent-auto-acceleration), su presencia invalida la aceleración automática que se establezca para la aplicación en la directiva HRD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Directiva de detección del dominio de inicio para la aceleración automática

Algunas aplicaciones no proporcionan ninguna manera de configurar la solicitud de autenticación que emiten. En estos casos, no es posible utilizar sugerencias de dominio para controlar la aceleración automática. La aceleración automática se puede [configurar mediante la directiva de detección del dominio de inicio](configure-authentication-for-federated-users-portal.md) y así lograr el mismo comportamiento.

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Directiva Detección de dominio principal para impedir la aceleración automática

Algunas aplicaciones de Microsoft y SaaS incluyen domain_hints de forma automática (por ejemplo, `https://outlook.com/contoso.com` genera una solicitud de inicio de sesión con `&domain_hint=contoso.com` anexado), lo que puede interrumpir la implementación de credenciales administradas, como Fido.  Puede usar la [directiva de detección del dominio de inicio para omitir las sugerencias de dominio](prevent-domain-hints-with-home-realm-discovery.md) que proceden de determinadas aplicaciones, o para determinados dominios, durante la implementación de credenciales administradas.

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Habilitación de la autenticación de ROPC directa de los usuarios federados para aplicaciones heredadas

El procedimiento recomendado es para que las aplicaciones usen el inicio de sesión interactivo y las bibliotecas de Azure AD para autenticar a los usuarios. Las bibliotecas se ocupan de los flujos de los usuarios federados.  A veces, las aplicaciones heredadas, especialmente las que usan concesiones de credenciales de contraseña del propietario del recurso (ROPC), envían el nombre de usuario y la contraseña directamente a Azure AD y no fueron escritas con los conceptos de la federación. No realizan el proceso de HRD y no interactúan con el punto de conexión federado correcto para autenticar a un usuario. Si lo desea, puede usar la directiva de [detección del dominio de inicio para permitir que determinadas aplicaciones heredadas](configure-authentication-for-federated-users-portal.md) que envían las credenciales de nombre de usuario y contraseña mediante la concesión de ROPC para autenticarse directamente con Azure Active Directory. La sincronización de hash de contraseñas se debe habilitar.

> [!IMPORTANT]
> Habilite solo la autenticación directa si ha activado la sincronización de hash de contraseña y sabe que es correcto autenticar esta aplicación sin las directivas implementadas por el IdP local. Si, por cualquier motivo, desactiva la sincronización de hash de contraseña o la sincronización de directorios con AD Connect, debe quitar esta directiva para evitar la posibilidad de que la autenticación directa use un hash de contraseña obsoleto.

## <a name="set-hrd-policy"></a>Establecimiento de la directiva HRD

Hay que seguir tres pasos para configurar la directiva de HRD en una aplicación para la aceleración automática del inicio de sesión federado o las aplicaciones directas basadas en la nube:

1. Cree una directiva de HRD.

2. Busque la entidad de servicio a la que se debe asociar la directiva.

3. Asocie la directiva a la entidad de servicio.

Las directivas solo surten efecto para una aplicación específica cuando se adjuntan a una entidad de servicio.

Solo puede haber una directiva de HRD activa en una entidad de servicio en un momento dado.

Puede usar los cmdlets de PowerShell de Azure Active Directory para crear y administrar la directiva de HRD.

Este es un ejemplo de la definición de la directiva de HRD:

```json
{  
  "HomeRealmDiscoveryPolicy":
  {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
  }
}
```

El tipo de directiva es "[HomeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy)".

El valor **AccelerateToFederatedDomain** es opcional. Si **AccelerateToFederatedDomain** es false, la directiva no tiene ningún efecto en la aceleración automática. Si **AccelerateToFederatedDomain** es true y solo hay un dominio federado y verificado en el inquilino, los usuarios se dirigirán directamente al IdP federado para el inicio de sesión. Si es true y hay más de un dominio verificado en el inquilino, debe especificarse **PreferredDomain**.

El valor **PreferredDomain** es opcional. **PreferredDomain** debe indicar un dominio al que se debe acelerar. Puede omitirse si el inquilino tiene solo un dominio federado.  Si se omite y se verifica que hay más de un dominio federado, entonces la directiva no tendrá ningún efecto.

 Si se especifica **PreferredDomain**, debe coincidir con un dominio federado verificado para el inquilino. Todos los usuarios de la aplicación deben poder iniciar sesión en ese dominio; los usuarios que no puedan iniciar sesión en el dominio federado se interceptarán y no podrán completar el inicio de sesión.

El valor **AllowCloudPasswordValidation** es opcional. Si **AllowCloudPasswordValidation** es true, la aplicación tendrá autorización para autenticar a un usuario federado si presenta las credenciales de nombre de usuario y contraseña directamente al punto de conexión del token de Azure Active Directory. Esto solo funciona si se habilita la sincronización de hash de contraseña.

Además, existen dos opciones de HRD de nivel de inquilino, que no se muestran anteriormente:

- **AlternateIdLogin** es opcional.  Si está habilitada, [permite a los usuarios iniciar sesión con sus direcciones de correo electrónico en lugar de su UPN](../authentication/howto-authentication-use-email-signin.md) en la página de inicio de sesión de Azure AD.  Los identificadores alternativos confían en que no se produzca la aceleración automática del usuario a un IdP federado.

- **DomainHintPolicy** es un objeto complejo opcional que [*impide* que las sugerencias de dominio aceleren automáticamente a los usuarios a dominios federados](prevent-domain-hints-with-home-realm-discovery.md). Esta configuración del inquilino se usa para que las aplicaciones que envían sugerencias de dominio no impidan que los usuarios inicien sesión con credenciales administradas por la nube.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioridad y evaluación de las directivas de HRD

Se pueden crear directivas de HRD y asignarlas a organizaciones y entidades de servicio específicas. Esto significa que se pueden aplicar varias directivas a una aplicación específica, por lo que Azure AD debe decidir cuál tiene prioridad. Un conjunto de reglas decide qué directiva HRD (de las muchas aplicadas) se aplica:

- Si una sugerencia de dominio está presente en la solicitud de autenticación, se comprueba la directiva HRD del inquilino (la directiva establecida como valor predeterminado del inquilino) para ver si se deben omitir las sugerencias de dominio. Si se permiten sugerencias de dominio, se utiliza el comportamiento especificado por la sugerencia de dominio.

- Por otro lado, si una directiva se asigna explícitamente a la entidad de servicio, esta se aplicará.

- Si no hay sugerencias de dominio y ninguna directiva se asigna explícitamente a la entidad de servicio, se aplicará una directiva asignada explícitamente a la organización principal de la entidad de servicio.

- Si no hay ninguna sugerencia de dominio y no se asignó ninguna directiva a la entidad de servicio o a la organización, se usa el comportamiento de HRD predeterminado.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración del comportamiento de inicio de sesión de una aplicación mediante una directiva de detección del dominio de inicio](configure-authentication-for-federated-users-portal.md)
- [Deshabilitación de la aceleración automática en un IDP federado durante el inicio de sesión de usuario con la directiva de detección del dominio de inicio](prevent-domain-hints-with-home-realm-discovery.md)
- Para obtener más información sobre el funcionamiento de la autenticación en Azure AD, consulte [Escenarios de autenticación para Azure AD](../develop/authentication-vs-authorization.md).
