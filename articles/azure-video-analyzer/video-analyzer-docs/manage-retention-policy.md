---
title: Administración de la directiva de retención con Azure Video Analyzer
description: En este tema se explica cómo administrar la directiva de retención con Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 10/17/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0da96aa507db1f7514ee394be52a8ca7723562db
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093295"
---
# <a name="manage-retention-policy-with-video-analyzer"></a>Administración de la directiva de retención con Video Analyzer

Puede usar Azure Video Analyzer para archivar contenido durante largos períodos de tiempo, que abarcan desde unos segundos hasta años de contenido de vídeo desde un único origen. Puede controlar explícitamente la directiva de retención del contenido, lo que garantiza que el contenido anterior se recorta periódicamente. Puede aplicar diversas directivas a diferentes archivos; por ejemplo, puede conservar las grabaciones de los 3 días más recientes de la cámara de un parking y las grabaciones de los 30 días más recientes de la cámara que enfoca una caja registradora.

## <a name="retention-period"></a>Período de retención

Para cada [recurso de vídeo](terminology.md#video) de la cuenta de Video Analyzer, puede aplicar opcionalmente una directiva de retención. Cuando se especifica un período de retención (como se describe a continuación), el servicio recorta periódicamente el contenido anterior a ese período. Si no especifica una directiva, el contenido se almacena indefinidamente.

El período de retención normalmente se establece en las propiedades de un nodo receptor de vídeo al crear una topología de canalización. Encontrará la propiedad `retentionPeriod` en la seción `videoCreationProperties`. Como sugiere el nombre, la propiedad se usa cuando se activa una canalización en directo con esta topología y se crea un nuevo recurso de vídeo. Estas propiedades de creación no se usan si el recurso de vídeo ya existe (por ejemplo, cuando se reactiva una canalización). A continuación se muestra un ejemplo de cómo se podría aplicar una directiva de retención de 3 días a todas las cámaras de un parking.

```
{
  "@type": "#Microsoft.VideoAnalyzer.VideoSink",
  "name": "{nodeName}",         
  "videoName": "{nameForVideoResource}",
  "videoCreationProperties":
  {
    "title": "{titleForVideo}",
    "description": "{descriptionForVideo}",
    "segmentLength": "PT30S",
    "retentionPeriod": "P3D"
  },
}
```

También puede establecer o actualizar la propiedad `retentionPeriod` de un recurso de vídeo, mediante Azure Portal o a través de la [API de REST](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/Videos.json). A continuación se muestra un ejemplo de cómo establecer una directiva de retención de 3 días.

```
"archival":
{
   "retentionPeriod": "P3D",
},
```

## <a name="rules-and-limitations"></a>Reglas y limitaciones

* La directiva de retención puede tardar hasta 24 horas en entrar en vigor
* Formato: la propiedad `retentionPeriod` toma el valor de duración ISO 8601, que debe estar en varios días. Valores aceptables de ejemplo: P1D, P20D, P1M, P365D, P1Y, P5Y
    * El valor mínimo es P1D y el máximo es P5Y
    * Cuando se establece en NULL, se borra la directiva de retención y el contenido del vídeo se almacena indefinidamente.
* Cuando se usa la directiva de retención, incurrirá en costos de transacción de almacenamiento de Azure adicionales a medida que el servicio localice y elimine blobs.

## <a name="next-steps"></a>Pasos siguientes

[Reproducción de grabaciones](playback-recordings-how-to.md)
