---
title: 'Documentación de Swagger: servicio de voz'
titleSuffix: Azure Cognitive Services
description: La documentación de Swagger se puede utilizar para generar automáticamente SDK para varios lenguajes de programación. Todas las operaciones de nuestro servicio son compatibles con Swagger
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: 3b27bd16168f4949081f3f399f7f8f8293d2aa0b
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292573"
---
# <a name="swagger-documentation"></a>Documentación de Swagger

El servicio de voz ofrece una especificación de Swagger para interactuar con varias de las API REST que se usan para importar datos, crear modelos, probar la precisión de los modelos, crear puntos de conexión personalizados, poner en cola las transcripciones en lote y administrar las suscripciones. La mayoría de las operaciones disponibles en el [área de Habla personalizada de Speech Studio](./custom-speech-overview.md) se pueden completar mediante programación con estas API.

> [!NOTE]
> Los servicios de voz tienen varias API REST para la [conversión de voz en texto](rest-speech-to-text.md) y la [conversión de texto a voz](rest-text-to-speech.md).  
>
> Sin embargo, solo la [API REST Speech-to-text v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) y v2.0 se documentan en la especificación de Swagger. Vea los documentos a los que se hace referencia en el párrafo anterior para obtener información sobre todas las demás API REST de Speech Services.

## <a name="generating-code-from-the-swagger-specification"></a>Generación de código desde la especificación Swagger

La [especificación Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) tiene opciones que permiten probar rápidamente varias rutas de acceso. Sin embargo, a veces es conveniente generar código para todas las rutas de acceso, lo que crea una sola biblioteca de llamadas en la que se puedan basar las soluciones futuras. Echemos un vistazo al proceso para generar una biblioteca Python.

Tendrá que configurar Swagger en la región del recurso de Voz. Puede confirmar la región en la parte de **información general** de la configuración de los recursos de Voz en Azure portal. [Aquí](regions.md#speech-to-text) se encuentra la lista completa de regiones admitidas.

1. En un explorador, vaya a la especificación de Swagger de la [región](regions.md#speech-to-text):  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. En esa página, haga clic en **Definición de API** y en **Swagger**. Copie la dirección URL de la página que aparece.
1. En un nuevo explorador, vaya a [https://editor.swagger.io](https://editor.swagger.io)
1. Haga clic en **Archivo**, en **Import URL** (URL de importación), pegue la dirección URL y haga clic en **Aceptar**.
1. Haga clic en **Generate Client** (Generar cliente) y seleccione **Python**. La biblioteca cliente se descarga en el equipo en un archivo `.zip`.
1. Extraiga todo de la descarga. Puede usar `tar -xf` para extraer todo.
1. Instale el módulo extraído en el entorno de Python:  
      `pip install path/to/package/python-client`
1. El nombre del paquete instalado es `swagger_client`. Compruebe que la instalación ha funcionado:  
       `python -c "import swagger_client"`

Puede usar la biblioteca Python que generó con los [ejemplos del servicio de voz en GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-documents"></a>Documentación de referencia

* [Swagger: Speech-to-text REST API v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [Speech-to-text REST API](rest-speech-to-text.md)
* [Text-to-speech REST API](rest-text-to-speech.md)

## <a name="next-steps"></a>Pasos siguientes

* [Ejemplos del servicio de voz en GitHub](https://aka.ms/csspeech/samples).
* [Obtenga una clave de suscripción gratuita a los servicios de Voz](overview.md#try-the-speech-service-for-free)