---
title: Configuración de contenedores de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Vea cómo configurar el contenedor Form Recognizer para analizar los datos de formularios y tablas.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
ms.openlocfilehash: a7a42f9a2078a3384949704a6319c8acbedcb17d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326582"
---
# <a name="configure-form-recognizer-containers"></a>Configuración de contenedores de Form Recognizer

> [!IMPORTANT]
>
> Los contenedores de Form Recognizer están en versión preliminar validada. Para usarlos, debe enviar una [solicitud en línea](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) y recibir la aprobación. Consulte [**Solicitud de aprobación para ejecutar un contenedor**](form-recognizer-container-install-run.md#request-approval-to-run-the-container) a continuación para obtener más información.

Con los contenedores de Azure Form Recognizer, puede crear una arquitectura de aplicación optimizada para aprovechar las sólidas funcionalidades de la nube y la localidad del perímetro. Los contenedores proporcionan un entorno minimalista prácticamente aislado que se puede implementar fácilmente en el entorno local y en la nube. En este artículo, aprenderá a configurar el entorno en tiempo real del contenedor de Form Recognizer mediante los argumentos del comando `docker compose`. Las características de Form Recognizer son ahora compatibles con seis contenedores de características: **Diseño**, **Tarjeta de presentación**,**Documento de identificación**,  **Recibo**, **Factura** y **Personalizado**. Estos contenedores tienen varias configuraciones necesarias y algunas opcionales. Para ver algunos ejemplos, consulte la sección [Archivo de ejemplo docker-compose.yml](#example-docker-composeyml-file).

## <a name="configuration-settings"></a>Parámetros de configuración

Cada contenedor tiene las siguientes opciones de configuración:

|Obligatorio|Configuración|Propósito|
|--|--|--|
|Sí|[ApiKey](#apikey-and-billing-configuration-setting)|Realiza el seguimiento de la información de facturación.|
|Sí|[Facturación](#apikey-and-billing-configuration-setting)|Especifica el URI del punto de conexión del recurso de servicio en Azure. Para más información sobre cómo obtenerlo, _consulte_ [Facturación]](form-recognizer-container-install-run.md#billing). Para más información y obtener una lista completa de los puntos de conexión regionales, _consulte_ [Nombres de subdominios personalizados para Cognitive Services](../../../cognitive-services/cognitive-services-custom-subdomains.md).|
|Sí|[Eula](#eula-setting)| Indica que ha aceptado la licencia del contenedor.|
|No|[ApplicationInsights](#applicationinsights-setting)|Permite agregar compatibilidad con los datos de telemetría de [Azure Application Insights](/azure/application-insights) al contenedor.|
|No|[Fluentd](#fluentd-settings)|Escribe el registro y, opcionalmente, los datos de métricas en un servidor de Fluentd.|
|No|Proxy HTTP|Configura un proxy HTTP para realizar solicitudes de salida.|
|No|[Logging](#logging-settings)|Proporciona compatibilidad con el registro de ASP.NET Core al contenedor. |

> [!IMPORTANT]
> Los valores de configuración [`ApiKey`](#apikey-and-billing-configuration-setting), [`Billing`](#apikey-and-billing-configuration-setting) y [`Eula`](#eula-setting) se usan juntos. Debe proporcionar valores válidos para los tres o los contenedores no se iniciarán. Para obtener más información sobre el uso de estas opciones de configuración para crear instancias de un contenedor, consulte [Facturación](form-recognizer-container-install-run.md#billing).

## <a name="apikey-and-billing-configuration-setting"></a>Configuración de ApiKey y Billing

La opción de configuración `ApiKey` especifica la clave de recurso de Azure usada para realizar un seguimiento de la información de facturación del contenedor. El valor de ApiKey debe ser una clave válida para el recurso especificado en `Billing` en la sección de configuración de la facturación.

El valor `Billing` especifica el URI de punto de conexión del recurso de Azure que se usa para medir la información de facturación del contenedor. El valor de esta opción de configuración debe ser un URI de punto de conexión válido para un recurso de Azure. El contenedor informa sobre el uso cada 10 a 15 minutos.

 Puede encontrar estos valores en Azure Portal, en la página **Keys and Endpoint** (Claves y puntos de conexión).

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: página Claves y punto de conexión de Azure Portal.":::

## <a name="eula-setting"></a>Opción de configuración Eula

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="applicationinsights-setting"></a>Opción de configuración ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="fluentd-settings"></a>Opción de configuración Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configuración de las credenciales del proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Opción de configuración Logging

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="volume-settings"></a>Configuración del volumen

Utilice [**volúmenes**](https://docs.docker.com/storage/volumes/) para leer y escribir datos en el contenedor y desde ahí. Los volúmenes son el medio preferido para conservar los datos generados y usados por los contenedores de Docker. Puede especificar un montaje de entrada o un montaje de salida incluyendo la opción `volumes` y especificando `type` (enlazar), `source` (ruta de acceso a la carpeta) y `target` (parámetro de ruta de acceso de archivo).

El contenedor de Form Recognizer requiere un volumen de entrada y un volumen de salida. El volumen de entrada puede ser de solo lectura (`ro`) y es necesario para acceder a los datos que se usan para el entrenamiento y la puntuación. El volumen de salida se debe poder escribir y se usará para almacenar los modelos y los datos temporales.

La sintaxis exacta de la ubicación de volumen del host varía según el sistema operativo host. Además, la ubicación de volumen del [equipo host](form-recognizer-container-install-run.md#host-computer-requirements) puede no ser accesible debido a un conflicto entre los permisos de la cuenta de servicio de Docker y los permisos de la ubicación de montaje del host.

## <a name="example-docker-composeyml-file"></a>Archivo docker-compose.yml de ejemplo

El método **docker compose** consta de tres pasos:

 1. Creación de un archivo Dockerfile.
 1. Defina los servicios en un archivo **docker-compose.yml**, para que se puedan ejecutar juntos en un entorno aislado.
 1. Ejecute `docker-compose up` para iniciar y ejecutar los servicios.

### <a name="single-container-example"></a>Ejemplo de contenedor único

En este ejemplo, escriba los valores {FORM_RECOGNIZER_ENDPOINT_URI} y {FORM_RECOGNIZER_API_KEY} para la instancia de contenedor de diseño.

#### <a name="layout-container"></a>**Contenedor de diseño**

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

### <a name="multiple-containers-example"></a>Ejemplo de varios contenedores

#### <a name="receipt-and-ocr-read-containers"></a>**Contenedores de recibo y lectura de OCR**

En este ejemplo, escriba los valores {FORM_RECOGNIZER_ENDPOINT_URI} y {FORM_RECOGNIZER_API_KEY} para el contenedor de recibos y los valores {COMPUTER_VISION_ENDPOINT_URI} y {COMPUTER_VISION_API_KEY} para el contenedor de lectura de Computer Vision.

```yml
version: "3"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: cognitiveservicespreview.azurecr.io/microsoft/cognitive-services-form-recognizer-receipt:2.1
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

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre la ejecución de varios contenedores y el comando docker compose](form-recognizer-container-install-run.md)
