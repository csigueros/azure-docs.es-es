---
title: Recursos compartidos de archivos SMB en Azure Files
description: Obtenga información sobre los recursos compartidos de archivos hospedados en Azure Files con el protocolo Bloque de mensajes del servidor (SMB).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 15a3064ab02a9c83a489219210be32ee99134e47
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723348"
---
# <a name="smb-file-shares-in-azure-files"></a>Recursos compartidos de archivos SMB en Azure Files
Azure Files ofrece dos protocolos estándar del sector para el montaje de recursos compartidos de archivos de Azure: el protocolo [Bloque de mensajes del servidor (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) y el protocolo [Network File System (NFS)](https://en.wikipedia.org/wiki/Network_File_System) (versión preliminar). Azure Files le permite seleccionar el protocolo del sistema de archivos más adecuado para su carga de trabajo. Los recursos compartidos de archivos de Azure no admiten el acceso a un recurso compartido de archivos de Azure individual con los protocolos SMB y NFS, aunque se pueden crear recursos compartidos de archivos SMB y NFS dentro de la misma cuenta de almacenamiento. En general, Azure Files ofrece recursos compartidos de archivos de nivel empresarial que se pueden escalar verticalmente para satisfacer sus necesidades de almacenamiento y a los que pueden acceder simultáneamente miles de clientes.

En este artículo se tratan los recursos compartidos de archivos SMB de Azure. Para obtener información sobre los recursos compartidos de archivos NFS de Azure, consulte el artículo sobre [Recursos compartidos de archivos NFS en Azure Files](files-nfs-protocol.md).

## <a name="common-scenarios"></a>Escenarios frecuentes
Los recursos compartidos de archivos SMB se usan para diversas aplicaciones, incluidos recursos compartidos de archivos de usuario final y recursos compartidos de archivos que respaldan las bases de datos y las aplicaciones. Los recursos compartidos de archivos SMB se suelen usar en los escenarios siguientes:

- Recursos compartidos de archivos de usuario final, como recursos compartidos de equipo, directorios particulares, etc.
- Almacenamiento auxiliar para aplicaciones basadas en Windows, como bases de datos de SQL Server o aplicaciones de línea de negocio escritas para las API del sistema de archivos local Win32 o .NET. 
- Desarrollo de aplicaciones y servicios nuevos, sobre todo si esa aplicación o servicio tiene como requisito una E/S aleatoria y almacenamiento jerárquico.

## <a name="features"></a>Características
Azure Files admite las características principales de SMB y Azure que se requieren para las implementaciones de producción de recursos compartidos de archivos SMB:

- Listas de control de acceso discrecional (DACL) y unión a un dominio de AD.
- Copia de seguridad sin servidor integrada con Azure Backup.
- Aislamiento de red con puntos de conexión privados de Azure.
- Alto rendimiento de red con SMB multicanal (solo recursos compartidos de archivos premium).
- Cifrado de canal SMB que incluye AES-256-GCM, AES-128-GCM y AES-128-CCM.
- Compatibilidad con versiones anteriores mediante instantáneas de recurso compartido integradas de VSS.
- Eliminación temporal automática en los recursos compartidos de archivos de Azure para evitar eliminaciones accidentales.
- Opcionalmente, recursos compartidos de archivos accesibles desde Internet con SMB 3.0 y versiones posteriores seguro para Internet.

Los recursos compartidos de archivos SMB pueden montarse directamente en el entorno local o también se pueden [almacenar en caché en el entorno local con Azure File Sync](../file-sync/file-sync-introduction.md).  

## <a name="security"></a>Seguridad
Todos los datos almacenados en Azure Files se cifran en reposo mediante el cifrado de servicio de almacenamiento (SSE) de Azure. El cifrado del servicio de almacenamiento funciona de forma similar a BitLocker en Windows: los datos se cifran bajo el nivel del sistema de archivos. Dado que los datos se cifran bajo el sistema de archivos del recurso compartido de archivos de Azure, ya que se codifican en el disco, no es necesario tener acceso a la clave subyacente en el cliente para leer o escribir en dicho recurso compartido. El cifrado en reposo se aplica a los protocolos SMB y NFS.

De forma predeterminada, todas las cuentas de Azure Storage tienen habilitado el cifrado en tránsito. Esto significa que, al montar un recurso compartido de archivos a través de SMB (o acceder a él a través del protocolo FileREST), Azure Files solo permitirá la conexión si se realiza con una versión SMB 3.x con cifrado o HTTPS. Los clientes que no admiten SMB 3.x con cifrado del canal SMB no podrán montar el recurso compartido de archivos de Azure si está habilitado el cifrado en tránsito. 

Azure Files admite AES-256-GCM con SMB 3.1.1, líder del sector, cuando se usa con Windows 10, versión 21H1. SMB 3.1.1 también admite AES-128-GCM y SMB 3.0 admite AES-128-CCM. AES-128-GCM se negocia de forma predeterminada en Windows 10, versión 21H1, por motivos de rendimiento.

Puede deshabilitar el cifrado en tránsito para una cuenta de almacenamiento de Azure. Cuando el cifrado está deshabilitado, Azure Files también permite el uso de SMB 2.1 y SMB 3.x sin cifrado. La razón principal para deshabilitar el cifrado en tránsito es admitir una aplicación heredada que debe ejecutarse en un sistema operativo anterior, como Windows Server 2008 R2 o una distribución de Linux anterior. Azure Files solo permite conexiones SMB 2.1 dentro de la misma región de Azure del recurso compartido de archivos de Azure. Un cliente SMB 2.1 fuera de la región de Azure del recurso compartido de archivos de Azure (por ejemplo, en un entorno local o en una región de Azure diferente) no podrá acceder al recurso compartido de archivos.

## <a name="smb-protocol-settings"></a>Configuración del protocolo SMB
Azure Files ofrece varias configuraciones para el protocolo SMB.

- **SMB multicanal**: habilite o deshabilite SMB multicanal (solo recursos compartidos de archivos premium). Para obtener información sobre cómo habilitar SMB multicanal, consulte el documento para [habilitar SMB multicanal en una cuenta de almacenamiento FileStorage](storage-files-enable-smb-multichannel.md). El valor predeterminado de SMB multicanal está deshabilitado.
- **Versiones de SMB**: qué versiones de SMB se permiten. Las versiones admitidas del protocolo son SMB 3.1.1, SMB 3.0 y SMB 2.1. De forma predeterminada, se admiten todas las versiones de SMB, salvo en el caso de SMB 2.1 si está habilitada la opción para "requerir tránsito seguro", ya que SMB 2.1 no admite el cifrado en tránsito.
- **Métodos de autenticación**: qué métodos de autenticación SMB se permiten. Los métodos de autenticación admitidos son NTLMv2 y Kerberos. De forma predeterminada, se admiten todos los métodos de autenticación. La eliminación de NTLMv2 impide el uso de la clave de la cuenta de almacenamiento para montar el recurso compartido de archivos de Azure.
- **Cifrado de vales Kerberos**: qué algoritmos de cifrado se permiten. Los algoritmos de cifrado admitidos son RC4-HMAC y AES-256.
- **Cifrado del canal SMB**: qué algoritmos de cifrado del canal SMB se permiten. Los algoritmos de cifrado admitidos son AES-256-GCM, AES-128-GCM y AES-128-CCM.

La configuración del protocolo SMB puede alternarse a través del módulo de Azure PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)
La configuración del protocolo SMB se puede ver y cambiar mediante PowerShell o la CLI. Seleccione la pestaña que quiera para ver los pasos sobre cómo obtener y establecer la configuración del protocolo SMB.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para obtener o establecer la configuración del protocolo SMB, debe proporcionar una referencia a una cuenta de almacenamiento. Puede hacerlo directamente, si proporciona los nombres del grupo de recursos y de la cuenta de almacenamiento, o bien proporcionar un objeto de cuenta de almacenamiento que haya adquirido del cmdlet `Get-AzStorageAccount`. En los ejemplos siguientes se usa este último método para obtener y establecer la configuración del protocolo SMB.

No olvide reemplazar `<resource-group>` y `<storage-account>` por los valores adecuados para su entorno antes de ejecutar estos comandos de PowerShell.

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName
```

Para obtener la configuración del protocolo SMB, use el cmdlet `Get-AzStorageFileServiceProperty`. Si no ha modificado nunca la configuración del protocolo SMB, los valores que devuelva el cmdlet serán NULL. Los valores NULL devueltos deben interpretarse como que "la configuración predeterminada está en vigor". Para hacer esto más fácil, los comandos de PowerShell siguientes reemplazan los valores NULL por los valores predeterminados en lenguaje natural. 

```PowerShell
# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
$smbMultichannelEnabled = $false
$smbProtocolVersions = "SMB2.1", "SMB3.0", "SMB3.1.1"
$smbAuthenticationMethods = "NTLMv2", "Kerberos"
$smbKerberosTicketEncryption = "RC4-HMAC", "AES-256"
$smbChannelEncryption = "AES-128-CCM", "AES-128-GCM", "AES-256-GCM"

Get-AzStorageFileServiceProperty -StorageAccount $storageAccount | `
    Select-Object -Property `
        ResourceGroupName, `
        StorageAccountName, `
        @{ 
            Name = "SmbMultichannelEnabled"; 
            Expression = { 
                if ($null -eq $_.ProtocolSettings.Smb.Multichannel.Enabled) { 
                    $smbMultichannelEnabled 
                } else { 
                    $_.ProtocolSettings.Smb.Multichannel.Enabled 
                } 
            } 
        }, 
        @{ 
            Name = "SmbProtocolVersions";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.Versions) {
                    [String]::Join(", ", $smbProtocolVersions)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.Versions)
                }
            }
        },
        @{
            Name = "SmbChannelEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.ChannelEncryption) {
                    [String]::Join(", ", $smbChannelEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.ChannelEncryption)
                }
            }
        },
        @{
            Name = "SmbAuthenticationMethods";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.AuthenticationMethods) {
                    [String]::Join(", ", $smbAuthenticationMethods)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.AuthenticationMethods)
                }
            }
        },
        @{
            Name = "SmbKerberosTicketEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.KerberosTicketEncryption) {
                    [String]::Join(", ", $smbKerberosTicketEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.KerberosTicketEncryption)
                }
            }
        }
```

Puede que quiera modificar la configuración del protocolo SMB en función de los requisitos de seguridad, rendimiento y compatibilidad de su organización. El comando de PowerShell siguiente restringe los recursos compartidos de archivos SMB exclusivamente a las opciones más seguras.

> [!Important]  
> Si los recursos compartidos de archivos SMB de Azure se restringen exclusivamente a las opciones más seguras, es posible que algunos clientes no puedan conectarse si no cumplen los requisitos. Por ejemplo, AES-256-GCM se introdujo como opción para el cifrado del canal SMB a partir de Windows 10, versión 21H1. Esto significa que los clientes más antiguos que no admiten AES-256-GCM no podrán conectarse.

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -SmbAuthenticationMethod "Kerberos" `
    -SmbChannelEncryption "AES-256-GCM" `
    -SmbKerberosTicketEncryption "AES-256" `
    -SmbProtocolVersion "SMB3.1.1"
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
Para obtener la configuración del protocolo SMB, use el comando `az storage account file-service-properties show`. Si no ha modificado nunca la configuración del protocolo SMB, los valores que devuelva el comando serán NULL. Los valores NULL devueltos deben interpretarse como que "la configuración predeterminada está en vigor". Para hacer esto más fácil, los comandos de Bash siguientes reemplazan los valores NULL por los valores predeterminados en lenguaje natural. 

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

# Values to be replaced
replaceSmbMultichannel="\"smbMultichannelEnabled\": null"
replaceSmbProtocolVersion="\"smbProtocolVersions\": null"
replaceSmbChannelEncryption="\"smbChannelEncryption\": null"
replaceSmbAuthenticationMethods="\"smbAuthenticationMethods\": null"
replaceSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": null"

# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
defaultSmbMultichannelEnabled="\"smbMultichannelEnabled\": false"
defaultSmbProtocolVersions="\"smbProtocolVersions\": \"SMB2.1;SMB3.0;SMB3.1.1\""
defaultSmbChannelEncryption="\"smbChannelEncryption\": \"AES-128-CCM;AES-128-GCM;AES-256-GCM\""
defaultSmbAuthenticationMethods="\"smbAuthenticationMethods\": \"NTLMv2;Kerberos\""
defaultSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": \"RC4-HMAC;AES-256\""

# Build JMESPath query string
query="{" 
query="${query}smbMultichannelEnabled: protocolSettings.smb.multichannel.enabled,"
query="${query}smbProtocolVersions: protocolSettings.smb.versions,"
query="${query}smbChannelEncryption: protocolSettings.smb.channelEncryption,"
query="${query}smbAuthenticationMethods: protocolSettings.smb.authenticationMethods,"
query="${query}smbKerberosTicketEncryption: protocolSettings.smb.kerberosTicketEncryption"
query="${query}}"

# Get protocol settings from the Azure Files FileService object
protocolSettings=$(az storage account file-service-properties show \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "${query}")

# Replace returned values if null with default values 
protocolSettings="${protocolSettings/$replaceSmbMultichannel/$defaultSmbMultichannelEnabled}"
protocolSettings="${protocolSettings/$replaceSmbProtocolVersion/$defaultSmbProtocolVersion}"
protocolSettings="${protocolSettings/$replaceSmbChannelEncryption/$defaultSmbChannelEncryption}"
protocolSettings="${protocolSettings/$replaceSmbAuthenticationMethods/$defaultSmbAuthenticationMethods}"
protocolSettings="${protocolSettings/$replaceSmbKerberosTicketEncryption/$defaultSmbKerberosTicketEncryption}"

# Print returned settings
echo $protocolSettings
```

Puede que quiera modificar la configuración del protocolo SMB en función de los requisitos de seguridad, rendimiento y compatibilidad de su organización. El comando de la CLI de Azure siguiente restringe los recursos compartidos de archivos SMB exclusivamente a las opciones más seguras.

> [!Important]  
> Si los recursos compartidos de archivos SMB de Azure se restringen exclusivamente a las opciones más seguras, es posible que algunos clientes no puedan conectarse si no cumplen los requisitos. Por ejemplo, AES-256-GCM se introdujo como opción para el cifrado del canal SMB a partir de Windows 10, versión 21H1. Esto significa que los clientes más antiguos que no admiten AES-256-GCM no podrán conectarse.

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --versions "SMB3.1.1" \
    --channel-encryption "AES-256-GCM" \
    --auth-methods "Kerberos" \
    --kerb-ticket-encryption "AES-256"
```
---

## <a name="limitations"></a>Limitaciones
Los recursos compartidos de archivos SMB en Azure Files admiten un subconjunto de características compatibles con el protocolo SMB y el sistema de archivos NTFS. Aunque la mayoría de los casos de uso y de las aplicaciones no requieren estas características, es posible que algunas aplicaciones no funcionen correctamente con Azure Files si dependen de características no admitidas. No se admiten las siguientes características:

- [SMB directo](/windows-server/storage/file-server/smb-direct)  
- Concesión de directorio SMB
- [VSS para recursos compartidos de archivos SMB](/archive/blogs/clausjor/vss-for-smb-file-shares) (esto permite a los proveedores de VSS vaciar sus datos en el recurso compartido de archivos SMB antes de tomar una instantánea).
- Flujos de datos alternativos
- Atributos ampliados
- Identificadores de objeto
- [Vínculos físicos](/windows/win32/fileio/hard-links-and-junctions)
- [Enlaces simbólicos](/windows/win32/fileio/creating-symbolic-links)  
- [Puntos de repetición de análisis](/windows/win32/fileio/reparse-points)  
- [Archivos dispersos](/windows/win32/fileio/sparse-files)
- [Nombres de archivos cortos (alias 8.3)](/windows/win32/fileio/naming-a-file#short-vs-long-names)  
- [Compresión](https://techcommunity.microsoft.com/t5/itops-talk-blog/smb-compression-deflate-your-io/ba-p/1183552)

## <a name="regional-availability"></a>Disponibilidad regional
Los recursos compartidos de archivos SMB de Azure están disponibles en todas las regiones de Azure, incluidas todas las públicas y soberanas. Los recursos compartidos de archivos SMB premium están disponibles en [un subconjunto de regiones](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Pasos siguientes
- [Planeamiento de una implementación de Azure Files](storage-files-planning.md)
- [Creación de un recurso compartido de archivos de Azure](storage-how-to-create-file-share.md)
- Montaje de recursos compartidos de archivos SMB en su sistema operativo preferido:
    - [Montaje de recursos compartidos de archivos SMB en Windows](storage-how-to-use-files-windows.md)
    - [Montaje de recursos compartidos de archivos SMB en Linux](storage-how-to-use-files-linux.md)
    - [Montaje de recursos compartidos de archivos SMB en macOS](storage-how-to-use-files-mac.md)