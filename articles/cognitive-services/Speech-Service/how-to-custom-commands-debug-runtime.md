---
title: Guía de solución de problemas de una aplicación Comandos personalizados en tiempo de ejecución
titleSuffix: Azure Cognitive Services
description: En este artículo, aprenderá a depurar los errores del tiempo de ejecución en una aplicación de Comandos personalizados.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 7577a1c2666c8b5d34920e3827a9f4549b69edd2
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455809"
---
# <a name="troubleshoot-a-custom-commands-application-at-runtime"></a>Solucionar problemas de una aplicación Comandos personalizados en tiempo de ejecución

En este artículo se describe cómo realizar una depuración cuando se ven errores durante la ejecución de una aplicación de Comandos personalizados. 

## <a name="connection-failed"></a>Error en la conexión

Si ejecuta la aplicación de Comandos personalizados desde una [aplicación cliente (con el SDK de Voz)](./how-to-custom-commands-setup-speech-sdk.md) o el [cliente del asistente de voz de Windows](./how-to-custom-commands-developer-flow-test.md), puede experimentar errores de conexión como se indica a continuación:

| Código de error | Detalles |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: Error de actualización de WebSocket con un error de autenticación |
| [1002](#error-1002) | El servidor devolvió el código de estado ''404" cuando se esperaba el código "101". |

### <a name="error-401"></a>Error 401
- La región especificada en la aplicación cliente no coincide con la región de la aplicación de comandos personalizados.

- La clave de recurso de voz no es válida.
    
    Asegúrese de que la clave de recurso de voz esté correcta.

### <a name="error-1002"></a>Error 1002 
- No está publicada la aplicación de comandos personalizados.
    
    Publique la aplicación en el portal.

- El applicationId de comandos personalizados no es válido.

    Asegúrese de que el id. de la aplicación de comandos personalizados esté correcto.
 aplicación de comandos personalizada fuera del recurso de voz

    Asegúrese de que la aplicación de comandos personalizada se cree en el recurso de voz.

Para más información acerca de la solución de problemas de conexión, consulte [Solución de problemas de cliente de Windows Voice Assistant](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)


## <a name="dialog-is-canceled"></a>Diálogo cancelado

Al ejecutar la aplicación de Comandos personalizados, el diálogo se cancelará cuando se produzcan algunos errores.

- Si probará la aplicación en el portal, se mostrará directamente la descripción de la cancelación y se reproducirá un aviso sonoro de error. 

- Si está ejecutando la aplicación con el [cliente del asistente de voz de Windows](./how-to-custom-commands-developer-flow-test.md), se reproducirá un aviso sonoro de error. Puede encontrar el **Evento: CancelledDialog** en la sección **Registros de actividad**.

- Si sigue el ejemplo de [aplicación cliente (con el SDK de Voz)](./how-to-custom-commands-setup-speech-sdk.md), se reproducirá un aviso sonoro de error. Puede encontrar el **Evento: CancelledDialog** en la sección **Estado**.

- Si está creando su propia aplicación cliente, siempre puede diseñar la lógica que quiera para controlar los eventos de CancelledDialog.

El evento CancelledDialog se compone del código y la descripción de cancelación, como se muestra a continuación:

| Código de cancelación | Descripción de cancelación |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | No hubo ningún avance después del número máximo de turnos permitidos |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | Se superó la cuota de uso del reconocedor |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | No se pudo establecer la conexión con el reconocedor |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | No se puede acceder a esta aplicación con la suscripción actual |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | La entrada supera la longitud máxima que admite el reconocedor |
| [RecognizerNotFound](#recognizer-not-found) | No se encontró el reconocedor |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | Consulta no válida para el reconocedor |
| [RecognizerError](#recognizer-return-an-error) | El reconocedor devuelve un error |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>No hubo ningún avance después del número máximo de turnos permitidos
El diálogo se cancela cuando una ranura necesaria no se actualiza correctamente después de cierto número de turnos. El número máximo integrado es 3.

### <a name="recognizer-usage-quota-exceeded"></a>Se superó la cuota de uso del reconocedor
Language Understanding (LUIS) tiene límites sobre el uso de recursos. Normalmente, el "error Se superó la cuota de uso del reconocedor" puede deberse a lo siguiente: 
- La creación con LUIS supera el límite

    Agregue un recurso de predicción a la aplicación de Comandos personalizados: 
    1. Vaya a **Configuración**, recurso de LUIS
    1. Elija un recurso de predicción desde **Recurso de predicción** o haga clic en **Crear nuevo recurso**. 

- El recurso de predicción de LUIS supera el límite

    Si está en un recurso de predicción de F0, tiene un límite de 10 mil por mes y 5 consultas por segundo.

Para obtener más información sobre los límites de recursos de LUIS, consulte [Uso y límites de recursos de Language Understanding](../luis/luis-limits.md#resource-usage-and-limits).

### <a name="connection-to-the-recognizer-failed"></a>No se pudo establecer la conexión con el reconocedor
Normalmente, significa que hay un error de conexión transitorio con el reconocedor de Language Understanding (LUIS). Inténtelo de nuevo y el problema debería resolverse.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>No se puede acceder a esta aplicación con la suscripción actual
La suscripción no está autorizada para acceder a la aplicación de LUIS. 

### <a name="input-exceeds-the-maximum-supported-length"></a>La entrada supera la longitud máxima admitida
La entrada ha superado los 500 caracteres. Solo permitimos como máximo 500 caracteres para la expresión de entrada.

### <a name="invalid-query-for-the-recognizer"></a>Consulta no válida para el reconocedor
La entrada ha superado los 500 caracteres. Solo permitimos como máximo 500 caracteres para la expresión de entrada.

### <a name="recognizer-return-an-error"></a>El reconocedor devuelve un error
El reconocedor de LUIS devolvió un error al tratar de reconocer la entrada.

### <a name="recognizer-not-found"></a>No se encontró el reconocedor
No se encuentra el tipo de reconocedor especificado en el modelo de diálogo de Comandos personalizados. Actualmente, solo se admite el [reconocedor de Language Understanding (LUIS)](https://www.luis.ai/).

## <a name="other-common-errors"></a>Otros errores comunes
### <a name="unexpected-response"></a>Respuesta inesperada
Las respuestas inesperadas pueden tener varias causas. Puede comenzar con algunas comprobaciones:
- Intenciones de tipo sí/no en oraciones de ejemplo

    Dado que actualmente no se admiten las intenciones de tipo sí/no, salvo cuando se usa con la característica de confirmación. No se detectarán todas las intenciones de tipo sí/no que se hayan definido en las oraciones de ejemplo.

- Intenciones y oraciones de ejemplo similares entre comandos

    La precisión del reconocimiento de LUIS puede verse afectada cuando dos comandos comparten oraciones de ejemplo e intenciones similares. Debe tratar de crear funcionalidades de comando y oraciones de ejemplo lo más distintas posible.

    Para obtener un procedimiento recomendado para mejorar la precisión del reconocimiento, consulte el [procedimiento recomendado de LUIS](../luis/luis-concept-best-practices.md).

- Diálogo cancelado
    
    Compruebe los motivos de la cancelación en la sección anterior.

### <a name="error-while-rendering-the-template"></a>Error al representar la plantilla
Un parámetro sin definir se usó en la respuesta de voz. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Referencia a objeto no establecida como instancia de un objeto
Hay un parámetro vacío en la carga de JSON que se define en la acción **Send activity to client** (enviar una actividad al cliente).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Consulte los ejemplos en GitHub](https://aka.ms/speech/cc-samples)
