---
title: Instalación y ejecución de contenedores de Docker para el Análisis de sentimiento
titleSuffix: Azure Cognitive Services
description: Use los contenedores de Docker para que la API de Análisis de sentimiento realice un procesamiento del lenguaje natural, como un análisis de sentimiento, en el entorno local.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
keywords: local, Docker, contenedor, análisis de sentimiento, procesamiento de lenguaje natural
ms.openlocfilehash: 0239280045cab18627ba6e888cd75b03de0d54e4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093230"
---
# <a name="install-and-run-sentiment-analysis-containers"></a>Instalación y ejecución de contenedores de Análisis de sentimiento

Los contenedores permiten hospedar la API de Análisis de sentimiento en su propia infraestructura. Si tiene requisitos de seguridad o de gobernanza de datos que no se pueden cumplir llamando al Análisis de sentimiento de forma remota, los contenedores podrían ser una buena opción.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

Debe cumplir los siguientes requisitos previos para poder usar contenedores del Análisis de sentimiento. Si no tiene una suscripción de Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.

* [Docker](https://docs.docker.com/) instalado en un equipo host. Docker debe configurarse para permitir que los contenedores se conecten con Azure y envíen datos de facturación a dicho servicio. 
    * En Windows, Docker también debe estar configurado de forma que admita los contenedores de Linux.
    * Debe estar familiarizado con los [conceptos de Docker](https://docs.docker.com/get-started/overview/). 
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Creación de un recurso de idioma"  target="_blank">, el recurso de idioma </a> con el [plan de tarifa](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) gratis (F0) o estándar (S).

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Recomendaciones y requisitos del equipo host

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

En la tabla siguiente se describen las especificaciones mínima y recomendada para los contenedores disponibles. Cada núcleo de CPU debe ser de 2,6 gigahercios (GHz) como mínimo. También se enumeran las transacciones permitidas por segundo (TPS).

|  | Especificaciones de host mínimas | Especificaciones de host recomendadas | TPS mínimas | TPS máximas|
|---|---------|-------------|--|--|
| **Análisis de sentimiento**   | 1 núcleo, 2 GB de memoria | 4 núcleos, 8 GB de memoria |15 | 30|

El núcleo de CPU y la memoria corresponden a los valores de `--cpus` y `--memory` que se usan como parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtención de la imagen del contenedor con `docker pull`

El contenedor de Análisis de sentimiento v3 está disponible en varios idiomas. Para descargar el contenedor en inglés, use el siguiente comando. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Para descargar el contenedor para otro idioma, reemplace `en` por uno de los siguientes códigos de idioma. 

| Contenedor de Análisis de sentimiento | Código de lenguaje |
|--|--|
| Chino simplificado    |   `zh-hans`   |
| Chino (tradicional)   |   `zh-hant`   |
| Neerlandés                 |     `nl`      |
| Inglés               |     `en`      |
| Francés                |     `fr`      |
| Alemán                |     `de`      |
| Hindi                 |    `hi`       |
| Italiano               |     `it`      |
| Japonés              |     `ja`      |
| Coreano                |     `ko`      |
| Noruego (Bokmål)   |     `no`      |
| Portugués (Brasil)   |    `pt-BR`    |
| Portugués (Portugal) |    `pt-PT`    |
| Español               |     `es`      |
| Turco               |     `tr`      |

[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Ejecute el contenedor con `docker run`.

Una vez que el contenedor esté en el equipo host, use el comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para ejecutar los contenedores. El contenedor continuará ejecutándose hasta que lo detenga.

> [!IMPORTANT]
> * Los comandos de Docker de las secciones siguientes usan la barra diagonal inversa, `\`, como un carácter de continuación de línea. Puede quitarla o reemplazarla en función de los requisitos del sistema operativo del host. 
> * Para poder ejecutar el contenedor, las opciones `Eula`, `Billing` y `ApiKey` deben estar especificadas; de lo contrario, el contenedor no se iniciará.  Para obtener más información, vea [Facturación](#billing).

Para ejecutar el contenedor Análisis de sentimiento, ejecute el siguiente comando `docker run`. Reemplace los marcadores de posición por sus propios valores:

| Marcador de posición | Value | Formato o ejemplo |
|-------------|-------|---|
| **{CLAVE_API}** | Clave del recurso de lenguaje. Puede encontrarla en la página **Clave y punto de conexión** del recurso en Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{URI_PUNTODECONEXIÓN}** | Punto de conexión para acceder a la API. Puede encontrarla en la página **Clave y punto de conexión** del recurso en Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{LENGUAJE}** | El lenguaje del contenedor que quiere ejecutar. Asegúrese de que esto coincide con el comando `docker pull` que ha usado. Observe el guion (`-`) que se usa antes del lenguaje en el ejemplo siguiente. | `en` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment-{LANGUAGE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Ejecuta un contenedor de *Análisis de sentimiento* desde la imagen de contenedor.
* Asigna un núcleo de CPU y 8 gigabytes (GB) de memoria.
* Expone el puerto TCP 5000 y asigna un seudo-TTY para el contenedor.
* Una vez que se produce la salida, quita automáticamente el contenedor. La imagen del contenedor sigue estando disponible en el equipo host.

[!INCLUDE [Running multiple containers on the same host](../../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consulta del punto de conexión de predicción del contenedor

El contenedor proporciona varias API de puntos de conexión de predicción de consultas basadas en REST.

Utilice el host, `http://localhost:5000`, con las API de contenedor.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Detención del contenedor

[!INCLUDE [How to stop the container](../../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solución de problemas

Si ejecuta el contenedor con un [montaje](../../concepts/configure-containers.md#mount-settings) de salida y el registro habilitados, el contenedor genera archivos de registro que resultan útiles para solucionar problemas que se producen al iniciar o ejecutar el contenedor.

[!INCLUDE [Cognitive Services FAQ note](../../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Facturación

Los contenedores de Análisis de sentimiento envían información de facturación a Azure mediante un recurso de _Lenguaje_ en la cuenta de Azure.

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obtener más información acerca de estas opciones, consulte [Configure containers](../../concepts/configure-containers.md) (Configuración de contenedores).

## <a name="summary"></a>Resumen

En este artículo, aprendió los conceptos y el flujo de trabajo para la descarga, instalación y ejecución de los contenedores de Análisis de sentimiento. En resumen:

* El Análisis de sentimiento proporciona contenedores de Linux para Docker.
* Las imágenes del contenedor se descargan desde Microsoft Container Registry (MCR).
* Las imágenes de contenedor se ejecutan en Docker.
* Debe especificar la información de facturación al crear una instancia de un contenedor.

> [!IMPORTANT]
> Los contenedores de Cognitive Services no tienen licencia para ejecutarse sin estar conectados a Azure para realizar mediciones. Los clientes tienen que habilitar los contenedores para comunicar la información de facturación con el servicio de medición en todo momento. Los contenedores de Cognitive Services no envían datos de los clientes (por ejemplo, el texto que se está analizando) a Microsoft.

## <a name="next-steps"></a>Pasos siguientes

* Para ver las opciones de configuración, consulte [Configuración de contenedores](../../concepts/configure-containers.md).
