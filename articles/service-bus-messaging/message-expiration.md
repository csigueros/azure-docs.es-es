---
title: Expiración de mensajes de Azure Service Bus
description: En este artículo se explica la expiración y período de vida de los mensajes de Azure Service Bus. Después de una fecha límite de este tipo, ya no se entrega el mensaje.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: d41cb7a5e04753989c0e65e8afb8d74a11cd1af2
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110614887"
---
# <a name="message-expiration-time-to-live"></a>Expiración de mensajes (período de vida)
La carga de un mensaje, o un comando o una consulta que transmite un mensaje a un receptor, casi siempre está sujeta a alguna forma de fecha límite de expiración de nivel de aplicación. Después de esta fecha límite, el contenido ya no se entrega o la operación solicitada ya no se ejecuta.

En los entornos de desarrollo y pruebas en los que las colas y los temas se usan a menudo en el contexto de ejecuciones parciales de aplicaciones o partes de aplicaciones, también conviene que los mensajes de prueba perdidos se recopilen automáticamente como elementos no utilizados para que la siguiente serie de pruebas pueda comenzar de forma limpia.

La expiración de los mensajes individuales puede controlarse mediante la propiedad del sistema **time-to-live**, que especifica una duración relativa. La expiración se convierte en un instante absoluto cuando el mensaje se pone en cola en la entidad. En ese momento, la propiedad **expires-at-utc** adopta el valor **enqueued-time-utc** + **time-to-live**. La configuración de período de vida (TTL) en un mensaje asincrónico no se aplica cuando no hay ningún cliente que escuche activamente.

Tras el instante **expires-at-utc**, los mensajes se vuelven inteligibles para la recuperación. La expiración no afecta a los mensajes que están bloqueados actualmente para la entrega. Esos mensajes se siguen controlando con normalidad. Si el bloqueo expira o se abandona el mensaje, la expiración surte efecto inmediato.

Mientras el mensaje está bajo bloqueo, la aplicación puede estar en posesión de un mensaje que ha expirado. Es decisión del implementador que la aplicación esté dispuesta a seguir adelante con el procesamiento o elija abandonar el mensaje.

Un valor de TTL extremadamente bajo del orden de milisegundos o segundos puede hacer que los mensajes expiren antes de que las aplicaciones receptoras lo reciban. Considere la posibilidad de usar el valor de TTL más alto que funcione para su aplicación.

## <a name="entity-level-expiration"></a>Expiración de nivel de entidad
Todos los mensajes enviados a una cola o un tema están sujetos a una expiración predeterminada que se establece en el nivel de entidad. También se puede establecer en el portal durante la creación y ajustarse más adelante. La expiración predeterminada se usa con todos los mensajes enviados a la entidad donde time-to-live no se establece explícitamente. La expiración predeterminada también funciona como un límite superior para el valor time-to-live. Los mensajes que tienen una expiración de time-to-live más larga respecto al valor predeterminado se ajustan de forma silenciosa al valor time-to-live del mensaje antes de ponerse en cola.

> [!NOTE]
> El valor de período de vida predeterminado para un mensaje asincrónico es el valor más alto posible para un entero de 64 bits firmado, si no se especifica lo contrario.
>
> En el caso de las entidades de mensajería (colas y temas), el tiempo de expiración predeterminado también es el valor más alto posible para un entero de 64 bits firmado para los niveles estándar y prémium de Service Bus. En el nivel **básico**, el tiempo de expiración predeterminado (también máximo) es de **14 días**.

De forma opcional, los mensajes expirados se pueden mover a una [cola de mensajes con problemas de entrega](service-bus-dead-letter-queues.md). Puede configurar esta opción mediante programación o en Azure Portal. Si la opción se deja deshabilitada, se quitan los mensajes que han expirado. Los mensajes expirados movidos a la cola de mensajes con problemas de entrega se pueden distinguir de otros mensajes con problemas de entrega mediante la evaluación de la propiedad [dead-letter reason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) que el agente almacena en la sección de propiedades del usuario. 

En el caso mencionado anteriormente en el que el mensaje está protegido de la expiración mientras está bajo bloqueo y si la marca se establece en la entidad, el mensaje se mueve a la cola de mensajes fallidos cuando se abandone el bloqueo o este expire. Sin embargo, no se mueve si el mensaje se ha liquidado correctamente, y se asume entonces que la aplicación ha podido gestionarlo correctamente, a pesar de la expiración nominal.

La combinación de time-to-live y las colas automáticas y transaccionales de mensajes con problemas de entrega cuando ocurre la expiración es una valiosa herramienta para establecer confianza en si un trabajo proporcionado a un controlador o un grupo de controladores con una fecha límite se recupera para el procesamiento cuando se alcance dicha fecha límite.

Por ejemplo, imagine un sitio web que necesita ejecutar trabajos de forma confiable en un back-end con restricción de escala, y que experimenta de vez en cuando picos de tráfico o quiere aislarse contra episodios de ese back-end. En el caso normal, el controlador del lado servidor de los datos de usuario enviados inserta la información en una cola y, como consecuencia, recibe una respuesta para confirmar que se ha gestionado correctamente la transacción a una cola de respuestas. Si hay un aumento del tráfico y el controlador de back-end no puede procesar sus elementos de trabajo pendiente a tiempo, los trabajos que han expirado se devuelven a la cola de mensajes con problemas de entrega. El usuario interactivo puede recibir la notificación de que la operación solicitada tardará un poco más de lo normal, y la solicitud se puede colocar entonces en una cola diferente en una ruta de acceso de procesamiento donde el resultado final de dicho procesamiento se envía al usuario por correo electrónico. 


## <a name="temporary-entities"></a>Entidades temporales

Las colas, los temas y las suscripciones de Service Bus se pueden crear como entidades temporales, que se eliminan automáticamente cuando no se han usado durante un período de tiempo especificado.
 
La limpieza automática resulta útil en escenarios de desarrollo y pruebas en los que las entidades se crean dinámicamente y no se limpian tras su uso debido a alguna interrupción de la ejecución de la depuración o prueba. También es útil cuando una aplicación crea entidades dinámicas, como una cola de respuestas, para volver a recibir respuestas en un proceso de servidor web, o en otro objeto de duración relativamente corta donde resulta difícil limpiar esas entidades de forma confiable cuando la instancia del objeto desaparece.

La característica se habilita mediante la propiedad **auto delete on idle** del espacio de nombres. Esta propiedad se establece en el período durante el cual una entidad debe estar inactiva (sin usar) antes de que se elimine automáticamente. El valor mínimo para esta propiedad es de 5 minutos.
 
## <a name="idleness"></a>Inactividad

Esto es lo que se considera inactividad de las entidades (colas, temas y suscripciones):

- Colas
    - No envía  
    - No recibe  
    - No hay actualizaciones a la cola  
    - No hay mensajes programados  
    - No hay navegación o inspección 
- Temas  
    - No envía  
    - No hay actualizaciones al tema  
    - No hay mensajes programados 
- Suscripciones
    - No recibe  
    - No hay actualizaciones a la suscripción  
    - No se han agregado nuevas reglas a la suscripción  
    - No hay navegación o inspección  
 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas:

* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
