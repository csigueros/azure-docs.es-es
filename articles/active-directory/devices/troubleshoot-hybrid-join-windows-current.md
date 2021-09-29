---
title: Solución de problemas de dispositivos unidos a Azure Active Directory híbrido
description: Este artículo facilita la solución de problemas de dispositivos Windows&nbsp;10 y Windows Server 2016 unidos a Azure Active Directory híbrido.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5a89c2dd574c1910d2c35c78aee4f5ee8b561dfc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592800"
---
# <a name="troubleshoot-hybrid-azure-ad-joined-devices"></a>Solución de problemas de dispositivos unidos a Azure AD híbrido

En este artículo se proporcionan instrucciones para solucionar problemas que le ayudarán a resolver posibles problemas con dispositivos que ejecutan Windows&nbsp;10 o Windows Server 2016.

La unión a Azure Active Directory (Azure AD) híbrido admite la actualización de Windows&nbsp;10 de noviembre de 2015 y versiones posteriores.

Para solucionar problemas de otros clientes Windows, vea [Solución de problemas de dispositivos híbridos de nivel inferior unidos a Azure AD](troubleshoot-hybrid-join-windows-legacy.md).

En este artículo se da por supuesto que [ha configurado dispositivos unidos a Azure AD híbrido](hybrid-azuread-join-plan.md) para admitir los escenarios siguientes:

- Acceso condicional basado en dispositivos
- [Enterprise State Roaming](./enterprise-state-roaming-overview.md)
- [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification)


## <a name="troubleshoot-join-failures"></a>Solución de errores de unión

### <a name="step-1-retrieve-the-join-status"></a>Paso 1: Recuperación del estado de unión

1. Abra una ventana de símbolo del sistema como administrador.
1. Escriba `dsregcmd /status`.

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

Revise los campos de la tabla siguiente y asegúrese de que tengan los valores esperados:

| Campo | Valor esperado | Descripción |
| --- | --- | --- |
| DomainJoined | SÍ | Este campo indica si el dispositivo está unido a una implementación local de Active Directory. <br><br>Si el valor es *NO*, el dispositivo no puede realizar una unión a Azure AD híbrido. |
| WorkplaceJoined | No | Este campo indica si el dispositivo está registrado con Azure AD, pero como un dispositivo personal (marcado como *Unido al área de trabajo*). Este valor debe ser *NO* para un equipo unido a un dominio que esté también unido a Azure AD híbrido. <br><br>Si el valor es *SÍ*, se agrega una cuenta profesional o educativa antes de la finalización de la unión a Azure AD híbrido. En este caso, la cuenta se omite cuando se usa Windows&nbsp;10, versión 1607 o posterior. |
| AzureAdJoined | SÍ | Este campo indica si el dispositivo está unido. El valor será *SÍ* si el dispositivo es un dispositivo unido a Azure AD o un dispositivo unido a Azure AD híbrido. <br><br>Si el valor es *NO*, todavía no ha finalizado la unión a Azure AD. |
|  |  |

Continúe con los pasos siguientes para solucionar problemas adicionales.

### <a name="step-3-find-the-phase-in-which-the-join-failed-and-the-error-code"></a>Paso 3: Búsqueda de la fase en la que se ha producido el error de unión y el código de error

**Para Windows&nbsp;10, versión 1803 o posterior**

Busque la subsección "Previous Registration" (Registro previo) en la sección "Diagnostic Data" (Datos de diagnóstico) de la salida de estado de la unión. Esta sección solo se muestra si el dispositivo está unido a un dominio y no puede realizar la unión a Azure AD híbrido.

El campo "Error Phase" (Fase del error) denota la fase del error de unión y "Client ErrorCode" (Código de error del cliente), el código de error de la operación de unión.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) isn't found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

**Para versiones anteriores de Windows&nbsp;10**

Use registros de Visor de eventos para buscar la fase y el código de error para los errores de unión.

1. En el Visor de eventos, abra los registros de eventos de **User Device Registration** (Registro de dispositivos del usuario). Se almacenan en **Applications and Services Log** > **Microsoft** > **Windows** > **User Device Registration** (Registros de aplicaciones y servicios > Microsoft > Windows > Registro de dispositivos del usuario)
1. Busque eventos con los siguientes identificadores de eventos: 304, 305 y 307.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="Captura de pantalla del Visor de eventos, con el identificador de evento 304 seleccionado, su información mostrada y su código de error y fase resaltados." border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="Captura de pantalla del Visor de eventos, con el identificador de evento 305 seleccionado, su información mostrada y su código de error resaltado." border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions"></a>Paso 4: Comprobación de posibles causas y soluciones

#### <a name="pre-check-phase"></a>Fase de comprobación previa

Posibles motivos del error:

- El dispositivo no tiene línea de visión al controlador de dominio.
   - El dispositivo debe encontrarse en la red interna de la organización o en una red privada virtual con línea de visión de red a una implementación local del controlador de dominio de Active Directory.

#### <a name="discover-phase"></a>Fase de detección

Posibles motivos del error:

-  El objeto de punto de conexión de servicio está mal configurado o no se puede leer desde el controlador de dominio.
   - Se necesita un objeto de punto de conexión de servicio válido en el bosque de AD al que pertenece el dispositivo, que apunte a un nombre de dominio comprobado en Azure AD.
   - Para obtener más información, vea la sección "Configuración de un punto de conexión de servicio" de [Tutorial: Configuración de la unión a Azure Active Directory híbrido para dominios federados](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Error al conectar y capturar los metadatos de detección del punto de conexión de detección.
   - El dispositivo debe poder acceder a `https://enterpriseregistration.windows.net`, en el contexto del sistema, para detectar los puntos de conexión de autorización y registro.
   - Si el entorno local requiere un proxy de salida, el administrador de TI debe asegurarse de que la cuenta del equipo del dispositivo detecte el proxy de salida y se autentique en él en modo silencioso.
- Error al conectarse al punto de conexión del dominio kerberos de usuario y realizar la detección de dominios kerberos (solo Windows&nbsp;10, versión 1809 y posteriores).
   - El dispositivo debe poder acceder a `https://login.microsoftonline.com`, en el contexto del sistema, para realizar la detección de dominios para el dominio comprobado y determinar el tipo de dominio (administrado o federado).
   - Si en el entorno local se necesita un proxy de salida, el administrador de TI debe asegurarse de que el contexto del sistema del dispositivo puede detectar el proxy de salida y autenticarse en él en modo silencioso.

**Códigos de error comunes**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611) | No se puede leer el objeto de punto de conexión de servicio (SCP) y obtener la información del inquilino de Azure AD. | Consulte la sección [Configuración de un punto de conexión de servicio](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join). |
| **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607) | Error de detección genérico. No se han podido obtener los metadatos de detección del servicio de replicación de datos (DRS). | Para investigar más, busque el suberror en las secciones siguientes. |
| **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609) | Se ha agotado el tiempo de espera de la operación al realizar la detección. | Asegúrese de que `https://enterpriseregistration.windows.net` sea accesible en el contexto del sistema. Para obtener más información, vea la sección [Requisitos de conectividad de red](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c003d/-2145648579) | Error de detección de dominio kerberos genérico. No se pudo determinar el tipo de dominio (administrado o federado) desde STS. | Para investigar más, busque el suberror en las secciones siguientes. |
| | |

**Códigos de suberror comunes:**

Para buscar el código de suberror del código de error de detección, use uno de los métodos siguientes.

##### <a name="windowsnbsp10-version-1803-or-later"></a>Windows&nbsp;10, versión 1803 o posterior

Busque "DRS Discovery Test" (Prueba de detección de DRS) en el apartado "Diagnostic Data" (Datos de diagnóstico) de la salida de estado de la unión. Esta sección solo se muestra si el dispositivo está unido a un dominio y no puede realizar la unión a Azure AD híbrido.

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

##### <a name="earlier-windowsnbsp10-versions"></a>Versiones anteriores de Windows&nbsp;10

Use los registros del Visor de eventos para buscar la fase y el código de error para los errores de unión.

1. En el Visor de eventos, abra los registros de eventos de **User Device Registration** (Registro de dispositivos del usuario). Se almacenan en **Applications and Services Log** > **Microsoft** > **Windows** > **User Device Registration** (Registros de aplicaciones y servicios > Microsoft > Windows > Registro de dispositivos del usuario)
1. Busque el identificador de evento 201.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="Captura de pantalla del Visor de eventos, con el identificador de evento 201 seleccionado, su información mostrada y su código de error resaltado." border="false":::

**Errores de red**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867) | No se ha podido establecer la conexión con el servidor. | Garantice la conectividad de red con los recursos de Microsoft necesarios. Para más información, consulte la sección sobre los [Requisitos de conectividad de red](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **WININET_E_TIMEOUT** (0x80072ee2/-2147012894) | Tiempo de expiración de red general. | Garantice la conectividad de red con los recursos de Microsoft necesarios. Para más información, consulte la sección sobre los [Requisitos de conectividad de red](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721) | La pila de red no pudo descodificar la respuesta del servidor. | Asegúrese de que el proxy no interfiera ni modifique la respuesta del servidor. |
| | |


**Errores HTTP**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582) | El objeto de punto de conexión de servicio está configurado con el identificador de inquilino incorrecto, o bien no se ha encontrado ninguna suscripción activa en el inquilino. | Asegúrese de que el objeto de punto de conexión de servicio está configurado con el identificador de inquilino de Azure AD y las suscripciones activas correctos, o bien que el servicio está presente en el inquilino. |
| **DSREG_SERVER_BUSY** (0x801c0025/-2145648603) | HTTP 503 desde el servidor DRS. | El servidor no está disponible actualmente. Los intentos futuros de unión probablemente se realizarán correctamente una vez que el servidor vuelva a estar en línea. |
| | |


**Otros errores**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **E_INVALIDDATA** (0x8007000d/-2147024883) | No se ha podido analizar el código JSON de respuesta del servidor, probablemente porque el proxy devuelve un error HTTP 200 con una página de autorización HTML. | Si en el entorno local se necesita un proxy de salida, el administrador de TI debe asegurarse de que el contexto del sistema del dispositivo puede detectar el proxy de salida y autenticarse en él en modo silencioso. |
| | |


#### <a name="authentication-phase"></a>Fase de autenticación

Este contenido solo se aplica a las cuentas de dominio federadas.

Motivos del error:

- No se puede obtener un token de acceso en modo silencioso para el recurso de DRS.
   - Los dispositivos Windows&nbsp;10 adquieren el token de autenticación del Servicio de federación mediante la autenticación integrada de Windows en un punto de conexión activo de WS-Trust. Para obtener más información, vea [Configuración del servicio de federación](hybrid-azuread-join-manual.md#set-up-issuance-of-claims).

**Códigos de error comunes**:

Use los registros del Visor de eventos para buscar el código de error, el código de suberror, el código de error del servidor y el mensaje de error del servidor.

1. En el Visor de eventos, abra los registros de eventos de **User Device Registration** (Registro de dispositivos del usuario). Se almacenan en **Applications and Services Log** > **Microsoft** > **Windows** > **User Device Registration** (Registros de aplicaciones y servicios > Microsoft > Windows > Registro de dispositivos del usuario)
1. Busque el identificador de evento 305.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="Captura de pantalla del Visor de eventos, con el identificador de evento 305 seleccionado, su información mostrada y los código de error ADAL y el estado resaltados." border="false":::

**Errores de configuración**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057) | El protocolo de autenticación de la Biblioteca de Autenticación de Azure AD (ADAL) no es WS-Trust. | El proveedor de identidades local debe admitir WS-Trust. |
| **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036) | El servicio de federación local no ha devuelto una respuesta XML. | Asegúrese de que el punto de conexión MetadataExchange(MEX) devuelve XML válido. Asegúrese de que el proxy no interfiera ni devuelva respuestas que no sean XML. |
| **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045) | No se ha podido detectar el punto de conexión para la autenticación de nombre de usuario y contraseña. | Compruebe la configuración del proveedor de identidades local. Asegúrese de que los puntos de conexión de WS-Trust estén habilitados y de que la respuesta MEX contiene estos puntos de conexión correctos. |
| | |


**Errores de red**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614) | Tiempo de expiración de red general. | Asegúrese de que `https://login.microsoftonline.com` sea accesible en el contexto del sistema. Asegúrese de que el proveedor de identidades local sea accesible en el contexto del sistema. Para más información, consulte la sección sobre los [Requisitos de conectividad de red](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586) | Se ha anulado la conexión con el punto de conexión de autorización. | Vuelva a intentar la unión después de un tiempo o intente unirse desde otra ubicación de red estable. |
| **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441) | No se ha podido validar el certificado de Seguridad de la capa de transporte (TLS) (anteriormente denominado certificado de Capa de sockets seguros [SSL]) enviado por el servidor. | Compruebe el sesgo de tiempo del cliente. Vuelva a intentar la unión después de un tiempo o intente unirse desde otra ubicación de red estable. |
| **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587) | Error al intentar conectarse a `https://login.microsoftonline.com`. | Compruebe la conexión de red a `https://login.microsoftonline.com`. |
| | |


**Otros errores**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829) | Azure AD no ha aceptado el token SAML del proveedor de identidades local. | Compruebe la configuración del servidor de federación. Busque el código de error del servidor en los registros de autenticación. |
| **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060) | La respuesta de WS-Trust del servidor ha notificado una excepción de error y no se ha podido obtener la aserción. | Compruebe la configuración del servidor de federación. Busque el código de error del servidor en los registros de autenticación. |
| **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074) | Se recibió un error al intentar obtener el token de acceso desde el punto de conexión del token. | Busque el error subyacente en el registro de ADAL. |
| **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323) | Error general de ADAL. | Busque el código de suberror o de error del servidor en los registros de autenticación. |
| | |


#### <a name="join-phase"></a>Fase de unión

Motivos del error:

Busque el tipo de registro y el código de error en las tablas siguientes, en función de la versión de Windows 10 que use.

#### <a name="windowsnbsp10-version-1803-or-later"></a>Windows&nbsp;10, versión 1803 o posterior

Busque la subsección "Previous Registration" (Registro previo) en la sección "Diagnostic Data" (Datos de diagnóstico) de la salida de estado de la unión. Esta sección solo se muestra si el dispositivo está unido a un dominio y no puede realizar la unión a Azure AD híbrido.

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

#### <a name="earlier-windowsnbsp10-versions"></a>Versiones anteriores de Windows&nbsp;10

Use registros de Visor de eventos para buscar la fase y el código de error para los errores de unión.

1. En el Visor de eventos, abra los registros de eventos de **User Device Registration** (Registro de dispositivos del usuario). Se almacenan en **Applications and Services Log** > **Microsoft** > **Windows** > **User Device Registration** (Registros de aplicaciones y servicios > Microsoft > Windows > Registro de dispositivos del usuario)
1. Busque el identificador de evento 204.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="Captura de pantalla del Visor de eventos, con el identificador de evento 204 seleccionado y su código de error, el estado HTTP y el mensaje resaltados." border="false":::

**Errores HTTP devueltos por el servidor DRS**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630) | Se ha recibido una respuesta de error de DRS con ErrorCode: "DirectoryError". | Consulte el código de error del servidor para ver posibles causas y soluciones. |
| **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638) | Se ha recibido una respuesta de error de DRS con ErrorCode: "AuthenticationError" y ErrorSubCode *no* es "DeviceNotFound". | Consulte el código de error del servidor para ver posibles causas y soluciones. |
| **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634) | Se ha recibido una respuesta de error de DRS con ErrorCode: "DirectoryError". | Consulte el código de error del servidor para ver posibles causas y soluciones. |
| | |


**Errores de TPM**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **NTE_BAD_KEYSET** (0x80090016/-2146893802) | Se ha producido un error en la operación Módulo de plataforma segura (TPM) o no es válida. | Es probable que el error se deba a una imagen de sysprep errónea. Asegúrese de que la máquina desde la que se ha creado la imagen de sysprep no está unida a Azure AD, unida a Azure AD híbrido ni registrada en Azure AD. |
| **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641) | Error de TPM genérico. | Deshabilite TPM en los dispositivos con este error. En la versión 1809 y posteriores de Windows&nbsp;10 se detectan automáticamente los errores de TPM y completan la unión a Azure AD híbrido sin usar TPM. |
| **TPM_E_NOTFIPS** (0x80280036/-2144862154) | Actualmente no se admite TPM en modo FIPS. | Deshabilite TPM en los dispositivos con este error. En la versión 1809 de Windows 10 se detectan automáticamente los errores de TPM y se completa la unión a Azure AD híbrido sin usar TPM. |
| **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775) | TPM está bloqueado. | Se trata de un error transitorio. Espere el tiempo de recuperación. El intento de unión debe realizarse correctamente después de un tiempo. Para obtener más información, vea [Aspectos básicos de TPM](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering). |
| | |


**Errores de red**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **WININET_E_TIMEOUT** (0x80072ee2/-2147012894) | Tiempo de espera de red general para intentar registrar el dispositivo en DRS. | Compruebe la conectividad de la red a `https://enterpriseregistration.windows.net`. |
| **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889) | no se pudo resolver el nombre o la dirección del servidor. | Compruebe la conectividad de la red a `https://enterpriseregistration.windows.net`. |
| **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866) | La conexión con el servidor terminó de forma anómala. | Vuelva a intentar la unión después de un tiempo o intente unirse desde otra ubicación de red estable. |
| | |


**Otros errores**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611) | El id. de evento 220 está presente en los registros de eventos de registro de dispositivos de usuario. Windows no puede acceder al objeto de equipo en Active Directory. Es posible que se incluya un código de error de Windows en el evento. Los códigos de error ERROR_NO_SUCH_LOGON_SESSION (1312) y ERROR_NO_SUCH_USER (1317) están relacionados con problemas de replicación en la instancia local de Active Directory. | Solucione los problemas de replicación en Active Directory. Es posible que estos problemas de replicación sean transitorios y desaparezcan después de un tiempo. |
| | |


**Errores del servidor de unión federado**:

| Código de error del servidor | Mensaje de error del servidor | Razones posibles | Solución |
| --- | --- | --- | --- |
| DirectoryError | La solicitud está limitada temporalmente. Vuelva a intentarlo después de 300 segundos. | Se trata de un error esperado, posiblemente porque se han realizado varias solicitudes de registro de forma rápida. | Vuelva a intentar la unión tras el tiempo de recuperación |
| | |

**Errores del servidor de unión sincronizado**:

| Código de error del servidor | Mensaje de error del servidor | Razones posibles | Solución |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: no se ha encontrado del inquilino `UUID`. Es posible que se produzca este error si no hay suscripciones activas para el inquilino. Compruébelo con el administrador de la suscripción. | El identificador de inquilino del objeto de punto de conexión de servicio es incorrecto. | Asegúrese de que el objeto de punto de conexión de servicio está configurado con el identificador de inquilino de Azure AD y las suscripciones activas correctos, o bien que el servicio está presente en el inquilino. |
| DirectoryError | No se encuentra el objeto de dispositivo por el identificador especificado. | Es un error esperado para la unión de sincronización. El objeto de dispositivo no se ha sincronizado de AD a Azure AD. | Espere a que finalice la sincronización de Azure AD Connect y el siguiente intento de unión después de la finalización de la sincronización resolverá el problema. |
| AuthenticationError | Comprobación del identificador de seguridad del equipo de destino | El certificado del dispositivo de Azure AD no coincide con el que se usa para iniciar sesión en el blob durante la unión de sincronización. Normalmente, este error significa que la sincronización todavía no ha finalizado. |  Espere a que se complete la sincronización de Azure AD Connect y el siguiente intento de unión después de la finalización de la sincronización resolverá el problema. |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Paso 5: Recopilación de los registros y contacto con el soporte técnico de Microsoft

1. [Descargue el archivo *Auth.zip*](https://cesdiagtools.blob.core.windows.net/windows/Auth.zip).

1. Extraiga los archivos en una carpeta como *c:\temp* y, después, vaya a la carpeta.
1. Desde una sesión de Azure PowerShell con privilegios elevados, ejecute `.\start-auth.ps1 -v -accepteula`.
1. Seleccione **Cambiar cuenta** para ir a otra sesión con el usuario con el problema.
1. Reproduzca el problema.
1. Seleccione **Cambiar cuenta** para volver a la sesión de administrador que ejecuta el seguimiento.
1. Desde la sesión de PowerShell con privilegios elevados, ejecute `.\stop-auth.ps1`.
1. Comprima y envíe la carpeta *Authlogs* desde la carpeta en la que se han ejecutado los scripts.
    
## <a name="troubleshoot-post-join-authentication-issues"></a>Solución de problemas de autenticación después de la unión

### <a name="step-1-retrieve-the-prt-status-by-using-dsregcmd-status"></a>Paso 1: Recuperación del estado de PRT mediante `dsregcmd /status`

1. Abra una ventana de símbolo del sistema. 
   > [!NOTE] 
   > Para obtener el estado del Token de actualización principal (PRT), abra la ventana de símbolo del sistema en el contexto del usuario que ha iniciado sesión. 

1. Ejecute `dsregcmd /status`. 

   En la sección "Estado de SSO" se proporciona el estado actual del PRT. 

   Si el campo AzureAdPrt está establecido en *NO*, se ha producido un error al adquirir el estado del PRT de Azure AD. 

1. Si AzureAdPrtUpdateTime es superior a cuatro horas, es probable que haya un problema con la actualización del PRT. Bloquee y desbloquee el dispositivo para forzar la actualización del PRT y, después, compruebe si se ha actualizado la hora.

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

**En la salida de `dsregcmd`**

> [!NOTE]
>  La salida está disponible en la actualización de mayo de 2021 de Windows&nbsp;10 (versión 21H1).

El campo "Attempt Status" (Estado del intento) en el campo "AzureAdPrt" proporcionará el estado del intento de PRT anterior, junto con otra información de depuración necesaria. Para versiones anteriores de Windows, extraiga la información de los registros de análisis y operativos de Azure AD.

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

**Desde los registros de análisis y operativos de Azure AD**

Use el Visor de eventos para buscar las entradas de registro registradas por el complemento Azure AD CloudAP durante la adquisición del PRT. 

1. En el Visor de eventos, abra los registros de eventos de Azure AD. Se almacenan en **Applications and Services Log** > **Microsoft** > **Windows** > **User Device Registration** (Registros de aplicaciones y servicios > Microsoft > Windows > Registro de dispositivos del usuario) 

   > [!NOTE]
   > El complemento CloudAP registra eventos de error en los registros operativos y eventos de información en los registros de análisis. Tanto los eventos de registros de análisis como los operativos son necesarios para solucionar incidencias. 

1. El evento 1006 de los registros de análisis denota el inicio del flujo de adquisición del PRT y, el evento 1007, el final. Todos los eventos de los registros de Azure AD (de análisis y operativos) registrados entre los eventos 1006 y 1007 se han registrado como parte del flujo de adquisición del PRT. 

1. El evento 1007 registra el código de error final.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/event-viewer-prt-acquire.png" alt-text="Captura de pantalla del Visor de eventos, con los identificadores de evento 1006 y 1007 seleccionados y el código de error final resaltado." border="false":::

### <a name="step-3-troubleshoot-further-based-on-the-found-error-code"></a>Paso 3: Solución de problemas adicionales, en función del código de error encontrado

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **STATUS_LOGON_FAILURE** (-1073741715/ 0xc000006d)<br>**STATUS_WRONG_PASSWORD** (-1073741718/ 0xc000006a) | <li>El dispositivo no se puede conectar al servicio de autenticación de Azure AD.<li>Se ha recibido una respuesta de error (HTTP 400) del servicio de autenticación de Azure AD o del punto de conexión de WS-Trust.<br>**Nota**: WS-Trust es necesario para la autenticación federada. | <li>Si el entorno local requiere un proxy de salida, el administrador de TI debe asegurarse de que la cuenta del equipo del dispositivo detecte el proxy de salida y se autentique en él en modo silencioso.<li>Los eventos 1081 y 1088 (registros operativos de Azure AD) contendrían el código de error del servidor de los errores que se originan en el servicio de autenticación de Azure AD y la descripción de los que se originan en el punto de conexión de WS-Trust. Los códigos de error comunes del servidor y sus resoluciones se muestran en la sección siguiente. La primera instancia del evento 1022 (registros de análisis de Azure AD), que precede a los eventos 1081 o 1088, contendrá la dirección URL a la que se accede. |
| **STATUS_REQUEST_NOT_ACCEPTED** (-1073741616/ 0xc00000d0) | Se ha recibido una respuesta de error (HTTP 400) del servicio de autenticación de Azure AD o del punto de conexión de WS-Trust.<br>**Nota**: WS-Trust es necesario para la autenticación federada. | Los eventos 1081 y 1088 (registros operativos de Azure AD) contendrían el código de error del servidor y la descripción del error para los errores que se originan en el servicio de autenticación de Azure AD y el punto de conexión WS-Trust, respectivamente. Los códigos de error comunes del servidor y sus resoluciones se muestran en la sección siguiente. La primera instancia del evento 1022 (registros de análisis de Azure AD), que precede a los eventos 1081 o 1088, contendrá la dirección URL a la que se accede. |
| **STATUS_NETWORK_UNREACHABLE** (-1073741252/ 0xc000023c)<br>**STATUS_BAD_NETWORK_PATH** (-1073741634/ 0xc00000be)<br>**STATUS_UNEXPECTED_NETWORK_ERROR** (-1073741628/ 0xc00000c4) | <li>Se ha recibido una respuesta de error (HTTP > 400) del servicio de autenticación de Azure AD o del punto de conexión de WS-Trust.<br>**Nota**: WS-Trust es necesario para la autenticación federada.<li>Problema de conectividad de red a un punto de conexión necesario. | <li>Para los errores de servidor, los eventos 1081 y 1088 (registros operativos de Azure AD) contendrían el código de error del servicio de autenticación de Azure AD y la descripción del error del punto de conexión de WS-Trust. Los códigos de error comunes del servidor y sus resoluciones se muestran en la sección siguiente.<li>En el caso de los problemas de conectividad, el evento 1022 (registros de análisis de Azure AD) contendrá la dirección URL a la que se accede y el evento 1084 (registros operativos de Azure AD) contendrá el código de suberror de la pila de red. |
| **STATUS_NO_SUCH_LOGON_SESSION**    (-1073741729/ 0xc000005f) | Error de detección del dominio kerberos del usuario porque el servicio de autenticación de Azure AD no ha podido encontrar el dominio del usuario. | <li>El dominio del UPN del usuario se debe agregar como un dominio personalizado en Azure AD. El evento 1144 (registros de análisis de Azure AD) contendrá el UPN proporcionado.<li>Si el nombre de dominio local no es enrutable (jdoe@contoso.local), configure un identificador de inicio de sesión alternativo (AltID). Referencias: [Requisitos previos](hybrid-azuread-join-plan.md); [Configuración del identificador de inicio de sesión alternativo](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). |
| **AAD_CLOUDAP_E_OAUTH_USERNAME_IS_MALFORMED**   (-1073445812/ 0xc004844c) | El UPN del usuario no tiene el formato esperado.<br>**Notas**:<li>Para los dispositivos unidos a Azure AD, el UPN es el texto escrito por el usuario en LoginUI. <li>Para dispositivos unidos a Azure AD híbrido, el UPN se devuelve desde el controlador de dominio durante el proceso de inicio de sesión. | <li>El UPN del usuario debería estar en el nombre de inicio de sesión del estilo de Internet, según el estándar de Internet [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). El evento 1144 (registros de análisis de Azure AD) contendrá el UPN proporcionado.<li>En el caso de los dispositivos unidos híbridos, asegúrese de que el controlador de dominio está configurado para devolver el UPN en el formato correcto. En el controlador de dominio, `whoami /upn` debe mostrar el UPN configurado.<li>Si el nombre de dominio local no es enrutable (jdoe@contoso.local), configure el identificador de inicio de sesión alternativo (AltID). Referencias: [Requisitos previos](hybrid-azuread-join-plan.md); [Configuración del identificador de inicio de sesión alternativo](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). |
| **AAD_CLOUDAP_E_OAUTH_USER_SID_IS_EMPTY** (-1073445822/ 0xc0048442) | Falta el SID de usuario en el token de identificador devuelto por el servicio de autenticación de Azure AD. | Asegúrese de que el proxy no interfiera ni modifique la respuesta del servidor. |
| **AAD_CLOUDAP_E_WSTRUST_SAML_TOKENS_ARE_EMPTY** (--1073445695/ 0xc00484c1) | Se ha recibido un error del punto de conexión de WS-Trust.<br>**Nota**: WS-Trust es necesario para la autenticación federada. | <li>Asegúrese de que el proxy no interfiera ni modifique la respuesta de WS-Trust.<li>El evento 1088 (registros operativos de Azure AD) contendría el código de error del servidor y la descripción del error del punto de conexión de WS-Trust. Los códigos de error comunes del servidor y sus resoluciones se muestran en la sección siguiente. |
| **AAD_CLOUDAP_E_HTTP_PASSWORD_URI_IS_EMPTY** (-1073445749/ 0xc004848b) | El punto de conexión MEX está configurado incorrectamente. La respuesta MEX no contiene direcciones URL de contraseña. | <li>Asegúrese de que el proxy no interfiera ni modifique la respuesta del servidor.<li>Corrija la configuración de MEX para devolver direcciones URL válidas en respuesta. |
| **WC_E_DTDPROHIBITED** (-1072894385/ 0xc00cee4f) | La respuesta XML, del punto de conexión de WS-Trust, incluía una definición de tipo de documento (DTD). No se esperaba una DTD en las respuestas XML y se producirá un error al analizar la respuesta si se incluye una DTD.<br>**Nota**: WS-Trust es necesario para la autenticación federada. | <li>Corrija la configuración en el proveedor de identidades para evitar el envío de una DTD en la respuesta XML.<li>El evento 1022 (registros de análisis de Azure AD) contendrá la dirección URL a la que se accede y que devuelve una respuesta XML con una DTD. |
| | |


**Códigos de error comunes del servidor:**

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **AADSTS50155: Error de autenticación del dispositivo** | <li>Azure AD no puede autenticar el dispositivo para emitir un PRT.<li>Confirme que el dispositivo no se ha eliminado o deshabilitado en Azure Portal. Para obtener más información sobre este problema, vea [Preguntas más frecuentes sobre la administración de dispositivos de Azure Active Directory](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-devices). | Siga las instrucciones para este problema en [Preguntas más frecuentes sobre la administración de dispositivos de Azure Active Directory](faq.yml#i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell--but-the-local-state-on-the-device-says-it-s-still-registered--what-should-i-do) a fin de volver a registrar el dispositivo según su tipo de unión. |
| **AADSTS50034: La cuenta de usuario `Account` no existe en el `tenant id` directorio** | Azure AD no puede encontrar la cuenta de usuario en el inquilino. | <li>Asegúrese de que el usuario escribe el UPN correcto.<li>Asegúrese de que la cuenta de usuario local se sincroniza con Azure AD.<li>El evento 1144 (registros de análisis de Azure AD) contendrá el UPN proporcionado. |
| **AADSTS50126: se ha producido un error al validar las credenciales debido a un nombre de usuario o contraseña no válidos.** | <li>El nombre de usuario y la contraseña escritos por el usuario en LoginUI de Windows son incorrectos.<li>Si el inquilino tiene habilitada la sincronización de hash de contraseñas, el dispositivo es de unión híbrida y el usuario acaba de cambiar la contraseña, es probable que la nueva contraseña no se haya sincronizado con Azure AD. | Para adquirir un PRT nuevo con las nuevas credenciales, espere a que finalice la sincronización de contraseñas de Azure AD. |
| | |


**Códigos de error de red comunes**:

| Código de error | Motivo | Solución |
| --- | --- | --- |
| **ERROR_WINHTTP_TIMEOUT\** (12002)<br>**ERROR_WINHTTP_NAME_NOT_RESOLVED** (12007)<br>**ERROR_WINHTTP_CANNOT_CONNECT** (12029)<br>**ERROR_WINHTTP_CONNECTION_ERROR** (12030) | Problemas comunes relacionados con la red general. | <li>Los eventos 1022 (registros de análisis de Azure AD) y 1084 (registros operativos de Azure AD) contendrán la dirección URL a la que se accede.<li>Si el entorno local requiere un proxy de salida, el administrador de TI debe asegurarse de que la cuenta del equipo del dispositivo detecte el proxy de salida y se autentique en él en modo silencioso.<br><br>Obtenga [códigos de error de red](/windows/win32/winhttp/error-messages) adicionales. |
| | |


### <a name="step-4-collect-logs"></a>Paso 4: recopilación de registros

**Registros normales**

1. Vaya a https://aka.ms/icesdptool para descargar automáticamente un archivo *.cab* que contiene la herramienta de diagnóstico.
1. Ejecute la herramienta y reproduzca el escenario.
1. En el caso de los seguimientos de Fiddler, acepte las solicitudes de certificado que aparecen.
1. El asistente le pedirá una contraseña para proteger los archivos de seguimiento. Indique una contraseña.
1. Por último, abra la carpeta donde se almacenan todos los registros recopilados, como *%LOCALAPPDATA%\ElevatedDiagnostics\numbers*.
1. Póngase en contacto con el servicio de soporte técnico con el contenido del archivo *.cab* más reciente.

**Seguimientos de red**

> [!NOTE]
> Cuando recopile seguimientos de red, es importante *no* usar Fiddler durante la reproducción.

1.  Ejecute `netsh trace start scenario=internetClient_dbg capture=yes persistent=yes`.
1. Bloquee y desbloquee el dispositivo. En el caso de los dispositivos de unión híbrida, espere un minuto o más para permitir que se complete la tarea de adquisición del PRT.
1. Ejecute `netsh trace stop`.
1. Comparta el archivo *nettrace.cab* con el servicio de soporte técnico.

---

## <a name="known-issues"></a>Problemas conocidos
- Si se conecta a una zona activa para dispositivos móviles o a una red Wi-Fi externa, y va a **Configuración** > **Cuentas** > **Access Work or School** (Acceder a trabajo o escuela), es posible que los dispositivos unidos a Azure AD híbrido muestren dos cuentas diferentes, una para Azure AD y otra para AD en el entorno local. Se trata solo de un problema de la interfaz de usuario y no afecta a la funcionalidad.

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de dispositivos con el comando `dsregcmd`](troubleshoot-device-dsregcmd.md).
- Vaya a la [Herramienta de búsqueda de errores de Microsoft](/windows/win32/debug/system-error-code-lookup-tool).
