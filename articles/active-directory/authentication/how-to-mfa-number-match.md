---
title: 'Uso de la coincidencia de números en las notificaciones de autenticación multifactor (versión preliminar): Azure Active Directory'
description: Aprenda a usar la coincidencia de números en las notificaciones de autenticación multifactor
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6428bf7a2680d59ae42a8236b3a7a5aea82b1b2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725325"
---
# <a name="how-to-use-number-matching-in-multifactor-authentication-mfa-notifications-preview---authentication-methods-policy"></a>Uso de la coincidencia de números en notificaciones de autenticación multifactor (versión preliminar): directiva de métodos de autenticación

En este tema se explica cómo habilitar la coincidencia de números en las notificaciones push de Microsoft Authenticator para mejorar la seguridad en el inicio de sesión del usuario.  

>[!NOTE]
>La coincidencia de números es una actualización de seguridad importante de las notificaciones tradicionales de segundo factor de la aplicación Microsoft Authenticator que se habilitará de forma predeterminada para todos los inquilinos pocos meses después de la disponibilidad general (GA).<br> Se recomienda encarecidamente habilitar la coincidencia de números a corto plazo para mejorar la seguridad del inicio de sesión.

## <a name="prerequisites"></a>Requisitos previos

La organización deberá habilitar las notificaciones push de Microsoft Authenticator (segundo factor tradicional) para algunos usuarios o grupos mediante la nueva API de la directiva de métodos de autenticación. Para lograr coherencia, si la organización usa extensiones NPS o adaptador de ADFS, es preciso actualizarlos a las versiones más recientes. 

## <a name="number-matching"></a>Coincidencia de números

<!---check below with Mayur. The bit about the policy came from the number match FAQ at the end.--->

La coincidencia de números se puede dirigir a un único grupo, que puede ser dinámico o anidado. En la directiva de métodos de autenticación, se admiten tanto los grupos de seguridad sincronizados locales como los grupos de seguridad solo en la nube. 

La coincidencia de números está disponible para los escenarios siguientes. Si se habilita, la coincidencia de números se admite en todos los escenarios.

- [Autenticación multifactor](tutorial-enable-azure-mfa.md)
- [Restablecimiento de la contraseña de autoservicio](howto-sspr-deployment.md)
- [Registro combinado de autoservicio de restablecimiento de contraseña y autenticación multifactor al configurar la aplicación Authenticator](howto-registration-mfa-sspr-combined.md)
- [Adaptador de AD FS](howto-mfaserver-adfs-windows-server.md)
- [Extensión NPS](howto-mfa-nps-extension.md)

### <a name="multifactor-authentication"></a>Autenticación multifactor

Cuando un usuario responde a una notificación push de autenticación multifactor mediante Microsoft Authenticator, se le mostrará un número, que debe escribir en la aplicación para completar la aprobación. 

![Captura de pantalla en la que el usuario escribe un número coincidente.](media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

### <a name="sspr"></a>SSPR

Durante el proceso en que el propio usuario restablece la contraseña, la notificación de Microsoft Authenticator mostrará un número que el usuario deberá escribir en la notificación de la aplicación Authenticator. Este número solo los podrán ver los usuarios que tengan habilitada la coincidencia de números.

>[!NOTE]
>La coincidencia de números para los roles de administrador durante el autoservicio de restablecimiento de contraseña está pendiente y no está disponible durante un par de días.

### <a name="combined-registration"></a>Registro combinado

Cuando un usuario realiza el registro combinado para configurar Microsoft Authenticator, se le pide que apruebe una notificación para poder agregar la cuenta. En el caso de los usuarios que tengan la coincidencia de números habilitada, esta notificación mostrará un número que deben escribir en la notificación de la aplicación Authenticator. 

### <a name="ad-fs-adapter"></a>Adaptador de AD FS

El adaptador de AD FS admite la coincidencia de números si se instala una actualización concreta. Las versiones anteriores de Windows Server no admiten la coincidencia de números. En estas versiones, los usuarios seguirán teniendo que usar **Aprobar**/**Denegar** y no verán la coincidencia de números hasta que realicen la actualización.

| Versión | Actualización |
|---------|--------|
| Windows Server 2022 | [26 de octubre de 2021: KB5006745 (compilación 20348.320 del sistema operativo), versión preliminar](https://support.microsoft.com/topic/october-26-2021-kb5006745-os-build-20348-320-preview-8ff9319a-19e7-40c7-bbd1-cd70fcca066c) |
| Windows Server 2019 | [19 de octubre de 2021: KB5006744 (compilación 17763.2268 del sistema operativo), versión preliminar](https://support.microsoft.com/topic/october-19-2021-kb5006744-os-build-17763-2268-preview-e043a8a3-901b-4190-bb6b-f5a4137411c0) |
| Windows Server 2016 | [12 de octubre de 2021: KB5006669 (compilación 14393.4704 del sistema operativo)](https://support.microsoft.com/topic/october-12-2021-kb5006669-os-build-14393-4704-bcc95546-0768-49ae-bec9-240cc59df384) |


### <a name="nps-extension"></a>Extensión NPS

Asegúrese de ejecutar la versión más reciente de la [extensión NPS](https://www.microsoft.com/download/details.aspx?id=54688). A partir de la 1.0.1.40, las versiones de la extensión NPS admiten la coincidencia de números. 

Como la extensión NPS no puede mostrar números, a los usuarios que tengan habilitación para la coincidencia de números se les seguirá pidiendo que usen el método de **Aprobar**/**Denegar**. Sin embargo, puede crear una clave del Registro que invalide las notificaciones push para pedir a un usuario que escriba un código de acceso de un solo uso (OTP). Para ello, el usuario debe tener un método de autenticación mediante un código de acceso de un solo uso registrado. Los métodos comunes de autenticación de OTP incluyen el un código de acceso de un solo uso disponible en la aplicación Microsoft Authenticator, otros tokens de software, etc. 

Si el usuario no tiene registrado un método OTP, seguirá recibiendo la experiencia **Aprobar**/**Denegar**. Los usuarios que tengan la coincidencia de números deshabilitada, siempre verán la experiencia **Aprobar**/**Denegar**.

Para crear la clave del Registro que invalida las notificaciones push:

1. En el servidor NPS, abra el Editor del Registro.
1. Vaya a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa.
1. Establezca el siguiente par de clave-valor: Key: OVERRIDE_NUMBER_MATCHING_WITH_OTP Value = TRUE
1. Reinicie el servicio NPS. 

### <a name="policy-schema-changes"></a>Cambios de esquema de directiva 

Identifique el grupo de destino único para la configuración del esquema. A continuación, use el siguiente punto de conexión de API para cambiar la propiedad numberMatchingRequiredState a **habilitada**:

https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator


#### <a name="microsoftauthenticatorauthenticationmethodconfiguration-properties"></a>Propiedades de MicrosoftAuthenticatorAuthenticationMethodConfiguration

**PROPIEDADES**

| Propiedad | Tipo | Descripción |
|---------|------|-------------|
| id | String | El identificador de la directiva de métodos de autenticación. |
| state | authenticationMethodState | Los valores posibles son: **enabled**<br>**disabled** |
 
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
| targetType | authenticationMethodTargetType | Los valores posibles son **user** o **group**.<br>Nota: Solo se podrá establecer un grupo o usuario para la coincidencia de números. |
| numberMatchingRequiredState | advancedConfigState | Los valores posibles son:<br>**enabled** habilita explícitamente la característica para el grupo seleccionado.<br>**disabled** deshabilita explícitamente la característica para el grupo seleccionado.<br>**default** permite a Azure AD administrar si la característica está habilitada o no para el grupo seleccionado. |

>[!NOTE]
>La coincidencia de números solo se puede habilitar para grupos individuales.

#### <a name="example-of-how-to-enable-number-matching-for-all-users"></a>Ejemplo de cómo habilitar la coincidencia de números para todos los usuarios

Tendrá que cambiar el valor de **numberMatchingRequiredState** de **default** a **enabled**. 

Tenga en cuenta que el valor del modo de autenticación puede ser **any** o **push**, en función de si también se desea habilitar el inicio de sesión de teléfono sin contraseña, o no. En estos ejemplos, se usara **any**, pero si no desea permitir que se pueda iniciar sesión sin contraseña, use **push**. 

>[!NOTE]
>Para los usuarios sin contraseña, la habilitación de la coincidencia de números no tiene ningún impacto, porque ya forma parte de la experiencia sin contraseña. 

Es posible que tenga que aplicar una revisión a la totalidad de includeTarget para evitar sobrescribir cualquier configuración anterior. En ese caso, realice primero una operación GET, actualice solo los campos pertinentes y, después, realice una operación PATCH. En el ejemplo siguiente solo se muestra la actualización de **numberMatchingRequiredState**. 

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
 
Para confirmar que se ha aplicado, utilice el siguiente punto de conexión para ejecutar la solicitud GET.
GET: https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator
 
 
#### <a name="example-of-how-to-enable-number-matching-for-a-single-group"></a>Ejemplo de cómo habilitar la coincidencia de números para un grupo único
 
Habrá que cambiar el valor de **numberMatchingRequiredState** de **default** a **enabled**. Deberá cambiar el **identificador** de **all_users** al identificador de objeto del grupo desde el portal de Azure AD.

Es posible que tenga que utilizar la operación PATCH en la totalidad de includeTarget para evitar sobrescribir cualquier configuración anterior. Es aconsejable que primero realice una operación GET, después, actualice solo los campos pertinentes y, luego, realice una operación PATCH. En el ejemplo siguiente solo se muestra la actualización de **numberMatchingRequiredState**. 

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
 
Para comprobarlo, vuelva a ejecutar GET y compruebe identificador de objeto GET https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator
 

#### <a name="example-of-error-when-enabling-number-matching-for-multiple-groups"></a>Ejemplo de error al habilitar la coincidencia de números para varios grupos

Se producirá el error 400 Solicitud errónea en la solicitud de PATCH y aparecerá el siguiente mensaje: 


`Persistance of policy failed with error: You cannot enable multiple targets for feature 'Require Number Matching'. Choose only one of the following includeTargets to enable: aede0efe-c1b4-40dc-8ae7-2c402f23e312,aede0efe-c1b4-40dc-8ae7-2c402f23e317.`

### <a name="test-the-end-user-experience"></a>Prueba de la experiencia del usuario final
Agregue la cuenta de usuario de prueba a la aplicación Microsoft Authenticator. Para iniciar sesión mediante el teléfono **no** es necesario habilitar la cuenta. 

Consulte la experiencia del usuario final de una notificación push de autenticación multifactor de Authenticator con coincidencia de número. Para ello, debe iniciar sesión en aka.ms/MFAsetup. 

### <a name="turn-off-number-matching"></a>Desactivación de la coincidencia de números

Para desactivar la coincidencia de números, tendrá que realizar la operación PATCH y pasar **numberMatchingRequiredState** de **enabled** a **disabled**/**default**.

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

## <a name="enable-number-matching-in-the-portal"></a>Habilitación de la coincidencia de números en el portal

Para habilitar la coincidencia de números en el portal de Azure AD, siga estos pasos:

1. En el portal de Azure AD, haga clic en **Seguridad** > **Métodos de autenticación** > **Microsoft Authenticator**.
1. Seleccione los usuarios de destino, haga clic en los tres puntos de la derecha y después en **Configurar**.
   
   ![Captura de pantalla de la configuración de la coincidencia de números.](media/howto-authentication-passwordless-phone/configure.png)

1. Seleccione el **modo de autenticación** y, después, en **Require number matching (Preview)** [Requerir coincidencia de números (versión preliminar)], haga clic en **Enable** (Habilitar) y, después, en **Done** (Listo). 

   ![Captura de pantalla de habilitación de la coincidencia de números.](media/howto-authentication-passwordless-phone/enable-number-matching.png)


## <a name="known-issues"></a>Problemas conocidos

- La coincidencia de números para los roles de administrador durante el autoservicio de restablecimiento de contraseña está pendiente y no está disponible durante un par de días.

## <a name="next-steps"></a>Pasos siguientes

[Métodos de autenticación en Azure Active Directory: aplicación Microsoft Authenticator](concept-authentication-authenticator-app.md)

