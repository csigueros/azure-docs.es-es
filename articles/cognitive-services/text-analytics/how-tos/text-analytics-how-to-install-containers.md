---
title: Instalación y ejecución de contenedores de Docker para Text Analytics API
titleSuffix: Azure Cognitive Services
description: Use los contenedores de Docker para que Text Analytics API realice un procesamiento del lenguaje natural, como un análisis de sentimiento, en el entorno local.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020, devx-track-azurecli
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: aahi
keywords: local, Docker, contenedor, análisis de sentimiento, procesamiento de lenguaje natural
ms.openlocfilehash: 4cf8ddce1045209f6d5e45a2eaccbbf4d2f03afc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677574"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalación y ejecución de contenedores de Text Analytics

Los contenedores permiten ejecutar las API Text Analytics en su propio entorno y son ideales para sus requisitos específicos de seguridad y gobernanza de datos. Los siguientes contenedores de Text Analytics están disponibles:

* análisis de opinión
* detección de idioma
* extracción de frases clave (versión preliminar)
* Text Analytics for Health 

> [!NOTE]
> * La vinculación de entidades y NER no están disponibles actualmente como contenedor.
> * Es posible que las ubicaciones de las imágenes de contenedor hayan cambiado recientemente. Lea este artículo para ver la ubicación actualizada de este contenedor.
> * La cuenta gratuita se limita a 5000 registros de texto al mes y solo los [planes de tarifa](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics) **Gratis** y **Estándar** son válidos para los contenedores. Para más información sobre las tasas de solicitudes de las transacciones, consulte [Límites de datos](../concepts/data-limits.md).

Los contenedores permiten ejecutar las API Text Analytics en su propio entorno y son ideales para sus requisitos específicos de seguridad y gobernanza de datos. Los contenedores de Text Analytics proporcionan un procesamiento avanzado de lenguaje natural sobre texto sin formato, e incluye tres funciones principales: análisis de sentimiento, extracción de frases clave y detección de idioma.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Debe cumplir los siguientes requisitos previos para poder usar contenedores de Text Analytics. Si no tiene una suscripción de Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.

* [Docker](https://docs.docker.com/) instalado en un equipo host. Docker debe configurarse para permitir que los contenedores se conecten con Azure y envíen datos de facturación a dicho servicio. 
    * En Windows, Docker también debe estar configurado de forma que admita los contenedores de Linux.
    * Debe estar familiarizado con los [conceptos de Docker](https://docs.docker.com/get-started/overview/). 
* Un <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="recurso de Text Analytics"  target="_blank"> </a> con el [plan de tarifa](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) gratis (F0) o estándar (S).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Recomendaciones y requisitos del equipo host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

En la tabla siguiente se describen las especificaciones mínima y recomendada para los contenedores de Text Analytics disponibles. Cada núcleo de CPU debe ser de 2,6 gigahercios (GHz) como mínimo. También se enumeran las transacciones permitidas por segundo (TPS).

|  | Especificaciones de host mínimas | Especificaciones de host recomendadas | TPS mínimas | TPS máximas|
|---|---------|-------------|--|--|
| **Detección de idioma**   | 1 núcleo, 2 GB de memoria | 1 núcleo, 4 GB de memoria |15 | 30| 
| **extracción de frases clave (versión preliminar)**   | 1 núcleo, 2 GB de memoria | 1 núcleo, 4 GB de memoria |15 | 30| 
| **Análisis de sentimiento**   | 1 núcleo, 2 GB de memoria | 4 núcleos, 8 GB de memoria |15 | 30|
| **Text Analytics para el estado: 1 documento/solicitud**   |  4 núcleos, 10 GB de memoria | 6 núcleos, 12 GB de memoria |15 | 30|
| **Text Analytics para el estado: 10 documento/solicitud**   |  6 núcleos, 16 GB de memoria | 8 núcleos, 20 GB de memoria |15 | 30|

El núcleo de CPU y la memoria corresponden a los valores de `--cpus` y `--memory` que se usan como parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtención de la imagen del contenedor con `docker pull`

# <a name="sentiment-analysis"></a>[Análisis de sentimiento ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extracción de frases clave (versión preliminar)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Detección de idioma](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[Text Analytics for Health](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Ejecute el contenedor con `docker run`.

Una vez que el contenedor esté en el equipo host, use el comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para ejecutar los contenedores. El contenedor continuará ejecutándose hasta que lo detenga.

> [!IMPORTANT]
> * Los comandos de Docker de las secciones siguientes usan la barra diagonal inversa, `\`, como un carácter de continuación de línea. Puede quitarla o reemplazarla en función de los requisitos del sistema operativo del host. 
> * Para poder ejecutar el contenedor, las opciones `Eula`, `Billing` y `ApiKey` deben estar especificadas; de lo contrario, el contenedor no se iniciará.  Para obtener más información, vea [Facturación](#billing).
>   * Si usa Text Analytics para el contenedor de mantenimiento, la confirmación de [IA responsable](/legal/cognitive-services/text-analytics/transparency-note-health) (RAI) también debe estar presente con un valor de `accept`.
> * Los contenedores de análisis de sentimiento y detección de idioma usan la versión v3 de la API y están disponibles con carácter general. El contenedor de extracción de frases clave usa la versión 2 de la API y está en versión preliminar.

# <a name="sentiment-analysis"></a>[Análisis de sentimiento](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extracción de frases clave (versión preliminar)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Detección de idioma](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[Text Analytics for Health](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consulta del punto de conexión de predicción del contenedor

El contenedor proporciona varias API de puntos de conexión de predicción de consultas basadas en REST.

Utilice el host, `http://localhost:5000`, con las API de contenedor.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Detención del contenedor

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solución de problemas

Si ejecuta el contenedor con un [montaje](../text-analytics-resource-container-config.md#mount-settings) de salida y el registro habilitados, el contenedor genera archivos de registro que resultan útiles para solucionar problemas que se producen al iniciar o ejecutar el contenedor.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Facturación

Los contenedores de Text Analytics envían información de facturación a Azure mediante un recurso de _Text Analytics_ en la cuenta de Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obtener más información acerca de estas opciones, consulte [Configure containers](../text-analytics-resource-container-config.md) (Configuración de contenedores).

## <a name="summary"></a>Resumen

En este artículo, ha aprendido los conceptos y el flujo de trabajo para la descarga, instalación y ejecución de contenedores de Text Analytics. En resumen:

* Text Analytics proporciona tres contenedores Linux para Docker, que encapsulan varias funcionalidades:
   * *Análisis de sentimiento*
   * *Extracción de frases clave (versión preliminar)* 
   * *Detección de idioma*
   * *Text Analytics for Health*
* Las imágenes del contenedor se descargan desde Microsoft Container Registry (MCR).
* Las imágenes de contenedor se ejecutan en Docker.
* Puede usar la API REST o el SDK para llamar a operaciones en contenedores de Text Analytics mediante la especificación del URI del host del contenedor.
* Debe especificar la información de facturación al crear una instancia de un contenedor.

> [!IMPORTANT]
> Los contenedores de Cognitive Services no tienen licencia para ejecutarse sin estar conectados a Azure para realizar mediciones. Los clientes tienen que habilitar los contenedores para comunicar la información de facturación con el servicio de medición en todo momento. Los contenedores de Cognitive Services no envían datos de los clientes (por ejemplo, el texto que se está analizando) a Microsoft.

## <a name="next-steps"></a>Pasos siguientes

* Para ver las opciones de configuración, consulte [Configuración de contenedores](../text-analytics-resource-container-config.md).
