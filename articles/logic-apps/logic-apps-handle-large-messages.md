---
title: Control de mensajes grandes en flujos de trabajo mediante la fragmentación
description: Controle mensajes grandes mediante la fragmentación en Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.topic: how-to
ms.date: 12/18/2020
ms.openlocfilehash: bcbad7c9a71ae5045f24ee25b8de409ece544c0f
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179156"
---
# <a name="handle-large-messages-in-workflows-using-chunking-in-azure-logic-apps"></a>Control de mensajes grandes en flujos de trabajo mediante la fragmentación en Azure Logic Apps

Azure Logic Apps tiene distintos límites máximos en el tamaño del contenido del mensaje que los desencadenadores y las acciones pueden controlar en los flujos de trabajo de la aplicación lógica, en función del tipo de recurso de la aplicación lógica y del entorno donde se ejecuta ese flujo de trabajo de aplicación lógica. Estos límites ayudan a reducir cualquier sobrecarga que se produce al almacenar y procesar [mensajes grandes](#what-is-large-message). Para más información sobre los límites de tamaño de mensaje, revise [Límites de mensajes en Azure Logic Apps](logic-apps-limits-and-config.md#messages).

Si usa acciones HTTP integradas o acciones específicas del conector administrado y necesita Azure Logic Apps para trabajar con mensajes mayores que los límites predeterminados, puede habilitar la *fragmentación*, que divide un mensaje grande en mensajes más pequeños. De este modo, todavía puede transferir archivos de gran tamaño en condiciones específicas. De hecho, cuando se usan estas acciones HTTP integradas o acciones específicas del conector administrado, la fragmentación es la única manera en que Azure Logic Apps puede consumir mensajes grandes. Este requisito significa que el intercambio de mensajes HTTP subyacente entre Azure Logic Apps y otros servicios debe usar la fragmentación, o que las conexiones creadas por los conectores administrados que desea usar también deben admitir la fragmentación.

> [!NOTE]
> Azure Logic Apps no admite la fragmentación en desencadenadores debido a la mayor sobrecarga al intercambiar varios mensajes.
> Además, Azure Logic Apps implementa la fragmentación para acciones HTTP mediante su propio protocolo, tal como se describe en este artículo. Por lo tanto, incluso si el sitio web o el servicio web admiten la fragmentación, no funcionarán con la fragmentación de acciones HTTP. Para usar la fragmentación de acciones HTTP con el sitio web o el servicio web, debe implementar el mismo protocolo que usa Azure Logic Apps. De lo contrario, no habilite la fragmentación en la acción HTTP. 

En este artículo se proporciona información general sobre cómo funciona la fragmentación en Azure Logic Apps y cómo configurar la fragmentación en las acciones admitidas.

<a name="what-is-large-message"></a>

## <a name="what-makes-messages-large"></a>¿Qué hace que los mensajes sean "grandes"?

Los mensajes son "grandes" en función del servicio que los administra. El límite de tamaño exacto en mensajes grandes varía entre Logic Apps y los conectores. Tanto Logic Apps como los conectores no pueden consumir directamente mensajes grandes, así que es necesario fragmentarlos. Para conocer el límite de tamaño de los mensajes de Logic Apps, consulte [Límites y configuración de Logic Apps](../logic-apps/logic-apps-limits-and-config.md).
Para información sobre el límite de tamaño de los mensajes de cada conector, consulte los [detalles técnicos específicos del conector](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Control de mensajes fragmentados en Logic Apps

Logic Apps no puede usar directamente las salidas de los mensajes fragmentados con un tamaño superior al límite. Solo las acciones que admiten la fragmentación pueden acceder al contenido de mensaje en estas salidas. Por lo tanto, una acción que controla mensajes grandes debe satisfacer *cualquiera* de estos criterios:

* Admitir la fragmentación de forma nativa cuando esa acción pertenece a un conector 
* Tener habilitada la compatibilidad con la fragmentación en la configuración de tiempo de ejecución de esa acción 

De lo contrario, al intentar acceder a una salida con contenido de gran tamaño, recibirá un error. Para habilitar la fragmentación, consulte [Set up chunking support](#set-up-chunking) (Configuración de la compatibilidad con la fragmentación).

### <a name="chunked-message-handling-for-connectors"></a>Control de mensajes fragmentados de conectores

Los servicios que se comunican con Logic Apps pueden tener sus propios límites de tamaño de mensaje. Estos límites suelen ser menores que el límite de Logic Apps. Por ejemplo, suponiendo que admita la fragmentación, un conector podría considerar que un mensaje de 30 MB es grande, mientras que Logic Apps no. Para cumplir con el límite de este conector, Logic Apps divide los mensajes de más de 30 MB en fragmentos más pequeños.

Para los conectores que admiten la fragmentación, el protocolo de fragmentación subyacente es invisible para los usuarios finales. Sin embargo, no todos los conectores admiten la fragmentación, por lo que estos conectores generan errores en tiempo de ejecución cuando los mensajes entrantes superan los límites de tamaño de los conectores.

Para las acciones que admiten y están habilitadas para la fragmentación, no se pueden usar cuerpos de desencadenador, variables ni expresiones como `@triggerBody()?['Content']` porque el uso de cualquiera de estas entradas impide que se produzca la operación de fragmentación. En su lugar, use la [acción **Compose**](../logic-apps/logic-apps-perform-data-operations.md#compose-action). En concreto, debe crear un campo `body` mediante la acción **Compose** para almacenar la salida de datos del cuerpo del desencadenador, la variable, la expresión, etc., por ejemplo:

```json
"Compose": {
    "inputs": {
        "body": "@variables('myVar1')"
    },
    "runAfter": {
        "Until": [
            "Succeeded"
        ]
    },
    "type": "Compose"
},
```
Después, para hacer referencia a los datos, en la acción de fragmentación, use `@body('Compose')`.

```json
"Create_file": {
    "inputs": {
        "body": "@body('Compose')",
        "headers": {
            "ReadFileMetadataFromServer": true
        },
        "host": {
            "connection": {
                "name": "@parameters('$connections')['sftpwithssh_1']['connectionId']"
            }
        },
        "method": "post",
        "path": "/datasets/default/files",
        "queries": {
            "folderPath": "/c:/test1/test1sub",
            "name": "tt.txt",
            "queryParametersSingleEncoded": true
        }
    },
    "runAfter": {
        "Compose": [
            "Succeeded"
        ]
    },
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "Chunked"
        }
    },
    "type": "ApiConnection"
},
```

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Configuración de la fragmentación mediante HTTP

En escenarios de HTTP genéricos, puede dividir las cargas y descargas de contenido de gran tamaño mediante HTTP, de forma que la aplicación lógica y un punto de conexión pueden intercambiar mensajes grandes. Sin embargo, debe fragmentar los mensajes de la forma esperada por Logic Apps. 

Si un punto de conexión ha habilitado la fragmentación para cargas o descargas, las acciones HTTP de la aplicación lógica fragmentan automáticamente los mensajes grandes. En caso contrario, debe configurar la compatibilidad con la fragmentación en el punto de conexión. Si no posee ni controla el punto de conexión o el conector, posiblemente no tenga la opción de configurar la fragmentación.

Además, si una acción HTTP ya no permite la fragmentación, también debe configurarla en la propiedad `runTimeConfiguration` de la acción. Puede establecer esta propiedad dentro de la acción, ya sea directamente en el editor de vista de código, tal y como se describe más adelante, o en el Diseñador de aplicaciones lógicas, como se describe aquí:

1. En la esquina superior derecha de la acción HTTP, elija el botón de puntos suspensivos ( **...** ) y, a continuación, elija **Configuración**.

   ![En la acción, abra el menú Configuración.](./media/logic-apps-handle-large-messages/http-settings.png)

2. En **Transferencia de contenido**, establezca **Permitir fragmentación** en **Activado**.

   ![Activar la fragmentación](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Para seguir con la configuración de la fragmentación para cargas y descargas, continúe con las siguientes secciones.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Descarga del contenido en fragmentos

Muchos puntos de conexión envían automáticamente mensajes grandes en fragmentos cuando se descargan mediante una solicitud HTTP GET. Para descargar mensajes fragmentados desde un punto de conexión mediante HTTP, el punto de conexión debe admitir las solicitudes de contenido parciales, o *Descargas fragmentadas*. Cuando la aplicación lógica envía una solicitud HTTP GET a un punto de conexión para la descarga de contenido y el punto de conexión responde con un código de estado "206", la respuesta contiene contenido fragmentado. Logic Apps no puede controlar si un punto de conexión admite solicitudes parciales. Sin embargo, cuando la aplicación lógica obtiene primero la respuesta "206", envía automáticamente varias solicitudes para descargar todo el contenido.

Para comprobar si un punto de conexión puede admitir contenido parcial, envíe una solicitud HEAD. Esta solicitud le ayuda a determinar si la respuesta contiene el encabezado `Accept-Ranges`. De este modo, si el punto de conexión admite descargas fragmentadas pero no envía contenido fragmentado, puede *sugerir* esta opción si establece el encabezado `Range` de la solicitud HTTP GET. 

Estos pasos describen el proceso detallado que usa Logic Apps para descargar contenido fragmentado desde un punto de conexión hasta la aplicación lógica:

1. La aplicación lógica envía una solicitud HTTP GET al punto de conexión.

   El encabezado de solicitud puede incluir opcionalmente un campo `Range` que describe un intervalo de bytes de fragmentos de contenido solicitados.

2. El punto de conexión responde con el código de estado "206" y un cuerpo del mensaje HTTP.

    Los detalles sobre el contenido de este fragmento aparecen en el encabezado `Content-Range` de la respuesta, junto con información que ayuda a Logic Apps a determinar el inicio y la finalización del fragmento, más el tamaño total de todo el contenido antes de la fragmentación.

3. La aplicación lógica envía automáticamente solicitudes HTTP GET de seguimiento.

    La aplicación lógica envía solicitudes GET de seguimiento hasta que se recupera todo el contenido.

Por ejemplo, la definición de esta acción muestra una solicitud HTTP GET que establece el encabezado `Range`. El encabezado *sugiere* que el punto de conexión debe responder con contenido fragmentado:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

La solicitud GET establece el encabezado "Range" en "bytes = 0-1023", que es el intervalo de bytes. Si el punto de conexión admite solicitudes de contenido parcial, responde con un fragmento de contenido del intervalo solicitado. En función del punto de conexión, el formato exacto del campo de encabezado "Range" puede variar.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Carga de contenido en fragmentos

Para cargar contenido fragmentado desde una acción HTTP, la acción debe tener habilitada la compatibilidad con la fragmentación mediante la propiedad `runtimeConfiguration` de la acción. Esta configuración permite que la acción inicie el protocolo de fragmentación. La aplicación lógica, a continuación, puede enviar un mensaje POST o PUT inicial al punto de conexión de destino. Una vez que el punto de conexión responde con un tamaño de fragmento sugerido, la aplicación lógica realiza el seguimiento mediante el envío de solicitudes HTTP PATCH que contienen los fragmentos de contenido.

Estos pasos describen el proceso detallado que usa Logic Apps para cargar contenido fragmentado desde la aplicación lógica hasta un punto de conexión:

1. La aplicación lógica envía una solicitud HTTP POST o PUT inicial con un cuerpo de mensaje vacío. El encabezado de solicitud incluye esta información sobre el contenido que la aplicación lógica desea cargar en fragmentos:

   | Campo de encabezado de solicitud de Logic Apps | Value | Tipo | Descripción |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | chunked | String | Indica que el contenido se carga en fragmentos |
   | **x-ms-content-length** | <*content-length*> | Entero | El tamaño del contenido completo en bytes antes de la fragmentación |
   ||||

2. El punto de conexión responde con el código de estado correcto "200" y esta información opcional:

   | Campo de encabezado de respuesta del punto de conexión | Tipo | Obligatorio | Descripción |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Entero | No | El tamaño de fragmento sugerido en bytes |
   | **Ubicación** | String | Sí | La ubicación de la dirección URL a la que enviar los mensajes HTTP PATCH |
   ||||

3. La aplicación lógica crea y envía mensajes HTTP PATCH de seguimiento, cada uno con esta información:

   * Un fragmento de código basado en **x-ms-chunk-size** o en algún tamaño calculado interno hasta que todo el contenido que suma **x-ms-content-length** se carga de manera secuencial

   * Estos detalles de encabezado sobre el fragmento de contenido enviado en cada mensaje PATCH:

     | Campo de encabezado de solicitud de Logic Apps | Value | Tipo | Descripción |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*range*> | String | El intervalo de bytes del fragmento de contenido actual, que incluye el valor inicial, el valor final y el tamaño total del contenido, por ejemplo: "bytes = 0-1023/10100" |
     | **Content-Type** | <*content-type*> | String | El tipo de contenido fragmentado |
     | **Content-Length** | <*content-length*> | String | La longitud de tamaño en bytes del fragmento actual |
     |||||

4. Después de cada solicitud PATCH, el punto de conexión responde con el código de estado "200" y los siguientes encabezados de respuesta para confirmar la recepción de cada fragmento:

   | Campo de encabezado de respuesta del punto de conexión | Tipo | Obligatorio | Descripción |
   |--------------------------------|------|----------|-------------|
   | **Range** | String | Sí | Intervalo de bytes para el contenido recibido por el punto de conexión, por ejemplo: "bytes = 0-1023". |   
   | **x-ms-chunk-size** | Entero | No | El tamaño de fragmento sugerido en bytes |
   ||||

Por ejemplo, la definición de esta acción muestra una solicitud HTTP POST para cargar contenido fragmentado en un punto de conexión. En la propiedad `runTimeConfiguration` de la acción, la propiedad `contentTransfer` establece `transferMode` en `chunked`:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
