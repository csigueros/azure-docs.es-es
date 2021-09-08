---
title: 'Introducción a la conversión de voz en texto: el servicio Voz'
titleSuffix: Azure Cognitive Services
description: El software de conversión de voz en texto permite la transcripción en tiempo real de secuencias de audio en texto. Las aplicaciones, las herramientas o los dispositivos pueden consumir y mostrar esta entrada de texto, así como manipularla. En este artículo, encontrará información general sobre las ventajas y las funcionalidades del servicio de conversión de voz en texto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: conversión de voz a texto, software de conversión de voz en texto
ms.openlocfilehash: 7da34e76d760b8f29a90112c00a1f8f05655d940
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543995"
---
# <a name="what-is-speech-to-text"></a>¿Qué es la conversión de voz a texto?

En esta introducción, descubrirá las ventajas y las funcionalidades del servicio de conversión de voz en texto.
La conversión de voz en texto, que también se conoce como "reconocimiento de voz", permite transcribir secuencias de audio como texto en tiempo real. Las aplicaciones, las herramientas o los dispositivos pueden consumir y mostrar este texto como una entrada de comando, así como manipularlo. Este servicio funciona con la misma tecnología de reconocimiento que Microsoft utiliza para los productos de Cortana y Office. Funciona sin problemas con las ofertas de servicio de <a href="./speech-translation.md" target="_blank">traducción</a> y <a href="./text-to-speech.md" target="_blank">conversión de texto en voz</a>. Si desea obtener una lista completa de los idiomas disponibles para la conversión de voz a texto, consulte [Idiomas admitidos](language-support.md#speech-to-text).

De forma predeterminada, el servicio de conversión de voz en texto utiliza el modelo de lenguaje universal. Este modelo se entrenó con datos propiedad de Microsoft y se implementa en la nube. Resulta óptimo para escenarios de conversación y dictado. Si usa la conversión de voz en texto para el reconocimiento y la transcripción en un entorno único, puede crear y entrenar modelos acústicos, de lenguaje y pronunciación personalizados. La personalización es útil para abordar el ruido ambiente o el vocabulario específico del sector.

Esta documentación contiene los siguientes tipos de artículos:

* Los **inicios rápidos** son instrucciones de inicio que le guiarán a la hora de hacer solicitudes al servicio.
* Las **guías de procedimientos** contienen instrucciones para usar el servicio de una manera más específica o personalizada.
* Los **conceptos** proporcionan explicaciones detalladas sobre la funcionalidad y las características del servicio.
* Los **tutoriales** son guías más largas que muestran cómo usar el servicio como componente de soluciones empresariales más amplias.

> [!NOTE]
> Bing Speech se ha retirado el 15 de octubre de 2019. Si sus aplicaciones, herramientas o productos usan Bing Speech API, hemos creado guías para que le ayuden a migrar al servicio de voz.
> - [Migración de Bing Speech al servicio de voz](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>Introducción

Consulte el [inicio rápido](get-started-speech-to-text.md) para empezar a usar la conversión de voz en texto. El servicio está disponible con el [SDK de voz](speech-sdk.md), la [API REST](rest-speech-to-text.md#pronunciation-assessment-parameters) y la [CLI de voz](spx-overview.md).

## <a name="sample-code"></a>Código de ejemplo

Hay un ejemplo de código para el SDK de voz disponible en GitHub. En estos ejemplos se tratan escenarios comunes como la lectura de audio de un archivo o flujo, el reconocimiento continuo y de una sola emisión, y el trabajo con modelos personalizados.

- [Ejemplos de conversión de voz a texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ejemplos de transcripción de Azure Batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Ejemplos de evaluación de pronunciación (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Personalización

Además del modelo de servicio de voz estándar, puede crear modelos personalizados. La personalización ayuda a eliminar las barreras del reconocimiento de voz, como el estilo de habla, el vocabulario y el ruido de fondo. Consulte [Habla personalizada](./custom-speech-overview.md). Las opciones de personalización varían según el idioma o la configuración regional (consulte los [idiomas admitidos](./language-support.md) para comprobar la compatibilidad).

## <a name="batch-transcription"></a>Transcripción de Azure Batch

La transcripción por lotes es un conjunto de operaciones de API REST que permite transcribir una gran cantidad de audio en almacenamiento. Puede apuntar a archivos de audio con un identificador URI de firma de acceso compartido (SAS) y recibir los resultados de las transcripciones de forma asincrónica. Para más información sobre cómo usar la API de transcripción por lotes, consulte el [procedimiento](batch-transcription.md).

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga una clave de suscripción gratuita a los servicios de Voz](overview.md#try-the-speech-service-for-free)
- [Obtención del SDK de voz](speech-sdk.md)