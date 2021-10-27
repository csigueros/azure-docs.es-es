---
title: Introducción al enrutador de trabajos de Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Obtenga información sobre el enrutador de trabajos de Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1178eaa37170228c58f044a616a2bfe230bd3f4c
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166704"
---
# <a name="job-router-concepts"></a>Conceptos del enrutador de trabajos

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

El enrutador de trabajos de Azure Communication Services resuelve el problema de emparejar una oferta con una demanda abstractas en un sistema. Integrado en Azure Event Grid, el enrutador de trabajos entrega notificaciones prácticamente en tiempo real, lo que permite compilar aplicaciones reactivas para controlar el comportamiento de la instancia del enrutador de trabajos.

## <a name="job-router-overview"></a>Introducción al enrutador de trabajos

Los SDK del enrutador de trabajos se pueden usar para compilar diversos escenarios empresariales en los que existe la necesidad de emparejar una unidad de trabajo con un recurso determinado. Por ejemplo, el trabajo podría definirse como una serie de llamadas telefónicas con muchos posibles agentes del centro de contacto, o una solicitud de chat web con un agente en directo que controla varias sesiones simultáneas con otras personas. La necesidad de enrutar una unidad de trabajo abstracta a un recurso disponible exige definir el trabajo, lo que se conoce como un [Trabajo](#job), una [Cola](#queue), el [Rol de trabajo](#worker) y un conjunto de [Directivas](#policies) que definen los aspectos de comportamiento de cómo interactúan entre sí estos componentes.

## <a name="job-router-architecture"></a>Arquitectura del enrutador de trabajos

El enrutador de trabajos de Azure Communication Services usa eventos para notificar a las aplicaciones las acciones del servicio. En los diagramas siguientes se muestra un flujo simplificado común al enrutador de trabajos: envío de un trabajo, registro de un rol de trabajo y control de la oferta de trabajo.

### <a name="job-submission-flow"></a>Flujo de envío de trabajos

1. La aplicación de Contoso envía un trabajo al enrutador de trabajos de la instancia de Azure Communication Services.
2. El trabajo se clasifica y se genera un evento denominado **RouterJobClassified**, que incluye toda la información sobre el trabajo y cómo puede haber modificado sus propiedades el proceso de clasificación.
 
    :::image type="content" source="../media/router/acs-router-job-submission.png" alt-text="Diagrama que muestra al enrutador de trabajos de Communication Services enviando un trabajo.":::

### <a name="worker-registration-flow"></a>Flujo de registro de un rol de trabajo

1. Cuando un rol de trabajo está listo para aceptar un trabajo, se registra en el enrutador de trabajos mediante la aplicación de Contoso.
2. Luego, el enrutador de trabajos devuelve **RouterWorkerRegistered**.

    :::image type="content" source="../media/router/acs-router-worker-registration.png" alt-text="Diagrama que muestra el registro del rol de trabajo del enrutador de trabajos de Communication Services.":::

### <a name="matching-and-accepting-a-job-flow"></a>Emparejamiento y aceptación de un flujo de trabajo

1. Cuando el enrutador de trabajos encuentra un rol de trabajo coincidente para un trabajo, ofrece este mediante el envío de **RouterWorkerOfferIssued**, que la aplicación de Contoso recibiría, y envía una señal al usuario conectado mediante una plataforma como Azure SignalR Service.
2. El rol de trabajo acepta la oferta.
3. El enrutador de trabajos envía **routerWorkerOfferAccepted**, que indica a la aplicación de Contoso que el rol de trabajo está asignado al trabajo.

    :::image type="content" source="../media/router/acs-router-accept-offer.png" alt-text="Diagrama que muestra la oferta aceptada del enrutador de trabajos de Communication Services.":::

## <a name="real-time-notifications"></a>Notificaciones en tiempo real

Azure Communication Services se basa en la plataforma de mensajería de Event Grid para enviar notificaciones sobre las acciones que el enrutador de trabajos está llevando a cabo en la carga de trabajo enviada. El enrutador de trabajos envía mensajes en forma de eventos cada vez que se produce una acción importante, como eventos de ciclo de vida de un trabajo que incluyen su creación, su finalización, la aceptación de una oferta, etc.

## <a name="job"></a>Trabajo

Un trabajo representa la unidad de trabajo que se debe enrutar a un rol de trabajo disponible. Los trabajos se definen mediante SDK del enrutador de trabajos de Azure Communication Services o mediante el envío de una solicitud autenticada a la API de REST. Los trabajos suelen contener una referencia a algún identificador único que pueda tener, como un identificador de llamada o un número de vale, junto con las características del trabajo que se va a realizar.

## <a name="queue"></a>Cola

Cuando se crea un trabajo, se asigna a una cola, ya sea estáticamente en el momento del envío, o dinámicamente por medio de la aplicación de una directiva de clasificación. Los trabajos se agrupan por su cola asignada y pueden tener diferentes características en función de cómo se pretenda distribuir la carga de trabajo. Las colas necesitan una **directiva de distribución** para determinar cómo se ofrecen los trabajos a los roles de trabajo disponibles.

Las colas del enrutador de trabajos también pueden contener directivas de excepciones que determinan el comportamiento de los trabajos cuando surgen determinadas condiciones. Por ejemplo, puede que quiera que un trabajo se traslade a otra cola, que la prioridad aumente o que ambos se basen en un temporizador o en alguna otra condición.

## <a name="worker"></a>Trabajo

Un rol de trabajo representa el suministro disponible para controlar un trabajo en una cola determinada. Cada rol de trabajo registrado en el enrutador de trabajos incluye un conjunto de **Etiquetas**, sus **Colas** asociadas, **configuraciones de canal** y una **puntuación de capacidad total**. El enrutador de trabajos usa estos factores para determinar cuándo y cómo enrutar trabajos a un rol de trabajo en tiempo real.

El enrutador de trabajos de Azure Communication Services mantiene y usa el estado de un rol de trabajo mediante estados simples **Activo**, **Inactivo** o **Drenaje** para determinar cuándo se pueden emparejar los trabajos disponibles con un rol de trabajo. Junto con el estado, la configuración del canal y la puntuación de capacidad total, el enrutador de trabajos calcula los roles de trabajo viables y emite ofertas relacionadas con el trabajo.

## <a name="policies"></a>Directivas

El enrutador de trabajos de Azure Communication Services aplica directivas flexibles para asociar el comportamiento dinámico a varios aspectos del sistema. Según la directiva, las etiquetas de un trabajo se pueden consumir y evaluar para modificar la prioridad de un trabajo, en qué cola debe estar, etc. Determinadas directivas del enrutador de trabajos ofrecen procesamiento de funciones insertadas mediante PowerFx o, en escenarios más complejos, una devolución de llamada a una función de Azure.

**Directiva de clasificación**: una directiva de clasificación ayuda al enrutador de trabajos a definir la cola, la prioridad, y puede modificar los selectores de roles de trabajo cuando el remitente no puede o no está al tanto de estos parámetros en el momento del envío. Para obtener más información sobre la clasificación, vea la página [Conceptos de clasificación](classification-concepts.md).

**Directiva de distribución**: cuando el enrutador de trabajos recibe un nuevo trabajo, se usa la directiva de distribución para buscar un rol de trabajo adecuado y administrar las ofertas de trabajo. Los roles de trabajo se seleccionan con distintos **modos** y, en función de la directiva, el enrutador de trabajos puede avisar a uno o varios roles de trabajo simultáneamente.

**Directiva de excepciones**: una directiva de excepciones controla el comportamiento de un trabajo en función de un desencadenador y ejecuta una acción deseada. La directiva de excepciones se asocia a una cola para que pueda controlar el comportamiento de los trabajos de esa cola.

## <a name="next-steps"></a>Pasos siguientes

- [Conceptos de reglas del enrutador](router-rule-concepts.md)
- [Conceptos de clasificación](classification-concepts.md)
- [Conceptos de distribución](distribution-concepts.md)
- [Guía de inicio rápido](../../quickstarts/router/get-started-router.md)
- [Administración de colas](../../how-tos/router-sdk/manage-queue.md)
- [Clasificación de un trabajo](../../how-tos/router-sdk/job-classification.md)
- [Escalamiento de un trabajo](../../how-tos/router-sdk/escalate-job.md)
- [Suscripción a eventos](../../how-tos/router-sdk/subscribe-events.md)