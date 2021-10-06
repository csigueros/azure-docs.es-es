---
title: Uso de PowerShell para administrar datos en Azure Data Lake Storage Gen2
description: Use cmdlets de PowerShell para administrar directorios y archivos en cuentas de almacenamiento que tengan habilitado un espacio de nombres jerárquico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f03426ab734101657ad5609fcd7782f7c75a6f9b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553210"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Uso de PowerShell para administrar directorios y archivos en Azure Data Lake Storage Gen2

En este artículo se explica cómo usar PowerShell para crear y administrar directorios y archivos en cuentas de almacenamiento que tengan habilitado un espacio de nombres jerárquico.

Para obtener información sobre cómo obtener, establecer y actualizar las listas de control de acceso (ACL) de directorios y archivos, consulte [Uso de PowerShell para administrar listas de control de acceso en Azure Data Lake Storage Gen2](data-lake-storage-acl-powershell.md).

[Referencia](/powershell/module/Az.Storage/) | [Asignación de Gen1 a Gen2](#gen1-gen2-map) | [Envíenos su comentarios](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Requisitos previos

- Suscripción a Azure. Para obtener más información, vea [Obtención de una evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

- Una cuenta de almacenamiento que tenga habilitado un espacio de nombres jerárquico. Siga [estas](create-data-lake-storage-account.md) instrucciones para crear uno.

- Debe tener instalada la versión de .NET Framework 4.7.2 o superior. Para obtener más información, vea [Descarga de .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).

- La versión de PowerShell `5.1` o posterior.

## <a name="install-the-powershell-module"></a>Instalación del módulo de PowerShell

1. Use el siguiente comando para comprobar que la versión de PowerShell que ha instalado es `5.1` o posterior.

   ```powershell
   echo $PSVersionTable.PSVersion.ToString()
   ```

   Para actualizar la versión de PowerShell, vea [Actualización de Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell).

2. Instale el módulo **Az.Storage**.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Para más información sobre cómo instalar módulos de PowerShell, vea [Instalación del módulo de Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="connect-to-the-account"></a>Conexión con la cuenta

Elija cómo quiere que sus comandos obtengan autorización para la cuenta de almacenamiento.

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>Opción 1: Obtención de autorización mediante Azure Active Directory (Azure AD)

Con este enfoque, el sistema garantiza que su cuenta de usuario tiene los permisos de ACL y las asignaciones de control de acceso basado en rol de Azure (Azure RBAC) apropiados.

1. Abra una ventana de comandos de Windows PowerShell y, a continuación, inicie sesión en su suscripción de Azure con el comando `Connect-AzAccount` y siga las instrucciones de la pantalla.

   ```powershell
   Connect-AzAccount
   ```

2. Si su identidad está asociada a más de una suscripción, establezca la suscripción activa en la suscripción de la cuenta de almacenamiento en la que quiere crear y administrar directorios. En este ejemplo, reemplace el valor de marcador de posición `<subscription-id>` por el identificador de la suscripción.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

3. Obtenga el contexto de la cuenta de almacenamiento.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opción 2: Obtención de autorización mediante la clave de cuenta de almacenamiento

Con este enfoque, el sistema no comprueba los permisos ACL o de RBAC. Obtenga el contexto de la cuenta de almacenamiento mediante una clave de cuenta.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="create-a-container"></a>Crear un contenedor

Un contenedor actúa como sistema de archivos para sus archivos. Puede crear uno con el cmdlet `New-AzStorageContainer`.

En este ejemplo se crea un contenedor denominado `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Creación de un directorio

Cree una referencia de directorio con el cmdlet `New-AzDataLakeGen2Item`.

En este ejemplo se agrega un directorio denominado `my-directory` a un contenedor.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

En este ejemplo se agrega el mismo directorio, pero también se establecen los permisos, la máscara de permisos, los valores de propiedad y los valores de metadatos.

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Visualización de las propiedades de directorio

En este ejemplo se obtiene un directorio con el cmdlet `Get-AzDataLakeGen2Item` y, después, se imprimen los valores de propiedad en la consola.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

> [!NOTE]
> Para obtener el directorio raíz del contenedor, omita el parámetro `-Path`.

## <a name="rename-or-move-a-directory"></a>Cambio de nombre o traslado de un directorio

Cambie el nombre de un directorio o muévalo con el cmdlet `Move-AzDataLakeGen2Item`.

En este ejemplo se cambia el nombre de un subdirectorio de `my-directory` a `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Use el parámetro `-Force` si desea sobrescribir sin preguntar.

En este ejemplo se mueve un directorio denominado `my-directory` a un subdirectorio de `my-directory-2` denominado `my-subdirectory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Eliminación de un directorio

Elimine un directorio con el cmdlet `Remove-AzDataLakeGen2Item`.

En este ejemplo se elimina un directorio denominado `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Puede usar el parámetro `-Force` para quitar el archivo sin preguntar.

## <a name="download-from-a-directory"></a>Descarga de un directorio

Descargue un archivo de un directorio con el cmdlet `Get-AzDataLakeGen2ItemContent`.

En este ejemplo se descarga un archivo denominado `upload.txt` de un directorio denominado `my-directory`.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Lista del contenido del directorio

Muestre el contenido de un directorio con el cmdlet `Get-AzDataLakeGen2ChildItem`. Puede usar el parámetro opcional `-OutputUserPrincipalName` para obtener el nombre (en lugar del identificador de objeto) de los usuarios.

En este ejemplo se muestra el contenido de un directorio denominado `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

En el ejemplo siguiente se enumeran las propiedades `ACL`, `Permissions`, `Group` y `Owner` de cada elemento del directorio. Se necesita el parámetro `-FetchProperty` para obtener los valores de la propiedad `ACL`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Para mostrar el contenido del directorio raíz del contenedor, omita el parámetro `-Path`.

## <a name="upload-a-file-to-a-directory"></a>Carga de un archivo en un directorio

Cargue un archivo en un directorio con el cmdlet `New-AzDataLakeGen2Item`.

En este ejemplo se carga un archivo denominado `upload.txt` en un directorio denominado `my-directory`.

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force
```

En este ejemplo se carga el mismo archivo, pero después se establecen los permisos, la máscara de permisos, los valores de propiedad y los valores de metadatos del archivo de destino. En este ejemplo también se imprimen estos valores en la consola.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Para cargar un archivo en el directorio raíz del contenedor, omita el parámetro `-Path`.

## <a name="show-file-properties"></a>Visualización de las propiedades del archivo

En este ejemplo se obtiene un archivo con el cmdlet `Get-AzDataLakeGen2Item` y, después, se imprimen los valores de propiedad en la consola.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Eliminación de un archivo

Elimine un archivo con el cmdlet `Remove-AzDataLakeGen2Item`.

En este ejemplo se elimina un archivo denominado `upload.txt`.

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath
```

Puede usar el parámetro `-Force` para quitar el archivo sin preguntar.

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Asignación de Gen1 a Gen2

En la siguiente tabla se refleja la asignación de cmdlets usados con Data Lake Storage Gen1 a cmdlets de Data Lake Storage Gen2.

|Cmdlet de Data Lake Storage Gen1| Cmdlet de Data Lake Storage Gen2| Notas |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|De forma predeterminada, el cmdlet Get-AzDataLakeGen2ChildItem solo muestra los elementos secundarios de primer nivel. El parámetro -Recurse muestra elementos secundarios de forma recursiva. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Los elementos de salida del cmdlet Get-AzDataLakeGen2Item tiene estas propiedades: Acl, Owner, Group y Permission.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|El cmdlet Get-AzDataLakeGen2FileContent descarga el contenido del archivo en el archivo local.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Este cmdlet carga el contenido del archivo nuevo desde un archivo local.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|El cmdlet Update-AzDataLakeGen2Item actualiza solo un único elemento, y no de forma recursiva. Si desea actualizar de forma recursiva, muestre los elementos mediante el cmdlet Get-AzDataLakeStoreChildItem y, a continuación, canalice al cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|El cmdlet Get-AzDataLakeGen2Item notificará un error si el elemento no existe.|

## <a name="see-also"></a>Consulte también

- [Problemas conocidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Cmdlets de PowerShell de almacenamiento](/powershell/module/az.storage)
