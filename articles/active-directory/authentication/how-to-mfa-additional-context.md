---
title: 'Uso de contexto adicional en las notificaciones de autenticación multifactor (versión preliminar): Azure Active Directory'
description: Aprenda a usar contexto adicional en las notificaciones de autenticación multifactor.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139c7a8ea707fc0c9614082cae577eda15f4f664
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725331"
---
# <a name="how-to-use-additional-context-in-multifactor-authentication-mfa-notifications-preview---authentication-methods-policy"></a>Uso de contexto adicional en notificaciones de autenticación multifactor (versión preliminar): directiva de métodos de autenticación

En este tema se describe cómo mejorar la seguridad del inicio de sesión del usuario mediante la adición de la ubicación de la aplicación basada en direcciones IP en las notificaciones push de Microsoft Authenticator.  

## <a name="prerequisites"></a>Requisitos previos

La organización deberá habilitar las notificaciones push de Microsoft Authenticator para algunos usuarios o grupos mediante la nueva API de la directiva de métodos de autenticación. 

>[!NOTE]
>El contexto adicional se puede dirigir a un único grupo, que puede ser dinámico o anidado. En la directiva de métodos de autenticación, se admiten tanto los grupos de seguridad sincronizados locales como los grupos de seguridad solo en la nube.

## <a name="passwordless-phone-sign-in-and-multifactor-authentication"></a>Inicio de sesión con teléfono sin contraseña y autenticación multifactor 

Cuando un usuario recibe un inicio de sesión de teléfono sin contraseña o una notificación push de autenticación multifactor en la aplicación Microsoft Authenticator, verá el nombre de la aplicación que solicita la aprobación y la ubicación de la aplicación basada en su dirección IP.

![Captura de pantalla del contexto adicional en la notificación push de autenticación multifactor.](media/howto-authentication-passwordless-phone/location.png)

El contexto adicional se puede combinar con la [coincidencia de números](how-to-mfa-number-match.md) para mejorar aún más la seguridad de los inicios de sesión. 

![Captura de pantalla del contexto adicional con coincidencia de números en la notificación push de autenticación multifactor.](media/howto-authentication-passwordless-phone/location-with-number-match.png)

### <a name="policy-schema-changes"></a>Cambios de esquema de directiva 

Identifique un grupo de destino único para la configuración del esquema. A continuación, use el siguiente punto de conexión de API para cambiar la propiedad displayAppInformationRequiredState a **habilitada**:

https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator


#### <a name="microsoftauthenticatorauthenticationmethodconfiguration-properties"></a>Propiedades de MicrosoftAuthenticatorAuthenticationMethodConfiguration

**PROPIEDADES**

| Propiedad | Tipo | Descripción |
|---------|------|-------------|
| id | String | El identificador de la directiva de métodos de autenticación. |
| state | authenticationMethodState | Los valores posibles son: **enabled** (habilitado)<br>**disabled** (deshabilitado) |
 
**RELACIONES**

| Relación | Tipo | Descripción |
|--------------|------|-------------|
| includeTargets | [microsoftAuthenticatorAuthenticationMethodTarget](/graph/api/resources/passwordlessmicrosoftauthenticatorauthenticationmethodtarget.md?view=graph-rest-beta) |
| collection | Una colección de usuarios o grupos habilitados para usar el método de autenticación. |
 
#### <a name="microsoftauthenticator-includetarget-properties"></a>Propiedades MicrosoftAuthenticator includeTarget
 
**PROPIEDADES**

| Propiedad | Tipo | Descripción |
|----------|------|-------------|
| authenticationMode | String | Los valores posibles son:<br>**any**: se permiten tanto el inicio de sesión mediante teléfono sin contraseña como las notificaciones tradicionales de segundo factor.<br>**deviceBasedPush**: solo se permiten notificaciones de inicio de sesión mediante teléfono sin contraseña.<br>**push**: solo se permiten las notificaciones push de segundo factor tradicionales. |
| id | String | Identificador de objeto de un usuario o grupo de Azure AD. |
| targetType | authenticationMethodTargetType | Los valores posibles son **user** o **group**.<br>Solo puede establecer un grupo o usuario para un contexto adicional. |
| displayAppInformationRequiredState | advancedConfigState | Los valores posibles son:<br>**enabled** habilita explícitamente la característica para el grupo seleccionado.<br>**disabled** deshabilita explícitamente la característica para el grupo seleccionado.<br>**default** permite a Azure AD administrar si la característica está habilitada o no para el grupo seleccionado. |

>[!NOTE]
>El contexto adicional solo se puede habilitar para un único grupo.

#### <a name="example-of-how-to-enable-additional-context-for-all-users"></a>Ejemplo de cómo habilitar contexto adicional para todos los usuarios

Cambie **displayAppInformationRequiredState** de **default** (predeterminado) a **enabled** (habilitado). 

El valor del modo de autenticación puede ser **any** o **push**, en función de si también se desea habilitar el inicio de sesión de teléfono sin contraseña, o no. En estos ejemplos, se usara **any**, pero si no desea permitir que se pueda iniciar sesión sin contraseña, use **push**.

Es posible que tenga que utilizar la operación PATCH de includeTarget en su totalidad para evitar sobrescribir cualquier configuración anterior. En ese caso, realice primero una operación GET, actualice solo los campos pertinentes y, después, realice una operación PATCH. En el ejemplo siguiente solo se muestra la actualización de **displayAppInformationRequiredState**. 

```json
//Retrieve your existing policy via a GET. 
//Leverage the Response body to create the Request body section. Then update the Request body similar to the Request body as shown below.
//Change the Query to PATCH and Run query
 
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodConfigurations/$entity",
    "@odata.type": "#microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration",
    "id": "MicrosoftAuthenticator",
    "state": "enabled",
    "includeTargets@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodsPolicy/authenticationMethodConfigurations('MicrosoftAuthenticator')/microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration/includeTargets",
    "includeTargets": [
        {
            "targetType": "group",
            "id": "all_users",
            "authenticationMode": "any",
            "displayAppInformationRequiredState": "enabled",
            "numberMatchingRequiredState": "enabled"
        }
    ]
}
 
```
 
Para confirmar que esta actualización se ha aplicado, ejecute la solicitud GET mediante el siguiente punto de conexión.
GET - https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator
 
 
#### <a name="example-of-how-to-enable-additional-context-for-a-single-group"></a>Ejemplo de cómo habilitar contexto adicional para un grupo único
 
Cambie el valor **displayAppInformationRequiredState** de **default** (predeterminado) a **enabled** (habilitado). Cambie el **identificador** de **all_users** al identificador de objeto del grupo desde el portal de Azure AD.

Es posible que tenga que utilizar la operación PATCH de includeTarget en su totalidad para evitar sobrescribir cualquier configuración anterior. Es aconsejable que primero realice una operación GET, después, actualice solo los campos pertinentes y, luego, realice una operación PATCH. En el ejemplo siguiente solo se muestra la actualización de **displayAppInformationRequiredState**. 

```json
//Copy paste the below in the Request body section as shown below.
//Leverage the Response body to create the Request body section. Then update the Request body similar to the Request body as shown below.
//Change query to PATCH and run query

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodConfigurations/$entity",
    "@odata.type": "#microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration",
    "id": "MicrosoftAuthenticator",
    "state": "enabled",
    "includeTargets@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodsPolicy/authenticationMethodConfigurations('MicrosoftAuthenticator')/microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration/includeTargets",
    "includeTargets": [
        {
            "targetType": "group",
            "id": "1ca44590-e896-4dbe-98ed-b140b1e7a53a”,
            "authenticationMode": "any",
            "displayAppInformationRequiredState": "enabled",
            "numberMatchingRequiredState": "enabled"
        }
    ]
}
```
 
Para comprobarlo, vuelva a ejecutar GET y compruebe el identificador de objeto GET https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator
 

#### <a name="example-of-error-when-enabling-additional-context-for-multiple-groups"></a>Ejemplo de error al habilitar contexto adicional para varios grupos

Se producirá el error 400 Solicitud errónea en la solicitud de PATCH y aparecerá el siguiente mensaje: 

`Persistance of policy failed with error: You cannot enable multiple targets for feature 'Require Display App Information'. Choose only one of the following includeTargets to enable: aede0efe-c1b4-40dc-8ae7-2c402f23e312,aede0efe-c1b4-40dc-8ae7-2c402f23e317.`

### <a name="test-the-end-user-experience"></a>Prueba de la experiencia del usuario final
Agregue la cuenta de usuario de prueba a la aplicación Microsoft Authenticator. Para iniciar sesión mediante el teléfono **no** es necesario habilitar la cuenta. 

Consulte la experiencia del usuario final de una notificación push de autenticación multifactor de Authenticator con contexto adicional. Para ello, debe iniciar sesión en aka.ms/MFAsetup. 

### <a name="turn-off-additional-context"></a>Desactivación del contexto adicional

Para desactivar el contexto adicional, tendrá que cambiar la operación PATCH de eliminación de **displayAppInformationRequiredState** de **enabled** a **disabled**/**default**.

```json
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodConfigurations/$entity",
    "@odata.type": "#microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration",
    "id": "MicrosoftAuthenticator",
    "state": "enabled",
    "includeTargets@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodsPolicy/authenticationMethodConfigurations('MicrosoftAuthenticator')/microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration/includeTargets",
    "includeTargets": [
        {
            "targetType": "group",
            "id": "all_users",
            "authenticationMode": "any",
            "displayAppInformationRequiredState": "enabled",
            "numberMatchingRequiredState": "default"
        }
    ]
}
```

## <a name="enable-additional-context-in-the-portal"></a>Habilitar contexto adicional en el portal

Para habilitar el contexto adicional en el portal de Azure AD, complete los pasos siguientes:

1. En el portal de Azure AD, haga clic en **Seguridad** > **Métodos de autenticación** > **Microsoft Authenticator**.
1. Seleccione los usuarios de destino, haga clic en los tres puntos de la derecha y después en **Configurar**.
   
   ![Captura de pantalla de la configuración de la coincidencia de números.](media/howto-authentication-passwordless-phone/configure.png)

1. Seleccione el **modo de autenticación** y, después, en **Mostrar contexto adicional en las notificaciones (versión preliminar)** , haga clic en **Habilitar** y, después, en **Listo**.

   ![Captura de pantalla de la habilitación del contexto adicional.](media/howto-authentication-passwordless-phone/enable-additional-context.png)

## <a name="next-steps"></a>Pasos siguientes

[Métodos de autenticación en Azure Active Directory: aplicación Microsoft Authenticator](concept-authentication-authenticator-app.md)

