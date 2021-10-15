---
title: Personalización de las notificaciones de aplicación del inquilino de Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Aprenda a personalizar las notificaciones emitidas en tokens para una aplicación en un inquilino de Azure Active Directory específico.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 06/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 71677a085627f46a995530c522a3a480be188042
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350996"
---
# <a name="customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant"></a>Personalizar las notificaciones emitidas en tokens para una determinada aplicación de un inquilino

Una notificación es información que un proveedor de identidades declara sobre un usuario dentro del token que se emite para dicho usuario. Los administradores de inquilinos usan la personalización de notificaciones para personalizar las notificaciones que se emiten en tokens para una aplicación específica de su inquilino. Puede usar directivas de asignación de notificaciones para:

- seleccionar las notificaciones que se incluyen en tokens.
- crear tipos de notificación que aún no existen.
- elegir o cambiar el origen de los datos emitidos en notificaciones concretas.

La personalización de notificaciones admite la configuración de directivas de asignación de notificaciones para los protocolos WS-Fed, SAML, OAuth y OpenID Connect.

> [!NOTE]
> Esta característica reemplaza a la [personalización de notificaciones](active-directory-saml-claims-customization.md) que se ofrece a través de Azure Portal. Si en la misma aplicación personaliza las notificaciones mediante el portal y usa al mismo tiempo el método de Microsoft Graph/PowerShell que se detalla en este documento, los tokens emitidos para esa aplicación harán caso omiso de la configuración del portal. Las configuraciones realizadas mediante los métodos que se detallan en este documento no se reflejarán en el portal.

En este artículo se abordan algunos escenarios comunes que pueden ayudarle a entender cómo usar el [tipo de directiva de asignación de notificaciones](reference-claims-mapping-policy-type.md).

## <a name="get-started"></a>Introducción

En los ejemplos siguientes, va a crear, actualizar, vincular y eliminar directivas de entidades de servicio. Las directivas de asignación de notificaciones solo pueden asignarse a objetos de entidades de servicio. Si no está familiarizado con Azure AD, es conveniente que [aprenda a obtener un inquilino de Azure AD](quickstart-create-new-tenant.md) antes de continuar con estos ejemplos.

Al crear una directiva de asignación de notificaciones, también puede emitir una notificación de un atributo de extensión de esquema de directorio en los tokens. Use *ExtensionID* para el atributo de extensión en lugar de *ID* en el elemento `ClaimsSchema`.  Para más información sobre los atributos de extensión, consulte [Uso de atributos de extensión de esquema de directorio](active-directory-schema-extensions.md).

> [!NOTE]
> La [versión preliminar pública del módulo de PowerShell de Azure AD](https://www.powershellgallery.com/packages/AzureADPreview) es necesaria para configurar directivas de asignación de notificaciones. El módulo de PowerShell está en versión preliminar, mientras que el entorno de ejecución de creación de tokens y asignación de notificaciones en Azure está disponible con carácter general. Las actualizaciones del módulo de PowerShell en versión preliminar pueden requerir la actualización o modificación de los scripts de configuración. 

Para comenzar, realice uno de los pasos siguientes:

1. Descargue la [versión preliminar pública más reciente del módulo de PowerShell de Azure AD](https://www.powershellgallery.com/packages/AzureADPreview).
1. Ejecute el comando [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0-preview&preserve-view=true) para iniciar sesión en la cuenta de administrador de Azure AD. Ejecute este comando cada vez que inicie una nueva sesión.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Ejecute el siguiente comando para ver todas las directivas que se han creado en la organización. Se recomienda ejecutar este comando después de la mayoría de las operaciones en los escenarios siguientes, para comprobar que las directivas se van a crear según lo previsto.

   ``` powershell
   Get-AzureADPolicy
   ```

A continuación, cree una directiva de asignación de notificaciones y asígnela a una entidad de servicio.  Consulte estos ejemplos para ver escenarios comunes:
- [Omisión de las notificaciones básicas de los tokens](#omit-the-basic-claims-from-tokens)
- [Inclusión de EmployeeID y TenantCountry como notificaciones en los tokens](#include-the-employeeid-and-tenantcountry-as-claims-in-tokens)
- [Uso de una transformación de notificaciones en los tokens](#use-a-claims-transformation-in-tokens)

Después de crear una directiva de asignación de notificaciones, configure la aplicación para que reconozca que los tokens contendrán notificaciones personalizadas.  Para obtener más información, lea las [consideraciones sobre la seguridad](#security-considerations).

## <a name="omit-the-basic-claims-from-tokens"></a>Omisión de las notificaciones básicas de los tokens

En este ejemplo se crea una directiva que quita el [conjunto de notificaciones básicas](reference-claims-mapping-policy-type.md#claim-sets) de los tokens emitidos para entidades de servicio vinculadas.

1. Cree una directiva de asignación de notificaciones. Esta directiva, que se vincula a entidades de servicio concretas, quita el conjunto de notificaciones básicas de los tokens.
   1. Ejecute este comando para crear la directiva:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Para ver la nueva directiva y obtener su ObjectID, ejecute el siguiente comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Asigne la directiva a su entidad de servicio. También necesita obtener el valor de ObjectId de su entidad de servicio.
   1. Para ver todas las entidades de servicio de su organización, puede [consultar la Microsoft Graph API](/graph/traverse-the-graph). O bien, inicie sesión en su cuenta de Azure AD en el [Explorador de Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer).
   2. Cuando tenga el valor de ObjectId de la entidad de servicio, ejecute el siguiente comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>Inclusión de EmployeeID y TenantCountry como notificaciones en los tokens

En este ejemplo se crea una directiva que agrega EmployeeID y TenantCountry a los tokens emitidos para entidades de servicio vinculadas. EmployeeID se emite como tipo de notificación de nombre en los tokens SAML y JWT. TenantCountry se emite como tipo de notificación de país o región en los tokens SAML y JWT. En este ejemplo se sigue incluyendo el conjunto de notificaciones básicas en los tokens.

1. Cree una directiva de asignación de notificaciones. Esta directiva, que se vincula a entidades de servicio concretas, agrega las notificaciones EmployeeID y TenantCountry a los tokens.
   1. Ejecute el siguiente comando para crear la directiva:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"employeeid"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

      > [!WARNING]
      > Al definir una directiva de asignación de notificaciones para un atributo de extensión de directorio, use la propiedad `ExtensionID` en lugar de la propiedad `ID` en el cuerpo de la matriz `ClaimsSchema`.

   2. Para ver la nueva directiva y obtener su ObjectID, ejecute el siguiente comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Asigne la directiva a su entidad de servicio. También necesita obtener el valor de ObjectId de su entidad de servicio.
   1. Para ver todas las entidades de servicio de su organización, puede [consultar la Microsoft Graph API](/graph/traverse-the-graph). O bien, inicie sesión en su cuenta de Azure AD en el [Explorador de Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer).
   2. Cuando tenga el valor de ObjectId de la entidad de servicio, ejecute el siguiente comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Uso de una transformación de notificaciones en los tokens

En este ejemplo se crea una directiva que emite una notificación "JoinedData" personalizada para los tokens JWT emitidos para entidades de servicio vinculadas. Esta notificación contiene un valor creado mediante la combinación de los datos almacenados en el atributo extensionattribute1 en el objeto de usuario que incluye ".sandbox". En este ejemplo se excluye el conjunto de notificaciones básicas en los tokens.

1. Cree una directiva de asignación de notificaciones. Esta directiva, que se vincula a entidades de servicio concretas, agrega las notificaciones EmployeeID y TenantCountry a los tokens.
   1. Ejecute el siguiente comando para crear la directiva:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Para ver la nueva directiva y obtener su ObjectID, ejecute el siguiente comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Asigne la directiva a su entidad de servicio. También necesita obtener el valor de ObjectId de su entidad de servicio.
   1. Para ver todas las entidades de servicio de su organización, puede [consultar la Microsoft Graph API](/graph/traverse-the-graph). O bien, inicie sesión en su cuenta de Azure AD en el [Explorador de Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer).
   2. Cuando tenga el valor de ObjectId de la entidad de servicio, ejecute el siguiente comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

Las aplicaciones que reciben tokens se basan en el hecho de que los valores de notificación se emiten de forma autoritaria por parte de Azure AD y no se pueden alterar. Sin embargo, al modificar el contenido del token a través de las directivas de asignación de notificaciones, es posible que estas suposiciones ya no sean correctas. Las aplicaciones deben reconocer explícitamente que el creador de la directiva de asignación de notificaciones ha modificado los tokens para protegerse de las directivas de asignación de notificaciones creadas por actores malintencionados. Esto puede hacerse de una de las formas siguientes:

- [Configurar una clave de firma personalizada](#configure-a-custom-signing-key)
- O bien, [actualizar el manifiesto de aplicación](#update-the-application-manifest) para aceptar notificaciones asignadas.
 
Sin esto, Azure AD devolverá un [código de error `AADSTS50146`](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="configure-a-custom-signing-key"></a>Configurar una clave de firma personalizada

En el caso de las aplicaciones multiinquilino, se debe usar una clave de firma personalizada.  No establezca `acceptMappedClaims` en el manifiesto de la aplicación. Si configura una aplicación en Azure Portal, obtiene un objeto de registro de la aplicación y una entidad de servicio en el inquilino.  Esa aplicación usa la clave de inicio de sesión global de Azure, que no se puede usar para personalizar las notificaciones en los tokens.  Para obtener notificaciones personalizadas en los tokens, cree una clave de inicio de sesión personalizada a partir de un certificado y agréguela a la entidad de servicio.  Para fines de prueba, puede usar un certificado autofirmado. Después de configurar la clave de firma personalizada, el código de la aplicación debe [validar la clave de firma de token](#validate-token-signing-key).

Agregue la siguiente información a la entidad de servicio:

- Clave privada (como [credenciales de clave](/graph/api/resources/keycredential))
- Contraseña (como [credenciales de contraseña](/graph/api/resources/passwordcredential))
- Clave privada (como [credenciales de clave](/graph/api/resources/keycredential))

Extraiga las claves pública y privada codificadas en base 64 de la exportación de archivos PFX del certificado. Asegúrese de que el valor `keyId` de `keyCredential` que se usa para "Sign" coincide con el valor `keyId` de `passwordCredential`. Puede generar el `customkeyIdentifier` obteniendo el hash de la huella digital del certificado.

#### <a name="request"></a>Solicitud

A continuación, se muestra el formato de la solicitud PATCH HTTP para agregar una clave de firma personalizada a una entidad de servicio.  El valor "key" de la propiedad `keyCredentials` se acorta para mejorar la legibilidad. El valor está codificado en base 64. En la clave privada, el uso de la propiedad es "Sign". En la clave pública, el uso de la propiedad es "Verify".

```
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json
Authorization: Bearer {token}

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "X509CertAndPassword",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=contoso"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=contoso"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "mypassword"
        }
    ]
}
```

#### <a name="configure-a-custom-signing-key-using-powershell"></a>Configurar una clave de firma personalizada con PowerShell

Use PowerShell para [crear instancias de una aplicación cliente pública de MSAL](msal-net-initializing-client-applications.md#initializing-a-public-client-application-from-code) y usar el flujo de [concesión de código de autorización](v2-oauth2-auth-code-flow.md) para obtener un token de acceso de permiso delegado para Microsoft Graph. Use el token de acceso para llamar a Microsoft Graph y configurar una clave de firma personalizada para la entidad de servicio. Después de configurar la clave de firma personalizada, el código de la aplicación debe [validar la clave de firma de token](#validate-token-signing-key).

Para ejecutar el script, necesita lo siguiente:
1. El identificador de objeto de la entidad de servicio de la aplicación, que se encuentra en la hoja **Información general** de la entrada de la aplicación en [Aplicaciones empresariales](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) en Azure Portal.
2. Un registro de la aplicación para iniciar la sesión de un usuario y obtener un token de acceso para llamar a Microsoft Graph. Obtenga el identificador de esta aplicación (cliente) en la hoja **Información general** de la entrada de la aplicación en [Registros de aplicaciones](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) en Azure Portal. El registro de la aplicación debe tener la configuración siguiente:
    - Un identificador URI de redireccionamiento de "http://localhost" que aparezca en la configuración de la plataforma **Aplicaciones móviles y de escritorio**.
    - En **Permisos de API**, los permisos delegados de Microsoft Graph **Application.ReadWrite.All** y **User.Read** (asegúrese de conceder consentimiento de administrador a estos permisos).
3. Un usuario que inicie sesión para obtener el token de acceso de Microsoft Graph. El usuario debe tener uno de los siguientes roles administrativos de Azure AD (necesarios para actualizar la entidad de servicio):
    - Administrador de aplicaciones en la nube
    - Administrador de aplicaciones
    - Administrador global
4. Un certificado que se configurará como clave de firma personalizada para nuestra aplicación. Puede crear un certificado autofirmado o bien obtener uno de la entidad de certificación de confianza. En el script se usan los componentes del certificado siguientes:
    - clave pública (normalmente un archivo .cer)
    - clave privada en formato PKCS #12 (en un archivo .pfx)
    - contraseña de la clave privada (archivo .pfx)

> [!IMPORTANT]
> La clave privada debe estar en formato PKCS #12, ya que Azure AD no admite otros tipos de formato. Si se usa un formato incorrecto, puede producirse un error de tipo "Certificado no válido: el valor de la clave es un certificado no válido" al usar Microsoft Graph para aplicar PATCH a la entidad de servicio con un objeto `keyCredentials` que contenga la información del certificado.

```powershell

$fqdn="fourthcoffeetest.onmicrosoft.com" # this is used for the 'issued to' and 'issued by' field of the certificate
$pwd="mypassword" # password for exporting the certificate private key
$location="C:\\temp" # path to folder where both the pfx and cer file will be written to

# Create a self-signed cert
$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx"
 
# Export the public and private keys
Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile

$ClientID = "<app-id>"
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "fourthcoffeetest.onmicrosoft.com"
$redirectURL = "http://localhost" # this reply URL is needed for PowerShell Core 
[string[]] $Scopes = "https://graph.microsoft.com/.default"
$pfxpath = $pfxFile # path to pfx file
$cerpath = $cerFile # path to cer file
$SPOID = "<service-principal-id>"
$graphuri = "https://graph.microsoft.com/v1.0/serviceprincipals/$SPOID"
$password = $pwd  # password for the pfx file
 
 
# choose the correct folder name for MSAL based on PowerShell version 5.1 (.Net) or PowerShell Core (.Net Core)
 
if ($PSVersionTable.PSVersion.Major -gt 5)
    { 
        $core = $true
        $foldername =  "netcoreapp2.1"
    }
else
    { 
        $core = $false
        $foldername = "net45"
    }
 
# Load the MSAL/microsoft.identity/client assembly -- needed once per PowerShell session
[System.Reflection.Assembly]::LoadFrom((Get-ChildItem C:/Users/<username>/.nuget/packages/microsoft.identity.client/4.32.1/lib/$foldername/Microsoft.Identity.Client.dll).fullname) | out-null
  
$global:app = $null
  
$ClientApplicationBuilder = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($ClientID)
[void]$ClientApplicationBuilder.WithAuthority($("$loginURL/$tenantdomain"))
[void]$ClientApplicationBuilder.WithRedirectUri($redirectURL)
 
$global:app = $ClientApplicationBuilder.Build()
  
Function Get-GraphAccessTokenFromMSAL {
    [Microsoft.Identity.Client.AuthenticationResult] $authResult  = $null
    $AquireTokenParameters = $global:app.AcquireTokenInteractive($Scopes)
    [IntPtr] $ParentWindow = [System.Diagnostics.Process]::GetCurrentProcess().MainWindowHandle
    if ($ParentWindow)
    {
        [void]$AquireTokenParameters.WithParentActivityOrWindow($ParentWindow)
    }
    try {
        $authResult = $AquireTokenParameters.ExecuteAsync().GetAwaiter().GetResult()
    }
    catch {
        $ErrorMessage = $_.Exception.Message
        Write-Host $ErrorMessage
    }
     
    return $authResult
}
  
$myvar = Get-GraphAccessTokenFromMSAL
if ($myvar)
{
    $GraphAccessToken = $myvar.AccessToken
    Write-Host "Access Token: " $myvar.AccessToken
    #$GraphAccessToken = "eyJ0eXAiOiJKV1QiL ... iPxstltKQ"
    
 
    #  this is for PowerShell Core
    $Secure_String_Pwd = ConvertTo-SecureString $password -AsPlainText -Force
 
    # reading certificate files and creating Certificate Object
    if ($core)
    {
        $pfx_cert = get-content $pfxpath -AsByteStream -Raw
        $cer_cert = get-content $cerpath -AsByteStream -Raw
        $cert = Get-PfxCertificate -FilePath $pfxpath -Password $Secure_String_Pwd
    }
    else
    {
        $pfx_cert = get-content $pfxpath -Encoding Byte
        $cer_cert = get-content $cerpath -Encoding Byte
        # Write-Host "Enter password for the pfx file..."
        # calling Get-PfxCertificate in PowerShell 5.1 prompts for password
        # $cert = Get-PfxCertificate -FilePath $pfxpath
        $cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new($pfxpath, $password)
    }
 
    # base 64 encode the private key and public key
    $base64pfx = [System.Convert]::ToBase64String($pfx_cert)
    $base64cer = [System.Convert]::ToBase64String($cer_cert)
 
    # getting id for the keyCredential object
    $guid1 = New-Guid
    $guid2 = New-Guid
 
    # get the custom key identifier from the certificate thumbprint:
    $hasher = [System.Security.Cryptography.HashAlgorithm]::Create('sha256')
    $hash = $hasher.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($cert.Thumbprint))
    $customKeyIdentifier = [System.Convert]::ToBase64String($hash)
 
    # get end date and start date for our keycredentials
    $endDateTime = ($cert.NotAfter).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
    $startDateTime = ($cert.NotBefore).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
 
    # building our json payload
    $object = [ordered]@{    
    keyCredentials = @(       
         [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid1
            startDateTime = $startDateTime 
            type = "X509CertAndPassword"
            usage = "Sign"
            key = $base64pfx
            displayName = "CN=fourthcoffeetest" 
        },
        [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid2
            startDateTime = $startDateTime 
            type = "AsymmetricX509Cert"
            usage = "Verify"
            key = $base64cer
            displayName = "CN=fourthcoffeetest"   
        }
        )  
    passwordCredentials = @(
        [ordered]@{
            customKeyIdentifier = $customKeyIdentifier
            keyId = $guid1           
            endDateTime = $endDateTime
            startDateTime = $startDateTime
            secretText = $password
        }
    )
    }
 
    $json = $object | ConvertTo-Json -Depth 99
    Write-Host "JSON Payload:"
    Write-Output $json
 
    # Request Header
    $Header = @{}
    $Header.Add("Authorization","Bearer $($GraphAccessToken)")
    $Header.Add("Content-Type","application/json")
 
    try 
    {
        Invoke-RestMethod -Uri $graphuri -Method "PATCH" -Headers $Header -Body $json
    } 
    catch 
    {
        # Dig into the exception to get the Response details.
        # Note that value__ is not a typo.
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__ 
        Write-Host "StatusDescription:" $_.Exception.Response.StatusDescription
    }
 
    Write-Host "Complete Request"
}
else
{
    Write-Host "Fail to get Access Token"
}
```

#### <a name="validate-token-signing-key"></a>Validar la clave de firma de tokens
Las aplicaciones que tienen habilitada la asignación de notificaciones deben validar sus claves de firma de tokens mediante la anexión de `appid={client_id}` a las [solicitudes de metadatos de OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). A continuación se muestra el formato del documento de metadatos de OpenID Connect que se debe usar:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Actualización del manifiesto de aplicación

Para las aplicaciones de inquilino único, puede establecer la propiedad `acceptMappedClaims` en `true` en el [manifiesto de aplicación](reference-app-manifest.md).  Como se documenta en el [tipo de recurso apiApplication](/graph/api/resources/apiapplication#properties), esto permite que una aplicación use la asignación de notificaciones sin especificar una clave de firma personalizada. 

> [!WARNING]
> No establezca la propiedad `acceptMappedClaims` en `true` para las aplicaciones multiinquilino, ya que puede permitir que actores malintencionados creen directivas de asignación de notificaciones para la aplicación.

Esto requiere que la audiencia de tokens solicitada use un nombre de dominio comprobado del inquilino de Azure AD, lo que significa que debe asegurarse de establecer `Application ID URI` (representado por `identifierUris` en el manifiesto de aplicación), por ejemplo, en `https://contoso.com/my-api` o (simplemente mediante el nombre de inquilino predeterminado) `https://contoso.onmicrosoft.com/my-api`.

Si no usa un dominio comprobado, Azure AD devolverá un código de error `AADSTS501461` con *el mensaje "AcceptMappedClaims solo se admite para una audiencia de tokens que coincida con el GUID de la aplicación o con una audiencia dentro de los dominios comprobados del inquilino. Cambie el identificador de recursos o use una clave de firma específica de la aplicación".*

## <a name="next-steps"></a>Pasos siguientes

- Consulte el artículo de referencia sobre el [tipo de directiva de asignación de notificaciones](reference-claims-mapping-policy-type.md) para más información.
- Para obtener información sobre cómo personalizar las notificaciones emitidas en el token SAML a través de Azure Portal, vea [Procedimientos para: Personalización de las notificaciones emitidas en el token SAML para aplicaciones empresariales](active-directory-saml-claims-customization.md).
- Para más información sobre los atributos de extensión, consulte [Uso de atributos de extensión de esquema de directorio en las notificaciones](active-directory-schema-extensions.md).
