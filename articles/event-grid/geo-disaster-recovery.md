---
title: Recuperación ante desastres geográfica en Azure Event Grid | Microsoft Docs
description: Describe cómo Azure Event Grid admite la recuperación ante desastres geográfica (GeoDR) automáticamente.
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 47899ba8fe4dd114d1fc3c0fb729589abf3ebd92
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829978"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Recuperación ante desastres geográfica del servidor en Azure Event Grid
Event Grid admite una recuperación ante desastres geográfica automática de metadatos con relación a temas, dominios y suscripciones a eventos. Event Grid sincroniza automáticamente su infraestructura relacionada con eventos con una región emparejada. Si toda una región de Azure se queda sin servicio, los eventos comenzarán a fluir a la región emparejada geográficamente sin necesidad de que intervenga el usuario. 

Tenga presente que los datos de eventos no se replican en la región emparejada. Solo se replican los metadatos. Sin embargo, si una región admite zonas de disponibilidad, los datos de eventos se replican entre zonas de disponibilidad. 

La recuperación ante desastres se mide con dos métricas:

- Objetivo de punto de recuperación (RPO): minutos u horas de datos que se pueden perder.
- Objetivo de tiempo de recuperación (RTO): minutos u horas que el servicio puede estar inactivo.

La conmutación automática por error de Event Grid tiene diferentes RPO y RTO para los metadatos (temas, dominios y suscripciones a eventos) y los datos (eventos). Si necesita una especificación distinta a las siguientes, siempre puede implementar su propia [conmutación por error del lado cliente con las API de estado de temas](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Objetivo de punto de recuperación (RPO)
- **RPO de metadatos**: cero minutos. Cada vez que se crea un recurso en Event Grid, se replica al instante en otras regiones. Cuando se produce una conmutación por error, no se pierde ningún metadato.
- **RPO de datos**: si el sistema funciona correctamente y tenía controlado el tráfico en el momento de la conmutación por error regional, el RPO para eventos es de unos 5 minutos.

## <a name="recovery-time-objective-rto"></a>Objetivo de tiempo de recuperación (RTO)
- **RTO de metadatos**: aunque esto suele ocurrir con mucha más rapidez, en 60 minutos, Event Grid empezará a aceptar llamadas de creación, actualización y eliminación en los temas y las suscripciones.
- **RTO de datos**: al igual que con los metadatos, esto suele ocurrir mucho más rápido, pero, en un plazo de 60 minutos, Event Grid comenzará a aceptar el tráfico nuevo después de una conmutación por error regional.

> [!IMPORTANT]
> - No hay ningún contrato de nivel de servicio (SLA) para la recuperación ante desastres del lado servidor. Si la región emparejada no tiene capacidad extra para asumir el tráfico adicional, Event Grid no puede iniciar la conmutación por error. Los objetivos de nivel de servicio se intentan cumplir en la medida de lo posible. 
> - El costo de GeoDR de metadatos en Event Grid es de 0 USD.


## <a name="next-steps"></a>Pasos siguientes
Si quiere implementar la lógica de conmutación por error del lado cliente, consulte [# Creación de una recuperación ante desastres propia para temas personalizados en Event Grid](custom-disaster-recovery.md).
