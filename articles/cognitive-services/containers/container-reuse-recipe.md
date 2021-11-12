---
title: Fórmulas para contenedores de Docker
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo compilar, probar y almacenar contenedores con algunos o todos los valores de configuración para implementación y reutilización.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: aahi
ms.openlocfilehash: e11de4634d7488f75649535b56bd5129d7c1d65a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473402"
---
# <a name="create-containers-for-reuse"></a>Creación de contenedores para su reutilización

Use estas fórmulas de contenedor para crear contenedores de Cognitive Services que pueden reutilizarse. Los contenedores pueden crearse con algunos o todos los valores de configuración para que _no_ sean necesarios cuando se inicia el contenedor.

Una vez que crea esta nueva capa de contenedor (con la configuración) y la ha probado localmente, puede almacenar el contenedor en un registro de contenedor. Cuando se inicia el contenedor, solo necesita los valores de configuración que no están almacenados actualmente en el contenedor. El contenedor de registro privado ofrece espacio de configuración para que pueda pasar esos valores de configuración.

## <a name="docker-run-syntax"></a>Sintaxis de Docker run

Todos los ejemplos de `docker run` en este documento suponen el uso de una consola de Windows con un carácter de continuación de línea `^`. Tenga en cuenta lo siguiente para su propio uso:

* No cambie el orden de los argumentos a menos que esté muy familiarizado con los contenedores de Docker.
* Si usa un sistema operativo distinto de Windows o una consola que no sea la de Windows, use la consola o terminal correctos, la sintaxis de carpeta para montajes y el carácter de continuación de línea para su consola y sistema.  Dado que el contenedor de Cognitive Services es un sistema operativo Linux, el montaje de destino usa una sintaxis de carpeta basada en Linux.
* Los ejemplos de `docker run` usan el directorio que está en la unidad `c:` para evitar conflictos con los permisos de Windows. Si necesita usar un directorio específico como directorio de entrada, tal vez tenga que conceder permiso al servicio de Docker.

## <a name="store-no-configuration-settings-in-image"></a>Imágenes sin valores de configuración almacenados

Los comandos `docker run` de ejemplo para cada servicio no almacenan los valores de configuración en el contenedor. Cuando se inicia el contenedor desde un servicio de registro o de consola, se deben pasar esos valores de configuración. El contenedor de registro privado ofrece espacio de configuración para que pueda pasar esos valores de configuración.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Reutilización de fórmulas: almacenamiento de todos los valores de configuración con el contenedor

Para almacenar todos los valores de configuración, cree un `Dockerfile` con dichos valores.

Problemas con este procedimiento:

* El contenedor nuevo tiene un nombre y una etiqueta diferentes a los del contenedor original.
* Para cambiar esta configuración, tendrá que cambiar los valores del Dockerfile, volver a generar la imagen y volver a publicarla en el registro.
* Si alguien obtiene acceso a su registro de contenedor o al host local, esa persona puede ejecutar el contenedor y usar los puntos de conexión de Cognitive Services.
* Si su instancia de Cognitive Services no requiere montajes de entrada, no agregue las líneas `COPY` al Dockerfile.

Cree el Dockerfile, extráigalo del contenedor de Cognitive Services existente que quiere usar y, a continuación, use los comandos de docker en el Dockerfile para establecer o extraer la información que el contenedor necesita.

En este ejemplo:

* El punto de conexión de facturación (`{BILLING_ENDPOINT}`) se establece a partir de la clave de entorno del host con `ENV`.
* La clave de API de facturación (`{ENDPOINT_KEY}`) se establece a partir de la clave de entorno del host con `ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Reutilización fórmulas: almacenamiento de la configuración de facturación con el contenedor

En este ejemplo se muestra cómo crear el contenedor de opiniones del servicio Language a partir de un Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Compile y ejecute el contenedor [localmente](#how-to-use-container-on-your-local-host) o desde el [contenedor del registro privado](#how-to-add-container-to-private-registry) según sea necesario.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Reutilización de fórmulas: almacenamiento de la configuración de facturación y montaje con el contenedor

Este ejemplo muestra cómo usar Language Understanding para guardar la facturación y modelos del archivo Dockerfile.

* Se copia el archivo de modelo Language Understanding desde el sistema de archivos del host mediante `COPY`.
* El contenedor de LUIS admite más de un modelo. Si todos los modelos se almacenan en la misma carpeta, solo se necesita una instrucción `COPY`.
* Ejecute el archivo de docker desde el elemento primario relativo del directorio de entrada del modelo. El ejemplo siguiente, ejecute los comandos `docker build` y `docker run` desde el elemento primario relativo de `/input`. El primer `/input` en el comando `COPY` es el directorio del equipo host. El segundo `/input` es el directorio del contenedor.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Compile y ejecute el contenedor [localmente](#how-to-use-container-on-your-local-host) o desde el [contenedor del registro privado](#how-to-add-container-to-private-registry) según sea necesario.

## <a name="how-to-use-container-on-your-local-host"></a>Procedimiento para usar el contenedor en el host local

Para compilar el archivo de Docker, reemplace `<your-image-name>` con el nuevo nombre de la imagen y después use:

```console
docker build -t <your-image-name> .
```

Para ejecutar la imagen y quitarla cuando se detiene el contenedor (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Procedimiento para agregar el contenedor en el registro privado

Siga estos pasos para usar el archivo Dockerfile y colocar la nueva imagen en el registro de contenedor privado.  

1. Cree un archivo `Dockerfile` con el texto de la fórmula de reutilización. Un archivo `Dockerfile` no tiene extensión.

1. Reemplace los valores de los corchetes angulares con sus propios valores.

1. Compile el archivo en una imagen en la línea de comandos o un terminal con el siguiente comando. Reemplace los valores de los corchetes angulares (`<>`) con su propio nombre y etiqueta de contenedor.  

    La opción de etiqueta (`-t`) es una forma de agregar información sobre lo que ha cambiado en el contenedor. Por ejemplo, un nombre de contenedor `modified-LUIS` indica que el contenedor original ahora tiene capas. Un nombre de etiqueta `with-billing-and-model` indica con qué método se ha modificado el contenedor de Language Understanding.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Inicie sesión en la CLI de Azure desde una consola. Este comando abre un explorador y requiere autenticación. Una vez autenticado, puede cerrar el explorador y seguir trabajando en la consola.

    ```azurecli
    az login
    ```

1. Inicie sesión en su registro privado con la CLI de Azure desde una consola.

    Reemplace los valores de los corchetes angulares (`<my-registry>`) con su propio nombre de registro.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    También puede iniciar sesión con el inicio de sesión de docker si está asignado como entidad de servicio.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Etiquetar el contenedor con la ubicación del registro privado. Reemplace los valores de los corchetes angulares (`<my-registry>`) con su propio nombre de registro. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Si no usa un nombre de etiqueta, `latest` está implícito.

1. Inserte la imagen nueva en el registro de contenedor privado. Cuando visualice el registro de contenedor privado, el nombre de contenedor utilizado en el siguiente comando CLI será el nombre del repositorio.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Creación y uso de Azure Container Instances](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->