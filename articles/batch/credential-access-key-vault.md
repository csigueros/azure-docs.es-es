---
title: Uso de certificados y acceso seguro a Azure Key Vault con Batch
description: Obtenga información sobre cómo tener acceso mediante programación a las credenciales de Key Vault mediante Azure Batch.
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 20af753813ead55a3107b952a56d92b16135b523
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098970"
---
# <a name="use-certificates-and-securely-access-azure-key-vault-with-batch"></a>Uso de certificados y acceso seguro a Azure Key Vault con Batch

En este artículo, aprenderá a configurar nodos de Batch para acceder de forma segura a las credenciales almacenadas en [Azure Key Vault](../key-vault/general/overview.md).

Para autenticarse en Azure Key Vault desde un nodo de Batch, necesita:

- Una credencial de Azure Active Directory (Azure AD)
- Un certificado
- Una cuenta de Batch
- Un grupo de Batch con al menos un nodo

> [!IMPORTANT]
> Batch ahora ofrece una opción mejorada para acceder a las credenciales almacenadas en Azure Key Vault. Al crear el grupo con una identidad administrada asignada por el usuario que pueda acceder al certificado en Azure Key Vault, no es necesario enviar el contenido del certificado al servicio Batch, lo que mejora la seguridad. Se recomienda usar la rotación automática de certificados en lugar del método descrito en este tema. Para obtener más información, vea [Habilitación de la rotación automática de certificados en un grupo de Batch](automatic-certificate-rotation.md).

## <a name="obtain-a-certificate"></a>Obtener un certificado

Si aún no tiene un certificado, la forma más sencilla de obtenerlo es generar un certificado autofirmado mediante la herramienta de línea de comandos `makecert`.

Normalmente, puede encontrar `makecert` en esta ruta de acceso: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`. Abra un símbolo del sistema como administrador y vaya a `makecert` mediante el ejemplo siguiente.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

A continuación, use la herramienta `makecert` para crear archivos de certificado autofirmados denominados `batchcertificate.cer` y `batchcertificate.pvk`. El nombre común (cn) utilizado no es importante para esta aplicación, pero resulta útil si le indica en qué se usa el certificado.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch requiere un archivo de `.pfx`. Use la herramienta [pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) para convertir los archivos `.cer` y `.pvk` creados por `makecert` en un solo archivo `.pfx`.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

El acceso a Key Vault se concede a un **usuario** o a una **entidad de servicio**. Para acceder a Key Vault mediante programación, use una [entidad de servicio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) con el certificado que creó en el paso anterior. La entidad de servicio debe estar en el mismo inquilino de Azure AD que Key Vault.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

Las direcciones URL de la aplicación no son importantes, ya que solo se usan para el acceso a Key Vault.

## <a name="grant-rights-to-key-vault"></a>Concesión de derechos a Key Vault

La entidad de servicio creada en el paso anterior necesita permiso para recuperar los secretos de Key Vault. Los permisos se pueden conceder mediante [Azure Portal](../key-vault/general/assign-access-policy-portal.md) o con el siguiente comando de PowerShell.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Asignación de un certificado a una cuenta de Batch

Cree un grupo de Batch y, a continuación, vaya a la pestaña Certificado del grupo y asigne el certificado que creó. El certificado está ahora en todos los nodos de Batch.

A continuación, asigne el certificado a la cuenta de Batch. La asignación del certificado a la cuenta permite a Batch asignarlo a los grupos y, a continuación, a los nodos. La forma más fácil de hacerlo es ir a la cuenta de Batch en el portal, ir a **Certificados** y seleccionar **Agregar**. Cargue el archivo `.pfx` que ha generado anteriormente y proporcione la contraseña. Una vez completado, el certificado se agrega a la lista y puede comprobar la huella digital.

Ahora, cuando cree un grupo de Batch, puede ir a **Certificados** dentro del grupo y asignar el certificado que creó a ese grupo. Al hacerlo, asegúrese de seleccionar **LocalMachine** para la ubicación del almacén. El certificado se carga en todos los nodos de Batch del grupo.

## <a name="install-azure-powershell"></a>Instalar Azure Powershell

Si planea acceder a Key Vault mediante scripts de PowerShell en los nodos, deberá tener instalada la biblioteca de Azure PowerShell. Si los nodos tienen instalado Windows Management Framework (WMF) 5, puede usar el comando install-module para descargarlo. Si usa nodos que no tienen WMF 5, la forma más fácil de instalarlo es agrupar el archivo `.msi` de Azure PowerShell con los archivos de Batch y, a continuación, llamar al instalador como la primera parte del script de inicio de Batch. Para más información, vea este ejemplo:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Acceda a Key Vault.

Ahora ya está todo listo para acceder a Key Vault en scripts que se ejecutan en los nodos de Batch. Para acceder a Key Vault desde un script, lo único que necesita es que el script se autentique en Azure AD mediante el certificado. Para hacer esto en PowerShell, use los siguientes comandos de ejemplo. Especifique el GUID adecuado para **Huella digital**, **Id. de aplicación** (el identificador de la entidad de servicio) e **Id. del inquilino** (el inquilino en el que existe la entidad de servicio).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Una vez autenticado, acceda a KeyVault como lo haría normalmente.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Estas son las credenciales que se usan en el script.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre [Azure Key Vault](../key-vault/general/overview.md).
- Revise la [Línea de base de seguridad de Azure para Batch](security-baseline.md).
- Obtenga información acerca de las características de Batch, como la [configuración del acceso a los nodos de proceso](pool-endpoint-configuration.md), el [uso nodos de proceso de Linux](batch-linux-nodes.md)y [uso de puntos de conexión privados](private-connectivity.md).
