---
title: Uso de PowerShell para administrar listas de control de acceso en Azure Data Lake Storage Gen2
description: Use cmdlets de PowerShell para administrar listas de control de acceso (ACL) en cuentas de almacenamiento que tengan habilitado un espacio de nombres jerárquico (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d0d7a6c1cb91daa4eb75f5e980355d866346c8e3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555534"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Uso de PowerShell para administrar listas de control de acceso en Azure Data Lake Storage Gen2

En este artículo se muestra cómo usar PowerShell para obtener, establecer y actualizar las listas de control de acceso de directorios y archivos.

La herencia de ACL ya está disponible para los nuevos elementos secundarios que se crean en un directorio primario. Ahora bien, también se pueden agregar, actualizar y quitar listas de control de acceso de forma recursiva en los elementos secundarios existentes de un directorio primario sin tener que realizar estos cambios individualmente para cada elemento secundario.

[Referencia](/powershell/module/Az.Storage/) | [Ejemplos de ACL recursivas](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D) | [Enviar comentarios](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. Para obtener más información, vea [Obtención de una evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

- Una cuenta de almacenamiento que tenga habilitado el espacio de nombres jerárquico (HNS). Siga [estas](create-data-lake-storage-account.md) instrucciones para crear uno.

- CLI de Azure versión `2.6.0` o posterior.

- Uno de los siguientes permisos de seguridad:

  - Una [entidad de seguridad](../../role-based-access-control/overview.md#security-principal) aprovisionada de Azure Active Directory a la que se ha asignado el rol [Propietario de datos de blobs de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) en el ámbito del contenedor de destino, el grupo de recursos primario o la suscripción.

  - El usuario propietario del contenedor o directorio de destino al que va a aplicar la configuración de ACL. Para establecer listas de control de acceso de forma recursiva, se incluyen todos los elementos secundarios en el contenedor o el directorio de destino.

  - Clave de la cuenta de almacenamiento.

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

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opción 1: Obtención de autorización mediante Azure Active Directory (AD)

> [!NOTE]
> Si usa Azure Active Directory (Azure AD) para autorizar el acceso, asegúrese de que la entidad de seguridad tenga asignado el [rol Propietario de datos de blobs de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para obtener más información sobre cómo se aplican los permisos de ACL y las consecuencias de cambiarlos, vea [Modelo de control de acceso de Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

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

## <a name="get-acls"></a>Obtención de listas de control de acceso

Obtenga la ACL de un directorio o de un archivo con el cmdlet `Get-AzDataLakeGen2Item`.

En este ejemplo se obtiene la ACL del directorio raíz de un **contenedor** y luego se imprime la ACL en la consola.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

En este ejemplo se obtiene la ACL de un **directorio** y luego se imprime la ACL en la consola.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

En este ejemplo se obtiene la ACL de un **archivo** y luego se imprime la ACL en la consola.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

En la siguiente imagen se muestra la salida después de obtener la ACL de un directorio.

![Obtención de una salida de ACL para el directorio](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

En este ejemplo, el usuario propietario tiene permisos de lectura, escritura y ejecución. El grupo propietario tiene permisos de solo lectura y ejecución. Para más información sobre las listas de control de acceso, vea [Control de acceso en Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Establecimiento de listas de control de acceso

Cuando *establece* una ACL, debe **reemplazar** toda la ACL, incluidas todas sus entradas. Si quiere cambiar el nivel de permiso de una entidad de seguridad o agregar una nueva entidad de seguridad a la ACL sin que esto afecte a otras entradas existentes, debe *actualizar* la ACL en su lugar. Para actualizar una ACL en lugar de reemplazarla, consulte la sección [Actualización de ACL](#update-acls) de este artículo.

Si elige *establecer* la lista de control de acceso, debe agregar una entrada para el usuario propietario, otra para el grupo propietario y una tercera para los demás usuarios. Para más información sobre el usuario propietario, el grupo propietario y el resto de usuarios, consulte [Usuarios e identidades](data-lake-storage-access-control.md#users-and-identities).

Esta sección le muestra cómo:

- Establecimiento de una ACL
- Establecimiento de listas de control de acceso de forma recursiva

### <a name="set-an-acl"></a>Establecimiento de una ACL

Use el cmdlet `set-AzDataLakeGen2ItemAclObject` para crear una ACL para el usuario propietario, el grupo propietario u otros usuarios. Luego, use el cmdlet `Update-AzDataLakeGen2Item` para confirmar la ACL.

En este ejemplo se establece la ACL en el directorio raíz de un **contenedor** del usuario propietario, el grupo propietario o de otros usuarios, y luego se imprime la ACL en la consola.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw-
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

En este ejemplo se establece la ACL en un **directorio** del usuario propietario, el grupo propietario o de otros usuarios, y luego se imprime la ACL en la consola.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw-
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> Si desea establecer una entrada de la ACL **predeterminada**, use el parámetro **-DefaultScope** cuando ejecute el comando **Set-AzDataLakeGen2ItemAclObject**. Por ejemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

En este ejemplo se establece la ACL en un **archivo** del usuario propietario, el grupo propietario o de otros usuarios, y luego se imprime la ACL en la consola.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw-
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> Para establecer la ACL de un grupo o usuario específico, use sus identificadores de objeto correspondientes. Por ejemplo, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` o `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

En la siguiente imagen se muestra la salida después de establecer la ACL de un archivo.

![Obtención de una salida de ACL para el archivo](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

En este ejemplo, el usuario propietario y el grupo propietario tienen permisos de solo lectura y escritura. Los demás usuarios tienen permisos de escritura y ejecución. Para más información sobre las listas de control de acceso, vea [Control de acceso en Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Establecimiento de listas de control de acceso de forma recursiva

Establezca listas de control de acceso de forma recursiva mediante el cmdlet **Set-AzDataLakeGen2AclRecursive**.

En este ejemplo se establece la ACL de un directorio denominado `my-parent-directory`. En estas entradas se concede al usuario propietario permisos de lectura, escritura y ejecución, permisos solo de lectura y ejecución al grupo propietario, y ningún permiso al resto. La última entrada de ACL de este ejemplo proporciona a un usuario específico con el identificador de objeto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" permisos de lectura y ejecución.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Si desea establecer una entrada de la ACL **predeterminada**, use el parámetro **-DefaultScope** cuando ejecute el comando **Set-AzDataLakeGen2ItemAclObject**. Por ejemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Para ver un ejemplo que establezca ACL de forma recursiva en lotes especificando un tamaño de lote, consulte el artículo de referencia [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

## <a name="update-acls"></a>Actualización de ACL

Cuando *actualiza* una ACL, modifica la ACL en lugar de reemplazarla. Por ejemplo, puede agregar una nueva entidad de seguridad a la ACL sin que esto afecte a otras entidades de seguridad que se enumeran en la ACL. Para reemplazar la ACL en lugar de actualizarla, consulte la sección [Establecimiento de listas de control de acceso](#set-acls) de este artículo.

Para actualizar una ACL, cree un nuevo objeto ACL con la entrada de la ACL que quiera actualizar y, a continuación, use ese objeto en la operación para actualizar la ACL. No debe obtener la ACL existente, simplemente proporcione las entradas de la ACL que se vayan a actualizar.

Esta sección le muestra cómo:

- Actualización de una ACL
- Actualización de listas de control de acceso de forma recursiva

### <a name="update-an-acl"></a>Actualización de una ACL

En primer lugar, obtenga la lista de control de acceso. Luego, use el cmdlet `set-AzDataLakeGen2ItemAclObject` para agregar o actualizar una entrada de ACL. Luego, use el cmdlet `Update-AzDataLakeGen2Item` para confirmar la lista de control de acceso.

En este ejemplo se crea o actualiza la lista de control de acceso en un **directorio** para un usuario.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Si desea actualizar una entrada de la ACL **predeterminada**, use el parámetro **-DefaultScope** cuando ejecute el comando **Set-AzDataLakeGen2ItemAclObject**. Por ejemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Actualización de listas de control de acceso de forma recursiva

Actualice las listas de control de acceso de forma recursiva mediante el cmdlet **Update-AzDataLakeGen2AclRecursive**.

En este ejemplo se actualiza una entrada de ACL con permiso de escritura.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Para establecer la ACL de un grupo o usuario específico, use sus identificadores de objeto correspondientes. Por ejemplo, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` o `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Para ver un ejemplo que actualice ACL de forma recursiva en lotes especificando un tamaño de lote, consulte el artículo de referencia [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive).

## <a name="remove-acl-entries"></a>Eliminación de entradas de ACL

Esta sección le muestra cómo:

- Eliminación de una entrada de ACL
- Eliminación de las entradas de ACL de forma recursiva

### <a name="remove-an-acl-entry"></a>Eliminación de una entrada de ACL

En este ejemplo se quita una entrada de una lista de control de acceso existente.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>Eliminación de las entradas de ACL de forma recursiva

Puede quitar una o varias entradas de ACL de forma recursiva. Para quitar una entrada de ACL, cree un nuevo objeto de ACL para la entrada de ACL que se va a quitar y, a continuación, use ese objeto en la operación para quitar la ACL. No debe obtener la ACL existente; simplemente proporcione las entradas de la ACL que se van a quitar.

Quite las entradas de ACL mediante el cmdlet **Remove-AzDataLakeGen2AclRecursive**.

En este ejemplo se quita una entrada de ACL del directorio raíz del contenedor.

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---"

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Si desea quitar una entrada de la ACL **predeterminada**, use el parámetro **-DefaultScope** cuando ejecute el comando **Set-AzDataLakeGen2ItemAclObject**. Por ejemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Para ver un ejemplo que quite ACL de forma recursiva en lotes especificando un tamaño de lote, consulte el artículo de referencia [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive).

## <a name="recover-from-failures"></a>Recuperación de errores

Al modificar listas de control de acceso de forma recursiva, es posible que se produzcan errores del entorno de ejecución o de los permisos.

En el caso de los errores de tiempo de ejecución, reinicie el proceso desde el principio. Los errores de permisos pueden producirse si la entidad de seguridad no tiene permisos suficientes para modificar la ACL de un directorio o archivo que se encuentra en la jerarquía de directorios que se está modificando. Solucione el problema de permisos y, a continuación, elija reanudar el proceso desde el punto de error mediante un token de continuación, o bien reinicie el proceso desde el principio. No tiene que usar el token de continuación si prefiere reiniciar desde el principio. Puede volver a aplicar las entradas de ACL sin ningún efecto negativo.

Este ejemplo se devuelven los resultados a la variable y, a continuación, se canalizan las entradas con errores a una tabla con formato.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft
```

Según la salida de la tabla, puede corregir todos los errores de permisos y, a continuación, reanudar la ejecución mediante el token de continuación.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Para ver un ejemplo que establezca ACL de forma recursiva en lotes especificando un tamaño de lote, consulte el artículo de referencia [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

Si desea que el proceso se complete sin que se interrumpa por errores de permisos, puede especificarlo.

En este ejemplo se usa el parámetro `ContinueOnFailure` para que la ejecución continúe incluso si la operación encuentra un error de permiso.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft)
```

Para ver un ejemplo que establezca ACL de forma recursiva en lotes especificando un tamaño de lote, consulte el artículo de referencia [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Consulte también

- [Problemas conocidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Cmdlets de PowerShell de almacenamiento](/powershell/module/az.storage)
- [Modelo de control de acceso de Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listas de control de acceso en Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
