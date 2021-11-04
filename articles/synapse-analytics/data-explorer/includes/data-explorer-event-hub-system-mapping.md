---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: e7e723394bd7a90f6fd4cda8db7c7cb27c4cb9a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478170"
---
> [!NOTE]
> * Las propiedades del sistema son compatibles con los formatos `json` y tabulares (`csv`, `tsv`, etc.) pero no se admiten en datos comprimidos. Aunque se utilice un formato no admitido, los datos se ingerirán, pero se omitirán las propiedades.
> * En el caso de los datos tabulares, las propiedades del sistema solo se admiten para los mensajes de eventos de un solo registro.
> * En el caso de los datos JSON, también se admiten las propiedades del sistema para los mensajes de eventos de varios registros. En tales casos, las propiedades del sistema solo se agregan al primer registro del mensaje de evento. 
> * En el caso de la asignación `csv`, las propiedades se agregan al principio del registro en el orden que se muestra en la tabla [Propiedades del sistema](../ingest-data/data-explorer-ingest-event-hub-overview.md#system-properties).
> * Para la asignación `json`, las propiedades se agregan según los nombres de propiedad de la tabla [Propiedades del sistema](../ingest-data/data-explorer-ingest-event-hub-overview.md#system-properties).
