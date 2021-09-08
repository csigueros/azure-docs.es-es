---
title: Actualizar un comando desde un punto de conexión web
titleSuffix: Azure Cognitive Services
description: Aprenda a actualizar el estado de un comando mediante una llamada a un punto de conexión web.
services: cognitive-services
author: PatrickFarley
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: pafarley
ms.openlocfilehash: 3f75755a3ab9f327c526103e9fbe08d1c149dba6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540467"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Actualizar un comando desde un punto de conexión web

Si la aplicación cliente necesita que se actualice el estado de un comando en curso sin entrada de voz, puede usar una llamada a un punto de conexión web para actualizar el comando.

En este artículo, aprenderá a actualizar un comando en curso desde un punto de conexión web.

## <a name="prerequisites"></a>Requisitos previos
> [!div class = "checklist"]
> * Una [aplicación de Comandos personalizados creada](quickstart-custom-commands-application.md) previamente.

## <a name="create-an-azure-function"></a>Creación de una función de Azure 

En este ejemplo, se necesitará una [función de Azure](../../azure-functions/index.yml) desencadenada por HTTP que admita la entrada siguiente (o un subconjunto de esta entrada):

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Examinemos los atributos clave de esta entrada:

| Atributo | Explicación |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | El identificador único de la conversación. Tenga en cuenta que este identificador se puede generar desde la aplicación cliente. |
| **currentCommand** | El comando que está activo actualmente en la conversación. |
| **name** | El nombre del comando. El atributo `parameters` es un mapa con los valores actuales de los parámetros. |
| **currentGlobalParameters** | Un mapa como `parameters`, pero se usa para los parámetros globales. |

La salida de la función de Azure debe admitir el formato siguiente:

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Es posible que reconozca este formato, ya que es el mismo que usó al [actualizar un comando desde el cliente](./how-to-custom-commands-update-command-from-client.md). 

Ahora, cree una función de Azure basada en Node.js. Copie y pegue este código:

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Cuando llame a esta función de Azure desde Comandos personalizados, enviará los valores actuales de la conversación. Devolverá los parámetros que desea actualizar o si desea cancelar el comando actual.

## <a name="update-the-existing-custom-commands-app"></a>Actualización de la aplicación de Comandos personalizados existente

Vamos a enlazar la función de Azure con la aplicación de Comandos personalizados existente:

1. Agregue un nuevo comando denominado `IncrementCounter`.
1. Agregue solo una frase de ejemplo con el valor `increment`.
1. Agregue un nuevo parámetro llamado `Counter` (el mismo nombre que se especificó en la función de Azure) del tipo `Number` con un valor predeterminado de `0`.
1. Agregue un nuevo punto de conexión web denominado `IncrementEndpoint` con la dirección URL de la función de Azure y con la opción de **actualizaciones remotas** establecida en **Enabled** (Habilitado).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Captura de pantalla que muestra cómo establecer un punto de conexión web con actualizaciones remotas.":::
1. Cree una nueva regla de interacción denominada **IncrementRule** y agregue una acción de **llamar a un punto de conexión web**.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Captura de pantalla que muestra la creación de una regla de interacción.":::
1. En la configuración de la acción, seleccione `IncrementEndpoint`. En **En caso de éxito**, seleccione **Send speech response** (Enviar respuesta de voz) con el valor de `Counter`y configure **En caso de error** con un mensaje de error.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Captura de pantalla que muestra cómo establecer un contador de incremento para llamar a un punto de conexión web.":::
1. Establezca el estado posterior a la ejecución de la regla en **Wait for user's input** (Esperar la entrada del usuario).

## <a name="test-it"></a>Pruébelo

1. Guarde y entrene la aplicación.
1. Seleccione **Probar**.
1. Envíe `increment` (que es la frase de ejemplo para el comando `IncrementCounter`) varias veces.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example-no-mic.png" alt-text="Captura de pantalla que muestra un ejemplo de contador de incremento.":::

Observe que la función de Azure incrementa el valor del parámetro `Counter` en cada turno.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Habilite un proceso de CI/CD para su aplicación de Comandos personalizados](./how-to-custom-commands-deploy-cicd.md)