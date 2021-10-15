---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 41f8fc2adefd29bb7cfda31e3dbaa83de599b154
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837941"
---
## <a name="install-the-sdk"></a>Instalación del SDK

Busque el nivel de proyecto build.gradle y asegúrese de agregar `mavenCentral()` a la lista de repositorios en `buildscript` y `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
A continuación, en el nivel de módulo build.gradle, agregue las siguientes líneas a la sección de dependencias

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}
```

### <a name="initialize-the-required-objects"></a>Inicialización de los objetos necesarios

Para crear una instancia de `CallAgent` , debe llamar al método `createCallAgent` en una instancia de `CallClient`. De este modo, se devuelve un objeto de instancia de `CallAgent` de manera asincrónica.
El método `createCallAgent` toma `CommunicationUserCredential` como argumento, que encapsula un [token de acceso](../../../../quickstarts/access-tokens.md).
Para tener acceso a `DeviceManager`, debe crearse primero una instancia de callAgent y, a continuación, puede usar el método `CallClient.getDeviceManager` para obtener DeviceManager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```
Para establecer un nombre para mostrar para el autor de la llamada, use este método alternativo:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
```