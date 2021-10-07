---
title: Inicio rápido para administrar recursos compartidos de archivos de Azure
description: Vea cómo crear y administrar recursos compartidos de archivos de Azure con Azure Portal, la CLI de Azure o el módulo de Azure PowerShell. Cree una cuenta de almacenamiento, cree un recurso compartido de archivos de Azure y úselo.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 09/17/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 24a84fb0bc64c4c44d056098e8c429660b88e0e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603531"
---
# <a name="quickstart-create-and-manage-azure-file-shares"></a>Inicio rápido: Creación y administración de recursos compartidos de archivos de Azure
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. Los recursos compartidos de archivos de Azure se pueden montar en Windows, Linux y macOS. En esta guía se describen los conceptos básicos sobre cómo trabajar con recursos compartidos de archivos de Azure mediante Azure Portal, la CLI de Azure o el módulo de Azure PowerShell.

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="pre-requisites"></a>Requisitos previos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si desea instalar y usar PowerShell de forma local, para esta guía se requiere la versión 0.7 del módulo de Azure PowerShell o una versión posterior. Para averiguar qué versión del módulo de Azure PowerShell está ejecutando, ejecute `Get-Module -ListAvailable Az`. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-Az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzAccount` para iniciar sesión en la cuenta de Azure.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0.4 de la CLI de Azure, o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

- De forma predeterminada, los comandos de la CLI de Azure devuelven formato JSON (notación de objetos JavaScript). JSON es el método estándar para enviar y recibir mensajes desde las API REST. Para facilitar el trabajo con las respuestas JSON, algunos de los ejemplos de este artículo usan el parámetro *query* en los comandos de la CLI de Azure. Este parámetro utiliza el [lenguaje de consulta JMESPath](http://jmespath.org/) para el análisis de datos JSON. Para aprender más acerca de cómo usar los resultados de los comandos de la CLI de Azure mediante el lenguaje de consulta JMESPath, siga las indicaciones del [tutorial de JMESPath](http://jmespath.org/tutorial.html).

---

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)


### <a name="powershell---create-a-resource-group"></a>PowerShell: creación de un grupo de recursos
Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Si ya no tiene un grupo de recursos de Azure, puede crear uno nuevo con el cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Necesita un grupo de recursos para crear una cuenta de almacenamiento.

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la región Oeste de EE. UU. 2:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

### <a name="powershell---create-a-storage-account"></a>PowerShell: creación de una cuenta de almacenamiento

Una cuenta de almacenamiento es un grupo compartido de almacenamiento que puede utilizar para implementar recursos compartidos de archivos de Azure.

En este ejemplo se crea una cuenta de almacenamiento con el cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). La cuenta de almacenamiento se llama *mystorageaccount\<random number>* y se almacena una referencia a esa cuenta de almacenamiento en la variable **$storageAcct**. Los nombres de la cuenta de almacenamiento deben ser únicos, por lo que debe usar `Get-Random` para anexar un número al nombre para que sea único. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

### <a name="cli---create-a-resource-group"></a>CLI: creación de un grupo de recursos
Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Si aún no tiene un grupo de recursos de Azure, puede utilizar el comando [az group create](/cli/azure/group) para crear uno nuevo. Necesita un grupo de recursos para crear una cuenta de almacenamiento.

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *Oeste de EE. UU. 2*:

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

### <a name="cli---create-a-storage-account"></a>CLI: creación de una cuenta de almacenamiento
Una cuenta de almacenamiento es un grupo compartido de almacenamiento en el que puede implementar recursos compartidos de archivos de Azure.

En el ejemplo siguiente se crea una cuenta de almacenamiento mediante el comando [az storage account create](/cli/azure/storage/account). Los nombres de la cuenta de almacenamiento deben ser únicos, por lo que debe usar `$RANDOM` para anexar un número al nombre para que sea único.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```


---


## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de archivos de Azure


# <a name="portal"></a>[Portal](#tab/azure-portal)

Para crear un recurso compartido de archivos de Azure:

1. Seleccione la cuenta de almacenamiento desde el panel.
1. En la página de la cuenta de almacenamiento, en la sección **Services** (Servicios), seleccione **Files** (Archivos).
    
    ![Captura de pantalla de la sección de almacenamiento de datos de la cuenta de almacenamiento. Seleccionar recursos compartidos de archivos.](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. En el menú de la parte superior de la página **File service**, haga clic en **Recurso compartido de archivos**. Se abre la página **New file share** (Nuevo recurso compartido de archivos).
1. En **Nombre**, escriba *myshare*, escriba una comilla y deje la opción **Transacción optimizada** seleccionada para **Niveles**.
1. Seleccione **Crear** para crear el recurso compartido de archivos de Azure.

Los nombres de recursos compartidos deben estar formados por letras minúsculas, números y guiones sencillos, pero no pueden empezar con un guion. Para obtener detalles completos sobre cómo asignar un nombre a recursos compartidos y archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ahora que ha creado una cuenta de almacenamiento, puede crear su primer recurso compartido de archivos de Azure. Cree un recurso compartido de archivos mediante el cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare). Este ejemplo crea un recurso compartido denominado **myshare**.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Ahora que ha creado una cuenta de almacenamiento, puede crear su primer recurso compartido de archivos de Azure. Puede crear recursos compartidos de archivos con el comando [az storage share-rm create](/cli/azure/storage/share-rm?view=azure-cli-latest&preserve-view=false#az_storage_share_rm_create). Este ejemplo crea un recurso compartido de archivos de Azure llamado **myshare**: 

```azurecli-interactive
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

---

#### <a name="create-a-directory"></a>Creación de un directorio

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para crear un nuevo directorio denominado *myDirectory* en la raíz del recurso compartido de archivos de Azure:

1. En la página **File Service** (Servicio File), seleccione el recurso compartido de archivos **myshare**. Se abre la página del recurso compartido de archivos.
1. En el menú que aparece en la parte superior de la página, seleccione **+ Add directory** (Agregar directorio) y su cuenta. Se abre la página **New directory** (Nuevo directorio).
1. Escriba *myDirectory* y, luego, haga clic en **OK** (Aceptar).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para crear un nuevo directorio denominado **myDirectory** en la raíz del recurso compartido de archivos de Azure, use el cmdlet [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory).

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para crear un nuevo directorio llamado **myDirectory** en la raíz del recurso compartido de archivos de Azure, use el comando [`az storage directory create`](/cli/azure/storage/directory):

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

---


#### <a name="upload-a-file"></a>Cargar un archivo 
# <a name="portal"></a>[Portal](#tab/azure-portal)


Para demostrar la carga de un archivo, primero debe crear o seleccionar un archivo para cargar. Puede hacerlo por cualquier medio que considere oportuno. Una vez haya seleccionado el archivo que desea cargar:

1. Seleccione el directorio **myDirectory**. Se abre el panel **myDirectory**.
1. En el menú en la parte superior, seleccione **Cargar**. Se abre el panel **Upload files** (Cargar archivos).  
    
    ![Captura de pantalla del panel de carga de archivos](media/storage-how-to-use-files-portal/upload-file-1.png)

1. Seleccione el icono de carpeta para abrir una ventana donde examinar los archivos locales. 
1. Seleccione un archivo y, después, elija **Abrir**. 
1. En la página **Cargar archivos**, compruebe el nombre de archivo y, a continuación, haga clic en **Cargar**.
1. Cuando termine, el archivo debe aparecer en la lista en la página **myDirectory**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para demostrar cómo cargar un archivo mediante el cmdlet [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent), primero es necesario crear un archivo dentro de la unidad temporal de PowerShell Cloud Shell donde cargar. 

Este ejemplo pone la fecha y hora actuales en un nuevo archivo en el disco temporal y, a continuación, lo carga en el recurso compartido de archivos.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Si está ejecutando PowerShell localmente, reemplace `~/CloudDrive/` por una ruta de acceso que exista en la máquina.

Después de cargar el archivo, puede usar el cmdlet [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) para asegurarse de que el archivo se cargó en el recurso compartido de archivos de Azure. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```


# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para demostrar cómo cargar un archivo mediante el comando [`az storage file upload`](/cli/azure/storage/file), primero cree un archivo para cargarlo en la unidad temporal de Cloud Shell. En el ejemplo siguiente, se crea y se carga el archivo:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Si está ejecutando la CLI de Azure localmente, sustituya `~/clouddrive` por una ruta de acceso que exista en la máquina.

Después de cargar el archivo, puede usar el comando [`az storage file list`](/cli/azure/storage/file) para asegurarse de que el archivo se cargó en el recurso compartido de archivos de Azure:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```


---

#### <a name="download-a-file"></a>Descarga de un archivo
# <a name="portal"></a>[Portal](#tab/azure-portal)

Para descargar una copia del archivo cargado, haga clic en él con el botón derecho. Después de seleccionar el botón de descarga, la experiencia exacta dependerá del sistema operativo y el explorador que use.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Puede usar el cmdlet [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) para descargar una copia del archivo que cargada en la unidad temporal de Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Después de descargar el archivo, puede usar el cmdlet `Get-ChildItem` para ver que el archivo se ha descargado a la unidad temporal de PowerShell Cloud Shell.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Puede usar el comando [`az storage file download`](/cli/azure/storage/file) para descargar una copia del archivo que cargó en la unidad temporal de Cloud Shell:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

---

## <a name="clean-up-resources"></a>Limpieza de recursos
# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cuando haya terminado, puede usar el cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para eliminar el grupo de recursos y todos los recursos que contiene. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Cuando haya terminado, puede usar el cmdlet [`az group delete`](/cli/azure/group) para eliminar el grupo de recursos y todos los recursos que contiene. 

```azurecli-interactive 
az group delete --name $resourceGroupName
```


---

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [¿Qué es Azure Files?](storage-files-introduction.md)