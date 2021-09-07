---
title: 'Azure Communication Services: registros de resumen de llamadas y de diagnóstico de llamadas'
titleSuffix: An Azure Communication Services concept document
description: Obtenga información sobre los registros de resumen de llamadas y de diagnóstico de llamadas en Azure Monitor
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 07/22/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 3844fcab3f9830bbaca60042aedbfb75bf7f09e4
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114676149"
---
# <a name="call-summary-and-call-diagnostic-logs"></a>Registros de resumen de llamadas y de diagnóstico de llamadas

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

## <a name="data-concepts"></a>Conceptos de datos

### <a name="entities-and-ids"></a>Entidades e id.

Una *llamada*, en relación con las entidades representadas en los datos, es una abstracción representada por `correlationId`. Los elementos `CorrelationId` son únicos por llamada y están limitados en el tiempo por `callStartTime` y `callDuration`. Cada llamada es un evento que contiene datos de dos o más *puntos de conexión*, que representan a los distintos participantes humanos, bots o servidores de la llamada.

Un *participante* (`participantId`) solo está presente cuando la llamada es *grupal*, ya que representa la conexión entre un punto de conexión y el servidor. 

Un *punto de conexión* es la entidad más única, representada por `endpointId`. `EndpointType` indica si el punto de conexión representa un usuario humano (RTC, VoIP), un bot (bot) o el servidor que administra varios participantes dentro de una llamada. Cuando un elemento `endpointType` es `"Server"`, no se asignará un id. único al punto de conexión. Al consultar el elemento `endpointType` y el número de `endpointId`, siempre puede determinar cuántos usuarios y otros participantes no humanos (bots y servidores) están en la llamada. Los SDK nativos (como el SDK de llamada de Android) reutilizan el mismo `endpointId` para un usuario en varias llamadas, lo que permite comprender la experiencia entre sesiones. Esto difiere de los puntos de conexión basados en web, que siempre generarán un nuevo `endpointId` cada nueva llamada.

Una *secuencia* es la entidad más granular, ya que hay una secuencia por dirección (entrante/saliente) y `mediaType` (por ejemplo, audio o vídeo).  

### <a name="p2p-vs-group-calls"></a>P2P frente a llamadas grupales

Hay dos tipos de llamadas (representadas por `callType`): P2P y grupo. 

Las llamadas **P2P** son una conexión entre solo dos puntos de conexión, sin ningún punto de conexión de servidor. Las llamadas P2P se inician como una llamada entre esos puntos de conexión y no se crean como un evento de llamada grupal antes de la conexión.

:::image type="content" source="media\call-logs-images\call-logs-concepts-p2p.png" alt-text="Llamada P2P":::

Las llamadas **grupales** incluyen cualquier llamada que se cree con antelación como evento de reunión o calendario y cualquier llamada que tenga más de 2 puntos de conexión conectados. Las llamadas grupales incluirán un punto de conexión de servidor, y la conexión entre cada punto de conexión y el servidor constituye un participante. Las llamadas P2P que agregan un punto de conexión adicional durante la llamada dejan de ser P2P y se convierten en una llamada grupal. Al visualizar `participantStartTime` y `participantDuration`, se puede determinar la escala de tiempo de cuándo cada punto de conexión se ha unido a la llamada.

:::image type="content" source="media\call-logs-images\call-logs-concepts-group.png" alt-text="Llamada grupal":::

## <a name="log-structure"></a>Estructura de los registros
Se crean dos tipos de registros: registros de **resumen de llamadas** y registros de **diagnóstico de llamadas**. 

Los registros de resumen de llamadas contienen información básica sobre la llamada, incluidos todos los id. pertinentes, las marcas de tiempo, el punto de conexión y la información del SDK. Para cada punto de conexión dentro de una llamada (sin contar el servidor), se creará un registro de resumen de llamadas distinto.

Los registros de diagnóstico de llamadas contienen información sobre la secuencia, así como un conjunto de métricas que indican las medidas de la calidad de la experiencia. Para cada punto de conexión dentro de una llamada (incluido el servidor), se crea un registro de diagnóstico de llamadas distinto para cada flujo de datos (audio, vídeo, etc.) entre puntos de conexión. En una llamada P2P, cada registro contiene datos relacionados con cada una de las secuencias salientes asociadas a cada punto de conexión. En una llamada grupal, cada secuencia asociada a `endpointType`= `"Server"` creará un registro que contendrá los datos para las secuencias entrantes y todas las demás secuencias crearán registros que contendrán los datos para las secuencias salientes de todos los puntos de conexión que no son de servidor. En las llamadas grupales, use `participantId` como clave para unir los registros entrantes y salientes relacionados en una conexión de distintos participantes.

### <a name="example-1-p2p-call"></a>Ejemplo 1: Llamada P2P

El diagrama siguiente representa dos puntos de conexión conectados directamente en una llamada P2P. En este ejemplo, se crearán 2 registros de resumen de llamadas (1 por `endpointId`) y 4 registros de diagnóstico de llamadas (1 por secuencia multimedia). Cada registro contendrá datos relacionados con la secuencia de salida de `endpointId`.

:::image type="content" source="media\call-logs-images\call-logs-p2p-streams.png" alt-text="Secuencias de llamadas p2p":::


### <a name="example-2-group-call"></a>Ejemplo 2: Llamada grupal

En el diagrama siguiente se representa un ejemplo de llamada grupal con tres `particpantIds`, lo que significa tres `endpointIds` (los elementos `endpointIds` pueden aparecer en varios participantes, por ejemplo, al volver a unirse a una llamada desde el mismo dispositivo) y un punto de conexión de servidor. Para el elemento `participantId` 1, se crearán dos registros de resumen de llamadas: uno para `endpointId` y otro para el servidor. Se crearán cuatro registros de diagnóstico de llamadas relacionados con `participantId` 1, uno para cada secuencia multimedia. Los tres registros con `endpointId` 1 contendrán datos relacionados con los flujos multimedia salientes, y el registro con `endpointId = null, endpointType = "Server"` contendrá datos relacionados con la secuencia de entrada.

:::image type="content" source="media\call-logs-images\call-logs-concepts-group-call-endpoint-detail.png" alt-text="Detalle de llamada grupal":::

## <a name="data-definitions"></a>Definiciones de datos

### <a name="call-summary-log"></a>Registro de resumen de llamadas
El registro de resumen de llamadas contiene datos que le ayudarán a identificar las propiedades clave de todas las llamadas. Se creará un registro de resumen de llamadas diferente por cada `participantId` (`endpointId` en el caso de las llamadas P2P) en la llamada.

|     Propiedad                  |     Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     time                      |     Marca de tiempo (UTC) de cuando se generó el registro.                                                                                                                                                                                                                                                                                                                                                                                                                       |
|     operationName             |     Operación asociada al registro.                                                                                                                                                                                                                                                                                                                                                                                                                                |
|     operationVersion          |     La versión de API asociada con la operación, si `operationName` se ha realizado mediante una API. Si no hay ninguna API que se corresponde con esta operación, la versión representa la versión de esa operación en caso de que las propiedades asociadas con la operación cambien en el futuro.                                                                                                                                                                           |
|     category                  |     Categoría de registro del evento. La categoría es la granularidad con la que se pueden habilitar o deshabilitar los registros en un recurso determinado. Las propiedades que aparecen en el blob `properties` de un evento son las mismas dentro de una categoría de registro y un tipo de recurso concretos.                                                                                                                                                                                                    |
|     correlationIdentifier     |     La propiedad `correlationIdentifier` identifica los eventos correlacionados de todos los participantes y puntos de conexión que se conectan durante una sola llamada. `correlationIdentifier` es el id. único de una llamada. Si alguna vez necesita abrir un caso de soporte técnico en Microsoft, se usará `correlationID` para identificar fácilmente la llamada para la que está solucionando problemas.                                                                                                                                                                      |
|     identificador                |     Es el id. único del usuario, que coincide con la identidad asignada por el servicio de autenticación (MRI).                                                                                                                                                                                                                                                                                                                                                              |
|     callStartTime             |     Marca de tiempo para el inicio de la llamada, en función del primer intento de conexión desde cualquier punto de conexión.                                                                                                                                                                                                                                                                                                                                                                   |
|     callDuration              |     Duración de la llamada expresada en segundos, en función del primer intento de conexión y el final de la última conexión entre dos puntos de conexión.                                                                                                                                                                                                                                                                                                                         |
|     callType                  |     Contendrá `"P2P"` o `"Group"`. Una llamada `"P2P"` es una conexión directa 1:1 entre solo dos puntos de conexión que no son de servidor. Una llamada `"Group"` es aquella que tiene más de dos puntos de conexión o que se crea como llamada `"Group"` antes de la conexión.                                                                                                                                                                                                                                 |
|     teamsThreadId             |     Este id. solo es pertinente cuando la llamada se organiza como una reunión de Microsoft Teams, que representa el caso de uso de interoperabilidad de Microsoft Teams: Azure Communication Services. Este id. se expone en los registros operativos. También puede obtener este id. a través de las API de chat.                                                                                                                                                                                              |
|     participantId             |     Este id. se genera para representar la conexión bidireccional entre un punto de conexión `"Participant"` (`endpointType` = `"Server"`) y el servidor. Cuando `callType` = `"P2P"`, hay una conexión directa entre dos puntos de conexión y no se genera ningún `participantId`.                                                                                                                                                                                                               |
|     participantStartTime      |     Marca de tiempo para el inicio del primer intento de conexión por parte del participante.                                                                                                                                                                                                                                                                                                                                                                                            |
|     participantDuration       |     Duración de cada conexión de participante en segundos, desde `participantStartTime` hasta la marca de tiempo cuando finaliza la conexión.                                                                                                                                                                                                                                                                                                                                             |
|     participantEndReason      |     Contiene códigos de error del SDK de llamada emitidos por el SDK cuando es pertinente para cada `participantId`. Consulte los códigos de error del SDK de llamada a continuación.                                                                                                                                                                                                                                                                                                                          |
|     endpointId                |     Id. único que representa cada punto de conexión conectado a la llamada, donde el tipo de punto de conexión se define mediante `endpointType`. Cuando el valor es `null`, la entidad conectada es el servidor de Communication Services (`endpointType`= `"Server"`). `EndpointId` a veces puede persistir para el mismo usuario en varias llamadas (`correlationIdentifier`) para clientes nativos. El número de `endpointId` determinará el número de registros de resumen de llamadas. Se crea un registro de resumen distinto para cada `endpointId`.    |
|     endpointType              |     Este valor describe las propiedades de cada punto de conexión conectado a la llamada. Puede contener `"Server"`, `"VOIP"`, `"PSTN"`, `"BOT"` o `"Unknown"`.                                                                                                                                                                                                                                                                                                                               |
|     sdkVersion                |     Cadena de la versión del SDK de llamada de Communication Services usada por cada punto de conexión pertinente. (Ejemplo: `"1.1.00.20212500"`)                                                                                                                                                                                                                                                                                                                                                          |
|     osVersion                 |     Cadena que representa el sistema operativo y la versión de cada dispositivo de punto de conexión.                                                                                                                                                                                                                                                                                                                                                                                       |

### <a name="call-diagnostic-log"></a>Registro de diagnóstico de llamadas
Los registros de diagnóstico de llamadas proporcionan información importante sobre los puntos de conexión y las transferencias de medios para cada participante, así como las medidas que ayudan a comprender los problemas de calidad. 

|     Propiedad              |     Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     operationName         |     Operación asociada al registro.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     operationVersion      |     `api-version` asociada con la operación, si `operationName` se realizó mediante una API. Si no hay ninguna API que corresponda a esta operación, la versión representa la versión de esa operación en caso de que las propiedades asociadas con la operación cambien en el futuro.                                                                                                                                                                                                                                                                                                                                                                  |
|     category              |     Categoría de registro del evento. La categoría es la granularidad con la que se pueden habilitar o deshabilitar los registros en un recurso determinado. Las propiedades que aparecen en el blob `properties` de un evento son las mismas dentro de una categoría de registro y un tipo de recurso concretos.                                                                                                                                                                                                                                                                                                                                                                                         |
|     correlationIdentifier     |     La propiedad `correlationIdentifier` identifica los eventos correlacionados de todos los participantes y puntos de conexión que se conectan durante una sola llamada. `correlationIdentifier` es el id. único de una llamada. Si alguna vez necesita abrir un caso de soporte técnico en Microsoft, se usará `correlationID` para identificar fácilmente la llamada para la que está solucionando problemas.                                                                                                                                                                                                                                                                                                                                                                         |
|     participantId         |     Este id. se genera para representar la conexión bidireccional entre un punto de conexión "participante" (`endpointType` =  `“Server”`) y el servidor. Cuando `callType`   = `"P2P"`, hay una conexión directa entre dos puntos de conexión y no se genera ningún `participantId`.                                                                                                                                                                                                                                                                                                                                                                                                |
|     identificador            |     Este id. representa la identidad del usuario, tal como se define en el servicio de autenticación. Use este id. para correlacionar distintos eventos entre llamadas y servicios.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     endpointId            |     Id. único que representa cada punto de conexión conectado a la llamada, con el tipo de punto de conexión definido mediante `endpointType`.   Cuando el valor es `null`, significa que la entidad conectada es el servidor de Communication Services.  `EndpointId` puede persistir para el mismo usuario en varias llamadas (`correlationIdentifier`) para clientes nativos, pero será único para cada llamada cuando el cliente sea un explorador web.                                                                                                                                                                                                                                                                                  |
|     endpointType          |     Este valor describe las propiedades de cada `endpointId`. Puede contener `“Server”`, `“VOIP”`, `“PSTN”`, `“BOT”` o `“Unknown”`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|     mediaType             |     Este valor de cadena describe el tipo de medio que se transmite entre los puntos de conexión dentro de cada secuencia. Entre los valores posibles se incluyen `“Audio”`, `“Video”`, `“VBSS”` (uso compartido de pantalla basado en vídeo) y `“AppSharing”`.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|     streamId              |     Entero no único que, junto con `mediaType`, se puede usar para identificar de forma única secuencias del mismo `participantId`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     transportType         |     Valor de cadena que describe el protocolo de transporte de red por `participantId`. Puede contener `"UDP”`, `“TCP”` o `“Unrecognized”`. `"Unrecognized"` indica que el sistema no pudo determinar si `transportType`era TCP o UDP.                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     roundTripTimeAvg      |     Este es el tiempo medio que tarda un paquete IP de un punto de conexión a otro dentro de `participantDuration`. Este retraso de propagación de red está esencialmente asociado a la distancia física entre los dos puntos y la velocidad de la luz, incluida la sobrecarga adicional que suponen los distintos enrutadores entre ellos. La latencia se mide no solo como el tiempo que se tarda en realizar un recorrido unidireccional, sino también el tiempo de ida y vuelta (RTT).  Su valor se expresa en milisegundos, y un RTT superior a 500 ms se debe considerar que afecta negativamente a la calidad de la llamada.                                                                                                                                                                      |
|     roundTripTimeMax      |     El RTT máximo (ms) medido por secuencia multimedia durante `participantDuration` en una llamada grupal o `callDuration` en una llamada P2P.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|     jitterAvg             |     Es el cambio medio, en lo que se refiere al retraso, entre paquetes sucesivos. Azure Communication Services puede adaptarse a algunos niveles de inestabilidad a través del almacenamiento en búfer. Solo cuando la vibración supera el almacenamiento en búfer, que es aproximadamente de `jitterAvg` > 30 ms, es probable que se produzca un impacto negativo en la calidad. Los paquetes que llegan a velocidades diferentes hacen que la voz de un hablante suene robótica. Esto se mide por secuencia multimedia durante `participantDuration` en una llamada grupal o `callDuration` en una llamada P2P.                                                                                                                                              |
|     jitterMax             |     Es el valor de vibración máximo medido entre paquetes por secuencia multimedia. Las ráfagas en las condiciones de red pueden causar problemas en el flujo de tráfico de audio y vídeo.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|     packetLossRateAvg     |     Este es el porcentaje medio de paquetes que se pierden. La pérdida de paquetes afecta directamente a la calidad del audio (desde la pérdida de paquetes pequeños, lo que no tiene apenas impacto, hasta pérdidas de ráfagas completas que provocan una interrupción completa del audio). Los paquetes que se pierden y no llegan a su destino previsto provocan espacios en los medios, lo que da lugar a falta de sílabas y palabras, y a un vídeo y uso compartido entrecortados. Una tasa de pérdida de paquetes superior al 10 % (0,1) debe considerarse una tasa que probablemente tenga un impacto negativo en la calidad. Esto se mide por secuencia multimedia durante `participantDuration` en una llamada grupal o `callDuration` en una llamada P2P.    |
|     packetLossRateMax     |     Este valor representa la tasa máxima de pérdida de paquetes (%) por secuencia multimedia a través de `participantDuration` en una llamada grupal o `callDuration` en una llamada P2P. Las ráfagas en las condiciones de red pueden causar problemas en el flujo de tráfico de audio y vídeo.                                                                                                                                                                                                                                                                                                                                                                                                                  |
|                           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |

### <a name="error-codes"></a>Códigos de error
`participantEndReason` contendrá un valor del conjunto de códigos de error del SDK de llamada. Puede consultar estos códigos para solucionar problemas durante la llamada, por punto de conexión.

|     Código de error                 |     Descripción                                                                                                       |     Acción que realizar                                                                                                                                                                                                                                                             |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     0                          |     Correcto                                                                                                           |     La llamada (P2P) o el participante (grupo) finalizaron correctamente.                                                                                                                                                                                                                    |
|     403                        |     Prohibido o error de autenticación.                                                                               |     Asegúrese de que el token de Communication Services es válido y no ha expirado. Si usa la interoperabilidad de Teams, asegúrese de que el inquilino de Teams se ha agregado a la lista de permitidos de acceso de versión preliminar. Para habilitar o deshabilitar la interoperabilidad de los inquilinos de Teams, complete este formulario.    |
|     404                        |     Llamada no encontrada.                                                                                                   |     Asegúrese de que el número al que llama (o la llamada a la que se une) existe.                                                                                                                                                                                                   |
|     408                        |     Se agotó el tiempo de espera del controlador de llamadas.                                                                                        |     Se agotó el tiempo de espera del controlador de llamadas mientras esperaba los mensajes de protocolo de los puntos de conexión de usuario. Asegúrese de que los clientes están conectados y disponibles.                                                                                                                                                 |
|     410                        |     Error de infraestructura de medios o de pila de medios locales.                                                                  |     Asegúrese de que usa el SDK más reciente en un entorno compatible.                                                                                                                                                                                                      |
|     430                        |     No se puede enviar el mensaje a la aplicación cliente.                                                                  |     Asegúrese de que la aplicación cliente se está ejecutando y está disponible.                                                                                                                                                                                                             |
|     480                        |     Punto de conexión remoto del cliente no registrado.                                                                            |     Asegúrese de que el punto de conexión remoto está disponible.                                                                                                                                                                                                                              |
|     481                        |     No se pudo controlar la llamada entrante.                                                                                   |     Envíe una solicitud de soporte técnico mediante Azure Portal.                                                                                                                                                                                                                           |
|     487                        |     Llamada cancelada, rechazada localmente, finalizada debido a un problema de falta de coincidencia de punto de conexión o no se pudo generar una oferta multimedia.    |     Comportamiento esperado.                                                                                                                                                                                                                                                         |
|     490, 491, 496, 487, 498    |     Problemas de red en el punto de conexión local.                                                                                    |     Compruebe la red.                                                                                                                                                                                                                                                        |
|     500, 503, 504              |     Error de infraestructura de Communication Services.                                                                      |     Envíe una solicitud de soporte técnico mediante Azure Portal.                                                                                                                                                                                                                           |
|     603                        |     Un participante remoto de Communication Services ha rechazado globalmente la llamada.                                             |     Comportamiento esperado.                                                                                                                                                                                                                                                         |
|     Unknown                    |     Motivo final no estándar (no forma parte de los códigos SIP estándar).                                                      |                                                                                                                                                                                                                                                                                |

## <a name="call-examples-and-sample-data"></a>Ejemplos de llamadas y datos de ejemplo

### <a name="p2p-call"></a>Llamada P2P

:::image type="content" source="media\call-logs-images\call-logs-concepts-p2p-sample.png" alt-text="Ejemplo de registro P2P":::

Campos compartidos para todos los registros de la llamada:

```
"time":                     "2021-07-19T18:46:50.188Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "8d1a8374-344d-4502-b54b-ba2d6daaf0ae",
```

#### <a name="call-summary-logs"></a>Registros de resumen de llamadas
Los registros de resumen de llamadas contienen información compartida de categoría y operación:

```
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",

```
Resumen de llamadas para el usuario VoIP 1
```
"properties": {
    "identifier":               "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "callStartTime":            "2021-07-19T17:54:05.113Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",    
    "participantStartTime":     "2021-07-19T17:54:06.758Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.1.0",
    "osVersion":                "Windows 10.0.17763 Arch: x64"
}
```

Resumen de llamadas para el usuario VoIP 2
```
"properties": {
    "identifier":               "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "callStartTime":            "2021-07-19T17:54:05.335Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",
    "participantStartTime":     "2021-07-19T17:54:06.335Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.1.0.0",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>Registros de diagnóstico de llamadas
Los registros de diagnóstico de llamadas comparten información de la operación:
```
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
Registro de diagnóstico de la secuencia de audio del punto de conexión VoIP 1 al punto de conexión VoIP 2:
```
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "82",
    "roundTripTimeMax":     "88",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Registro de diagnóstico de la secuencia de audio del punto de conexión VoIP 2 al punto de conexión VoIP 1:
```
"properties": {
    "identifier":           "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "participantId":        "null",
    "endpointId":           "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1363841599",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "78",
    "roundTripTimeMax":     "84",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Registro de diagnóstico de la secuencia de vídeo del punto de conexión VoIP 1 al punto de conexión VoIP 2:
```
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Video",
    "streamId":             "2804",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "103",
    "roundTripTimeMax":     "143",
    "jitterAvg":            "0",
    "jitterMax":            "4",
    "packetLossRateAvg":    "3.146336E-05",
    "packetLossRateMax":    "0.001769911"
}
```
### <a name="group-call"></a>Llamada grupal
En el ejemplo siguiente, hay tres usuarios en una llamada grupal, dos conectados a través de VOIP y uno conectado a través de RTC. Todos usan solo audio. 

:::image type="content" source="media\call-logs-images\call-logs-concepts-group-sample.png" alt-text="Ejemplo de registro de llamada grupal":::

Los datos se generarán en tres registros de resumen de llamadas y seis registros de diagnóstico de llamadas.

Campos compartidos para todos los registros de la llamada:
```
"time":                     "2021-07-05T06:30:06.402Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "341acde7-8aa5-445b-a3da-2ddadca47d22",
```

#### <a name="call-summary-logs"></a>Registros de resumen de llamadas
Los registros de resumen de llamadas contienen información compartida de categoría y operación:
```
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",
```

Resumen de llamadas para el punto de conexión VoIP 1:
```
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "participantStartTime":     "2021-07-05T06:16:44.235Z",
    "participantDuration":      "82",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Darwin Kernel Version 18.7.0: Mon Nov 9 15:07:15 PST 2020; root:xnu-4903.272.3~3/RELEASE_ARM64_S5L8960X"
}
```
Resumen de llamadas para el punto de conexión VoIP 3:
```
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "participantStartTime":     "2021-07-05T06:16:40.240Z",
    "participantDuration":      "87",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Android 11.0; Manufacturer: Google; Product: redfin; Model: Pixel 5; Hardware: redfin"
}
```
Resumen de llamadas para el punto de conexión RTC 2:
```
"properties": {
    "identifier":               "null",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "515650f7-8204-4079-ac9d-d8f4bf07b04c",
    "participantStartTime":     "2021-07-05T06:17:10.447Z",
    "participantDuration":      "52",
    "participantEndReason":     "0",
    "endpointId":               "46387150-692a-47be-8c9d-1237efe6c48b",
    "endpointType":             "PSTN",
    "sdkVersion":               "null",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>Registros de diagnóstico de llamadas
Los registros de diagnóstico de llamadas comparten información de la operación:
```
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
Registro de diagnóstico de la secuencia de audio del punto de conexión VoIP 1 al punto de conexión de servidor:
```
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "14884",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "46",
    "roundTripTimeMax":     "48",
    "jitterAvg":            "0",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Registro de diagnóstico de la secuencia de audio del punto de conexión de servidor al punto de conexión VoIP 1:
```
"properties": {
    "identifier":           null,
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           null,
    "endpointType":         "Server",
    "mediaType":            "Audio",
    "streamId":             "2001",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "42",
    "roundTripTimeMax":     "44",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Registro de diagnóstico de la secuencia de audio del punto de conexión VoIP 3 al punto de conexión de servidor:
```
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "13783",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "2",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Registro de diagnóstico de la secuencia de audio del punto de conexión de servidor al punto de conexión VoIP 3:
```
"properties": {
    "identifier":           "null",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           null,
    "endpointType":         "Server"    
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "4",
    "packetLossRateAvg":    "0",
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [Registro y diagnóstico](./logging-and-diagnostics.md)
