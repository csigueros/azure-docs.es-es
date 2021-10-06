---
title: Instalación y ejecución de contenedores de Docker Form Recognizer v2.1
titleSuffix: Azure Applied AI Services
description: Use los contenedores de Docker Form Recognizer local para identificar y extraer pares clave-valor, marcas de selección, tablas y estructura de formularios y documentos.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
keywords: entorno local, Docker, contenedor, identificar
ms.openlocfilehash: 1fb19ef8f7b4855107d089e6b0ce21673ce5b841
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057348"
---
# <a name="install-and-run-form-recognizer-v21-preview-containers"></a>Instalación y ejecución de contenedores de la versión preliminar de Form Recognizer v2.1

> [!IMPORTANT]
>
> * Los contenedores de Form Recognizer están en versión preliminar validada. Para usarlos, debe enviar una [solicitud en línea](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) y recibir la aprobación. Consulte [**Solicitud de aprobación para ejecutar un contenedor**](#request-approval-to-run-the-container) a continuación para obtener más información.
>
> * El formulario de solicitud en línea requiere que proporcione una dirección de correo electrónico válida que pertenezca a la organización propietaria del identificador de suscripción de Azure y que tenga o se le haya concedido acceso a esa suscripción.

Azure Form Recognizer es uno de los servicios de Azure Applied AI Services y permite crear software de procesamiento de datos automatizado mediante la tecnología de aprendizaje automático. Form Recognizer le permite identificar y extraer texto, pares clave-valor, marcas de selección, datos de tabla y mucho más de los documentos de formulario y de los datos estructurados que incluyen las relaciones en el archivo original.

En este artículo, aprenderá a descargar, instalar y ejecutar contenedores de Form Recognizer. Los contenedores le permiten ejecutar el servicio Form Recognizer en su propio entorno. Los contenedores son excelentes para requisitos específicos de control de datos y seguridad. Las funciones de Form Recognizer son compatibles con seis contenedores de funciones de Form Recognizer: **Diseño**, **Tarjeta de visita**,**Documento de identidad**, **Recibo**, **Factura** y **Personalizado**. (para los contenedores de Recibo, Tarjeta de visita y Documento de identidad también necesitará el contenedor OCR **Lectura**).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará una [**cuenta de Azure**](https://azure.microsoft.com/free/cognitive-services/) activa.  En caso de no tener ninguna, puede crear una [**cuenta gratuita**](https://azure.microsoft.com/free/).

También va a necesitar lo siguiente para usar contenedores de Form Recognizer:

| Obligatorio | Propósito |
|----------|---------|
| **Conocimientos sobre Docker** | Debe tener una comprensión básica de conceptos de Docker, como los registros, los repositorios, los contenedores y las imágenes de contenedor, así como conocimientos de [terminología y comandos](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology) básicos de `docker`. |
| **Motor de Docker instalado** | <ul><li>Necesita que el motor de Docker esté instalado en un [equipo host](#host-computer-requirements). Docker dispone de paquetes que configuran el entorno de Docker en [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) y [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para conocer los principios básicos de Docker y de los contenedores, consulte [Introducción a Docker](https://docs.docker.com/engine/docker-overview/).</li><li> Docker debe configurarse para permitir que los contenedores se conecten con Azure y envíen datos de facturación a dicho servicio. </li><li> En **Windows**, Docker también debe estar configurado de forma que admita contenedores de **Linux**.</li></ul>  |
|**Recurso de Form Recognizer** | Un recurso de [**servicio único de Azure Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [**servicio múltiple de Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal. Para usar los contenedores, debe tener la clave de API y el URI del punto de conexión asociados. Ambos valores están disponibles en la página de **claves y punto de conexión** de Form Recognizer de Azure Portal: <ul><li>**{FORM_RECOGNIZER_API_KEY}** : una de las dos claves de recurso disponibles.<li>**{FORM_RECOGNIZER_ENDPOINT_URI}** : el punto de conexión del recurso que se usa para realizar el seguimiento de la información de facturación.</li></li></ul>|
| **Recurso de la API Computer Vision** | **Para procesar tarjetas de presentación, documentos de identificación o recibos, necesitará un recurso Computer Vision.** <ul><li>Puede acceder a la característica Reconocer texto como un recurso de Azure (API REST o SDK) o como un [contenedor](../../../cognitive-services/Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull) **cognitive-services-recognize-text**. Se aplican los honorarios de [facturación](#billing) habituales.</li> <li>Si utiliza el contenedor **cognitive-services-recognize-text**, asegúrese de que su clave de Computer Vision para el contenedor de Form Recognizer es la clave especificada en el comando de Computer Vision `docker run` o `docker compose` para el contenedor **cognitive-services-recognize-text** y su punto de conexión de facturación es el punto de conexión del contenedor (por ejemplo, `http://localhost:5000`). Si se utilizan los contenedores Computer Vision y Form Recognizer juntos en el mismo host, no se pueden iniciar con el puerto predeterminado de *5000*. </li></ul></br>Pase tanto la clave de API como los puntos de conexión de Computer Vision (nube de Azure o contenedor de Cognitive Services):<ul><li>**{COMPUTER_VISION_API_KEY}** : una de las dos claves de recurso disponibles.</li><li> **{COMPUTER_VISION_ENDPOINT_URI}** : el punto de conexión del recurso que se usa para realizar el seguimiento de la información de facturación.</li></ul> |

|Opcionales|Propósito|
|---------|----------|
|**CLI (interfaz de la línea de comandos) de Azure** | La [CLI de Azure](/cli/azure/install-azure-cli) permite usar un conjunto de comandos en línea para crear y administrar recursos de Azure. Está disponible para instalarse en entornos Windows, macOS y Linux, y se puede ejecutar en un contenedor de Docker y Azure Cloud Shell. |
|||

## <a name="request-approval-to-run-the-container"></a>Solicitud de aprobación para ejecutar el contenedor

Cumplimente y envíe el [formulario de solicitud de servicios validados](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) para solicitar la aprobación para ejecutar el contenedor.

El formulario solicita información acerca del usuario y de su empresa, así como del escenario de usuario para el que se va a usar el contenedor. Después de enviar el formulario, el equipo de Azure Cognitive Services lo revisará y le informará la decisión por correo electrónico.

En el formulario, debe usar una dirección de correo electrónico asociada a un identificador de suscripción de Azure. El recurso de Azure que se usa para ejecutar el contenedor se debe haber creado con el identificador de la suscripción de Azure aprobada. Compruebe el correo electrónico (bandeja de entrada y carpetas de correo no deseado) para obtener las actualizaciones del estado de la aplicación por parte de Microsoft. Una vez aprobado, podrá ejecutar el contenedor después de descargarlo de Microsoft Container Registry (MCR) que se describe más adelante en el artículo.

## <a name="host-computer-requirements"></a>Requisitos del equipo host

El host es un equipo basado en x64 que ejecuta el contenedor de Docker. Puede ser un equipo del entorno local o un servicio de hospedaje de Docker incluido en Azure, como:

* [Azure Kubernetes Service](../../../aks/index.yml).
* [Azure Container Instances](../../../container-instances/index.yml).
* Un clúster de [Kubernetes](https://kubernetes.io/) implementado en [Azure Stack](/azure-stack/operator). Para obtener más información, consulte [Implementación de Kubernetes en Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

### <a name="container-requirements-and-recommendations"></a>Recomendaciones y requisitos del contenedor

#### <a name="required-containers"></a>Contenedores necesarios

En la tabla siguiente se enumeran los contenedores de compatibilidad adicionales para cada contenedor de Form Recognizer que descargue. Consulte la sección [Facturación](#billing) para obtener más información.

| Contenedor de características | Compatibilidad con contenedores |
|---------|-----------|
| **Diseño** | None |
| **Tarjeta de presentación** | **Lectura de Computer Vision**|
| **Documento de identificación** | **Lectura de Computer Vision** |
| **Factura**   | **Diseño** |
| **Recibo** |**Lectura de Computer Vision** |
| **Personalizada** | **API personalizada**, **Supervisado personalizado**, **Diseño**|

#### <a name="recommended-cpu-cores-and-memory"></a>Memoria y núcleos de CPU recomendados

> [!Note]
> Los valores mínimos y recomendados se basan en los límites de Docker y *no* de los recursos de la máquina host.

##### <a name="read-layout-and-prebuilt-containers"></a>Lectura, diseño y contenedores precompilados

| Contenedor | Mínima | Recomendado |
|-----------|---------|-------------|
| Read 3.2 | 8 núcleos, 16 GB de memoria | 8 núcleos, 24 GB de memoria|
| Layout 2.1 (versión preliminar) | 8 núcleos, 16 GB de memoria | 8 núcleos, 24 GB de memoria |
| Business Card 2.1 (versión preliminar) | 2 núcleos, 4 GB de memoria | 4 núcleos, 4 GB de memoria |
| ID Document 2.1 (versión preliminar) | 1 núcleo, 2 GB de memoria |2 núcleos, 2 GB de memoria |
| Invoice 2.1 (versión preliminar) | 4 núcleos, 8 GB de memoria | 8 núcleos, 8 GB de memoria |
| Receipt 2.1 (versión preliminar) |  4 núcleos, 8 GB de memoria | 8 núcleos, 8 GB de memoria  |

##### <a name="custom-containers"></a>Contenedores personalizados

Los siguientes requisitos del equipo host son aplicables **para entrenar y analizar** solicitudes:

| Contenedor | Mínima | Recomendado |
|-----------|---------|-------------|
| API personalizada| 0,5 núcleos, 0,5 GB de memoria| 1 núcleo, 1 GB de memoria |
|Supervisado personalizado | 4 núcleos, 2 GB de memoria | 8 núcleos, 4 GB de memoria|

Si solo realiza llamadas de análisis, los requisitos del equipo host son los siguientes:

| Contenedor | Mínima | Recomendado |
|-----------|---------|-------------|
|Supervisado personalizado (analizar) | 1 núcleo, 0,5 GB | 2 núcleos, 1 GB de memoria |

* Cada núcleo debe ser de 2,6 gigahercios (GHz) como mínimo.
* El núcleo y la memoria se corresponden con los valores de `--cpus` y `--memory` que se usan como parte del comando `docker compose` o `docker run`.

> [!TIP]
> Puede usar el comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para enumerar las imágenes de contenedor descargadas. Por ejemplo, el comando siguiente muestra el id., el repositorio y la etiqueta de cada imagen de contenedor descargada, con formato de tabla:
>
>  ```docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID         REPOSITORY                TAG
>  <image-id>       <repository-path/name>    <tag-name>
>  ```

## <a name="run-the-container-with-the-docker-compose-up-command"></a>Ejecución del contenedor con el comando **docker-compose up**

* Reemplace los valores{ENDPOINT_URI} y {API_KEY} por el URI del punto de conexión del recurso y la clave de API de la página de recursos de Azure.

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: página Claves y punto de conexión de Azure Portal.":::

* Asegúrese de que el valor CLUF esté establecido en "accept".

* Los valores `EULA`, `Billing` y `ApiKey` deben estar especificados; de lo contrario, el contenedor no se iniciará.

> [!IMPORTANT]
> Las claves de suscripción se usan para acceder al recurso de Form Recognizer. No comparta las claves. Almacénelas de forma segura, por ejemplo, con Azure Key Vault. También se recomienda regenerar estas claves periódicamente. Solo se necesita una clave para realizar una llamada API. Al volver a generar la primera clave, puede usar la segunda clave para seguir teniendo acceso al servicio.

### <a name="layout"></a>[Diseño](#tab/layout)

A continuación se muestra un ejemplo de `docker compose` autocontenido para ejecutar el contenedor de diseño de Form Recognizer.  Con `docker compose`, se usa un archivo YAML para configurar los servicios de la aplicación. A continuación, con el comando `docker-compose up`, se crean e inician todos los servicios de la configuración. Escriba los valores {FORM_RECOGNIZER_ENDPOINT_URI} y {{FORM_RECOGNIZER_API_KEY} para la instancia de contenedor de diseño.

```yml
version: "3.9"
services:
azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    ports:
      - "5000"
    networks:
      - ocrvnet
networks:
  ocrvnet:
    driver: bridge
```

Ahora, puede iniciar el servicio con el comando [**docker compose**](https://docs.docker.com/compose/):

```bash
docker-compose up
```

### <a name="business-card"></a>[Tarjeta de presentación](#tab/business-card)

A continuación se muestra un ejemplo de `docker compose` autocontenido para ejecutar los contenedores de tarjeta de presentación y de lectura de Form Recognizer juntos. Con `docker compose`, se usa un archivo YAML para configurar los servicios de la aplicación. A continuación, con el comando `docker-compose up`, se crean e inician todos los servicios de la configuración. Escriba los valores {FORM_RECOGNIZER_ENDPOINT_URI} y {FORM_RECOGNIZER_API_KEY} para la instancia de contenedor de tarjeta de presentación. Escriba {COMPUTER_VISION_ENDPOINT_URI} y {COMPUTER_VISION_API_KEY} para el contenedor lectura de Computer Vision.

```yml
version: "3.9"
services:
  azure-cognitive-service-businesscard:
    container_name: azure-cognitive-service-businesscard
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Ahora, puede iniciar el servicio con el comando [**docker compose**](https://docs.docker.com/compose/):

```bash
docker-compose up
```

### <a name="id-document"></a>[Documento de identificación](#tab/id-document)

A continuación se muestra un ejemplo de `docker compose` autocontenido para ejecutar contenedores de documento de identificación y de lectura de Form Recognizer juntos. Con `docker compose`, se usa un archivo YAML para configurar los servicios de la aplicación. A continuación, con el comando `docker-compose up`, se crean e inician todos los servicios de la configuración. Escriba los valores {FORM_RECOGNIZER_ENDPOINT_URI} y {FORM_RECOGNIZER_API_KEY} para la instancia del contenedor de documento de identificación. Escriba los valores {COMPUTER_VISION_ENDPOINT_URI} y {COMPUTER_VISION_API_KEY} para el contenedor de lectura de Computer Vision.

```yml
version: "3.9"
services:
  azure-cognitive-service-id:
    container_name: azure-cognitive-service-id
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Ahora, puede iniciar el servicio con el comando [**docker compose**](https://docs.docker.com/compose/):

```bash
docker-compose up
```

### <a name="invoice"></a>[Factura](#tab/invoice)

A continuación se muestra un ejemplo de `docker compose` autocontenido para ejecutar los contenedores de factura y de diseño de Form Recognizer juntos. Con `docker compose`, se usa un archivo YAML para configurar los servicios de la aplicación. A continuación, con el comando `docker-compose up`, se crean e inician todos los servicios de la configuración.  Escriba los valores {FORM_RECOGNIZER_ENDPOINT_URI} y {FORM_RECOGNIZER_API_KEY} para la instancia del contenedor de factura y de diseño.

```yml
version: "3.9"
services:
  azure-cognitive-service-invoice:
    container_name: azure-cognitive-service-invoice
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceLayoutHost=http://azure-cognitive-service-layout:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    user: root
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Ahora, puede iniciar el servicio con el comando [**docker compose**](https://docs.docker.com/compose/):

```bash
docker-compose up
```

### <a name="receipt"></a>[Recibo](#tab/receipt)

A continuación se muestra un ejemplo de `docker compose` autocontenido para ejecutar contenedores de documento de recibo y de lectura de Form Recognizer juntos. Con `docker compose`, se usa un archivo YAML para configurar los servicios de la aplicación. A continuación, con el comando `docker-compose up`, se crean e inician todos los servicios de la configuración. Escriba los valores {FORM_RECOGNIZER_ENDPOINT_URI} y {FORM_RECOGNIZER_API_KEY} para la instancia del contenedor de recibo. Escriba los valores {COMPUTER_VISION_ENDPOINT_URI} y {COMPUTER_VISION_API_KEY} para el contenedor de lectura de Computer Vision.

```yml
version: "3.9"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
       - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Ahora, puede iniciar el servicio con el comando [**docker compose**](https://docs.docker.com/compose/):

```bash
docker-compose up
```

### <a name="custom"></a>[Personalizada](#tab/custom)

Además de los [requisitos previos](#prerequisites) mencionados anteriormente, deberá hacer lo siguiente para procesar un documento personalizado:

####  <a name="bullet-create-a-folder-to-store-the-following-files"></a>&bullet; Cree una carpeta para almacenar los archivos siguientes:

  1. [ **.env**](#-create-an-environment-file)
  1. [**nginx.conf**](#-create-a-nginx-file)
  1. [**docker-compose.yml**](#-create-a-docker-compose-file)

#### <a name="bullet-create-a-folder-to-store-your-input-data"></a>&bullet; Cree una carpeta para almacenar los datos de entrada

  1. Asigne a esta carpeta el nombre **compartido**.
  1. Haremos referencia a la ruta de acceso del archivo de esta carpeta como **{SHARED_MOUNT_PATH}** .
  1. Copie la ruta de acceso del archivo en una ubicación cómoda, como el *bloc de notas de Microsoft*. Deberá agregarlo al archivo **.env,** a continuación.

#### <a name="bullet-create-a-folder-to-store-the-logs--written-by-the-form-recognizer-service-on-your-local-machine"></a>&bullet; Cree una carpeta para almacenar los registros escritos por el servicio Form Recognizer en el equipo local.

  1. Asigne a esta carpeta el nombre **salida**.
  1. Haremos referencia a la ruta de acceso del archivo de esta carpeta como **{OUTPUT_MOUNT_PATH}** .
  1. Copie la ruta de acceso del archivo en una ubicación cómoda, como el *bloc de notas de Microsoft*. Deberá agregarlo al archivo **.env,** a continuación.

#### <a name="bullet-create-an-environment-file"></a>&bullet; Cree un archivo de entorno.

  1. Asigne a este archivo el nombre **.env**.

  1. Declare las siguientes variables de entorno:

  ```text
  SHARED_MOUNT_PATH="<file-path-to-shared-folder>"
  OUTPUT_MOUNT_PATH="<file -path-to-output-folder>"
  FORM_RECOGNIZER_ENDPOINT_URI="<your-form-recognizer-endpoint>"
  FORM_RECOGNIZER_API_KEY="<your-form-recognizer-apiKey>"
  RABBITMQ_HOSTNAME="rabbitmq"
  RABBITMQ_PORT=5672
  NGINX_CONF_FILE="<file-path>"
  ```

#### <a name="bullet-create-a-nginx-file"></a>&bullet; Cree un archivo **nginx**.

  1. Asigne a este archivo el nombre **nginx.conf**.

  1. Escriba la siguiente configuración:

```text
worker_processes 1;

events { worker_connections 1024; }

http {

    sendfile on;

    upstream docker-api {
        server azure-cognitive-service-custom-api:5000;
    }

    upstream docker-layout {
        server  azure-cognitive-service-layout:5000;
    }

    server {
        listen 5000;

        location = / {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-api/;

        }

        location /status {
            proxy_pass http://docker-api/status;

        }

        location /ready {
            proxy_pass http://docker-api/ready;

        }

        location /swagger {
            proxy_pass http://docker-api/swagger;

        }

        location /formrecognizer/v2.1/custom/ {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-api/formrecognizer/v2.1/custom/;

        }

        location /formrecognizer/v2.1/layout/ {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-layout/formrecognizer/v2.1/layout/;

        }
    }
}
```

* Reúna un conjunto de al menos seis formularios del mismo tipo. Usará estos datos para entrenar el modelo y probar un formulario. Puede usar un [conjunto de datos de ejemplo](https://go.microsoft.com/fwlink/?linkid=2090451) (descargue y extraiga *sample_data.zip*). Descargue los archivos de entrenamiento en la carpeta **compartida** que creó anteriormente.

* Si desea etiquetar los datos, descargue la herramienta [Form Recognizer de etiquetado de ejemplo para Windows](https://github.com/microsoft/OCR-Form-Tools/releases/tag/v2.1-ga). La descarga importará el archivo .exe de la herramienta de etiquetado que usará para etiquetar los datos presentes en el sistema de archivos local. Puede omitir las advertencias que se producen durante el proceso de descarga.

#### <a name="create-a-new-sample-labeling-tool-project"></a>Creación de un nuevo proyecto de herramienta de etiquetado de ejemplo

* Abra la herramienta de etiquetado haciendo doble clic en el archivo .exe de la herramienta de etiquetado de ejemplo.
* En el panel izquierdo de la herramienta, seleccione la pestaña Conexiones.
* Seleccione esta opción para crear un proyecto y asígnele un nombre y una descripción.
* Para el proveedor, elija la opción sistema de archivos local. En la carpeta local, asegúrese de escribir la ruta de acceso a la carpeta donde ha almacenado los archivos de datos de ejemplo.
* Vuelva a la pestaña de inicio y seleccione la opción "Usar personalizado para entrenar un modelo con etiquetas y pares clave-valor".
* Seleccione el botón Entrenar en el panel izquierdo para entrenar el modelo etiquetado.
* Guarde esta conexión y úsela para etiquetar las solicitudes.
* Puede elegir analizar el archivo que prefiera con el modelo entrenado.

#### <a name="bullet-create-a-docker-compose-file"></a>&bullet; Cree un archivo de **Docker Compose**.

1. Asigne a este archivo el nombre **docker-compose.yml**.

2. A continuación, se muestra un ejemplo independiente `docker compose` para ejecutar el diseño de Form Recognizer, la herramienta de etiquetado, la API personalizada y contenedores supervisados personalizados juntos. Con `docker compose`, se usa un archivo YAML para configurar los servicios de la aplicación. A continuación, con el comando `docker-compose up`, se crean e inician todos los servicios de la configuración.

  ```yml
  version: '3.3'
  services:
   nginx:
    image: nginx:alpine
    container_name: reverseproxy
    volumes:
      - ${NGINX_CONF_FILE}:/etc/nginx/nginx.conf
    ports:
      - "5000:5000"
   rabbitmq:
    container_name: ${RABBITMQ_HOSTNAME}
    image: rabbitmq:3
    expose:
      - "5672"
   layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      Logging:Console:LogLevel:Default: Information
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-api:
    container_name: azure-cognitive-service-custom-api
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-supervised:
    container_name: azure-cognitive-service-custom-supervised
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      CustomFormRecognizer:ContainerPhase: All
      CustomFormRecognizer:LayoutAnalyzeUri: http://azure-cognitive-service-layout:5000/formrecognizer/v2.1/layout/analyze
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
  ```

### <a name="ensure-the-service-is-running"></a>Compruebe que el servicio se esté ejecutando.

Para asegurarse de que el servicio está en funcionamiento. Ejecute estos comandos en un shell de Ubuntu.

```bash
$cd <folder containing the docker-compose file>

$source .env

$docker-compose up
```

### <a name="create-a-new-connection"></a>Crear una conexión

* En el panel izquierdo de la herramienta, seleccione la pestaña **Conexiones**.
* Seleccione esta opción para **crear un proyecto** y asígnele un nombre y una descripción.
* Para el proveedor, elija la opción de **sistema de archivos local**. En la carpeta local, asegúrese de escribir la ruta de acceso a la carpeta donde ha almacenado los archivos de **datos de ejemplo**.
* Vuelva a la pestaña de inicio y seleccione **Usar personalizado para entrenar un modelo con etiquetas y pares clave-valor**.
* Seleccione el botón **Entrenar** en el panel izquierdo para entrenar el modelo etiquetado.
* **Guarde** esta conexión y úsela para etiquetar las solicitudes.
* Puede elegir analizar el archivo que prefiera con el modelo entrenado.

---

## <a name="validate-that-the-service-is-running"></a>Valide que el servicio se está ejecutando.

Hay varias maneras de comprobar que el contenedor se está ejecutando:

* El contenedor proporciona una página principal en `\` como validación visual de que se está ejecutando.

* Puede abrir el explorador web que prefiera e ir a la dirección IP externa y el puerto expuesto del contenedor en cuestión. Use las distintas direcciones URL de solicitud para validar que el contenedor se está ejecutando. Las direcciones URL de solicitud de ejemplo que se enumeran a continuación son `http://localhost:5000`, pero el contenedor específico puede variar. Tenga en cuenta que va a ir a la **dirección IP externa** y el puerto expuesto del contenedor.

  URL de la solicitud    | Propósito
  ----------- | --------
  |**http://<span></span>localhost:5000/** | El contenedor proporciona una página principal.
  |**http://<span></span>localhost:5000/ready**     | Solicitado con GET, proporciona una comprobación de que el contenedor está listo para aceptar una consulta para el modelo. Esta solicitud se puede usar con los sondeos de ejecución y preparación de Kubernetes.
  |**http://<span></span>localhost:5000/status** | Se solicita con GET y comprueba si el valor de api-key usado para iniciar el contenedor es válido sin generar una consulta de punto de conexión. Esta solicitud se puede usar con los sondeos de ejecución y preparación de Kubernetes.
  |**http://<span></span>localhost:5000/swagger** | El contenedor cuenta con un completo conjunto de documentación sobre los puntos de conexión y una característica de prueba. Esta característica le permite especificar la configuración en un formulario HTML basado en web y realizar la consulta sin necesidad de escribir código. Una vez que la consulta devuelve resultados, se proporciona un ejemplo del comando CURL para mostrar los encabezados HTTP y el formato de cuerpo requeridos.
  |

:::image type="content" source="../media/containers/container-webpage.png" alt-text="Captura de pantalla: página principal del contenedor de Azure.":::

## <a name="stop-the-containers"></a>Detener los contenedores

Para detener los contenedores, use el comando siguiente:

```console
docker-compose down
```

## <a name="billing"></a>Facturación

Los contenedores de Form Recognizer envían información de facturación a Azure mediante un recurso de Form Recognizer en la cuenta de Azure.

Las consultas en el contenedor se facturan con el plan de tarifa del recurso de Azure que se usa para `ApiKey`. Se le facturará por cada instancia de contenedor que se utilice para procesar los documentos e imágenes. Por lo tanto, si usa la característica de tarjeta de presentación, se le facturará por las instancias de contenedor `BusinessCard` y `Compuer Vision Read` de Form Recognizer. Si usa la característica de tarjeta de presentación, se le facturará por las instancias de contenedor `Invoice` y `Layout` de Form Recognizer. *Consulte*, los precios del contenedor de la [característica de lectura](https://azure.microsoft.com/pricing/details/form-recognizer/) de [Form Recognizer](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) y de Computer Vision.

Los contenedores de Azure Cognitive Services no tienen licencia para ejecutarse si no están conectados al punto de conexión de facturación o a las mediciones. Debe habilitar los contenedores para que comuniquen la información de facturación al punto de conexión de facturación en todo momento. Los contenedores de Cognitive Services no envían datos de los clientes (por ejemplo, la imagen o el texto que se está analizando) a Microsoft.

### <a name="connect-to-azure"></a>Conexión con Azure

El contenedor necesita que se ejecuten los valores del argumento de facturación. Estos valores permiten al contenedor conectarse al punto de conexión de facturación. El contenedor informa sobre el uso cada 10 a 15 minutos. Si el contenedor no se conecta a Azure en la ventana de tiempo permitida, continuará ejecutándose pero no atenderá las consultas hasta que se restaure el punto de conexión de facturación. Se intenta 10 veces la conexión en el mismo intervalo de tiempo de 10 a 15 minutos. Si no se puede conectar con el punto de conexión de facturación en esos 10 intentos, el contenedor deja de atender solicitudes. Consulte [Preguntas más frecuentes acerca de los contenedores de Cognitive Services](../../../cognitive-services/containers/container-faq.yml#how-does-billing-work) para obtener un ejemplo de la información que se envía a Microsoft para la facturación.

### <a name="billing-arguments"></a>Argumentos de facturación

El comando [**docker-compose up**](https://docs.docker.com/engine/reference/commandline/compose_up/) iniciará el contenedor cuando se especifiquen las tres opciones siguientes con valores válidos:

| Opción | Descripción |
|--------|-------------|
| `ApiKey` | La clave de API del recurso de Cognitive Services que se usa para realizar un seguimiento de la información de facturación.<br/>El valor de esta opción se debe establecer en una clave de API para el recurso aprovisionado que se especifica en `Billing`. |
| `Billing` | El punto de conexión del recurso de Cognitive Services que se usa para realizar el seguimiento de la información de facturación.<br/>El valor de esta opción debe establecerse en el URI del punto de conexión de un recurso aprovisionado de Azure.|
| `Eula` | Indica que ha aceptado la licencia del contenedor.<br/>El valor de esta opción debe establecerse en **accept**. |

Para obtener más información acerca de estas opciones, consulte [Configure containers](form-recognizer-container-configuration.md) (Configuración de contenedores).

## <a name="summary"></a>Resumen

Eso es todo. En este artículo, ha aprendido los conceptos y el flujo de trabajo para la descarga, instalación y ejecución de contenedores de Form Recognizer. En resumen:

* Form Recognizer proporciona siete contenedores de Linux para Docker.
* Las imágenes de contenedor se descargan desde mcr.
* Las imágenes de contenedor se ejecutan en Docker.
* Debe especificar la información de facturación al crear una instancia de contenedor.

> [!IMPORTANT]
> Los contenedores de Cognitive Services no tienen licencia para ejecutarse sin estar conectados a Azure para realizar mediciones. Los clientes tienen que habilitar los contenedores para comunicar la información de facturación con el servicio de medición en todo momento. Los contenedores de Cognitive Services no envían datos de los clientes (por ejemplo, la imagen o el texto que se está analizando) a Microsoft.

## <a name="next-steps"></a>Pasos siguientes

* [Ajustes de configuración de contenedores de Form Recognizer](form-recognizer-container-configuration.md) 
