---
title: Licencias del kit de portabilidad de cliente de Microsoft&reg; Smooth Streaming
description: Obtenga más información sobre cómo obtener licencias del kit de portabilidad de cliente de Microsoft&reg; Smooth Streaming.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: xpouyat
ms.openlocfilehash: 0311948d679cee38dcc7113e4c0246c757409d95
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233452"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Licencias del kit de portabilidad de cliente de Microsoft&reg; Smooth Streaming

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
## <a name="overview"></a>Información general
Microsoft Smooth Streaming Client Porting Kit (**SSPK** para abreviar) es una implementación del cliente de Smooth Streaming que está optimizada para ayudar a los fabricantes de dispositivos insertados, los operadores de cable y dispositivos móviles, los proveedores de servicios de contenido, los fabricantes de auriculares, los fabricantes de software independientes (ISV) y los proveedores de soluciones a crear productos y servicios para hacer streaming de contenido adaptable en formato de Smooth Streaming. SSPK es una implementación independiente del dispositivo y la plataforma del cliente de Smooth Streaming que el licenciatario puede portar a cualquier dispositivo y plataforma. 

A continuación se incluye una arquitectura de alto nivel y el IIS Smooth Streaming Porting Kit es la implementación de Smooth Streaming Client proporcionada por Microsoft e incluye toda la lógica básica para la reproducción del contenido de Smooth Streaming. A continuación, los asociados portan este contenido para un dispositivo o plataforma específicos mediante la implementación de interfaces adecuadas. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Descripción
La licencia de SSPK tiene unos términos que ofrecen un valor empresarial excelente. La licencia SSPK proporciona al sector:

* Código fuente de Smooth Streaming Porting Kit en C++ 
  * Implementa la funcionalidad Smooth Streaming Client
  * Agrega análisis de formato, heurística, lógica de almacenamiento en búfer, etc.
* API de aplicación del reproductor 
  * Interfaces de programación para la interacción con una aplicación de reproductor multimedia
* Interfaz de capa de abstracción de plataforma (PAL) 
  * Interfaces de programación para la interacción con el sistema operativo (subprocesos, sockets)
* Interfaz de capa de abstracción de hardware (HAL) 
  * Interfaces de programación para la interacción con el  descodificadores de A/V de hardware (descodificación, representación)
* Interfaz de administración de derechos digitales (DRM) 
  * Interfaces de programación para administrar DRM a través de la capa de abstracción de DRM (DAL)
  * Microsoft PlayReady Porting Kit se distribuye por separado, pero se integra mediante esta interfaz. Consulte más detalles sobre las [licencias de Microsoft PlayReady](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Ejemplos de implementación 
  * Ejemplo de implementación de PAL para Linux
  * Ejemplo de implementación de HAL para Linux

## <a name="licensing-options"></a>Opciones de licencia
Microsoft Smooth Streaming Client Porting Kit está disponible para los licenciatarios con dos contratos de licencia distintos: uno para el desarrollo de productos provisional de Smooth Streaming Client Interim Products y otro para la distribución de Smooth Streaming Client Final Products a los usuarios finales.

* Para los fabricantes de conjuntos de chips, integradores de sistemas o fabricantes de software independientes (ISV) que requieren un kit de portabilidad de código fuente para desarrollar productos provisionales, debe ejecutarse una **licencia de productos provisionales** del Microsoft Smooth Streaming Client Porting Kit.
* Para los fabricantes de dispositivos o ISV que requieren derechos de distribución de Smooth Streaming Client Final Products a usuarios finales, debe ejecutarse una **licencia de productos finales** del Microsoft Smooth Streaming Client Porting Kit.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Licencia de producto provisional de Microsoft Smooth Streaming Client
Con esta licencia, Microsoft ofrece un Smooth Streaming Client Porting Kit y los derechos de propiedad intelectual necesarios para desarrollar y distribuir Smooth Streaming Client Interim Products a otros licenciados de dispositivos del Smooth Streaming Client Porting Kit que distribuyan Smooth Streaming Client Final Products.

#### <a name="fee-structure"></a>Estructura de tarifas
Una cuota de licencia única de 50.000 USD proporciona acceso al Smooth Streaming Client Porting Kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Licencia Microsoft Smooth Streaming Client Porting Kit Final Product
Con esta licencia, Microsoft ofrece todos los derechos de propiedad intelectual necesarios para recibir Smooth Streaming Client Interim Products de otros licenciados del Smooth Streaming Client Porting Kit y distribuir Smooth Streaming Client Final Products con marca de empresa a usuarios finales.

#### <a name="fee-structure"></a>Estructura de tarifas
La licencia Smooth Streaming Client Final Product se ofrece en un modelo de regalías como se indica a continuación:

* 0,10 USD por cada implementación de dispositivo enviada
* La regalía está limitada a 50.000 USD al año
* Sin regalía para las primeras 10.000 implementaciones de dispositivo cada año 

## <a name="licensing-procedure-and-sspk-access"></a>Procedimiento de licencias y acceso al SSPK
Envíe un mensaje de correo electrónico [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) para cualquier consulta sobre todas las licencias.

Los licenciatarios de productos provisionales registrados pueden acceder al portal de distribución de SSPK.

Los licenciatarios de productos provisionales y finales del SSPK pueden enviar preguntas técnicas a [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Licencias con contrato de producto provisional de Microsoft Smooth Streaming Client
Vea la lista de licencias actual aquí: https://go.microsoft.com/fwlink/?linkid=301271.

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Licencias con contrato de producto final de Microsoft Smooth Streaming Client
Vea la lista de licencias actual aquí: https://go.microsoft.com/fwlink/?linkid=301271.

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

