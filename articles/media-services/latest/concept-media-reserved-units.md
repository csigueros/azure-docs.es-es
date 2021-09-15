---
title: 'Unidades reservadas de multimedia: Azure'
description: Las unidades reservadas de multimedia le permiten escalar el proceso multimedia y determinar la velocidad de las tareas de procesamiento multimedia.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 3fc68505712bc5cae0defd216b30a3c5913c3c77
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866441"
---
# <a name="media-reserved-units"></a>Unidades reservadas de multimedia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Las unidades reservadas de multimedia (MRU) se usaban anteriormente en Azure Media Services v2 para controlar la simultaneidad y el rendimiento de codificación. Ya no es necesario administrar las MRU ni solicitar aumentos de cuota para ninguna cuenta de servicios multimedia, ya que el sistema se escalará y reducirá verticalmente automáticamente en función de la carga. También verá un rendimiento igual o superior en comparación con el uso de las MRU. 

Si tiene una cuenta que se creó antes de la versión 2020-05-01 de la API, seguirá teniendo acceso a las API para administrar unidades reservadas de multimedia, pero ninguna configuración de MRU que establezca se usará para controlar la simultaneidad o el rendimiento de codificación. Si no ve la opción para administrar las MRU en Azure Portal, significa que está usando una cuenta creada con la API 2020-05-01 o posterior. 

## <a name="billing"></a>Facturación

Antes, las unidades reservadas de multimedia tenían un coste, pero a partir del 17 de abril de 2021 ya no hay que abonar nada por las cuentas que tienen una configuración de unidades reservadas de multimedia. Para obtener más información sobre la facturación de trabajos de codificación, consulte [Codificación de vídeo y audio con Media Services](encoding-concept.md).

En el caso de las cuentas creadas con la versión **2020-05-01** de la API (es decir, la versión 3) o a través de Azure Portal, ya no se requieren la escalabilidad ni las unidades reservadas de multimedia. Ahora el servicio se encarga de aplicar automáticamente la escalabilidad. Las unidades reservadas de multimedia ya no son necesarias ni compatibles con ninguna cuenta de Azure Media Services. Consulte [Unidades reservadas de multimedia (heredadas)](concept-media-reserved-units.md) para obtener información adicional.

## <a name="see-also"></a>Vea también

* [Migración de Media Services v2 a v3](migrate-v-2-v-3-migration-introduction.md)
* [Escalado de unidades reservadas de multimedia con CLI](media-reserved-units-cli-how-to.md)
