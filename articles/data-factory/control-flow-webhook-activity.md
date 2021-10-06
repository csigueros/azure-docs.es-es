---
title: Actividad de webhook
titleSuffix: Azure Data Factory & Azure Synapse
description: La actividad de webhook de Azure Data Factory y Synapse Analytics controla la ejecución de canalizaciones mediante código personalizado.
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 8b0443cd44dffeec1ea9a70e460ca0f72e05b24d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798833"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Actividad de webhook en Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Una actividad de webhook puede controlar la ejecución de canalizaciones mediante código personalizado. Con la actividad de webhook, el código puede llamar a un punto de conexión y pasarle una dirección URL de devolución de llamada. La ejecución de la canalización espera por la invocación de devolución de llamada antes de pasar a la siguiente actividad.

> [!IMPORTANT]
> La actividad de webhook ahora permite exponer el estado de error y los mensajes personalizados de nuevo a la actividad y la canalización. Establezca _reportStatusOnCallBack_ en true e incluya _StatusCode_ y _Error_ en la carga de devolución de llamada. Para más información, consulte [Notas adicionales](#additional-notes).

## <a name="syntax"></a>Sintaxis

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Propiedades de tipo

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
**name** | El nombre de la actividad de webhook. | String | Sí |
**type** | Tienen que establecerse en "WebHook". | String | Sí |
**method** | El método de API REST para el punto de conexión de destino. | String. El tipo admitido es "POST". | Sí |
**url** | Punto de conexión y ruta de acceso de destino. | Una cadena o una expresión con el valor **resultType** de una cadena. | Sí |
**headers** | Encabezados que se envían a la solicitud. Este es un ejemplo que establece el idioma y el tipo en una solicitud: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Una cadena o una expresión con el valor **resultType** de una cadena. | Sí. Se necesita un encabezado `Content-Type` como `"headers":{ "Content-Type":"application/json"}`. |
**body** | Representa la carga útil que se envía al punto de conexión. | JSON válido o una expresión con el valor **resultType** de JSON. Consulte la sección [Solicitar un esquema de carga](./control-flow-web-activity.md#request-payload-schema) para ver el esquema de carga de la solicitud. | Sí |
**autenticación** | El método de autenticación que se usa para llamar al punto de conexión. Los tipos admitidos son "Basic" y "ClientCertificate". Para más información, consulte [Autenticación](./control-flow-web-activity.md#authentication). Si la autenticación no es necesaria, excluya esta propiedad. | Una cadena o una expresión con el valor **resultType** de una cadena. | No |
**timeout** | El tiempo que la actividad espera a que se invoque la devolución de llamada especificada por **callBackUri**. El valor predeterminado es 10 minutos ("00:10:00"). Los valores tienen el formato TimeSpan *d*.*hh*:*mm*:*ss*. | String | No |
**Notificar el estado de la devolución de llamada** | Permite a un usuario informar del estado de error de una actividad de webhook. | Boolean | No |

## <a name="authentication"></a>Authentication

Una actividad de webhook admite los siguientes tipos de autenticación.

### <a name="none"></a>None

Si la autenticación no es necesaria, no incluya la propiedad **authentication**.

### <a name="basic"></a>Básico

Especifique el nombre de usuario y la contraseña que se usarán con la autenticación básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado de cliente

Especifique un contenido codificado en Base64 de un archivo PFX y una contraseña.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidad administrada

Utilice la identidad administrada para la factoría de datos o el área de trabajo de Synapse para especificar el URI de recurso para el que el token de acceso se solicitará. Para llamar a la API de Azure Resource Management, use `https://management.azure.com/`. Para más información sobre cómo funcionan las identidades administradas, consulte [Información general sobre las identidades administradas de recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Si el servicio está configurado con un repositorio de Git, tiene que almacenar sus credenciales en Azure Key Vault para usar la autenticación básica o de certificado de cliente. El servicio no almacena contraseñas en Git.

## <a name="additional-notes"></a>Notas adicionales

El servicio pasa la propiedad adicional **callBackUri** en el cuerpo enviado al punto de conexión de la dirección URL. El servicio espera que se invoque este URI antes del valor de tiempo de espera especificado. Si no se invoca el URI, se produce un error en la actividad con el estado "TimedOut".

La propia actividad de webhook produce un error cuando la llamada al punto de conexión personalizado genera un error. Cualquier mensaje de error se puede agregar al cuerpo de devolución de llamada y usarse en una actividad posterior.

Para cada llamada a la API REST, el cliente agota el tiempo de espera si el punto de conexión no responde en un minuto. Este comportamiento es el procedimiento recomendado HTTP estándar. Para corregir este problema, implemente un patrón 202. En el caso actual, el punto de conexión devuelve 202 (aceptado) y el cliente sondea.

El tiempo de espera de un minuto en la solicitud no tiene nada que ver con el tiempo de espera de la actividad. El último se usa para esperar a la devolución de llamada especificada por **callbackUri**.

El cuerpo devuelto al URI de devolución de llamada tiene que ser un JSON válido. Establezca el encabezado `Content-Type` en `application/json`.

Al usar la propiedad **Notificar el estado de la devolución de llamada**, tiene que agregar el siguiente código al cuerpo al hacer la devolución de llamada:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Pasos siguientes

Observe las siguientes actividades de flujo de control admitidas:

- [Actividad If Condition](control-flow-if-condition-activity.md)
- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Para cada actividad](control-flow-for-each-activity.md)
- [Actividad GetMetadata](control-flow-get-metadata-activity.md)
- [Actividad Lookup](control-flow-lookup-activity.md)
- [Actividad web](control-flow-web-activity.md)
- [Actividad Until](control-flow-until-activity.md)