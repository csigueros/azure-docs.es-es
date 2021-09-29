---
title: 'Solución de problemas de dispositivos con el comando dsregcmd: Azure Active Directory'
description: En este artículo se explica cómo usar la salida del comando dsregcmd para comprender el estado de los dispositivos en Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad62e355fc54f08e6c21967c2359740f22323db9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616771"
---
# <a name="troubleshoot-devices-by-using-the-dsregcmd-command"></a>Solución de problemas de dispositivos con el comando dsregcmd

En este artículo se explica cómo usar la salida del comando `dsregcmd` para comprender el estado de los dispositivos en Azure Active Directory (Azure AD). La utilidad `dsregcmd /status` debe ejecutarse como una cuenta de usuario de dominio.

## <a name="device-state"></a>Estado del dispositivo

En esta sección se enumeran los parámetros de estado de la combinación de dispositivos. En la siguiente tabla se enumeran los criterios necesarios para que el dispositivo esté en varios estados de unión:

| AzureAdJoined | EnterpriseJoined | DomainJoined | Estado del dispositivo |
| ---   | ---   | ---   | ---   |
| SÍ | No | No | Unido a Azure AD |
| No | No | SÍ | Unido a dominio |
| SÍ | No | SÍ | Unido a AD híbrido |
| No | SÍ | SÍ | Unido a DRS en el entorno local |
| | |

> [!NOTE]
> El estado Unido al área de trabajo (registrado en Azure AD) se muestra en la sección ["Estado del usuario"](#user-state).

- **AzureAdJoined**: establézcalo en *YES* (Sí) si el dispositivo está unido a Azure AD. De lo contrario, establezca el estado en *NO*.
- **EnterpriseJoined**: establézcalo en *YES* (Sí) si el dispositivo está unido a un servicio de replicación de datos (DRS) en el entorno local. Un dispositivo no puede tener tanto el estado EnterpriseJoined como AzureAdJoined.
- **DomainJoined**: establézcalo en *YES* (Sí) si el dispositivo está unido a un dominio (Active Directory).
- **DomainName**: establezca el estado en el nombre del dominio si el dispositivo está unido a un dominio.

### <a name="sample-device-state-output"></a>Salida del estado del dispositivo de muestra

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Detalles del dispositivo

El estado solo se muestra cuando el dispositivo está unido a Azure AD o a Azure AD híbrido (no registrado en Azure AD). En esta sección se enumeran los detalles de identificación de los dispositivos que están almacenados en Azure AD.

- **DeviceId**: el identificador único del dispositivo en el inquilino de Azure AD.
- **Thumbprint**: la huella digital del certificado de dispositivo.
- **DeviceCertificateValidity**: el estado de validez del certificado de dispositivo.
- **KeyContainerId**: containerId de la clave privada del dispositivo que está asociada con el certificado de dispositivo.
- **KeyProvider**: el proveedor de claves (hardware/software) que se usa para almacenar la clave privada del dispositivo.
- **TpmProtected**:el estado se establece en *YES* (Sí) si la clave privada del dispositivo está almacenada en un Módulo de plataforma segura (TPM) de hardware.
- **DeviceAuthStatus**: realiza una comprobación para determinar el mantenimiento del dispositivo en Azure AD. Los estados de mantenimiento incluidos son:  
  * *SUCCESS* si el dispositivo está presente y habilitado en Azure AD.  
  * *FAILED* si el dispositivo está deshabilitado o eliminado. Para obtener más información sobre este problema, vea [Preguntas más frecuentes sobre la administración de dispositivos de Azure Active Directory](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-devices).  
  * *FAILED. ERROR* si la prueba no se pudo ejecutar. Esta prueba requiere conectividad de red a Azure AD.
    > [!NOTE]
    > El campo **DeviceAuthStatus** se agregó en la actualización de mayo de 2021 de Windows 10 (versión 21H1).  

### <a name="sample-device-details-output"></a>Salida de los detalles del dispositivo de muestra

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
          DeviceAuthStatus : SUCCESS
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Detalles de inquilino

Los detalles del inquilino solo se muestran cuando el dispositivo está unido a Azure AD o a Azure AD híbrido, no registrado en Azure AD. En esta sección se enumeran los detalles comunes del inquilino que se muestran cuando un dispositivo está unido a Azure AD.

> [!NOTE]
> Si los campos de direcciones URL de la administración de dispositivos móviles (MDM) de esta sección están vacíos, indica que la MDM no se configuró o que el usuario actual no está en el ámbito de la inscripción de MDM. Compruebe la configuración de movilidad en Azure AD para revisar la configuración de MDM.

> [!NOTE]
> Aunque vea las direcciones URL de MDM, esto no significa que el dispositivo esté administrado por MDM. La información se muestra si el inquilino tiene la configuración de MDM para la inscripción automática incluso si el dispositivo no se administra.

### <a name="sample-tenant-details-output"></a>Salida de los detalles del inquilino de muestra

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Estado de usuario

En esta sección se muestran los estados de varios atributos para los usuarios que han iniciado sesión actualmente en el dispositivo.

> [!NOTE]
> El comando debe ejecutarse en un contexto de usuario para recuperar un estado válido.

- **NgcSet**: establezca el estado en *YES* (Sí) si se establece una clave de Windows Hello para el usuario que ha iniciado sesión.
- **NgcKeyId**: el identificador de la clave de Windows Hello si se ha establecido una para el usuario que ha iniciado sesión.
- **CanReset**: indica si el usuario puede restablecer la clave de Windows Hello.
- **Valores posibles**: DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive o Unknown, si se ha producido un error.
- **WorkplaceJoined**: establezca el estado en *YES* (Sí) si se han agregado cuentas registradas de Azure AD al dispositivo en el contexto de NTUSER actual.
- **WamDefaultSet**: establezca el estado en *YES* (Sí) si se creó una cuenta WebAccount predeterminada del Administrador de cuentas web (WAM) para el usuario que ha iniciado sesión. Este campo podría mostrar un error si se ejecuta `dsregcmd /status` desde un símbolo del sistema con privilegios elevados.
- **WamDefaultAuthority**: establezca el estado en *organizations* (organizaciones) para Azure AD.
- **WamDefaultId**: use siempre *https://login.microsoft.com* para Azure AD.
- **WamDefaultGUID**: GUID del proveedor de WAM (cuenta de Azure AD/Microsoft) para la cuenta web predeterminada de WAM.

### <a name="sample-user-state-output"></a>Salida del estado del usuario de muestra

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>Estado de SSO

Puede pasar por alto esta sección para los dispositivos registrados de Azure AD.

> [!NOTE]
> El comando debe ejecutarse en un contexto de usuario para recuperar el estado válido de dicho usuario.

- **AzureAdPrt**: establezca el estado en *YES* (Sí) si hay un token de actualización principal (PRT) en el dispositivo para el usuario que ha iniciado sesión.
- **AzureAdPrtUpdateTime**: establezca el estado en la hora, en hora universal coordinada (UTC), en que el PRT se actualizó por última vez.
- **AzureAdPrtExpiryTime**: establezca el estado en la hora en formato UTC en que el PRT va a expirar si no se renueva.
- **AzureAdPrtAuthority**: la dirección URL de la autoridad de Azure AD.
- **EnterprisePrt**: establezca el estado en *YES* (Sí) si el dispositivo tiene un PRT de Servicios de federación de Active Directory (AD FS) en el entorno local. En el caso de los dispositivos unidos a Azure AD híbrido, el dispositivo podría tener un PRT tanto de Azure AD como de Active Directory local simultáneamente. Los dispositivos unidos a un entorno local solo tendrán un PRT empresarial.
- **EnterprisePrtUpdateTime**: establezca el estado en la hora, en formato UTC, cuando se actualizó por última vez el PRT empresarial.
- **EnterprisePrtExpiryTime**: establezca el estado en la hora en formato UTC en que el PRT va a expirar si no se renueva.
- **EnterprisePrtAuthority**: la dirección URL de la autoridad AD FS

>[!NOTE]
> Los siguientes campos de diagnóstico de PRT se agregaron en la actualización de mayo de 2021 de Windows 10 (versión 21H1).

>[!NOTE]
> * La información de diagnóstico que se muestra en el campo **AzureAdP** es para la adquisición o actualización de PRT de Azure AD, y la información de diagnóstico que se muestra en el campo **EnterprisePrt** es para la adquisición o actualización de PRT Enterprise.
> * La información de diagnóstico solo se muestra si el error de adquisición o actualización se produjo después de la última hora de actualización del PRT correcta (AzureAdPrtUpdateTime/EnterprisePrtUpdateTime).  
>En un dispositivo compartido, esta información de diagnóstico podría ser del intento de inicio de sesión de un usuario diferente.

- **AcquirePrtDiagnostics**: establezca el estado en *PRESENT* (Presente) si la información de diagnóstico de PRT adquirida está presente en los registros.  
   Este campo se omite si no hay información de diagnóstico disponible.
- **Intento de PRT anterior**: hora local, en UTC, a la que se produjo el intento de PRT con errores.  
- **Estado del intento:** el código de error de cliente que se devuelve (HRESULT).
- **Identidad del usuario**: el nombre principal del usuario para el que se produjo el intento de PRT.
- **Tipo de credencial**: la credencial que se usa para adquirir o actualizar el PRT. Los tipos de credenciales comunes son la contraseña y las credenciales de nueva generación (NGC) (para Windows Hello).
- **Id. de correlación**: el identificador. de correlación enviado por el servidor para el intento de PRT con errores.
- **URI de punto de conexión**: el último punto de conexión al que se tiene acceso antes del error.
- **Método HTTP**: el método HTTP que se usa para acceder al punto de conexión.
- **Error HTTP**: código de error de transporte de WinHttp. Obtenga [códigos de error de red](/windows/win32/winhttp/error-messages) adicionales.
- **Estado HTTP**: el estado HTTP devuelto por el punto de conexión.
- **Código de error del servidor**: el código de error del servidor.  
- **Descripción del error del servidor**: el mensaje de error del servidor.
- **RefreshPrtDiagnostics**:establezca el estado en *PRESENT* (Presente) si la información de diagnóstico de PRT adquirida está presente en los registros.  
Este campo se omite si no hay información de diagnóstico disponible.
Los campos de información de diagnóstico son los mismos que **AcquirePrtDiagnostics**.


### <a name="sample-sso-state-output"></a>Salida del estado SSO de muestra

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : NO
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
     AcquirePrtDiagnostics : PRESENT
      Previous Prt Attempt : 2020-07-18 20:10:33.789 UTC
            Attempt Status : 0xc000006d
             User Identity : john@contoso.com
           Credential Type : Password
            Correlation ID : 63648321-fc5c-46eb-996e-ed1f3ba7740f
              Endpoint URI : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token/
               HTTP Method : POST
                HTTP Error : 0x0
               HTTP status : 400
         Server Error Code : invalid_grant
  Server Error Description : AADSTS50126: Error validating credentials due to invalid username or password.
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostics-data"></a>Datos de diagnóstico

### <a name="pre-join-diagnostics"></a>Diagnóstico previo a la unión

Esta sección de diagnóstico solo se muestra si el dispositivo está unido a un dominio y no puede realizar una unión a Azure AD híbrido.

En esta sección se realizan una serie de pruebas para ayudar a diagnosticar errores de la unión. La información incluye la fase de error, el código de error, el identificador de la solicitud de servidor, el estado HTTP de respuesta del servidor y el mensaje de error de la respuesta del servidor.

- **User Context**: contexto en el que se ejecutan los diagnósticos. Valores posibles: SYSTEM, usuario UN-ELEVATED (sin privilegios), usuario ELEVATED (con privilegios).

   > [!NOTE]
   > Ya que la unión real se realiza en el contexto de SYSTEM, la ejecución del diagnóstico en el contexto de SYSTEM está más cercana al escenario de unión real. Para ejecutar diagnósticos en el contexto de SYSTEM, el comando `dsregcmd /status` se debe ejecutar desde un símbolo del sistema con privilegios elevados.

- **Client Time**: hora del sistema, en UTC.
- **AD Connectivity Test**: se realiza una prueba de conectividad con el controlador de dominio. Si se produce un error en esta prueba, es probable que se produzcan errores de unión en la fase previa a la comprobación.
- **Prueba de configuración de AD**: esta prueba lee y comprueba si el objeto Procedimientos de contención especiales (SCP) está configurado correctamente en el bosque Active Directory local. Los errores de esta prueba podrían producir errores de unión en la fase de detección con el código 0x801c001d.
- **Prueba de detección de DRS**: esta prueba obtiene los puntos de conexión de DRS del punto de conexión de los metadatos de detección y realiza una solicitud del dominio Kerberos del usuario. Los errores de esta prueba podrían producir errores de unión en la fase de detección.
- **Prueba de conectividad de DRS**: se realiza una prueba de conectividad básica del punto de conexión DRS.
- **Token acquisition Test**: esta prueba intenta obtener un token de autenticación de Azure AD si el inquilino del usuario está federado. Los errores de esta prueba podrían producir errores de unión en la fase de autenticación. Si la autenticación no puede realizarse, se intentará la unión de sincronización como reserva, a menos que la reserva se deshabilite explícitamente con la siguiente configuración de clave del Registro:

  ```
  Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
  Value: FallbackToSyncJoin
  Type:  REG_DWORD
  Value: 0x0 -> Disabled
  Value: 0x1 -> Enabled
  Default (No Key): Enabled
  ```

- **Fallback to Sync-Join**: establezca el estado en *Habilitado* si la anterior clave del Registro, a fin de evitar que la reserva realice la unión de sincronización con errores de autenticación, *no* está presente. Esta opción está disponible en Windows 10 1803 y versiones posteriores.
- **Registro anterior**: la hora a la que se produjo el intento de unión anterior. Solo se registran los intentos de unión erróneos.
- **Fase de error**: fase de la unión en la que se anuló. Los valores posibles son *pre-check* (comprobación previa), *discover* (detección), *auth* (autenticación) y *join* (unión).
- **Client ErrorCode**: el código de error de cliente que se devuelve (HRESULT).
- **Server ErrorCode**: el código de error del servidor que se muestra si se envió una solicitud al servidor y este respondió con un código de error.
- **Mensaje del servidor**: el mensaje del servidor que se devuelve junto con el código de error.
- **Estado HTTPS**: el estado HTTP devuelto por el servidor.
- **Identificador de solicitud**: el identificador de solicitud del cliente enviado al servidor. El identificador de solicitud es útil para correlacionar con los registros del lado servidor.

### <a name="sample-pre-join-diagnostics-output"></a>Muestra de la salida de diagnóstico previo a la unión

En el siguiente ejemplo se muestra un error de detección en una prueba de diagnóstico.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                       |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

En el ejemplo siguiente se muestra que las pruebas de diagnóstico se están superando, pero el intento de registro produjo un error de directorio, lo que se espera para la unión de sincronización. Después de que finalice el trabajo de sincronización de Azure AD Connect, el dispositivo puede combinarse.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                       |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) isn't found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnóstico posterior a la unión

En esta sección de diagnóstico se muestra el resultado de las comprobaciones de validez realizadas en un dispositivo unido a la nube.

- **AadRecoveryEnabled**: si el valor es *YES* (Sí), las claves almacenadas en el dispositivo no se pueden usar y el dispositivo está marcado para recuperación. El siguiente inicio de sesión desencadenará el flujo de recuperación y se volverá a registrar el dispositivo.
- **KeySignTest**: si el valor es *PASSED* (Superado), las claves del dispositivo están en buen estado. Si se produce un error en KeySignTest, normalmente el dispositivo se marca para la recuperación. El siguiente inicio de sesión desencadenará el flujo de recuperación y se volverá a registrar el dispositivo. En el caso de los dispositivos unidos a Azure AD híbrido, la recuperación es silenciosa. Aunque los dispositivos se unan a Azure AD o se registren en Azure AD, solicitarán la autenticación del usuario para recuperar y volver a registrar el dispositivo, si es necesario. 
   > [!NOTE]
   > KeySignTest requiere privilegios elevados.

#### <a name="sample-post-join-diagnostics-output"></a>Muestra de la salida de diagnóstico posterior a la unión

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisites-check"></a>Comprobación de los requisitos previos de NGC

Esta sección de diagnóstico realiza la comprobación de requisitos previos para configurar Windows Hello para empresas (WHFB).

> [!NOTE]
> Es posible que no vea los detalles de comprobación de los requisitos previos de NGC en `dsregcmd /status` si el usuario ya ha configurado WHFB correctamente.

- **IsDeviceJoined**: establezca el estado en *YES* (Sí) si el dispositivo está unido a Azure AD.
- **IsUserAzureAD**: establezca el estado en *YES* (Sí) si el usuario que ha iniciado sesión está presente en Azure AD.
- **PolicyEnabled**: establezca el estado en *YES* (Sí) si la directiva de WHFB está habilitada en el dispositivo.
- **PostLogonEnabled**: establezca el estado en *YES* (Sí) si la plataforma desencadena la inscripción de WHFB de forma nativa. Si el estado se establece en *NO*, indica que la inscripción de Windows Hello para empresas se desencadena mediante un mecanismo personalizado.
- **DeviceEligible**: establezca el estado en *YES* (Sí) si el dispositivo cumple los requisitos de hardware para inscribirse en WHFB.
- **SessionIsNotRemote**: establezca el estado en *YES* si el usuario actual ha iniciado sesión directamente en el dispositivo y no de forma remota.
- **CertEnrollment**: esta configuración es específica de la implementación de certificados de confianza de WHFB que indica la entidad de inscripción de certificados de WHFB. Establezca el estado en *enrollment authority* (entidad de inscripción) si el origen de la directiva de WHFB es una directiva de grupo o en *mobile device management* (administración de dispositivos móviles) si el origen es MDM. Si ninguno de los orígenes es aplicable, establezca el estado en *none* (ninguno).
- **AdfsRefreshToken**: esta configuración es específica de la implementación de confianza de certificados WHFB y solo está presente si el estado certEnrollment es *enrollment authority*. La configuración indica si el dispositivo tiene un PRT empresarial para el usuario.
- **AdfsRaIsReady**: esta configuración es específica de la implementación de confianza de certificados WHFB y solo está presente si el estado certEnrollment es *enrollment authority*. Establezca el estado en *YES* (Sí) si AD FS indica en los metadatos de detección que admite WHFB *y* si la plantilla de certificado de inicio de sesión está disponible.
- **LogonCertTemplateReady**: esta configuración es específica de la implementación de confianza de certificados WHFB y solo está presente si el estado certEnrollment es *enrollment authority*. Establezca el estado en *YES* (Sí) si el estado de la plantilla de certificado de inicio de sesión es válido y ayuda a solucionar problemas de la autoridad de registro (RA) de AD FS.
- **PreReqResult**: proporciona el resultado de toda la evaluación de requisitos previos de WHFB. Establezca el estado en *Will Provision* (Aprovisionará) si la inscripción de WHFB se iniciará como una tarea posterior al inicio de sesión cuando el usuario inicie sesión la próxima vez.

### <a name="sample-ngc-prerequisites-check-output"></a>Salida de comprobación de requisitos previos de NGC de muestra

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Pasos siguientes

Vaya a la [Herramienta de búsqueda de errores de Microsoft](/windows/win32/debug/system-error-code-lookup-tool).
