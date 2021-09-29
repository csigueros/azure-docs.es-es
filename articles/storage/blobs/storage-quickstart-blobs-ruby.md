---
title: 'Inicio rápido: Biblioteca cliente de Azure Blob Storage (Ruby)'
description: Cree una cuenta de almacenamiento y un contenedor en Azure Blob Storage. Use la biblioteca cliente de Storage para Ruby para crear un blob, descargar un blob o enumerar los blobs de un contenedor.
author: normesta
ms.author: normesta
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: c34c042598e94cb2a86d1a068caca40bd3240b29
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587487"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Inicio rápido: Biblioteca cliente de Azure Blob Storage para Ruby

Aprenda a usar Ruby para crear, descargar y enumerar los blobs de un contenedor en Microsoft Azure Blob Storage.

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Asegúrese de tener instalados los siguientes requisitos previos adicionales:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Biblioteca de Azure Storage para Ruby](https://github.com/azure/azure-storage-ruby), mediante el [paquete RubyGem](https://rubygems.org/gems/azure-storage-blob):

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>Descarga de la aplicación de ejemplo

La [aplicación de ejemplo](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) utilizada en esta guía de inicio rápido es una aplicación Ruby básica.

Use [Git](https://git-scm.com/) para descargar una copia de la aplicación en su entorno de desarrollo. Este comando clona el repositorio en una máquina local:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

Vaya a la carpeta *storage-blobs-ruby-quickstart* y abra el archivo *example.rb* en un editor de código.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.

Especifique el nombre y la clave de la cuenta de Storage para crear una instancia de [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) para la aplicación.

El siguiente código del archivo *example.rb* crea una instancia de un nuevo objeto [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService). Reemplace los valores *accountname* y *accountkey* con el nombre de cuenta y la clave.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>Ejecución del ejemplo

En el ejemplo se crea un contenedor en Blob Storage, se crea un blob en el contenedor, se enumeran los blobs del contenedor y se descarga el blob en un archivo local.

Ejecute el ejemplo. A continuación se muestra un ejemplo de la salida que se genera al ejecutarse la aplicación:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Si presionar Entrar para continuar, el programa de ejemplo elimina el contenedor de almacenamiento y el archivo local. Antes de continuar, compruebe la carpeta *Documents* del archivo descargado.

También puede usar el [Explorador de Azure Storage](https://storageexplorer.com) para ver los archivos de su cuenta de Storage. El Explorador de Azure Storage es una herramienta gratuita multiplataforma que permite acceder a la información de la cuenta de almacenamiento.

Después de haber comprobado los archivos, presione la tecla Entrar para eliminar los archivos de prueba y finalizar la demostración. Abra el archivo *example.rb* para ver el código.

## <a name="understand-the-sample-code"></a>Descripción del código de ejemplo

A continuación, se explicará el código de ejemplo, con el fin de que sepa cómo funciona.

### <a name="get-references-to-the-storage-objects"></a>Obtención de referencias a los objetos de almacenamiento

Lo primero que hay que hacer es crear las instancias de los objetos usados para acceder a Blob Storage y administrarlo. Estos objetos dependen unos de otros. Cada uno es utilizado por el siguiente de la lista.

- Cree una instancia del objeto [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) de Azure Storage para configurar las credenciales de conexión.
- Cree el objeto [Container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container), que representa el contenedor al que accede. Los contenedores se usan para organizar los blobs al igual que se usan las carpetas en el equipo para organizar los archivos.

Una vez que tenga el objeto Container, puede crear un objeto de blob [blobClient](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) que apunte al blob específico en el que está interesado. Use el objeto [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) para crear, descargar y copiar blobs.

> [!IMPORTANT]
> Los nombres de contenedor deben estar en minúsculas. Para más información sobre la nomenclatura de contenedores y blobs, consulte [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Asignación de nombres y referencia a contenedores, blobs y metadatos).

El siguiente código de ejemplo:

- Crea un contenedor.
- Establece permisos en el contenedor para que los blobs sean públicos. El contenedor se denomina *quickstartblobs* y tiene un identificador único anexo.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>Creación de un blob en el contenedor

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. Para crear un blob, llame al método [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) que pasa los datos del blob.

En el ejemplo siguiente se crea un blob denominado *QuickStart_* con un identificador único y la extensión *.txt* en el contenedor que se creó anteriormente.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

Los blobs en bloques pueden tener un tamaño de hasta 4,7 TB y pueden ser cualquier cosa, desde hojas de cálculo a archivos de vídeo grandes. Los blobs en páginas se usan principalmente para los archivos de disco duro virtual que respaldan las máquinas virtuales IaaS. Los blobs en anexos habitualmente se utilizan para el registro, por ejemplo, cuando desea escribir en un archivo y luego agregar más información.

### <a name="list-the-blobs-in-a-container"></a>Enumeración de los blobs de un contenedor

Obtenga una lista de archivos del contenedor con el método [list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method). El siguiente código recupera la lista de blobs y, después, muestra sus nombres.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Descarga de un blob

Descargue un blob en el disco local mediante el método [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method). El código siguiente descarga el blob creado en una sección anterior.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no se necesita un blob, use [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) para quitarlo. Elimine todo un contenedor mediante el método [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method). Al eliminar un contenedor también se eliminan los blobs almacenados en él.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Recursos para el desarrollo de aplicaciones Ruby con blobs

Consulte estos recursos adicionales para el desarrollo de Ruby:

- Consulte e instale el [código fuente de la biblioteca de cliente de Ruby](https://github.com/Azure/azure-storage-ruby) para Azure Storage en GitHub.
- Explore los [ejemplos de Azure](/samples/browse/?products=azure&languages=ruby) escritos mediante la biblioteca de cliente de Ruby.
- [Ejemplo: Introducción a Azure Storage en Ruby](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a transferir archivos entre Azure Blob Storage y un disco local mediante Ruby. Para más información sobre cómo trabajar con Blob Storage, continúe con la información general de la cuenta de Storage.

> [!div class="nextstepaction"]
> [Introducción a las cuentas de almacenamiento](../common/storage-account-overview.md)

Para más información sobre el Explorador de Storage y los blobs, consulte [Administración de recursos de Azure Blob Storage con el Explorador de Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
