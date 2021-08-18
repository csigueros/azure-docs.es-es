---
title: Imposibilidad de acceder a los archivos de Data Lake Storage en Azure HDInsight
description: Imposibilidad de acceder a los archivos de Data Lake Storage en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.openlocfilehash: 47130e97c46a799c8f344df37baaa3c43b140587
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299850"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Imposibilidad de acceder a los archivos de Data Lake Storage en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue-acl-verification-failed"></a>Problema: Error de verificación de ACL

Recibirá un mensaje de error similar al siguiente:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Causa

Es posible que el usuario haya revocado los permisos de la entidad de servicio (SP) en archivos o carpetas.

### <a name="resolution"></a>Solución

1. Compruebe que el SP tiene los permisos "x" para atravesar la ruta de acceso. Para más información, consulte [Permisos](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html). Comando `dfs` de ejemplo para comprobar el acceso a los archivos o las carpetas en la cuenta de Data Lake Storage:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Configure los permisos necesarios para acceder a la ruta de acceso en función de la operación de lectura y escritura que se va a realizar. Consulte aquí los permisos necesarios para varias operaciones del sistema de archivos.

---

## <a name="issue-service-principal-certificate-expiry"></a>Problema: Expiración del certificado de entidad de servicio

Recibirá un mensaje de error similar al siguiente:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Causa

El certificado proporcionado para el acceso a la entidad de servicio puede haber expirado.

1. SSH en el nodo principal. Compruebe el acceso a la cuenta de almacenamiento mediante el siguiente comando `dfs`:

    ```
    hdfs dfs -ls /
    ```

1. Confirme que el mensaje de error es similar a la salida siguiente:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Obtenga una de las direcciones URL de `core-site.xml property` - `fs.azure.datalake.token.provider.service.urls`.

1. Ejecute el siguiente comando CURL para recuperar el token de OAuth.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. La salida de una entidad de servicio válida debe ser similar a la siguiente:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Si el certificado de la entidad de servicio ha expirado, la salida tendrá un aspecto similar al siguiente:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Cualquier otro error relacionado con Azure Active Directory o los errores relacionados con el certificado se pueden reconocer haciendo ping en la dirección URL de la puerta de enlace para obtener el token de OAuth.

1. Si recibe el siguiente error al intentar obtener acceso a ADLS desde el clúster de HDI, siga los pasos mencionados anteriormente para comprobar si el certificado ha expirado.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Solución

Cree un nuevo certificado o asigne uno existente mediante el siguiente script de PowerShell:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

Para asignar un certificado existente, cree un certificado, y tenga la contraseña y el archivo. pfx listos. Asocie el certificado a la entidad de servicio con la que se creó el clúster mediante el elemento AppId preparado.

Ejecute el comando de PowerShell después de sustituir los parámetros por los valores reales.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]