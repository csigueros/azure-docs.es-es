---
title: La CLI de voz de Azure
titleSuffix: Azure Cognitive Services
description: La CLI de voz es una herramienta de línea de comandos para usar el servicio de voz sin necesidad de escribir código. La CLI de voz requiere una configuración mínima y es fácil empezar a experimentar de inmediato con características clave del servicio de voz para ver si pueden resultar útiles para sus casos de uso.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: pafarley
ms.custom: devx-track-azurecli
ms.openlocfilehash: f0764a6d9d3cf1539237e552d6a3324c5f6412c3
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538568"
---
# <a name="what-is-the-speech-cli"></a>¿Qué es la CLI de Voz?

La CLI de voz es una herramienta de línea de comandos para usar el servicio de voz sin necesidad de escribir código. La CLI de voz requiere una configuración mínima y es fácil empezar a experimentar de inmediato con características clave del servicio de voz para ver si pueden resultar útiles para sus casos de uso. En cuestión de minutos, podrá ejecutar flujos de trabajo de prueba sencillos como, por ejemplo, el reconocimiento de voz por lotes de un directorio de archivos o la conversión de texto a voz en una colección de cadenas de un archivo. Además de los flujos de trabajo sencillos, la CLI de voz está preparada para producción y se puede escalar verticalmente para ejecutar procesos más grandes mediante `.bat` automatizados o scripts de shell.

La mayoría de las características principales del SDK de voz están disponibles en la CLI de voz, y algunas características y personalizaciones avanzadas se han simplificado en esta última. Tenga en cuenta las siguientes instrucciones para decidir cuándo usar la CLI de voz o el SDK de voz.

Use la CLI de voz cuando:
* Quiera experimentar con las características del servicio de voz con una configuración mínima y sin código
* Tenga requisitos relativamente sencillos para una aplicación de producción que usa el servicio de voz

Use el SDK de voz cuando:
* Desee integrar la funcionalidad del servicio de voz con un lenguaje o plataforma específicos (por ejemplo, C#, Python, C++).
* Tiene requisitos complejos que pueden requerir solicitudes de servicio avanzadas o desarrollar un comportamiento personalizado que incluye el streaming de respuesta

## <a name="core-features"></a>Características principales

* Reconocimiento de voz: convierta voz en texto a partir de archivos de audio o directamente de un micrófono, o transcriba una conversación grabada.

* Síntesis de voz: convierta texto a voz mediante la entrada de archivos de texto o directamente desde la línea de comandos. Personalización de las características de salida de voz mediante [configuraciones de SSML](speech-synthesis-markup.md) y [voces estándar o neuronales](speech-synthesis-markup.md#neural-and-custom-voices).

* Traducción de voz: traduce el audio de un idioma de origen en texto o audio en un idioma de destino.

* Ejecución en recursos de proceso de Azure: envíe comandos de la CLI de Voz para ejecutarlos en un recurso de proceso remoto de Azure mediante `spx webjob`.

## <a name="get-started"></a>Introducción

Para empezar a usar la CLI de voz, consulte la [guía de inicio rápido](spx-basics.md). En este artículo se muestra cómo ejecutar algunos comandos básicos y también se muestran comandos algo más avanzados para ejecutar operaciones por lotes de conversión de voz a texto y texto a voz. Después de leer el artículo sobre conceptos básicos, debe tener suficiente conocimiento de la sintaxis para empezar a escribir algunos comandos personalizados o automatizar las operaciones sencillas de voz.

## <a name="next-steps"></a>Pasos siguientes

- Introducción a la [guía de inicio rápido de la CLI de voz](spx-basics.md)
- [Configuración del almacén de datos](./spx-data-store-configuration.md)
- Más información sobre cómo [ejecutar operaciones por lotes con la CLI de voz](./spx-batch-operations.md)
