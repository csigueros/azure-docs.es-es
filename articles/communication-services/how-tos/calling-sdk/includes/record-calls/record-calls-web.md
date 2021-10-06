---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7c739c65e638657cffbfade0c2a491cdefd6b626
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699430"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

### <a name="record-calls"></a>Registro de llamadas
> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión beta del SDK web de llamada de ACS.

La grabación de llamadas es una característica extendida de la API `Call` principal. Primero debe obtener el objeto de API de la característica de grabación:

```js
const callRecordingApi = call.api(Features.Recording);
```

Después, para comprobar si se está grabando la llamada, inspeccione la propiedad `isRecordingActive` de `callRecordingApi`. Devuelve `Boolean`.

```js
const isRecordingActive = callRecordingApi.isRecordingActive;
```

También puede suscribirse a los cambios de grabación:

```js
const isRecordingActiveChangedHandler = () => {
    console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```