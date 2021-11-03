---
title: Uso de contenedores de Docker para Extracción de frases clave en el entorno local
titleSuffix: Azure Cognitive Services
description: Obtenga más información acerca del uso de contenedores de Docker para Extracción de frases clave en el entorno local.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-key-phrase, ignite-fall-2021
keywords: entorno local, Docker, contenedor, procesamiento de lenguaje natural
ms.openlocfilehash: 51d7c53beaeabe57df5fe817cdb5c16c6d1d4252
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093758"
---
# <a name="install-and-run-key-phrase-extraction-containers"></a>Instalación y ejecución de contenedores de Extracción de frases clave


Los contenedores permiten hospedar la API de Extracción de frases clave en su propia infraestructura. Si tiene requisitos de seguridad o requisitos de gobernanza de datos que no se pueden cumplir llamando a Extracción de frases clave de forma remota, los contenedores podrían ser una buena opción.


> [!NOTE]
> * La cuenta gratuita se limita a 5000 registros de texto al mes y solo los [planes de tarifa](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics) **Gratis** y **Estándar** son válidos para los contenedores. Para más información sobre las tasas de solicitudes de las transacciones, consulte [Límites de datos](call-api.md#data-limits).

Los contenedores permiten ejecutar las API de Extracción de frases clave en su propio entorno y son ideales para sus requisitos específicos de seguridad y gobernanza de datos. Los contenedores de Extracción de frases clave proporcionan un procesamiento avanzado de lenguaje natural sobre texto sin formato, e incluye tres funciones principales: análisis de sentimiento, extracción de frases clave y detección de idioma.

## <a name="prerequisites"></a>Prerrequisitos

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/).
* [Docker](https://docs.docker.com/) instalado en un equipo host. Docker debe configurarse para permitir que los contenedores se conecten con Azure y envíen datos de facturación a dicho servicio. 
    * En Windows, Docker también debe estar configurado de forma que admita los contenedores de Linux.
    * Debe estar familiarizado con los [conceptos de Docker](https://docs.docker.com/get-started/overview/). 
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Creación de un recurso de idioma"  target="_blank">, el recurso de idioma </a> con el [plan de tarifa](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) gratis (F0) o estándar (S).

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Recomendaciones y requisitos del equipo host

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

En la tabla siguiente se describen las especificaciones mínima y recomendada para los contenedores de Extracción de frases clave disponibles. Cada núcleo de CPU debe ser de 2,6 gigahercios (GHz) como mínimo. También se enumeran las transacciones permitidas por segundo (TPS).

|  | Especificaciones de host mínimas | Especificaciones de host recomendadas | TPS mínimas | TPS máximas|
|---|---------|-------------|--|--|
| **Extracción de frases clave**   | 1 núcleo, 2 GB de memoria | 1 núcleo, 4 GB de memoria |15 | 30| 

El núcleo de CPU y la memoria corresponden a los valores de `--cpus` y `--memory` que se usan como parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtención de la imagen del contenedor con `docker pull`

Use el comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) para descargar la imagen de contenedor del Registro de contenedor de Microsoft.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```

[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Ejecute el contenedor con `docker run`.

Una vez que el contenedor esté en el equipo host, use el comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para ejecutar los contenedores. El contenedor continuará ejecutándose hasta que lo detenga.

> [!IMPORTANT]
> * Los comandos de Docker de las secciones siguientes usan la barra diagonal inversa, `\`, como un carácter de continuación de línea. Puede quitarla o reemplazarla en función de los requisitos del sistema operativo del host. 
> * Para poder ejecutar el contenedor, las opciones `Eula`, `Billing` y `ApiKey` deben estar especificadas; de lo contrario, el contenedor no se iniciará.  Para obtener más información, vea [Facturación](#billing).
> * Los contenedores de análisis de sentimiento y detección de idioma usan la versión v3 de la API y están disponibles con carácter general. El contenedor de Extracción de frases clave usa la versión 2 de la API y está en versión preliminar.

Para ejecutar el contenedor *Extracción de frases clave*, ejecute el siguiente comando `docker run`. Reemplace los marcadores de posición por sus propios valores:

| Marcador de posición | Value | Formato o ejemplo |
|-------------|-------|---|
| **{CLAVE_API}** | Clave del recurso Extracción de frases clave. Puede encontrarla en la página **Clave y punto de conexión** del recurso en Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{URI_PUNTODECONEXIÓN}** | Punto de conexión para acceder a la API de Extracción de frases clave. Puede encontrarla en la página **Clave y punto de conexión** del recurso en Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Ejecuta un contenedor de *extracción de frases clave* desde la imagen de contenedor.
* Asigna un núcleo de CPU y 4 gigabytes (GB) de memoria.
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

Los contenedores Extracción de frases clave envían información de facturación a Azure mediante un recurso de _Extracción de frases clave_ en su cuenta de Azure. 

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]
\
## <a name="summary"></a>Resumen

En este artículo, ha aprendido los conceptos y el flujo de trabajo para la descarga, instalación y ejecución de contenedores de Extracción de frases clave. En resumen:

* Extracción de frases clave proporciona contenedores de Linux para Docker.
* Las imágenes del contenedor se descargan desde Microsoft Container Registry (MCR).
* Las imágenes de contenedor se ejecutan en Docker.
* Puede usar la API de REST o el SDK para llamar a operaciones en contenedores de Extracción de frases clave mediante la especificación del URI del host del contenedor.
* Debe especificar la información de facturación al crear una instancia de un contenedor.

> [!IMPORTANT]
> Los contenedores de Cognitive Services no tienen licencia para ejecutarse sin estar conectados a Azure para realizar mediciones. Los clientes tienen que habilitar los contenedores para comunicar la información de facturación con el servicio de medición en todo momento. Los contenedores de Cognitive Services no envían datos de los clientes (por ejemplo, el texto que se está analizando) a Microsoft.

## <a name="next-steps"></a>Pasos siguientes

* Para ver las opciones de configuración, consulte [Configuración de contenedores](../../concepts/configure-containers.md).
