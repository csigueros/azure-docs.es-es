---
title: Grabación de vídeo para su reproducción con Azure Video Analyzer
description: En este artículo se describe la grabación de vídeo para su reproducción con Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 253db9fe0ad067f00647cb195995eaee4951a38a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563955"
---
# <a name="record-video-for-playback"></a>Grabación de vídeo para reproducción

En el contexto de un sistema de administración de vídeo para cámaras CCTV, la grabación de vídeo se refiere al proceso de capturar vídeo a través de las cámaras y grabarlo para su visualización posterior mediante aplicaciones móviles y de explorador. La grabación de vídeo se puede clasificar en grabación continua de vídeo y grabación de vídeo basada en eventos.

## <a name="continuous-video-recording"></a>Grabación de vídeo continua

La grabación de vídeo continua (CVR) consiste en grabar de forma continua todo el vídeo que se captura a través de un origen de vídeo. La CVR garantiza la disponibilidad de la duración de vídeo deseada (según la **[directiva de retención](#retention-policy))** para su posterior análisis o auditoría.

## <a name="event-based-video-recording"></a>Grabación de vídeo basada en eventos

La grabación de vídeo basada en eventos (EVR) hace referencia al proceso de grabación de vídeo desencadenado por un evento. El evento en cuestión podría generarse a partir del procesamiento de la propia señal de vídeo (por ejemplo, al detectar movimiento) o desde un origen independiente (por ejemplo, el sensor de una puerta). La EVR ayuda a ahorrar en el almacenamiento, pero requiere componentes adicionales que generen los eventos y desencadenen la grabación de vídeo. En otras palabras, la EVR requiere tomar decisiones adicionales sobre los eventos que deben desencadenar la grabación de vídeo y la duración de esa grabación. Un ejemplo podría ser grabar el vídeo durante dos minutos a partir de 30 segundos antes de la hora del evento.

## <a name="choosing-recording-modes"></a>Selección del modo de grabación

La opción de usar CVR o EVR depende de los objetivos empresariales. Azure Video Analyzer (AVA) proporciona funciones de plataforma tanto para CVR como para EVR. Puede obtener más información en los artículos de escenarios de **[CVR](continuous-video-recording.md)** y **[EVR](event-based-video-recording-concept.md)** .

## <a name="retention-policy"></a>Directiva de retención

La directiva de retención hace referencia a las directivas que dictan la longitud o la duración de la grabación de vídeo que se conserva. Las directivas de retención permiten equilibrar el costo del almacenamiento con los requisitos empresariales. En un escenario de CVR, la directiva de retención define durante cuántos días se debe almacenar el vídeo (por ejemplo, los últimos siete días). Puede obtener más información al respecto en el artículo **[Administración de directivas de retención](manage-retention-policy.md)** .

## <a name="next-steps"></a>Pasos siguientes

- [Obtener más información sobre los escenarios de EVR](event-based-video-recording-concept.md)
- [Obtener más información sobre los escenarios de CVR](continuous-video-recording.md)
