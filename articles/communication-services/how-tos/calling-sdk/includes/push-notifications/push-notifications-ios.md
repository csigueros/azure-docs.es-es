---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c1d03c13ac4d2f4911baf0315358003dd43e0b3b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837944"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="set-up-push-notifications"></a>Configuración de las notificaciones de inserción

Una notificación de inserción en el dispositivo móvil es la notificación emergente que recibe en el dispositivo móvil. En el caso de las llamadas, nos centraremos en las notificaciones de inserción de VoIP (voz sobre IP). 

En las secciones siguientes se describe cómo registrarse, controlar y anular el registro de las notificaciones de inserción. Antes de iniciar estas tareas, complete estos requisitos previos:

1. En Xcode, vaya a **Signing & Capabilities** (Firma y funcionalidades). Para agregar una funcionalidad, seleccione **+ Capability** (+ Funcionalidad) y, a continuación, seleccione **Push Notifications** (Notificaciones de inserción).
2. Para agregar otra funcionalidad, seleccione **+ Capability** (+ Funcionalidad) y, a continuación, seleccione **Background Modes** (Modos en segundo plano).
3. En **Background Modes** (Modos en segundo plano), seleccione las casillas **Voice over IP** (Voz sobre IP) y **Remote notifications** (Notificaciones remotas).

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png" alt-text="Captura de pantalla que muestra cómo agregar funcionalidades en Xcode." lightbox="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>Registro de notificaciones push

A fin de registrarse para recibir notificaciones de inserción, llame a `registerPushNotification()` en una instancia de `CallAgent` con un token de registro de dispositivo.

El registro de las notificaciones de inserción se debe producir después de una inicialización correcta. Cuando se destruya el objeto `callAgent`, se llamará a `logout`, lo que anulará automáticamente el registro para notificaciones de inserción.

```swift
let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken!) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}
```

## <a name="handle-push-notifications"></a>Control de las notificaciones de inserción
Para recibir notificaciones de inserción de llamadas entrantes, llame a `handlePushNotification()` en una instancia de `CallAgent` con una carga de diccionario.

```swift
let callNotification = PushNotificationInfo.fromDictionary(pushPayload.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error == nil) {
        print("Handling of push notification was successful")
    } else {
        print("Handling of push notification failed")
    }
}
```
## <a name="unregister-push-notifications"></a>Anulación del registro de notificaciones push

Las aplicaciones pueden anular el registro de notificaciones push en cualquier momento. Solo debe llamar al método `unregisterPushNotification` en `CallAgent`.

> [!NOTE]
> El registro para que las aplicaciones dejen de recibir notificaciones de inserción no se anula de manera automática al cerrar la sesión.

```swift
callAgent.unregisterPushNotification { (error) in
    if (error == nil) {
        print("Unregister of push notification was successful")
    } else {
       print("Unregister of push notification failed, please try again")
    }
}
```