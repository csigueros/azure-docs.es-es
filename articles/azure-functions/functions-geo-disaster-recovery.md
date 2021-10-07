---
title: Alta disponibilidad y recuperación ante desastres geográfica para Azure Functions
description: Uso de regiones geográficas para la redundancia y conmutar por error en Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 27730e2b6c1ed760f43a054901d16b58ca80acd9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645234"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Recuperación ante desastres geográfica de Azure Functions

Cuando regiones enteras de Azure o centros de datos experimentan tiempo de inactividad, el código crítico debe continuar procesándose en una región diferente. En este artículo se explican algunas de las estrategias que puede usar para implementar funciones que permitan la recuperación ante desastres.

## <a name="basic-concepts"></a>Conceptos básicos

Azure Functions se ejecuta en una aplicación de funciones en una región específica. No hay redundancia integrada disponible. Para evitar que se pierdan los datos de la ejecución durante las interrupciones, puede implementar de forma redundante las mismas funciones en aplicaciones de funciones en varias regiones.  

Al ejecutar el mismo código de función en varias regiones, hay dos patrones que se deben tener en cuenta:

| Patrón | Descripción |
| --- | --- |
|**Activo/activo** | Las funciones de ambas regiones están ejecutando y procesando eventos de forma activa, ya sea de manera duplicada o en rotación. Ser recomienda usar un patrón de tipo activo/activo junto con [Azure Front Door](../frontdoor/front-door-overview.md) para ñas funciones críticas desencadenadas mediante HTTP. |
|**Activo/pasivo** | Las funciones se ejecutan activamente en la región que recibe los eventos, mientras que las mismas funciones de una segunda región permanecen inactivas.  Cuando es necesario realizar la conmutación por error, la región secundaria se activa y se encarga del procesamiento. Se recomienda usar este patrón en las funciones desencadenadas por eventos y no en las que se desencadenen mediante HTTP, como las funciones desencadenadas por Service Bus y el Centro de eventos.

Para obtener más información sobre las implementaciones en varias regiones, consulte la guía que tiene en [Aplicación web de varias regiones de alta disponibilidad](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## <a name="redundancy-for-http-trigger-functions"></a>Redundancia para funciones de desencadenador HTTP

El patrón activo/activo es el mejor modelo de implementación para las funciones de desencadenador HTTP. En este caso, debe usar [Azure Front Door](../frontdoor/front-door-overview.md) para coordinar las solicitudes entre ambas regiones. Azure Front Door puede enrutar y usar el mecanismo round-robin para las solicitudes HTTP entre funciones que se ejecutan en varias regiones, y se encarga de comprobar periódicamente el estado de cada punto de conexión. Cuando una función de una región deja de responder a las comprobaciones de estado, Azure Front Door la quita de la rotación y solo reenvía el tráfico a las funciones restantes que estén en buen estado.  

![Arquitectura de Azure Front Door y Azure Functions](media/functions-geo-dr/front-door.png)  

## <a name="redundancy-for-non-http-trigger-functions"></a>Redundancia para funciones de desencadenador que no son HTTP

La redundancia de las funciones que consumen eventos de otros servicios requiere un patrón diferente, que funciona con el patrón de conmutación por error de los servicios relacionados. 

### <a name="activepassive-redundancy-for-non-http-trigger-functions"></a>Redundancia activa/pasiva para funciones de desencadenador que no son HTTP

El patrón activo/pasivo proporciona una manera de que solo una sola función procese cada mensaje, pero ofrece un mecanismo para conmutar por error a una región secundaria en caso de desastre. Las aplicaciones de funciones funcionan con los comportamientos de conmutación por error de los servicios asociados, como la [recuperación geográfica de Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md) y la [recuperación geográfica de Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md). La aplicación de funciones secundaria se considera _pasiva_ porque el servicio de conmutación por error al que está conectada no está activo actualmente, así que la aplicación de funciones está esencialmente _inactiva_.

Considere crear una topología de ejemplo mediante un desencadenador de Azure Event Hubs. En este caso, el patrón activo/pasivo requiere la participación de los siguientes componentes:

* Azure Event Hub implementado en una región primaria y una secundaria.
* [Recuperación ante desastres geográfica habilitada](../service-bus-messaging/service-bus-geo-dr.md) para emparejar el centro de eventos principal y el secundario. Esto también crea un _alias_ que puede usar para conectarse a los centros de eventos y cambiar del principal al secundario sin tener que cambiar la información de conexión.
* Las aplicaciones de funciones se implementan en la región principal y secundaria (conmutación por error), donde la aplicación de la región secundaria básicamente está inactiva porque no se envían mensajes allí.
* Las aplicaciones de funciones se desencadenan en la cadena de conexión *directa* (sin alias) de su centro de eventos correspondiente. 
* Los publicadores del centro de eventos deben publicar en la cadena de conexión de alias. 

![Arquitectura de ejemplo activo-pasivo](media/functions-geo-dr/active-passive.png)

Antes de conmutar por error, los publicadores que envían contenido al alias compartido se enrutarán al centro de eventos principal. La aplicación de función principal escucha exclusivamente al centro de eventos principal. La aplicación de funciones secundaria será pasiva y estará inactiva. En cuanto se inicia la conmutación por error, los publicadores que envíen contenido al alias compartido se enrutarán al centro de eventos secundario. Así pues, la aplicación de funciones secundaria pasará a estar activa y comenzará a desencadenarse automáticamente.  La conmutación por error efectiva a una región secundaria se puede controlar íntegramente desde el centro de eventos, donde las funciones solo se activan cuando lo hace el centro de eventos correspondiente.

Puede obtener más información y detalles sobre la conmutación por error con [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) y [Event Hubs](../event-hubs/event-hubs-geo-dr.md).

### <a name="activeactive-redundancy-for-non-http-trigger-functions"></a>Redundancia activa/activa para funciones de desencadenador que no son HTTP

Todavía puede realizar implementaciones de tipo activa/activa para funciones de desencadenador que no son HTTP. Sin embargo, debe tener en cuenta cómo interactuarán o se coordinarán entre sí las dos regiones. Cuando implemente la misma aplicación de funciones en dos regiones y cada una de ellas se desencadene en la misma cola de Service Bus, estas actuarán como consumidores rivales al quitar esa cola. Si bien esto significa que cada mensaje solo se procesará mediante una de las instancias, también significa que todavía hay un único punto de error en la única instancia de Service Bus. 

En su lugar, puede implementar dos colas de Service Bus: una en una región primaria y la otra en una región secundaria. En este caso, puede tener dos aplicaciones de funciones, cada una de las cuales apuntará a la cola de Service Bus que esté activa en su región. El desafío de esta topología es saber cómo se distribuyen los mensajes de cola entre las dos regiones.  A menudo, esto significa que cada publicador intenta publicar un mensaje en *ambas* regiones, y ambas aplicaciones de funciones activas procesan cada mensaje. Aunque esta situación crea un patrón activo/activo esperado, también presenta otras dificultades relacionadas con la duplicación del proceso y cuándo o cómo se consolidan los datos. Debido a estos desafíos, se recomienda usar el patrón activo/pasivo para las funciones de desencadenador que no sean HTTPS.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una instancia de Azure Front Door](../frontdoor/quickstart-create-front-door.md)
* [Consideraciones sobre la conmutación por error de Event Hubs](../event-hubs/event-hubs-geo-dr.md#considerations)
