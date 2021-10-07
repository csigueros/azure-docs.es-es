---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: df7a63c57674417bbee5bf04a154cf64bcdc474f
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910806"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

Con su Android SDK, puede suscribirse a la mayoría de las propiedades y colecciones que se van a notificar al cambiar los valores.

## <a name="properties"></a>Propiedades
Para suscribirse a eventos `property changed`:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```
Cuando use clientes de escucha de eventos definidos dentro de la misma clase, enlace el cliente de escucha a una variable. Pase la variable como argumento para agregar y quitar métodos del cliente de escucha.

Si intenta pasar el cliente de escucha directamente como argumento, perderá la referencia a ese cliente de escucha. Java crea nuevas instancias de estos clientes de escucha y no hace referencia a las creadas anteriormente. Se seguirán apagando correctamente, pero no se pueden quitar porque ya no tendrá una referencia a ellos.


## <a name="collections"></a>Colecciones
Para suscribirse a eventos `collection updated`:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
