---
title: Introducción a las unidades reservadas de multimedia | Microsoft Docs
description: Este artículo es una introducción al escalado del procesamiento de elementos multimedia con Azure Media Services.
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 19454d90b55ab92c92d2e9132198f918f38c6e2c
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835387"
---
# <a name="media-reserved-units"></a>Unidades reservadas de multimedia

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Las unidades reservadas de multimedia (MRU) se usaban anteriormente para controlar la simultaneidad y el rendimiento de la codificación. Las MRU solo se usan para los siguientes procesadores multimedia heredados que pronto dejarán de usarse. Consulte [Componentes heredados de Azure Media Services](legacy-components.md) para obtener información sobre la retirada de estos procesadores heredados:

* Flujo de trabajo del Codificador multimedia
* Media Indexer V1 y V2

Para todos los demás procesadores multimedia, ya no es necesario administrar las MRU ni solicitar aumentos de cuota para ninguna cuenta de servicios multimedia, ya que el sistema se escalará y reducirá verticalmente automáticamente en función de la carga. También verá un rendimiento igual o superior en comparación con el uso de las MRU.

## <a name="billing"></a>Facturación

Antes, las unidades reservadas de multimedia tenían un coste, pero a partir del 17 de abril de 2021 ya no hay que abonar nada por las cuentas que tienen una configuración de unidades reservadas de multimedia.

## <a name="scaling-mrus"></a>Escalado de MRU

Por motivos de compatibilidad, puede seguir usando Azure Portal o las API siguientes para administrar y escalar MRU:

[.NET](media-services-dotnet-encoding-units.md)
[Portal](media-services-portal-scale-media-processing.md)
[REST](/rest/api/media/operations/encodingreservedunittype)
[Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Pese a ello, ninguna de las configuraciones de unidades reservadas de multimedia establezca se usará de forma predeterminada para controlar la simultaneidad o el rendimiento de la codificación. La única excepción a esta configuración es si realiza la codificación con uno de los siguientes procesadores multimedia heredados: Media Encoder Premium Workflow o Media Indexer V1.  
