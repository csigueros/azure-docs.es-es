---
title: Validación del token de acceso del servicio FHIR
description: Procedimiento de validación de tokens de acceso y guía de solución de problemas para el servicio FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: cavoeg
ms.openlocfilehash: 678151fb59941fafb521a9fc62ef2be0b94cf6de
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781199"
---
# <a name="fhir-service-access-token-validation"></a>Validación del token de acceso del servicio FHIR

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

La forma en que el servicio FHIR valida el token de acceso depende de la implementación y la configuración. En este artículo se le guía a lo largo de los pasos de validación, lo que puede ser útil para solucionar problemas de acceso.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>El token de validación no tiene problemas con el proveedor de identidades

El primer paso de la validación del token es comprobar que ha sido emitido por el proveedor de identidades correcto y que no se ha modificado. El servidor de FHIR se configura para usar un proveedor de identidades concreto conocido como autoridad `Authority`. El servidor de FHIR recupera información sobre el proveedor de identidades del punto de conexión `/.well-known/openid-configuration`. Si se usa Azure AD, la dirección URL completa sería:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

donde `<TENANT-ID>` es el inquilino concreto de Azure AD (ya sea un identificador de inquilino o un nombre de dominio).

Azure AD devuelve un documento como el siguiente al servidor de FHIR.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
Las propiedades importantes para el servidor de FHIR son `jwks_uri`, que indica al servidor dónde capturar las claves de cifrado necesarias para validar la firma del token, y `issuer`, que indica al servidor qué va a haber en la notificación del emisor (`iss`) de los tokens emitidos por este servidor. El servidor de FHIR puede usar esto para validar que está recibiendo un token auténtico.

## <a name="validate-claims-of-the-token"></a>Validación de notificaciones del token

Una vez que el servidor ha comprobado la autenticidad del token, el servidor de FHIR pasa a validar que el cliente tenga las notificaciones necesarias para acceder al token.

Al usar el servicio FHIR, el servidor comprobará que:

1. El token tenga el derecho `Audience` (notificación `aud`).
1. El usuario o la entidad de seguridad para los que se ha emitido el token tenga acceso al plano de datos del servidor de FHIR. La notificación `oid` del token contiene un identificador de objeto de identidad que identifica de forma única al usuario o a la entidad de seguridad.

Se recomienda que el servicio FHIR se configure para usar RBAC de Azure para la administración de las asignaciones de roles del plano de datos. Pero también puede configurar RBAC local si el servicio FHIR usa un inquilino de Azure Active Directory externo o secundario. 

Si se usa el servidor de FHIR de Microsoft de OSS para Azure, el servidor valida que:

1. El token tenga el derecho `Audience` (notificación `aud`).
1. El token tenga un rol en la notificación `roles` a la que se permite el acceso al servidor de FHIR.

Vea detalles sobre cómo [definir roles en el servidor de FHIR](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md).

Un servidor de FHIR también puede validar que un token de acceso tenga los ámbitos (en la notificación del token `scp`) para acceder a la sección de FHIR API a la que un cliente está intentando acceder. Actualmente, el servicio FHIR no valida los ámbitos de token.