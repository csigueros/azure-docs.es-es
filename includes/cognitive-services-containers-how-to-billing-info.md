---
author: aahill
ms.author: aahi
ms.date: 07/27/2021
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 1013fc4746a858445ce8df6a451f05632a46aad4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024608"
---
Las consultas en el contenedor se facturan con el plan de tarifa del recurso de Azure que se usa para el parámetro `ApiKey`.

Los contenedores de Azure Cognitive Services no tienen licencia para ejecutarse si no están conectados al punto de conexión de facturación o a las mediciones. Debe habilitar los contenedores para que comuniquen la información de facturación al punto de conexión de facturación en todo momento. Los contenedores de Cognitive Services no envían datos de los clientes (por ejemplo, la imagen o el texto que se está analizando) a Microsoft.

### <a name="connect-to-azure"></a>Conexión con Azure

El contenedor necesita que se ejecuten los valores del argumento de facturación. Estos valores permiten al contenedor conectarse al punto de conexión de facturación. El contenedor informa sobre el uso cada 10 a 15 minutos. Si el contenedor no se conecta a Azure en la ventana de tiempo permitida, continuará ejecutándose pero no atenderá las consultas hasta que se restaure el punto de conexión de facturación. Se intenta 10 veces la conexión en el mismo intervalo de tiempo de 10 a 15 minutos. Si no se puede conectar con el punto de conexión de facturación en esos 10 intentos, el contenedor deja de atender solicitudes. Consulte [Preguntas más frecuentes acerca de los contenedores de Cognitive Services](../articles/cognitive-services/containers/container-faq.yml#how-does-billing-work) para obtener un ejemplo de la información que se envía a Microsoft para la facturación.

### <a name="billing-arguments"></a>Argumentos de facturación

El comando <a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank">`docker run` <span class="docon docon-navigate-external x-hidden-focus"></span></a> iniciará el contenedor cuando se especifiquen las tres opciones siguientes con valores válidos:

| Opción | Descripción |
|--------|-------------|
| `ApiKey` | La clave de API del recurso de Cognitive Services que se usa para realizar un seguimiento de la información de facturación.<br/>El valor de esta opción se debe establecer en una clave de API para el recurso aprovisionado que se especifica en `Billing`. |
| `Billing` | El punto de conexión del recurso de Cognitive Services que se usa para realizar el seguimiento de la información de facturación.<br/>El valor de esta opción debe establecerse en el URI del punto de conexión de un recurso aprovisionado de Azure.|
| `Eula` | Indica que ha aceptado la licencia del contenedor.<br/>El valor de esta opción debe establecerse en **accept**. |
