---
title: ¿Qué es Azure Video Analyzer?
description: En este tema, se proporciona información general sobre Azure Video Analyzer.
ms.topic: overview
ms.date: 10/30/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7485eb54996228ae9a781c0f0bb97c0fa55ba936
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058414"
---
# <a name="what-is-azure-video-analyzer-preview"></a>¿Qué es Azure Video Analyzer? (versión preliminar)
 
Azure Video Analyzer le proporciona una plataforma para crear aplicaciones de vídeo inteligentes que abarcan del perímetro a la nube. La plataforma consta de un módulo IoT Edge y un servicio de Azure. Permite capturar, grabar y analizar vídeos en directo y publicar los resultados, en especial, vídeo e información de vídeo, en el perímetro o en la nube.

El módulo perimetral de Video Analyzer se puede usar con otros módulos de Azure IoT Edge, como Stream Analytics, Cognitive Services y otros servicios de Azure Cloud Services, como Event Hub y Cognitive Services, para crear aplicaciones híbridas eficaces (en el perímetro y la nube). Este módulo perimetral extensible se integra perfectamente con varios módulos perimetrales de inteligencia artificial (IA), como los contenedores de Azure Cognitive Services o los módulos perimetrales personalizados que se crean mediante modelos de aprendizaje automático de código abierto y sus datos de aprendizaje. Al utilizar la plataforma de Video Analyzer, podrá analizar vídeo en directo sin la complejidad de tener que crear, manejar y actualizar un sistema complejo.

Además de analizar el vídeo en directo, el módulo perimetral también le permite grabar vídeo localmente, en el perímetro o la nube, y publicar información de vídeo en los servicios de Azure (tanto en el perímetro como en la nube). Si el vídeo y la información de vídeo se graban en la nube, se puede usar el servicio en la nube Video Analyzer para administrarlos.

Por lo tanto, el servicio en la nube Video Analyzer se puede usar para mejorar las soluciones de IoT con funcionalidad del [sistema de administración de vídeo (VMS)](https://en.wikipedia.org/wiki/Video_management_system), como la grabación, la reproducción y la exportación (es decir, la generación de archivos de vídeo que se pueden compartir externamente). También se puede usar para crear una solución nativa de la nube con la mismas funcionalidad, tal y como se muestra en el diagrama siguiente, con cámaras que se conectan directamente a la nube.

## <a name="accelerate-iot-solutions-development"></a>Aceleración del desarrollo de soluciones de IoT 

Las soluciones de IoT que combinan análisis de vídeo con señales de otros sensores y datos empresariales de IoT pueden ayudarle a automatizar o a automatizar parcialmente las decisiones empresariales, lo que da lugar a mejoras en la productividad. Video Analyzer le permite crear estas soluciones de manera más rápida. Puede centrarse en la creación de los módulos y la lógica de análisis de vídeo específicos de su negocio y permitir que la plataforma oculte las complejidades de la administración y ejecución de una canalización de vídeo.

Con Video Analyzer, puede seguir usando sus [cámaras CCTV](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) con los [sistemas de administración de vídeo (VMS)](https://en.wikipedia.org/wiki/Video_management_system) existentes y compilar aplicaciones de análisis de vídeo de manera independiente. El servicio Video Analyzer se puede usar con los SDK y kits de herramientas de Computer Vision para crear soluciones avanzadas de IoT. Esto se muestra en el diagrama siguiente.

![Desarrollo de soluciones de IoT con Video Analyzer](./media/overview/product-diagram.svg)

### <a name="concepts"></a>Conceptos

* [Canalización](pipeline.md)
* [Video Analyzer sin grabación de vídeo](analyze-live-video-without-recording.md)
* [Grabación de vídeo](video-recording.md)


## <a name="compliance-privacy-and-security"></a>Cumplimiento, privacidad y seguridad

Como recordatorio importante, debe cumplir todas las leyes aplicables al uso de Video Analyzer y no puede utilizar este servicio de Azure ni ningún otro de forma que infrinja los derechos de otras personas o que pueda ser perjudicial para ellas.

Antes de procesar un vídeo con Video Analyzer, debe tener todos los derechos adecuados para usar ese vídeo, incluidos (cuando lo exija la ley) todos los consentimientos necesarios de las personas (si las hay) que aparecen en él, para el uso, el procesamiento y el almacenamiento de sus datos en Video Analyzer y Azure. Algunas jurisdicciones pueden imponer requisitos legales especiales sobre la recopilación, el procesamiento en línea y el almacenamiento de determinadas categorías de datos, como los datos biométricos. Antes de usar Video Analyzer y Azure para el procesamiento y el almacenamiento de datos sujetos a requisitos legales especiales, debe garantizar el cumplimiento de tales requisitos legales que puedan aplicarse en su caso.

Para información sobre el cumplimiento, la privacidad y la seguridad en Video Analyzer, visite el [Centro de confianza](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx) de Microsoft. En lo que respecta a las obligaciones en materia de privacidad y a las prácticas de retención y tratamiento de datos de Microsoft, incluida la manera de eliminar los datos, consulte la [declaración de privacidad](https://privacy.microsoft.com/PrivacyStatement), los [términos de uso del producto](https://www.microsoft.com/licensing/terms/welcome/welcomepage) y el [anexo sobre protección de datos de los servicios y productos de Microsoft](https://www.microsoft.com/licensing/docs/view/Microsoft-Products-and-Services-Data-Protection-Addendum-DPA) ("DPA"). Al usar el servicio Video Analyzer, acepta los términos de uso del producto, el anexo DPA y la declaración de privacidad.

## <a name="next-steps"></a>Pasos siguientes

* Siga las indicaciones del artículo [Inicio rápido: Introducción a Azure Video Analyzer](get-started-detect-motion-emit-events.md) para ver cómo detectar movimiento en una fuente de vídeo en directo.
* Revise la [terminología](terminology.md).
