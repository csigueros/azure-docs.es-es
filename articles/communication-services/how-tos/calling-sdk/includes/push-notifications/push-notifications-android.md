---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 9ecf53a84a804cd312f4d3a98661f4e688726577
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837942"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

### <a name="additional-prerequisites-for-push-notifications"></a>Requisitos previos adicionales para las notificaciones push

Una cuenta de Firebase configurada con Cloud Messaging (FCM) habilitado y con su servicio Firebase Cloud Messaging conectado a una instancia de Azure Notification Hubs. Consulte [Notificaciones de Communication Services](../../../../concepts/notifications.md) para más información.
Además, en el tutorial se da por supuesto que está usando la versión 3.6 de Android Studio o una versión posterior para compilar la aplicación.

Para la aplicación de Android es necesario un conjunto de permisos a fin de poder recibir mensajes de notificaciones de Firebase Cloud Messaging. En el archivo `AndroidManifest.xml`, agregue el siguiente conjunto de permisos justo después de `<manifest ...>` o debajo de la etiqueta `</application>`.

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

## <a name="overview"></a>Información general
Las notificación push móviles son las notificaciones emergentes que se ven en los dispositivos móviles. En el caso de las llamadas, nos centraremos en las notificaciones push VoIP (voz sobre IP). Nos registraremos para recibir y gestionar notificaciones push y, a continuación, anular dicho registro.

## <a name="register-for-push-notifications"></a>Registro de notificaciones push

A fin de registrarse para recibir notificaciones push, la aplicación debe llamar a `registerPushNotification()` en una instancia de `CallAgent` con un token de registro de dispositivos.

Para obtener el token de registro de dispositivos, agregue el SDK de Firebase al archivo `build.gradle` del módulo de aplicación agregando las siguientes líneas en la sección `dependencies` si aún no está ahí:

```groovy
// Add the SDK for Firebase Cloud Messaging
implementation 'com.google.firebase:firebase-core:16.0.8'
implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

En el archivo *build.gradle* del nivel de proyecto, agregue lo siguiente en la sección `dependencies` si aún no está ahí:

```groovy
classpath 'com.google.gms:google-services:4.3.3'
```

Agregue el siguiente complemento al principio del archivo si aún no está ahí:

```groovy
apply plugin: 'com.google.gms.google-services'
```

Seleccione *Sincronizar ahora* en la barra de herramientas. Agregue el siguiente fragmento de código para obtener el token de registro de dispositivos generado por el SDK de Firebase Cloud Messaging para la instancia de la aplicación cliente. Asegúrese de agregar las siguientes importaciones al encabezado de la actividad principal de la instancia. Son necesarias para que el fragmento de código recupere el token:

```java
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Agregue este fragmento de código para recuperar el token:

```java
FirebaseInstanceId.getInstance().getInstanceId()
    .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
        @Override
        public void onComplete(@NonNull Task<InstanceIdResult> task) {
            if (!task.isSuccessful()) {
                Log.w("PushNotification", "getInstanceId failed", task.getException());
                return;
            }

            // Get new Instance ID token
            String deviceToken = task.getResult().getToken();
            // Log
            Log.d("PushNotification", "Device Registration token retrieved successfully");
        }
    });
```
Registre el token de registro de dispositivos con el SDK de servicios de llamada para recibir notificaciones push de llamadas entrantes:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

## <a name="push-notification-handling"></a>Control de notificaciones push

Para recibir notificaciones push de llamadas entrantes, llame a *handlePushNotification()* en una instancia de *CallAgent* con una carga.

Para obtener la carga de Firebase Cloud Messaging, primero cree un servicio (Archivo > Nuevo > Servicio > Servicio) que amplíe la clase *FirebaseMessagingService* del SDK de Firebase e invalide el método `onMessageReceived`. Este método es el controlador de eventos al que se llama cuando Firebase Cloud Messaging entrega la notificación push a la aplicación.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Agregue la siguiente definición de servicio al archivo `AndroidManifest.xml`, en la etiqueta `<application>`:

```xml
<service
    android:name=".MyFirebaseMessagingService"
    android:exported="false">
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
</service>
```

- Una vez recuperada la carga, puede pasarse al SDK de *Communication Services* que se va a analizar en un objeto *IncomingCallInformation* interno, que se administrará llamando al método *handlePushNotification* en una instancia de *CallAgent*. Se crea una instancia de `CallAgent` mediante la llamada al método `createCallAgent(...)` en la clase `CallClient`.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Cuando la administración del mensaje de notificación push se realice correctamente y todos los controladores de eventos se registren correctamente, la aplicación llamará.

## <a name="unregister-push-notifications"></a>Anulación del registro de notificaciones push

Las aplicaciones pueden anular el registro de notificaciones push en cualquier momento. Llame al método `unregisterPushNotification()` en callAgent para anular el registro.

```java
try {
    callAgent.unregisterPushNotification().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```