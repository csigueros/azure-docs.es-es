---
title: Uso de la inserción de Teams de Azure Communication Services para Android
description: En esta introducción, aprenderá a usar la biblioteca de inserción de Teams de Azure Communication Services para Android.
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5f74ab05517c2859cecb9913c19a9ab4c454c85c
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215135"
---
## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../create-communication-resource.md).
- `User Access Token` para habilitar el cliente de llamada. Para más información sobre [cómo obtener `User Access Token`](../../access-tokens.md)
- Complete el inicio rápido para [empezar a agregar la inserción de Teams a la aplicación](../getting-started-with-teams-embed.md).

## <a name="joining-a-group-call"></a>Unión a una llamada grupal

Para unirse a una llamada grupal, proporcione `MeetingUIClientGroupCallLocator` y `MeetingUIClientJoinOptions` a la API `meetingUIClient.join`. La llamada grupal no sonará para otros participantes. El usuario se unirá a la llamada de forma silenciosa.

Vaya al archivo de diseño principal (`app/src/main/res/layout/activity_main.xml`) para configurar el botón para unirse a una llamada grupal. Cree un botón con el identificador `join_groupCall`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_groupCall"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Group Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

Vaya a **MainActivity.java** y agregue el botón en la acción de hacer clic en el método `onCreate`:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientCall;
import com.azure.android.communication.ui.meetings.MeetingUIClientGroupCallLocator;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;

import java.util.UUID;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        Button joinGroupCallButton = findViewById(R.id.join_groupCall);
        joinGroupCallButton.setOnClickListener(l -> joinGroupCall());
    }
}
```

Para configurar el cliente y proporcionar el token, se siguen los mismos pasos que para la API de unión a una reunión, que se describen en el [inicio rápido](../getting-started-with-teams-embed.md).

El método `joinGroupCall` se establece como la acción que se llevará a cabo cuando se toque el botón *Join Group Call* (Unirse a la llamada grupal). La unión a una llamada grupal se puede realizar a través de `MeetingUIClient`. Cree un elemento `MeetingUIClientGroupCallLocator` y configure las opciones de unión mediante `MeetingUIClientJoinOptions`.
Tenga en cuenta que debe reemplazar `<GROUP_ID>` por una cadena UUID. La cadena de identificador de grupo debe tener el formato GUID o UUID.

```java
/**
 * Join a group call with a groupID.
 */
private void joinGroupCall() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        UUID groupUUID = UUID.fromString("<GROUP_ID>");
        MeetingUIClientGroupCallLocator meetingUIClientGroupCallLocator = new MeetingUIClientGroupCallLocator(groupUUID);
        meetingUIClientCall = meetingUIClient.join(meetingUIClientGroupCallLocator, meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join group call: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Captura de eventos de estado de llamada o reunión de inserción de Teams

El estado de la unión a una llamada o reunión grupal se puede capturar desde la clase `MeetingUIClientCallEventListener`. El estado incluye los estados de conexión, el número de participantes y las modalidades, como el estado del micrófono o la cámara.

Agregue el siguiente código a su `MainActivity.java`.

```java
import androidx.core.content.ContextCompat;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientCall;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallState;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallIdentityProvider;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallIdentityProviderCallback;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallUserEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientGroupCallLocator;
import com.azure.android.communication.ui.meetings.MeetingUIClientIconType;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClientTeamsMeetingLinkLocator;
```

Agregue `MeetingUIClientCallEventListener` a la clase.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientEventListener {
```

Llame a `setMeetingUIClientCallEventListener` con el parámetro `this` después de que la unión a la llamada o reunión se haya iniciado correctamente.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);
    
    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);
    
    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implemente los métodos `MeetingUIClientCallEventListener` que la aplicación necesite y agregue código auxiliar para los que no sean necesarios.

```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case CONNECTING:
            System.out.println("Call state changed to Connecting");
            break;
        case CONNECTED:
            System.out.println("Call state changed to Connected");
            break;
        case WAITING_IN_LOBBY:
            System.out.println("Call state changed to Waiting in Lobby");
            break;
        case ENDED:
            System.out.println("Call state changed to Ended");
            break;
    }
}

@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count changed: " + newCount);
}

@Override
public void onIsMutedChanged() {
}

@Override
public void onIsSendingVideoChanged() {
}

@Override
public void onIsHandRaisedChanged(List<String> participantIds) {
}
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-sdk-call"></a>Traiga su propia identidad de la aplicación a los participantes en la llamada del SDK

La aplicación puede asignar los valores de identidad de sus usuarios a los participantes en la llamada o reunión, e invalidar los valores predeterminados. Los valores incluyen avatar, nombre y subtítulo.

### <a name="assigning-avatars-for-call-participants"></a>Asignación de avatares para los participantes de la llamada

Agregue `MeetingUIClientCallIdentityProvider` a la clase.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallIdentityProvider {
```

Llame a `setMeetingUIClientCallIdentityProvider` con el parámetro `this`.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implemente el método `provideAvatarFor` y asigne cada `userIdentifier` al avatar correspondiente.

```java
@Override
public void provideAvatarFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientAvatarSize avatarSize, MeetingUIClientCallIdentityProviderCallback callback) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        System.out.println("MeetingUIClientIdentityProvider.provideAvatarFor called for userIdentifier: " + userIdentifier.getId());
        if (userIdentifier.getId().startsWith("8:teamsvisitor:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_pink));
        } else if (userIdentifier.getId().startsWith("8:orgid:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_doctor_avatar));
        } else if (userIdentifier.getId().startsWith("8:acs:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_green));
        }
    }
}
```

Agregue otros métodos de interfaz MeetingUIClientCallIdentityProvider obligatorios a la clase; es posible que se dejen con una implementación vacía.

```java
@Override
public void provideDisplayNameFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientCallIdentityProviderCallback callback) {
}

@Override
public void provideSubTitleFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientCallIdentityProviderCallback callback) {
}
```
## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>Recepción de información sobre las acciones del usuario en la interfaz de usuario y adición de sus propias funcionalidades personalizadas

### <a name="call-screen"></a>Pantalla de llamada

Se llama a los métodos de interfaz `MeetingUIClientCallUserEventListener` tras las acciones del usuario en el perfil del participante remoto.

Agregue `MeetingUIClientCallUserEventListener` a la clase.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallUserEventListener {
```

Llame a `setMeetingUIClientCallUserEventListener` con el parámetro `this`.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallUserEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
Agregue e implemente el método `onNamePlateOptionsClicked` y asigne cada `userIdentifier` al usuario correspondiente participante de la llamada.
Se llama a este método al pulsar una vez el texto de título del usuario desde la pantalla principal de la llamada.

```java
@Override
public void onNamePlateOptionsClicked(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On user name plate clicked");
        }
    }
}
```

Agregue e implemente el método `onParticipantViewLongPressed` y asigne cada `userIdentifier` al usuario correspondiente participante de la llamada.
Se llama a este método mediante una presión larga en el mosaico del usuario desde la pantalla principal de la llamada. Devuelve `true` para el control personalizado o `false` para el control predeterminado de la pulsación larga.

```java
@Override
public boolean onParticipantViewLongPressed(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On participant tile long pressed");
            return true;
        }
        return false;
    }
}
```

### <a name="call-roster"></a>Lista de llamadas

Se llama a los métodos de interfaz `MeetingUIClientCallRosterDelegate` tras las acciones del usuario en la lista de llamadas.

Agregue `MeetingUIClientCallRosterDelegate` a la clase.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallRosterDelegate {
```

Llame a `setMeetingUIClientCallRosterDelegate` con el parámetro `this`.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallRosterDelegate(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Agregue e implemente el método `onCallParticipantCellTapped` y asigne cada `userIdentifier` al usuario correspondiente participante de la llamada.
Se llama a este método con una sola pulsación de la celda del participante remoto desde la pantalla de lista de llamadas. Devuelve `true` para el control personalizado o `false` para el control predeterminado de la pulsación única.

```java
@Override
public boolean onCallParticipantCellTapped(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On call participant cell tapped");
            return true;
        }
        return false;
    }
}
```
## <a name="user-experience-customization"></a>Personalización de la experiencia del usuario

La experiencia del usuario en el SDK se puede personalizar mediante iconos específicos de la aplicación. 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>Personalización de los iconos de la UI en una llamada o reunión
Los iconos que se muestran en la llamada o reunión se pueden personalizar mediante el método `public void setIconConfig(Map<MeetingUIClientIconType, Integer> iconConfig)` expuesto en `MeetingUIClient`.

Después de crear meetingUIClient, establezca la configuración de icono `meetingUIClient.setIconConfig(getIconConfig())` para los iconos de llamada admitidos en `MeetingUIClientIconType`.

```java
private MeetingUIClient createMeetingUIClient() {
    MeetingUIClient meetingUIClient = new MeetingUIClient(credential);
    meetingUIClient.setIconConfig(getIconConfig());
}

private Map<IconType, Integer> getIconConfig() {
    Map<IconType, Integer> iconConfig = new HashMap<>();
    iconConfig.put(MeetingUIClientIconType.VIDEO_OFF, R.drawable.video_camera_off);
    iconConfig.put(MeetingUIClientIconType.VIDEO_ON, R.drawable.video_camera);
    iconConfig.put(MeetingUIClientIconType.MIC_ON, R.drawable.microphone_fill);
    iconConfig.put(MeetingUIClientIconType.MIC_OFF, R.drawable.microphone_off);
    iconConfig.put(MeetingUIClientIconType.MIC_PROHIBITED, R.drawable.mic_none);
    iconConfig.put(MeetingUIClientIconType.DEVICE_AUDIO, R.drawable.device_filled);
    iconConfig.put(MeetingUIClientIconType.SPEAKER, R.drawable.volume_high);
    iconConfig.put(MeetingUIClientIconType.SPEAKER_OFF, R.drawable.speaker_off);
    iconConfig.put(MeetingUIClientIconType.HEADSET, R.drawable.headset);
    iconConfig.put(MeetingUIClientIconType.BLUETOOTH, R.drawable.bluetooth_audio);
    iconConfig.put(MeetingUIClientIconType.HANGUP, R.drawable.close_app_bar);
    return iconConfig;
}
```

## <a name="perform-operations-with-the-call"></a>Realización de operaciones con la llamada

Las acciones de control de las llamadas se exponen a través de los métodos presentes en `MeetingUIClientCall`.
Estos métodos son útiles para controlar las acciones de llamada.

Agregar la variable para meetingUIClientCall:

```java
public class MainActivity extends AppCompatActivity {
    private MeetingUIClientCall meetingUIClientCall;
```
Asignar la variable meetingUIClientCall a partir del valor devuelto del método de unión:

```java
private void joinGroupCall() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        UUID groupUUID = UUID.fromString("<GROUP_ID>");
        MeetingUIClientGroupCallLocator meetingUIClientGroupCallLocator = new MeetingUIClientGroupCallLocator(groupUUID);
        meetingUIClientCall = meetingUIClient.join(meetingUIClientGroupCallLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join group call: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
### <a name="mute-and-unmute"></a>Silencio y reactivación del sonido

Invoque el método `mute` para silenciar el micrófono de una llamada activa, si existe.
Los cambios en el estado del micrófono se notifican en el método `onIsMutedChanged` de `MeetingUIClientCallEventListener`.

```java
// Mute the microphone for an active call.
public void mute() {
    try {
        meetingUIClientCall.mute();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Mute call failed: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Invoque el método `unmute` para reactivar el audio del micrófono de una llamada activa, si existe.

```java
// Unmute the microphone for an active call.
public void unmute() {
    try {
        meetingUIClientCall.unmute();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Unmute call failed: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>Otras operaciones disponibles de la clase `MeetingUIClientCall`

```java
// Start the video for an active call.
public void startVideo()

// Stop the video for an active call.
public void stopVideo()

// Set the preferred audio route in the call for self user.
public void setAudioRoute(MeetingUIClientAudioRoute audioRoute)

// Raise the hand of current user for an active call.
public void raiseHand()

// Lower the hand of current user for an active call.
public void lowerHand()

// Change the layout in the call for self user.
public List<MicrosoftTeamsSDKLayoutMode> getSupportedLayoutModes()
public void changeLayout(MeetingUIClientLayoutMode microsoftTeamsSDKLayoutMode)

// Hang up the call or leave the meeting.
public void hangUp()
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>Uso del SDK de inserción de Teams y Calling SDK de Azure Communication en la misma aplicación

El SDK de inserción de Teams también incluye Calling SDK de Azure Communication (ACS), que permite usar las características de ambos SDK en la misma aplicación. Solo se puede inicializar y usar un SDK en el mismo momento. Inicializar y usar ambos SDK al mismo tiempo dará lugar a un comportamiento inesperado. 

Agregar las importaciones siguientes a la clase:
```java
import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.CallState;
import com.azure.android.communication.calling.GroupCallLocator;
import com.azure.android.communication.calling.JoinCallOptions;
import com.azure.android.communication.common.CommunicationTokenCredential;
```

Declaración de variables para el uso de ACS
```java
public class MainActivity extends AppCompatActivity {
    private CallAgent agent;
    private CallClient mCallClient;
    private Call mCall;
```

La inicialización se realiza mediante la creación de un nuevo `CallClient`. Agregue la creación a `joinAcsCall` o a cualquier otro método.

```java
private void joinAcsCall() {
    if (mCallClient == null) {
        mCallClient = new CallClient();
    }
}
```
Use todas las API de ACS como se describen en la documentación. El uso de las API no se describe en esta documentación. 

Elimine el SDK de ACS y establezca `null` en sus variables después de que el uso ya no sea necesario o que la aplicación necesite usar el SDK de inserción de Teams.
```java
private void stopAcs() {
    mCall = null;
    agent.dispose();
    agent = null;
    mCallClient.dispose();
    mCallClient = null;
}
```

La inicialización del SDK de inserción de Teams también se realiza durante la creación de `MeetingUIClient`. Agregue la creación a `createMeetingUIClient` o a cualquier otro método.
```java
private MeetingUIClient createMeetingUIClient() { 
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        return new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting ui client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
Use las API del SDK de inserción de Teams como se describe en la documentación. El uso de las API no se describe en esta documentación. 

Elimine el SDK de inserción de Teams y establezca `null` en sus variables después de que el uso ya no sea necesario o que la aplicación necesite usar el SDK de ACS.
```java
private void disposeTeamsSdk() {
    try {
        meetingUIClient.dispose();
        meetingUIClientCall = null;
        meetingUIClient = null;
    } catch (Exception ex) {
        Toast.makeText(getContext(), "Failed to teardown Teams Sdk: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

La eliminación del SDK de inserción de Teams solo es posible si no hay llamadas activas. `meetingUIClient.dispose()` devolverá una excepción si hay una llamada activa. Cuelgue la llamada activa y, luego, llame a `disposeTeamsSdk()`.

```java
private void endMeeting() {
    try {
        meetingUIClientCall.hangUp();
        disposeTeamsSdk();
    } catch (Exception ex) {
        Toast.makeText(getContext(), "Failed to end meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implemente el método de interfaz `onCallStateChanged(MeetingUIClientCallState callState)` `MeetingUIClientCallEventListener` para obtener la actualización de estado sobre la llamada activa que finaliza y, luego, elimine el SDK de inserción de Teams.
```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case ENDED:
            disposeTeamsSdk();
            break;
        default:
        System.out.println("Call state changed to: " + callState.toString());
    }
}
```

Agregue otros métodos de interfaz `MeetingUIClientCallEventListener` obligatorios a la clase.

```java
@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count has changed to: " + newCount);
}

@Override
public void onIsMutedChanged() {
    System.out.println("Mute state changed to: " + meetingUIClientCall.isMuted());
}

@Override
public void onIsSendingVideoChanged() {
    System.out.println("Sending video state changed to: " + meetingUIClientCall.isSendingVideo());
}

@Override
public void onIsHandRaisedChanged(List<String> participantIds) {
    System.out.println("Self participant raise hand status changed to: " + meetingUIClientCall.isHandRaised());
}
```
