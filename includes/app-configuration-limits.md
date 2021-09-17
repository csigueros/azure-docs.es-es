---
author: tfitzmac
ms.service: azure-app-configuration
ms.topic: include
ms.date: 08/19/2021
ms.author: tomfitz
ms.openlocfilehash: 3b04007ef7329231da7de31ec465de2c1585c7e4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122752865"
---
| Resource | Límite | Comentario |
| --- | --- | ---|
| Almacenes de configuración para el nivel Gratis | 1 almacén por suscripción |
| Almacenes de configuración para el nivel Estándar | Almacenes ilimitados por suscripción | 
| Solicitudes del almacén de configuración para el nivel Gratis | 1000 solicitudes por día  | Una vez agotada la cuota, se devolverá el código de estado HTTP 429 para todas las solicitudes hasta que finalice el día. |
| Solicitudes del almacén de configuración para el nivel Estándar | 30 000 por hora  |Una vez agotada la cuota, las solicitudes pueden devolver el código de estado HTTP 429, que indica que hay demasiadas solicitudes hasta que finalice la hora.|  
| Almacenamiento para el nivel gratis | 10 MB |
| Almacenamiento para el nivel Estándar | 1 GB |
| Claves y valores | 10 kB  | Para un único elemento clave-valor, incluidos todos los metadatos
