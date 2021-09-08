---
title: Solución de problemas de dispositivos híbridos unidos a Azure Active Directory
description: Solución de problemas de dispositivos híbridos de Windows 10 y Windows Server 2016 unidos a Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5c601d81053979108ab7c49dee5b1bccbb33bf53
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464016"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Solución de problemas de dispositivos híbridos unidos a Azure Active Directory

El contenido de este artículo se aplica a los dispositivos que ejecutan Windows 10 o Windows Server 2016.

Para otros clientes Windows, consulte el artículo [Solución de problemas de dispositivos híbridos de nivel inferior unidos a Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md).

En este artículo se da por supuesto que [configuró dispositivos híbridos unidos a Azure Active Directory](hybrid-azuread-join-plan.md) para que admitan los escenarios siguientes:

- Acceso condicional basado en dispositivos
- [Perfiles móviles de empresa](./enterprise-state-roaming-overview.md)
- [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

En este documento se proporcionan instrucciones sobre la solución de posibles problemas.

Para Windows 10 y Windows Server 2016, la unión híbrida a Azure Active Directory es compatible con la actualización de noviembre de 2015 de Windows 10 y versiones posteriores.

## <a name="troubleshoot-join-failures"></a>Solución de errores de unión

### <a name="step-1-retrieve-the-join-status"></a>Paso 1: Recuperación del estado de unión

**Paso 1: Recuperación del estado de unión:**

1. Abra un símbolo del sistema como administrador
2. Escriba `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Paso 2: Evaluación del estado de unión

Revise los siguientes campos y asegúrese de que tengan los valores esperados:

#### <a name="domainjoined--yes"></a>DomainJoined: SÍ

Este campo indica si el dispositivo está unido o no a una implementación local de Active Directory. Si el valor es **NO**, el dispositivo no puede realizar una unión a Azure AD híbrido.

#### <a name="workplacejoined--no"></a>WorkplaceJoined: No

Este campo indica si el dispositivo está registrado con Azure AD, pero como un dispositivo personal (marcado como *Unido al área de trabajo*). Este valor debe ser **NO** para un equipo unido a un dominio que esté también unido a Azure AD híbrido. Si el valor es **SÍ**, se agrega una cuenta profesional o educativa antes de la finalización de la unión a Azure AD híbrido. En este caso, la cuenta se omite cuando se usa Windows 10, versión 1607 o posterior.

#### <a name="azureadjoined--yes"></a>AzureAdJoined : SÍ

Este campo indica si el dispositivo está unido. El valor será **SÍ** si el dispositivo es un dispositivo unido a un Azure AD o un dispositivo híbrido de Azure AD Unido.
Si el valor es **NO**, aún no ha finalizado la unión a Azure AD.

Continúe con los pasos siguientes para más información.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Paso 3: Busque la fase en la que se produjo el error de unión y el código de error

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 y versiones posteriores

Busque el subapartado "Previous Registration" (Registro previo) en el apartado "Diagnostic Data" (Datos de diagnóstico) de la salida de estado de la unión. Esta sección solo se muestra si el dispositivo está unido a un dominio y no puede realizar una unión a Azure AD híbrido.
El campo "Error Phase" (Fase del error) denota la fase del error de unión y "Client ErrorCode" (Código de error del cliente), el código de error de la operación de unión.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Versiones anteriores a Windows 10

Use registros de Visor de eventos para buscar la fase y el código de error para los errores de unión.

1. Abra los registros de eventos de **User Device Registration** (Registro de dispositivos del usuario) en el Visor de eventos; que se encuentran en **Applications and Services Log** > **Microsoft** > **Windows** > **User Device Registration** (Registros de aplicaciones y servicios > Microsoft > Windows > Registro de dispositivos del usuario)
2. Busque eventos con los siguientes identificadores de eventos: 304, 305, 307.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="Captura de pantalla del visor de eventos. Un evento con ID 304 está seleccionado y se muestra su información, con el código de error y la fase resaltados" border="false":::.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="Captura de pantalla del visor de eventos. Un evento con ID 305 está visible y se muestra su información, con el código de error resaltado" border="false":::.

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Paso 4: Busque posibles causas y soluciones en las siguientes listas

#### <a name="pre-check-phase"></a>Fase de comprobación previa

Posibles motivos del error:

- El dispositivo no tiene línea de visión al controlador de dominio.
   - El dispositivo debe encontrarse en la red interna de la organización o en una VPN con línea de visión de red a una implementación local del controlador de dominio de Active Directory (AD).

#### <a name="discover-phase"></a>Fase de detección

Posibles motivos del error:

- El objeto de punto de conexión de servicio (SCP) está mal configurado o no se puede leer el objeto SCP desde el controlador de dominio.
   - Se requiere un objeto SCP válido en el bosque de AD al que pertenece el dispositivo que apunte a un nombre de dominio comprobado en Azure AD.
   - Encontrará más información en la sección sobre la [Configuración de un punto de conexión de servicio](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Error al conectar y capturar los metadatos de detección del punto de conexión de detección.
   - El dispositivo debe poder acceder a `https://enterpriseregistration.windows.net` en el contexto del sistema para detectar los puntos de conexión de autorización y registro.
   - Si el entorno local requiere un proxy de salida, el administrador de TI debe asegurarse de que la cuenta del equipo del dispositivo detecte el proxy de salida y se autentique en él en modo silencioso.
- No se pudo conectar al punto de conexión del dominio Kerberos del usuario ni detectarlo (solo Windows 10 versión 1809 y versiones posteriores).
   - El dispositivo debe tener acceso a `https://login.microsoftonline.com` en el contexto del sistema para detectar dominio para el dominio Kerberos del dominio comprobado y determinar el tipo de dominio (administrado o federado).
   - Si el entorno local requiere un proxy de salida, el administrador de TI debe asegurarse de que el contexto del sistema del dispositivo detecte el proxy de salida y se autentique en él en modo silencioso.

**Códigos de error comunes**:

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Motivo: No se puede leer el objeto SCP y obtener la información del inquilino de Azure AD.
   - Resolución: Consulte la sección sobre la [Configuración de un punto de conexión de servicio](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Motivo: Error de detección general. No se pudieron obtener los metadatos de detección de DRS.
   - Resolución: Busque el suberror a continuación para investigar más.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - Motivo: Se agotó el tiempo de espera de la operación al realizar la detección.
   - Resolución: Asegúrese de que `https://enterpriseregistration.windows.net` sea accesible en el contexto del sistema. Para más información, consulte la sección sobre los [Requisitos de conectividad de red](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Motivo: Error de detección de dominio Kerberos genérico. No se pudo determinar el tipo de dominio (administrado o federado) desde STS.
   - Resolución: Busque el suberror a continuación para investigar más.

**Códigos de suberror comunes**:

Para buscar el código de suberror del código de error de detección, use uno de los métodos siguientes.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 y versiones posteriores

Busque el subapartado "DRS Discovery Test" (Prueba de detección de DRS) en el apartado "Diagnostic Data" (Datos de diagnóstico) de la salida de estado de la unión. Esta sección solo se muestra si el dispositivo está unido a un dominio y no puede realizar una unión a Azure AD híbrido.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Versiones anteriores a Windows 10

Use registros de Visor de eventos para buscar la fase y el código de error para los errores de unión.

1. Abra los registros de eventos de **User Device Registration** (Registro de dispositivos del usuario) en el Visor de eventos; que se encuentran en **Applications and Services Log** > **Microsoft** > **Windows** > **User Device Registration** (Registros de aplicaciones y servicios > Microsoft > Windows > Registro de dispositivos del usuario)
2. Busque eventos con los siguientes identificadores de eventos: 201.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="Captura de pantalla del visor de eventos. Un evento con ID 201 está seleccionado y se muestra su información, con el código de error resaltado" border="false":::.

###### <a name="network-errors"></a>Errores de red

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Motivo: No se pudo establecer una conexión con el servidor.
   - Resolución: Garantice la conectividad de red con los recursos de Microsoft necesarios. Para más información, consulte la sección sobre los [Requisitos de conectividad de red](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Motivo: Tiempo de expiración de red general.
   - Resolución: Garantice la conectividad de red con los recursos de Microsoft necesarios. Para más información, consulte la sección sobre los [Requisitos de conectividad de red](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Motivo: La pila de red no pudo descodificar la respuesta del servidor.
   - Resolución: Asegúrese de que el proxy de red no interfiera y modifique la respuesta del servidor.

###### <a name="http-errors"></a>Errores HTTP

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Motivo: Objeto SCP configurado con un identificador de inquilino incorrecto. O no se encontraron suscripciones activas en el inquilino.
   - Resolución: Asegúrese de que el objeto SCP está configurado con el identificador de inquilino de Azure AD correcto y suscripciones activas en el inquilino.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Motivo: HTTP 503 desde el servidor DRS.
   - Resolución: El servidor no está disponible actualmente. Los intentos futuros de unión probablemente se realizarán correctamente una vez que el servidor vuelva a estar en línea.

###### <a name="other-errors"></a>Otros errores

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Motivo: No se pudo analizar el JSON de respuesta del servidor. Probablemente se deba a que el proxy devuelve HTTP 200 con una página de autenticación HTML.
   - Resolución: Si el entorno local requiere un proxy de salida, el administrador de TI debe asegurarse de que el contexto del sistema del dispositivo detecte el proxy de salida y se autentique en él en modo silencioso.

#### <a name="authentication-phase"></a>Fase de autenticación

Solo se aplica a las cuentas de dominio federado.

Motivos del error:

- No se puede obtener un token de acceso en modo silencioso para el recurso de DRS.
   - Los dispositivos de Windows 10 adquieren el token de autenticación del servicio de federación mediante la autenticación integrada de Windows en un punto de conexión activo de WS-Trust. Detalles: [Configuración del servicio de federación](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Códigos de error comunes**:

Use los registros de Visor de eventos para buscar el código de error, el código de suberror, el código de error del servidor y el mensaje de error del servidor.

1. Abra los registros de eventos de **User Device Registration** (Registro de dispositivos del usuario) en el Visor de eventos; que se encuentran en **Applications and Services Log** > **Microsoft** > **Windows** > **User Device Registration** (Registros de aplicaciones y servicios > Microsoft > Windows > Registro de dispositivos del usuario)
2. Busque eventos con el siguiente identificador de eventos: 305.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="Captura de pantalla del visor de eventos. Un evento con ID 305 está visible. Se muestra su información, con los códigos de error A D A L y el estado resaltados" border="false":::.

##### <a name="configuration-errors"></a>Errores de configuración

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Motivo: El protocolo de autenticación no es WS-Trust.
   - Resolución: El proveedor de identidades local debe ser compatible con WS-Trust.
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Motivo: El servicio de federación local no devolvió una respuesta XML.
   - Resolución: Asegúrese de que el punto de conexión MEX devuelve un XML válido. Asegúrese de que el proxy no interfiera y que devuelva respuestas que no sean XML.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Motivo: No se pudo detectar el punto de conexión para la autenticación de nombre de usuario/contraseña.
   - Resolución: Compruebe la configuración del proveedor de identidades local. Asegúrese de que los puntos de conexión de WS-Trust estén habilitados y de que la respuesta MEX contiene estos puntos de conexión correctos.

##### <a name="network-errors"></a>Errores de red

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Motivo: Tiempo de expiración de red general.
   - Resolución: Asegúrese de que `https://login.microsoftonline.com` sea accesible en el contexto del sistema. Asegúrese de que el proveedor de identidades local esté accesible en el contexto del sistema. Para más información, consulte la sección sobre los [Requisitos de conectividad de red](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Motivo: Se anuló la conexión con el punto de conexión de autenticación.
   - Resolución: Vuelva a intentarlo más tarde o intente conectarse desde una ubicación de red estable alternativa.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Motivo: No se pudo validar el certificado de Seguridad de la capa de transporte (TLS), antes Capa de sockets seguros (SSL), enviado por el servidor.
   - Resolución: Compruebe el sesgo de tiempo del cliente. Vuelva a intentarlo más tarde o intente conectarse desde una ubicación de red estable alternativa.
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Motivo: Error al intentar conectarse a `https://login.microsoftonline.com`.
   - Resolución: Compruebe la conexión a `https://login.microsoftonline.com`.

##### <a name="other-errors"></a>Otros errores

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Motivo: Azure AD no aceptó el token SAML del proveedor de identidades local.
   - Resolución: Compruebe la configuración del servidor de federación. Busque el código de error del servidor en los registros de autenticación.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Motivo: La respuesta de WS-Trust del servidor ha comunicado una excepción de error y no se pudo obtener la aserción.
   - Resolución: Compruebe la configuración del servidor de federación. Busque el código de error del servidor en los registros de autenticación.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Motivo: Se recibió un error al intentar obtener el token de acceso desde el punto de conexión del token.
   - Resolución: Busque el error subyacente en el registro de ADAL.
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Motivo: Error general de ADAL.
   - Resolución: Busque el código de suberror o de error del servidor en los registros de autenticación.

#### <a name="join-phase"></a>Fase de unión

Motivos del error:

Busque el tipo de registro y busque el código de error en la siguiente lista.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 y versiones posteriores

Busque el subapartado "Previous Registration" (Registro previo) en el apartado "Diagnostic Data" (Datos de diagnóstico) de la salida de estado de la unión. Esta sección solo se muestra si el dispositivo está unido a un dominio y no puede realizar una unión a Azure AD híbrido.
El campo "Registration Type" (Tipo de registro) denota el tipo de unión realizada.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Versiones anteriores a Windows 10

Use registros de Visor de eventos para buscar la fase y el código de error para los errores de unión.

1. Abra los registros de eventos de **User Device Registration** (Registro de dispositivos del usuario) en el Visor de eventos; que se encuentran en **Applications and Services Log** > **Microsoft** > **Windows** > **User Device Registration** (Registros de aplicaciones y servicios > Microsoft > Windows > Registro de dispositivos del usuario)
2. Busque eventos con el siguiente identificador de eventos: 204.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="Captura de pantalla del visor de eventos. Se muestra información sobre un evento con ID 204, con el código de error, el estado HTTP y el mensaje resaltados." border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>Errores HTTP devueltos por el servidor DRS

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Motivo: Se recibió una respuesta de error de DRS con el código de error: "DirectoryError"
   - Resolución: Consulte el código de error del servidor para ver posibles causas y soluciones.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Motivo: Se recibió una respuesta de error de DRS con el código de error: "AuthenticationError" y ErrorSubCode NO es "DeviceNotFound".
   - Resolución: Consulte el código de error del servidor para ver posibles causas y soluciones.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Motivo: Se recibió una respuesta de error de DRS con el código de error: "DirectoryError"
   - Resolución: Consulte el código de error del servidor para ver posibles causas y soluciones.

##### <a name="tpm-errors"></a>Errores de TPM

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Motivo: Error en la operación de TPM o esta no es válida.
   - Resolución: Probablemente se debe a una imagen de Sysprep incorrecta. Asegúrese de que la máquina desde la que se creó la imagen de Sysprep no es Azure AD joined, Hybrid Azure AD joined ni Azure AD registered.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Motivo: Error de TPM genérico.
   - Resolución: Deshabilite TPM en los dispositivos con este error. La versión 1809 de Windows 10 y las posteriores detectan automáticamente los errores de TPM y completan Hybrid Azure AD joined sin usar TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Motivo: TPM en modo FIPS no se admite actualmente.
   - Resolución: Deshabilite TPM en los dispositivos con este error. La versión 1809 de Windows detecta automáticamente los errores de TPM y completa Hybrid Azure AD joined sin usar TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Motivo: TPM bloqueado.
   - Resolución: Se trata de un error transitorio. Espere el tiempo de enfriamiento. La unión debería poder realizarse correctamente tras un tiempo. Más información en la el artículo de [Conceptos básicos de TPM](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering).

##### <a name="network-errors"></a>Errores de red

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Motivo: El tiempo de espera de red general intenta registrar el dispositivo en DRS.
   - Resolución: Compruebe la conectividad de la red a `https://enterpriseregistration.windows.net`.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Motivo: No se pudo resolver el nombre o la dirección del servidor.
   - Resolución: Compruebe la conectividad de la red a `https://enterpriseregistration.windows.net`. Asegúrese de que la resolución de DNS para el nombre de host sea precisa en el puerto N/W y en el dispositivo.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Motivo: La conexión con el servidor terminó de forma anómala.
   - Resolución: Vuelva a intentarlo más tarde o intente conectarse desde una ubicación de red estable alternativa.

##### <a name="other-errors"></a>Otros errores

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Motivo: EventID 220 está presente en los registros de eventos de registro de dispositivos de usuario. Windows no puede tener acceso al objeto de equipo en Active Directory. Un código de error de Windows se puede incluir en el evento. En el caso de los códigos de error ERROR_NO_SUCH_LOGON_SESSION (1312) y ERROR_NO_SUCH_USER (1317), estos códigos de error se relacionan con los problemas de replicación en AD local.
   - Resolución: solucione los problemas de replicación en AD. Los problemas de replicación pueden ser transitorios y pueden pasar después de un período de tiempo.

##### <a name="federated-join-server-errors"></a>Errores del servidor de unión federado

| Código de error del servidor | Mensaje de error del servidor | Razones posibles | Solución |
| --- | --- | --- | --- |
| DirectoryError | La solicitud está limitada temporalmente. Vuelva a intentarlo después de 300 segundos. | Error previsto. Posiblemente debido a la realización de varias solicitudes de registro en una sucesión rápida. | Vuelva a intentar la unión tras el tiempo de enfriamiento. |

##### <a name="sync-join-server-errors"></a>Errores del servidor de unión sincronizado

| Código de error del servidor | Mensaje de error del servidor | Razones posibles | Solución |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: no se ha encontrado del inquilino <UUID>. Este error puede producirse si no hay suscripciones activas para el inquilino. Compruébelo con el administrador de la suscripción. | El identificador de inquilino en el objeto SCP es incorrecto. | Asegúrese de que el objeto SCP está configurado con el identificador de inquilino de Azure AD correcto y suscripciones activas en el inquilino. |
| DirectoryError | No se encuentra el objeto de dispositivo por el identificador especificado. | Error previsto para la unión de sincronización. El objeto de dispositivo no se ha sincronizado de AD a Azure AD. | Espere a que se complete la sincronización de Azure AD Connect y el siguiente intento de unión después de la finalización de la sincronización resolverá el problema. |
| AuthenticationError | Comprobación del identificador de seguridad del equipo de destino | El certificado del dispositivo de Azure AD no coincide con el certificado usado para firmar el blob durante la unión de sincronización. Este error normalmente significa que la sincronización no ha terminado aún. |  Espere a que se complete la sincronización de Azure AD Connect y el siguiente intento de unión después de la finalización de la sincronización resolverá el problema. |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Paso 5: Recopilación de los registros y contacto con el soporte técnico de Microsoft

Descargue el archivo Auth.zip desde [https://cesdiagtools.blob.core.windows.net/windows/Auth.zip](https://cesdiagtools.blob.core.windows.net/windows/Auth.zip).

1. Descomprima los archivos en una carpeta como c:\temp y muévalos a la carpeta.
1. Desde una sesión de PowerShell con privilegios elevados, ejecute **.\start-auth.ps1 -v -accepteula**.
1. Use Cambiar de cuenta para ir a otra sesión con el usuario problemático.
1. Reproduzca el problema.
1. Use Cambiar de cuenta para volver a la sesión de administrador que ejecuta el seguimiento.
1. Desde la sesión de PowerShell con privilegios elevados, ejecute **.\stop-auth.ps1**.
1. Comprima y envíe la carpeta **Authlogs** de la carpeta en la que se han ejecutado los scripts.
    
## <a name="troubleshoot-post-join-authentication-issues"></a>Solución de problemas de autenticación después de la unión

### <a name="step-1-retrieve-prt-status-using-dsregcmd-status"></a>Paso 1: recuperación del estado de PRT mediante dsregcmd /status

**Para recuperar el estado del PRT:**

1. Abra un símbolo del sistema. 
   > [!NOTE] 
   > Para obtener el estado del PRT, el símbolo del sistema debe ejecutarse en el contexto del usuario que ha iniciado sesión 

2. Escriba dsregcmd /status 

3. La sección "Estado de SSO" proporciona el estado actual del PRT. 

4. Si el campo AzureAdPrt está establecido en "NO", se produjo un error al adquirir el PRT de Azure AD. 

5. Si AzureAdPrtUpdateTime es superior a 4 horas, es probable que haya un problema al actualizar el PRT. Bloquee y desbloquee el dispositivo para forzar la actualización del PRT y compruebe si la hora se actualizó.

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2020-07-12 22:57:53.000 UTC
      AzureAdPrtExpiryTime : 2019-07-26 22:58:35.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2020-07-12 22:57:54.000 UTC
   EnterprisePrtExpiryTime : 2020-07-26 22:57:54.000 UTC
    EnterprisePrtAuthority : https://corp.hybridadfs.contoso.com:443/adfs

+----------------------------------------------------------------------+
```

### <a name="step-2-find-the-error-code"></a>Paso 2: buscar el código de error 

### <a name="from-dsregcmd-output"></a>Desde la salida dsregcmd

> [!NOTE]
>  Disponible desde **Actualización de mayo de 2021 de Windows 10 (versión 21H1)** .

El campo "Estado del intento" en AzureAdPrt proporcionará el estado del intento de PRT anterior junto con otra información de depuración necesaria. Para las versiones de Windows anteriores, esta información debe extraerse de los registros analíticos y operativos de AAD.

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
```

### <a name="from-aad-analytic-and-operational-logs"></a>De registros analíticos y operativos de AAD

Use Visor de eventos para localizar las entradas de registro registradas por el complemento CloudAP de AAD durante la adquisición del PRT 

1. Abra los registros de eventos de AAD en Visor de eventos. Ubicado en Aplicación y Registro de servicios > Microsoft > Windows > AAD 

   > [!NOTE]
   > El complemento CloudAP registra los eventos de error en los registros operativos mientras los eventos de información se registran en los registros analíticos. Tanto los eventos de registro analíticos como los operativos son necesarios para solucionar incidencias. 

2. El evento 1006 de los registros analíticos denota el inicio del flujo de adquisición del PRT y el evento 1007 en los registros analíticos denota el final del flujo de adquisición del PRT. Todos los eventos de los registros de AAD (analíticos y operativos) registrados entre los eventos 1006 y 1007 se registraron como parte del flujo de adquisición del PRT. 

3. El evento 1007 registra el código de error final.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/event-viewer-prt-acquire.png" alt-text="Captura de pantalla del visor de eventos. Los eventos con los id. 1006 y 1007 se describen en rojo y el código de error final está resaltado." border="false":::

### <a name="step-3-follow-additional-troubleshooting-based-on-the-found-error-code-from-the-list-below"></a>Paso 3: siga la solución de problemas adicional, en función del código de error encontrado, en la lista siguiente

**STATUS_LOGON_FAILURE** (-1073741715/ 0xc000006d)

**STATUS_WRONG_PASSWORD** (-1073741718/ 0xc000006a)

Motivos: 
-  El dispositivo no puede conectarse al servicio de autenticación de AAD
-  Recibió una respuesta de error (HTTP 400) del servicio de autenticación de AAD o punto de conexión de WS-Trust.
> [!NOTE]
> WS-Trust es necesario para la autenticación federada

Resolución: 
-  Si el entorno local requiere un proxy de salida, el administrador de TI debe asegurarse de que la cuenta del equipo del dispositivo detecte el proxy de salida y se autentique en él en modo silencioso.
-  Los eventos 1081 y 1088 (registros operativos de AAD) contendrían el código de error del servidor y la descripción del error para los errores que se originaron en el servicio de autenticación de AAD y el punto de conexión WS-Trust, respectivamente. Los códigos de error comunes del servidor y sus resoluciones se muestran en la sección siguiente. La primera instancia del evento 1022 (registros analíticos de AAD), que precede a los eventos 1081 o 1088, contendrá la dirección URL a la que se accede.

---

**STATUS_REQUEST_NOT_ACCEPTED** (-1073741616/ 0xc00000d0)

Motivos:
-  Recibió una respuesta de error (HTTP 400) del servicio de autenticación de AAD o punto de conexión de WS-Trust.
> [!NOTE]
> WS-Trust es necesario para la autenticación federada

Resolución:
-  Los eventos 1081 y 1088 (registros operativos de AAD) contendrían el código de error del servidor y la descripción del error para los errores que se originaron en el servicio de autenticación de AAD y el punto de conexión WS-Trust, respectivamente. Los códigos de error comunes del servidor y sus resoluciones se muestran en la sección siguiente. La primera instancia del evento 1022 (registros analíticos de AAD), que precede a los eventos 1081 o 1088, contendrá la dirección URL a la que se accede.

---

**STATUS_NETWORK_UNREACHABLE** (-1073741252/ 0xc000023c)

**STATUS_BAD_NETWORK_PATH** (-1073741634/ 0xc00000be)

**STATUS_UNEXPECTED_NETWORK_ERROR** (-1073741628/ 0xc00000c4)

Motivos:
-  Recibió una respuesta de error (HTTP > 400) del servicio de autenticación de AAD o punto de conexión de WS-Trust.
> [!NOTE]
> WS-Trust es necesario para la autenticación federada
-  Problema de conectividad de red a un punto de conexión necesario

Resolución: 
-  Para errores de servidor, los eventos 1081 y 1088 (registros operativos de AAD) contendrían el código de error y la descripción del error del servicio de autenticación de AAD y el punto de conexión de WS-Trust, respectivamente. Los códigos de error comunes del servidor y sus resoluciones se muestran en la sección siguiente.
-  En el caso de los problemas de conectividad, los eventos 1022 (registros analíticos de AAD) y 1084 (registros operativos de AAD) contendrán la dirección URL a la que se accede y el subcódigo de error de la pila de red, respectivamente.

---
**STATUS_NO_SUCH_LOGON_SESSION**    (-1073741729/ 0xc000005f)

Motivos: 
-  Error de detección del dominio Kerberos del usuario porque el servicio de autenticación de AAD no pudo encontrar el dominio del usuario

Resolución:
-  El dominio del UPN del usuario debe agregarse como un dominio personalizado en AAD. El evento 1144 (registros analíticos de AAD) contendrá el UPN proporcionado.
-  Si el nombre de dominio local no es enrutable (jdoe@contoso.local), configure el identificador de inicio de sesión alternativo (AltID). Referencias: [requisitos previos](hybrid-azuread-join-plan.md) [configuring-alternate-login-id](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) 

---

**AAD_CLOUDAP_E_OAUTH_USERNAME_IS_MALFORMED**   (-1073445812/ 0xc004844c)

Motivos: 
-  El UPN del usuario no tiene el formato esperado. 
> [!NOTE] 
> - Para los dispositivos unidos de Azure AD, el UPN es el texto escrito por el usuario en LoginUI.
> - Para dispositivos unidos de Azure AD híbrido, el UPN se devuelve desde el controlador de dominio durante el proceso de inicio de sesión.

Resolución:
-  El UPN del usuario debería estar en el nombre de inicio de sesión del estilo de Internet, basado en el estándar de Internet [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). El evento 1144 (registros analíticos de AAD) contendrá el UPN proporcionado.
-  En el caso de los dispositivos unidos híbridos, asegúrese de que el controlador de dominio está configurado para devolver el UPN en el formato correcto. whoami /upn debe mostrar el UPN configurado en el controlador de dominio.
-  Si el nombre de dominio local no es enrutable (jdoe@contoso.local), configure el identificador de inicio de sesión alternativo (AltID). Referencias: [requisitos previos](hybrid-azuread-join-plan.md) [configuring-alternate-login-id](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) 

---

**AAD_CLOUDAP_E_OAUTH_USER_SID_IS_EMPTY** (-1073445822/ 0xc0048442)

Motivos:
-  Falta el SID de usuario en el token de identificador devuelto por el servicio de autenticación de AAD

Resolución: 
-  Asegúrese de que el proxy de red no interfiera y modifique la respuesta del servidor. 

---

**AAD_CLOUDAP_E_WSTRUST_SAML_TOKENS_ARE_EMPTY** (--1073445695/ 0xc00484c1)

Motivos:
-  Se ha recibido un error del punto de conexión de WS-Trust.
> [!NOTE]
> WS-Trust es necesario para la autenticación federada

Resolución: 
-  Asegúrese de que el proxy de red no interfiera y modifique la respuesta de WS-Trust.
-  El evento 1088 (registros operativos de AAD) contendría el código de error del servidor y la descripción del error del punto de conexión de WS-Trust. Los códigos de error comunes del servidor y sus resoluciones se muestran en la sección siguiente

---

**AAD_CLOUDAP_E_HTTP_PASSWORD_URI_IS_EMPTY** (-1073445749/ 0xc004848b)

Motivo:
-  Punto de conexión MEX configurado incorrectamente. La respuesta MEX no contiene direcciones URL de contraseña

Resolución: 
-  Asegúrese de que el proxy no interfiera o modifique la respuesta del servidor
-  Corrija la configuración de MEX para devolver direcciones URL válidas en respuesta.    

---

**WC_E_DTDPROHIBITED** (-1072894385/ 0xc00cee4f)

Motivo: 
-  La respuesta XML, del punto de conexión WS-TRUST, incluía una DTD. No se espera DTD en las respuestas XML y se producirá un error al analizar la respuesta si se incluye DTD.
> [!NOTE]
> WS-Trust es necesario para la autenticación federada

Resolución:
-  Corrija la configuración en el proveedor de identidades para evitar el envío de DTD en la respuesta XML . 
-   El evento 1022 (registros analíticos de AAD) contendrá la dirección URL a la que se accede y que devuelve la respuesta XML con DTD.

---

**Códigos de error comunes del servidor:**

**AADSTS50155: Error de autenticación del dispositivo**

Motivo: 
-  AAD no puede autenticar el dispositivo para emitir un PRT
-  Confirme que el dispositivo no se ha eliminado o deshabilitado en Azure Portal. [Más información](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-devices)

Resolución:
-  Siga los pasos enumerados [aquí](faq.yml#i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell--but-the-local-state-on-the-device-says-it-s-still-registered--what-should-i-do) para volver a registrar el dispositivo según el tipo de unión del dispositivo.

---

**AADSTS50034: La cuenta de usuario <Account> no existe en el <tenant id> directorio**

Motivo: 
-  AAD no puede encontrar la cuenta de usuario en el inquilino.

Resolución:
-  Asegúrese de que el usuario escribe el UPN correcto.
-  Asegúrese de que la cuenta de usuario local se está sincronizando con AAD.
-  El evento 1144 (registros analíticos de AAD) contendrá el UPN proporcionado.

---

**AADSTS50126: se ha producido un error al validar las credenciales debido a un nombre de usuario o contraseña no válidos.**

Motivo: 
-  El nombre de usuario y la contraseña especificados por el usuario en loginUI de Windows son incorrectos.
-  Si el inquilino tiene habilitada la sincronización de hash de contraseñas, el dispositivo es híbrido unido y el usuario acaba de cambiar la contraseña, es probable que la nueva contraseña no se haya sincronizado con AAD. 

Resolución:
-  Espere a que se complete la sincronización de AAD para adquirir un PRT nuevo con las nuevas credenciales. 

---

**Códigos de error de red comunes:**

**ERROR_WINHTTP_TIMEOUT\** (12002)

**ERROR_WINHTTP_NAME_NOT_RESOLVED** (12007)

**ERROR_WINHTTP_CANNOT_CONNECT** (12029)

**ERROR_WINHTTP_CONNECTION_ERROR** (12030)

Motivo: 
-  Problemas comunes relacionados con la red genérica. 

Resolución: 
-  Los eventos 1022 (registros analíticos de AAD) y 1084 (registros operativos de AAD) contendrán la dirección URL a la que se accede.
-  Si el entorno local requiere un proxy de salida, el administrador de TI debe asegurarse de que la cuenta del equipo del dispositivo detecte el proxy de salida y se autentique en él en modo silencioso

> [!NOTE]
> Otros códigos de error de red que se encuentran [aquí](/windows/win32/winhttp/error-messages).

---

### <a name="step-4-collect-logs"></a>Paso 4: recopilación de registros ###

**Registros normales**

1. Vaya a https://aka.ms/icesdptool, que descargará automáticamente un archivo .cab que contiene la herramienta de diagnóstico.
2. Ejecute la herramienta y vuelva a reproducir el escenario, una vez completada la reproducción. Finalice el proceso.
3. En el caso de los seguimientos de Fiddler, acepte las solicitudes de certificado que aparecerán.
4. El asistente le pedirá una contraseña para proteger los archivos de seguimiento. Indique una contraseña.
5. Por último, abra la carpeta donde se almacenan todos los registros recopilados. Normalmente se encuentra en una carpeta como %LOCALAPPDATA%\ElevatedDiagnostics\<numbers>
7. Póngase en contacto con el soporte técnico de contenido de latest.cab, que contiene todos los registros recopilados.

**Seguimientos de red**

> [!NOTE]
> Recopilación de seguimientos de red: (es importante NO usar Fiddler durante la reproducción)

1.  netsh trace start scenario=InternetClient_dbg capture=yes persistent=yes
2.  Bloquee y desbloquee el dispositivo. En el caso de los dispositivos híbridos unidos, espere un > minuto para permitir que se complete la tarea de adquisición de PRT.
3.  netsh trace stop
4.  Compartir nettrace.cab

---

## <a name="known-issues"></a>Problemas conocidos
- En Configuración-> Cuentas-> Access Work or School, Hybrid Azure AD joined devices (Acceder a dispositivos profesionales o educativos, híbridos unidos a Azure AD) se pueden mostrar dos cuentas diferentes, una para Azure AD y otra para AD local, cuando se conecta a zonas activas móviles o a redes Wi-Fi externas. Esto es solo un problema de la interfaz de usuario y no afecta a la funcionalidad.

## <a name="next-steps"></a>Pasos siguientes

- Continúe la [solución de problemas de dispositivos con el comando dsregcmd](troubleshoot-device-dsregcmd.md)

- [Herramienta de búsqueda de errores de Microsoft](/windows/win32/debug/system-error-code-lookup-tool)
