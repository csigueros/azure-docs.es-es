---
title: Guía de migración de unidades reservadas de multimedia (MRU)
description: En este artículo se proporcionan instrucciones basadas en escenarios de MRU que le ayudarán a migrar de Azure Media Services v2 a v3.
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 01d62055de8e8327b518bc3638cab85426917247
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864646"
---
# <a name="media-reserved-units-migration-guidance"></a>Guía de migración de unidades reservadas de multimedia

![logotipo de la guía de migración](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![pasos de migración 2](./media/migration-guide/steps-4.svg)

En este artículo se proporcionan instrucciones basadas en escenarios de MRU que le ayudarán a migrar de Azure Media Services v2 a v3.

> [!Important]
> Las unidades reservadas de multimedia ya no son necesarias en las cuentas de Servicios multimedia, ya que el sistema se escalará y reducirá automáticamente en función de la carga. 

## <a name="scenario-guidance"></a>Instrucciones del escenario

Migre las unidades reservadas de multimedia en función de los siguientes escenarios:

* Ya no es necesario configurar unidades reservadas de multimedia en ninguna cuenta de Servicios multimedia. El sistema se escalará y reducirá verticalmente de manera automática en función de la carga.
* Si tiene una cuenta creada antes de la versión 2020-05-01 de la API, podrá seguir accediendo a las API para administrar unidades reservadas de multimedia, pero no se usará ninguna configuración de este tipo de unidades para controlar la simultaneidad o el rendimiento de la codificación. Para más información, consulte [Escalado del procesamiento de elementos multimedia](../previous/media-services-scale-media-processing-overview.md). Puede administrar las MRU con la CLI 2.0 para Media Services v3, o mediante Azure Portal.
* Si no ve la opción para administrar MRU en Azure Portal, está ejecutando una cuenta creada con la API 2020-05-01 o posterior.
* Si sabe cómo establecer el tipo de unidad reservada de multimedia en v3, el rendimiento mejorará o seguirá siendo el mismo con la eliminación de unidades reservadas de multimedia.
* Si ya es cliente de v2, debe crear una nueva cuenta de v3 para admitir su aplicación existente antes de que la migración se complete. 
* Quizá deba habilitar de nuevo el indizador v1 u otros procesadores multimedia que no estén totalmente en desuso. 

Para obtener más información acerca de las MRU, consulte [Unidades reservadas de multimedia](concept-media-reserved-units.md) y [Escalado de unidades reservadas de multimedia](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>Conceptos, tutoriales y guías de procedimientos de MRU

### <a name="concepts"></a>Conceptos

[Unidades reservadas de multimedia](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Guías de procedimientos

[Escalado de unidades reservadas de multimedia](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Ejemplos

También puede [comparar el código de la versión v2 y v3 en los ejemplos de código](migrate-v-2-v-3-migration-samples.md).
