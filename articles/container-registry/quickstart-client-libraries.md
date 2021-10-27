---
title: 'Inicio rápido: Administración del contenido del registro de contenedor con bibliotecas cliente'
description: Use este inicio rápido para administrar repositorios, imágenes y artefactos mediante las bibliotecas cliente de Azure Container Registry
author: dlepow
ms.topic: quickstart
ms.date: 10/05/2021
ms.author: danlep
ms.custom: ''
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 6af32e7bd841960cdf1fd3aef6af50171cc1e594
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007667"
---
# <a name="quickstart-use-the-azure-container-registry-client-libraries"></a>Inicio rápido: Uso de las bibliotecas cliente de Azure Container Registry

::: zone pivot="programming-language-csharp, programming-language-java, programming-language-javascript, programming-language-python"

Use este artículo para empezar a trabajar con la biblioteca cliente de Azure Container Registry. Siga estos pasos para probar código de ejemplo para operaciones del plano de datos en imágenes y artefactos.

Use la biblioteca cliente de Azure Container Registry para lo siguiente:

* Enumeración de imágenes o artefactos en un registro
* Obtención de metadatos para imágenes y artefactos, repositorios y etiquetas
* Establecimiento de propiedades de lectura, escritura y eliminación en elementos del registro
* Eliminación de imágenes y artefactos, repositorios y etiquetas

Azure Container Registry también tiene una biblioteca de administración para las operaciones del plano de control, incluida la creación y las actualizaciones del registro. 

## <a name="prerequisites"></a>Requisitos previos

* Necesita una [suscripción de Azure](https://azure.microsoft.com/free/) y una instancia de Azure Container Registry para usar esta biblioteca.

    Para crear una nueva instancia de Azure Container Registry, puede usar [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md) o la [CLI de Azure](container-registry-get-started-azure-cli.md). A continuación se facilita un ejemplo mediante el uso de la CLI de Azure:

    ```azurecli
    az acr create --name MyContainerRegistry --resource-group MyResourceGroup \
        --location westus --sku Basic
    ```

* Inserte una o varias imágenes de contenedor en el registro. Para ver los pasos, consulte [Inserción de la primera imagen en el registro de contenedor de Azure mediante la CLI de Docker](container-registry-get-started-docker-cli.md).

## <a name="key-concepts"></a>Conceptos clave

* Una instancia de Azure Container Registry almacena *imágenes de contenedor* y [artefactos de OCI](container-registry-oci-artifacts.md). 
* Una imagen o artefacto se compone de un *manifiesto* y *capas*. 
* Un manifiesto describe las capas que componen la imagen o el artefacto. Se identifica de forma única por su *código hash*. 
* Una imagen o artefacto también se puede *etiquetar* para asignarle un alias legible. Una imagen o artefacto puede tener cero o más etiquetas asociadas y cada etiqueta identifica de forma única la imagen. 
* Una colección de imágenes o artefactos que comparten el mismo nombre pero tienen etiquetas diferentes se denomina *repositorio*.

Para más información, consulte [Acerca de los registros, repositorios y artefactos](container-registry-concepts.md).

::: zone-end

::: zone pivot="programming-language-csharp"

## <a name="get-started"></a>Introducción

[Código fuente][dotnet_source] | [Paquete (NuGet)][dotnet_package] | [Referencia de API][dotnet_docs] | [Ejemplos][dotnet_samples]

Para desarrollar código de aplicación .NET que pueda conectarse a una instancia de Azure Container Registry, necesitará la biblioteca `Azure.Containers.ContainerRegistry`.

### <a name="install-the-package"></a>Instalar el paquete

Instale la biblioteca cliente de Azure Container Registry para .NET con [NuGet][nuget]:

```Powershell
dotnet add package Azure.Containers.ContainerRegistry --prerelease
```

## <a name="authenticate-the-client"></a>Autenticar el cliente

Para que la aplicación se conecte al registro, deberá crear un cliente `ContainerRegistryClient` que pueda autenticarse con él. Use la [biblioteca de identidades de Azure][dotnet_identity] para agregar compatibilidad con Azure Active Directory para la autenticación de clientes del SDK de Azure con sus servicios de Azure correspondientes.  

Al desarrollar y depurar la aplicación localmente, puede usar su propio usuario para autenticarse en el registro. Una manera de hacerlo es [autenticar el usuario con la CLI de Azure](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity#authenticating-via-the-azure-cli) y ejecutar la aplicación desde este entorno. Si la aplicación usa un cliente que se ha construido para autenticarse con `DefaultAzureCredential`, se autenticará correctamente con el registro en el punto de conexión especificado.  

```C#
// Create a ContainerRegistryClient that will authenticate to your registry through Azure Active Directory
Uri endpoint = new Uri("https://myregistry.azurecr.io");
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });
```

Consulte el [archivo Léame de identidad de Azure][dotnet_identity] para obtener más enfoques para la autenticación con `DefaultAzureCredential`, tanto localmente como en entornos de implementación. Para conectarse a registros en nubes de Azure no públicas, consulte la [referencia de API][dotnet_docs].

Para más información sobre el uso de Azure AD con Azure Container Registry, consulte la [información general sobre la autenticación](container-registry-authentication.md).

## <a name="examples"></a>Ejemplos

En cada ejemplo se asume que hay una variable de entorno `REGISTRY_ENDPOINT` establecida en una cadena que contiene el prefijo `https://` y el nombre del servidor de inicio de sesión (por ejemplo, "https://myregistry.azurecr.io").

En los ejemplos siguientes se usan API asincrónicas que devuelven una tarea. También hay disponibles API sincrónicas.

### <a name="list-repositories-asynchronously"></a>Enumeración asincrónica de repositorios

Recorra en iteración la colección de repositorios del registro.

```C# Snippet:ContainerRegistry_Tests_Samples_CreateClientAsync
// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Get the collection of repository names from the registry
AsyncPageable<string> repositories = client.GetRepositoryNamesAsync();
await foreach (string repository in repositories)
{
    Console.WriteLine(repository);
}
```

### <a name="set-artifact-properties-asynchronously"></a>Establecimiento de propiedades de artefactos de forma asincrónica

```C# Snippet:ContainerRegistry_Tests_Samples_CreateClientAsync
// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Get the collection of repository names from the registry
AsyncPageable<string> repositories = client.GetRepositoryNamesAsync();
await foreach (string repository in repositories)
{
    Console.WriteLine(repository);
}
```

### <a name="delete-images-asynchronously"></a>Eliminación asincrónica de imágenes

```C# Snippet:ContainerRegistry_Tests_Samples_DeleteImageAsync
using System.Linq;
using Azure.Containers.ContainerRegistry;
using Azure.Identity;

// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Iterate through repositories
AsyncPageable<string> repositoryNames = client.GetRepositoryNamesAsync();
await foreach (string repositoryName in repositoryNames)
{
    ContainerRepository repository = client.GetRepository(repositoryName);

    // Obtain the images ordered from newest to oldest
    AsyncPageable<ArtifactManifestProperties> imageManifests =
        repository.GetManifestPropertiesCollectionAsync(orderBy: ArtifactManifestOrderBy.LastUpdatedOnDescending);

    // Delete images older than the first three.
    await foreach (ArtifactManifestProperties imageManifest in imageManifests.Skip(3))
    {
        RegistryArtifact image = repository.GetArtifact(imageManifest.Digest);
        Console.WriteLine($"Deleting image with digest {imageManifest.Digest}.");
        Console.WriteLine($"   Deleting the following tags from the image: ");
        foreach (var tagName in imageManifest.Tags)
        {
            Console.WriteLine($"        {imageManifest.RepositoryName}:{tagName}");
            await image.DeleteTagAsync(tagName);
        }
        await image.DeleteAsync();
    }
}
```

::: zone-end

::: zone pivot="programming-language-java"

## <a name="get-started"></a>Introducción

[Código fuente][java_source] | [Paquete (Maven)][java_package] | [Referencia de API][java_docs] | [Ejemplos][java_samples]

### <a name="currently-supported-environments"></a>Entornos admitidos actualmente

* [Kit de desarrollo de Java (JDK)][jdk_link], versión 8 o posterior.

### <a name="include-the-package"></a>Inclusión del paquete

[//]: # ({x-version-update-start;com.azure:azure-containers-containerregistry;current})
```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-containers-containerregistry</artifactId>
  <version>1.0.0-beta.3</version>
</dependency>
```
[//]: # ({x-version-update-end})

## <a name="authenticate-the-client"></a>Autenticar el cliente

La [biblioteca de identidades de Azure][java_identity] proporciona compatibilidad con Azure Active Directory para la autenticación.

En los ejemplos siguientes se asume que tiene una cadena de punto de conexión del registro que contiene el prefijo `https://` y el nombre del servidor de inicio de sesión (por ejemplo, "https://myregistry.azurecr.io").

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L31-L35 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildClient();
```

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L39-L43 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryAsyncClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildAsyncClient();
```

Para más información sobre el uso de Azure AD con Azure Container Registry, consulte la [información general sobre la autenticación](container-registry-authentication.md).

## <a name="examples"></a>Ejemplos

En cada ejemplo se asume que hay una cadena de punto de conexión del registro que contiene el prefijo `https://` y el nombre del servidor de inicio de sesión (por ejemplo, "https://myregistry.azurecr.io").

### <a name="list-repository-names"></a>Enumeración de nombres de repositorio

Recorra en iteración la colección de repositorios del registro.

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L47-L53 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildClient();

client.listRepositoryNames().forEach(repository -> System.out.println(repository));
```

### <a name="set-artifact-properties"></a>Establecimiento de propiedades de artefactos

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L119-L132 -->
```Java
TokenCredential defaultCredential = new DefaultAzureCredentialBuilder().build();

ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(defaultCredential)
    .buildClient();

RegistryArtifact image = client.getArtifact(repositoryName, digest);

image.updateTagProperties(
    tag,
    new ArtifactTagProperties()
        .setWriteEnabled(false)
        .setDeleteEnabled(false));
```

### <a name="delete-images"></a>Eliminación de imágenes

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L85-L113 -->
```Java
TokenCredential defaultCredential = new DefaultAzureCredentialBuilder().build();

ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(defaultCredential)
    .buildClient();

final int imagesCountToKeep = 3;
for (String repositoryName : client.listRepositoryNames()) {
    final ContainerRepository repository = client.getRepository(repositoryName);

    // Obtain the images ordered from newest to oldest
    PagedIterable<ArtifactManifestProperties> imageManifests =
        repository.listManifestProperties(
            ArtifactManifestOrderBy.LAST_UPDATED_ON_DESCENDING,
            Context.NONE);

    imageManifests.stream().skip(imagesCountToKeep)
        .forEach(imageManifest -> {
            System.out.printf(String.format("Deleting image with digest %s.%n", imageManifest.getDigest()));
            System.out.printf("    This image has the following tags: ");

            for (String tagName : imageManifest.getTags()) {
                System.out.printf("        %s:%s", imageManifest.getRepositoryName(), tagName);
            }

            repository.getArtifact(imageManifest.getDigest()).delete();
        });
}
```

::: zone-end

::: zone pivot="programming-language-javascript"

## <a name="get-started"></a>Introducción

[Código fuente][javascript_source] | [Paquete (npm)][javascript_package] | [Referencia de API][javascript_docs] | [Ejemplos][javascript_samples]

### <a name="currently-supported-environments"></a>Entornos admitidos actualmente

- [Versiones de LTS de Node.js](https://nodejs.org/about/releases/)

Para más información, consulte la [directiva de compatibilidad](https://github.com/Azure/azure-sdk-for-js/blob/main/SUPPORT.md).

### <a name="install-the-azurecontainer-registry-package"></a>Instalar el paquete `@azure/container-registry`

Instale la biblioteca cliente de Container Registry para JavaScript con `npm`:

```bash
npm install @azure/container-registry
```

## <a name="authenticate-the-client"></a>Autenticar el cliente

La [biblioteca de identidades de Azure][javascript_identity] proporciona compatibilidad con Azure Active Directory para la autenticación.

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT;
// Create a ContainerRegistryClient that will authenticate through Active Directory
const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());
```

Para más información sobre el uso de Azure AD con Azure Container Registry, consulte la [información general sobre la autenticación](container-registry-authentication.md).

## <a name="examples"></a>Ejemplos

En cada ejemplo se asume que hay una variable de entorno `CONTAINER_REGISTRY_ENDPOINT` establecida en una cadena que contiene el prefijo `https://` y el nombre del servidor de inicio de sesión (por ejemplo, "https://myregistry.azurecr.io").

### <a name="list-repositories-asynchronously"></a>Enumeración asincrónica de repositorios

Recorra en iteración la colección de repositorios del registro.

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // endpoint should be in the form of "https://myregistryname.azurecr.io"
  // where "myregistryname" is the actual name of your registry
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());

  console.log("Listing repositories");
  const iterator = client.listRepositoryNames();
  for await (const repository of iterator) {
    console.log(`  repository: ${repository}`);
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

### <a name="set-artifact-properties-asynchronously"></a>Establecimiento de propiedades de artefactos de forma asincrónica

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";

  // Create a new ContainerRegistryClient and RegistryArtifact to access image operations
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());
  const image = client.getArtifact("library/hello-world", "v1");

  // Set permissions on the image's "latest" tag
  await image.updateTagProperties("latest", { canWrite: false, canDelete: false });
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

### <a name="delete-images-asynchronously"></a>Eliminación asincrónica de imágenes

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  // Create a new ContainerRegistryClient
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());

  // Iterate through repositories
  const repositoryNames = client.listRepositoryNames();
  for await (const repositoryName of repositoryNames) {
    const repository = client.getRepository(repositoryName);
    // Obtain the images ordered from newest to oldest by passing the `orderBy` option
    const imageManifests = repository.listManifestProperties({
      orderBy: "LastUpdatedOnDescending"
    });
    const imagesToKeep = 3;
    let imageCount = 0;
    // Delete images older than the first three.
    for await (const manifest of imageManifests) {
      imageCount++;
      if (imageCount > imagesToKeep) {
        const image = repository.getArtifact(manifest.digest);
        console.log(`Deleting image with digest ${manifest.digest}`);
        console.log(`  Deleting the following tags from the image:`);
        for (const tagName of manifest.tags) {
          console.log(`    ${manifest.repositoryName}:${tagName}`);
          image.deleteTag(tagName);
        }
        await image.delete();
      }
    }
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

::: zone-end

::: zone pivot="programming-language-python"

## <a name="get-started"></a>Introducción

[Código fuente][python_source] | [Paquete (Pypi)][python_package] | [Referencia de API][python_docs] | [Ejemplos][python_samples]

### <a name="install-the-package"></a>Instalar el paquete

Instale la biblioteca de Azure Container Registry para Python con [pip][pip_link]:

```bash
pip install --pre azure-containerregistry
```

## <a name="authenticate-the-client"></a>Autenticar el cliente

La [biblioteca de identidades de Azure][python_identity] proporciona compatibilidad con Azure Active Directory para la autenticación. `DefaultAzureCredential` asume que están establecidas las variables de entorno `AZURE_CLIENT_ID`, `AZURE_TENANT_ID` y `AZURE_CLIENT_SECRET`. Para más información, consulte [Variables de entorno de identidades de Azure](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#environment-variables).

```python
# Create a ContainerRegistryClient that will authenticate through Active Directory
from azure.containerregistry import ContainerRegistryClient
from azure.identity import DefaultAzureCredential

account_url = "https://mycontainerregistry.azurecr.io"
client = ContainerRegistryClient(account_url, DefaultAzureCredential())
```

## <a name="examples"></a>Ejemplos

En cada ejemplo se asume que hay una variable de entorno `CONTAINERREGISTRY_ENDPOINT` establecida en una cadena que contiene el prefijo `https://` y el nombre del servidor de inicio de sesión (por ejemplo, "https://myregistry.azurecr.io").

### <a name="list-tags-asynchronously"></a>Enumeración asincrónica de etiquetas

En este ejemplo se asume que el registro tiene un repositorio `hello-world`.

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class ListTagsAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def list_tags(self):
        # Create a new ContainerRegistryClient      
        audience = "https://management.azure.com"
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        manifest = await client.get_manifest_properties("library/hello-world", "latest")
        print(manifest.repository_name + ": ")
        for tag in manifest.tags:
            print(tag + "\n")
```

### <a name="set-artifact-properties-asynchronously"></a>Establecimiento de propiedades de artefactos de forma asincrónica

En este ejemplo se asume que el registro tiene un repositorio `hello-world` con la imagen `v1` etiquetada.

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class SetImagePropertiesAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def set_image_properties(self):
        # Create a new ContainerRegistryClient
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        audience = "https://management.azure.com"
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        # [START update_manifest_properties]
        # Set permissions on the v1 image's "latest" tag
        await client.update_manifest_properties(
            "library/hello-world",
            "latest",
            can_write=False,
            can_delete=False
        )
        # [END update_manifest_properties]
        # After this update, if someone were to push an update to "myacr.azurecr.io\hello-world:v1", it would fail.
        # It's worth noting that if this image also had another tag, such as "latest", and that tag did not have
        # permissions set to prevent reads or deletes, the image could still be overwritten. For example,
        # if someone were to push an update to "myacr.azurecr.io\hello-world:latest"
        # (which references the same image), it would succeed.
```

### <a name="delete-images-asynchronously"></a>Eliminación asincrónica de imágenes

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry import ManifestOrder
from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class DeleteImagesAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def delete_images(self):
        # [START list_repository_names]   
        audience = "https://management.azure.com"
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        async with client:
            async for repository in client.list_repository_names():
                print(repository)
                # [END list_repository_names]

                # [START list_manifest_properties]
                # Keep the three most recent images, delete everything else
                manifest_count = 0
                async for manifest in client.list_manifest_properties(repository, order_by=ManifestOrder.LAST_UPDATE_TIME_DESCENDING):
                    manifest_count += 1
                    if manifest_count > 3:
                        await client.delete_manifest(repository, manifest.digest)
                # [END list_manifest_properties]
```

::: zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y quitar una instancia de Azure Container Registry, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él.

* [Portal](container-registry-get-started-portal.md#clean-up-resources)
* [CLI de Azure](container-registry-get-started-azure-cli.md#clean-up-resources)

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a usar la biblioteca cliente de Azure Container Registry para realizar operaciones en imágenes y artefactos en el registro de contenedor.

* Para más información, vea la documentación de referencia de la API:

    * [.NET][dotnet_docs]
    * [Java][java_docs]
    * [JavaScript][javascript_docs]
    * [Python][python_docs]

* Más información acerca de la [API REST][rest_docs] de Azure Container Registry.

[dotnet_source]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/containerregistry/Azure.Containers.ContainerRegistry/src
[dotnet_package]: https://www.nuget.org/packages/Azure.Containers.ContainerRegistry/
[dotnet_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/containerregistry/Azure.Containers.ContainerRegistry/samples
[dotnet_docs]: /dotnet/api/azure.containers.containerregistry
[rest_docs]: /rest/api/containerregistry/
[product_docs]:  container-registry-intro.md
[nuget]: https://www.nuget.org/
[dotnet_identity]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity/README.md
[javascript_identity]: https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md
[javascript_source]: https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/containerregistry/container-registry/src
[javascript_package]: https://www.npmjs.com/package/@azure/container-registry
[javascript_docs]: /javascript/api/overview/azure/container-registry-readme
[jdk_link]: /java/azure/jdk/
[javascript_samples]: https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/containerregistry/container-registry/samples
[java_source]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/containerregistry/azure-containers-containerregistry/src
[java_package]: https://search.maven.org/artifact/com.azure/azure-containers-containerregistry
[java_docs]: /java/api/overview/azure/containers-containerregistry-readme
[java_identity]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/identity/azure-identity/README.md
[java_samples]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/containerregistry/azure-containers-containerregistry/src/samples/
[python_package]: https://pypi.org/project/azure-containerregistry/
[python_docs]: /python/api/overview/azure/containerregistry-readme
[python_samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/containerregistry/azure-containerregistry/samples
[pip_link]: https://pypi.org
[python_identity]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity
[python_source]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/containerregistry/azure-containerregistry
