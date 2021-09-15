---
title: Actualizar un parámetro de comando desde una aplicación cliente
titleSuffix: Azure Cognitive Services
description: Aprenda actualizar comandos desde una aplicación cliente.
services: cognitive-services
author: PatrickFarley
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: pafarley
ms.openlocfilehash: c5314e659379fb56aca1eea0f1834985fc303c86
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541034"
---
# <a name="update-a-command-from-a-client-app"></a>Actualización de un comando desde una aplicación cliente

En este artículo, aprenderá a actualizar un comando en curso desde una aplicación cliente.

## <a name="prerequisites"></a>Requisitos previos
> [!div class = "checklist"]
> * Una [aplicación de Comandos personalizados creada](quickstart-custom-commands-application.md) previamente.

## <a name="update-the-state-of-a-command"></a>Actualización del estado de un comando

Si la aplicación cliente necesita que se actualice el estado de un comando en curso sin una entrada de voz, se puede enviar un evento para actualizarlo.

Para ilustrar este escenario, envíe la siguiente actividad de evento para actualizar el estado de un comando en curso (`TurnOnOff`): 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Examinemos los atributos clave de esta actividad:

| Atributo | Explicación |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | El tipo de la actividad es `"event"`. |
| **name** | El nombre del evento debe ser `"RemoteUpdate"`. |
| **value** | El atributo `"value"` contiene los atributos necesarios para actualizar el comando actual. |
| **updatedCommand** | El atributo `"updatedCommand"` contiene el nombre del comando. Dentro de ese atributo, `"updatedParameters"` es un mapa con los nombres de los parámetros y sus valores actualizados. |
| **cancel** | Si es necesario cancelar el comando en curso, establezca el atributo `"cancel"` en `true`. |
| **updatedGlobalParameters** | El atributo `"updatedGlobalParameters"` es un mapa como `"updatedParameters"`, pero se usa para los parámetros globales. |
| **processTurn** | Si el turno debe procesarse una vez enviada la actividad, establezca el atributo `"processTurn"` en `true`. |

Este escenario se puede probar en el portal de Comandos personalizados:

1. Abra la aplicación de Comandos personalizados que creó anteriormente. 
1. Haga clic en **Train** (Entrenar) y después en **Test** (Probar).
1. Envíe `turn`.
1. Abra el panel lateral y seleccione **Activity editor** (Editor de actividades).
1. Escriba y envíe el evento `RemoteCommand` especificado en la sección anterior.
    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla que muestra el evento de un comando remoto.](media/custom-commands/send-remote-command-activity-no-mic.png)

Observe cómo el valor del parámetro `"OnOff"` se estableció en `"on"` mediante una actividad realizada desde el cliente en lugar de usar voz o texto.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Actualización del catálogo del parámetro para un comando

Al configurar la lista de opciones válidas para un parámetro, los valores del parámetro se definen globalmente para la aplicación. 

En nuestro ejemplo, el parámetro `SubjectDevice` tendrá una lista fija de valores admitidos, independientemente de la conversación.

Si desea agregar nuevas entradas al catálogo del parámetro por conversación, puede enviar la siguiente actividad:

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Con esta actividad, ha agregado una entrada para `"stereo"` al catálogo del parámetro `"SubjectDevice"` en el comando `"TurnOnOff"`.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Captura de pantalla que muestra una actualización del catálogo.":::

Tenga en cuenta un par de cuestiones:
- Solo es necesario enviar esta actividad una vez (preferiblemente justo después de iniciar una conexión).
- Después de enviar esta actividad, debe esperar a que el evento `ParameterCatalogsUpdated` se envíe de vuelta al cliente.

## <a name="add-more-context-from-the-client-application"></a>Incorporación de más contexto desde la aplicación cliente

Puede establecer contexto adicional desde la aplicación cliente por conversación, contexto que se puede usar posteriormente en la aplicación de Comandos personalizados. 

Por ejemplo, supongamos que desea enviar el identificador y el nombre del dispositivo conectado a la aplicación de Comandos personalizados.

Para probar este escenario, se va a crear un comando en la aplicación actual:
1. Cree un comando denominado `GetDeviceInfo`.
1. Agregue una oración de ejemplo de `get device info`.
1. En la regla de finalización **Done** (Listo), agregue una acción de **envío de respuesta de voz** que contenga los atributos de `clientContext`.
   ![Captura de pantalla que muestra una respuesta para enviar voz con contexto.](media/custom-commands/send-speech-response-context.png)
1. Guarde, entrene y pruebe la aplicación.
1. En la ventana de pruebas, envíe una actividad para actualizar el contexto de cliente.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. Envíe el texto `get device info`.
   ![Captura de pantalla que muestra una actividad para el envío del contexto de cliente.](media/custom-commands/send-client-context-activity-no-mic.png)

Tenga en cuenta algunas cuestiones:
- Solo es necesario enviar esta actividad una vez (preferiblemente justo después de iniciar una conexión).
- Puede usar objetos complejos para `clientContext`.
- Puede usar `clientContext` en las respuestas de voz tanto para enviar actividades como para llamar a puntos de conexión web.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Actualizar un comando desde un punto de conexión web](./how-to-custom-commands-update-command-from-web-endpoint.md)
