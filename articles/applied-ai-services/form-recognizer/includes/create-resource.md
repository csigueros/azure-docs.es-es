---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9f4a863cb0c3ecf087d5efdcde37ce907a7a7d41
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021717"
---
Vaya a Azure Portal y <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="cree un nuevo recurso de Form Recognizer" target="_blank">create a new Form Recognizer resource </a>. En el panel **Crear**, proporcione la siguiente información:

| Detalles del proyecto   | Descripción   |
|--|--|
| **Suscripción** | Seleccione la suscripción de Azure a la que se le ha concedido acceso. |
| **Grupos de recursos** | El grupo de recursos de Azure que contiene el recurso. Puede crear un nuevo grupo o agregarlo a uno ya existente. |
| **Región** | Ubicación de la instancia de Cognitive Services. Las diferentes ubicaciones pueden crear latencias, pero no tienen ningún impacto en la disponibilidad del tiempo de ejecución del recurso. |
| **Nombre** | Un nombre descriptivo para su recurso. Se recomienda usar un nombre descriptivo, como *MyNameFormRecognizer*. |
| **Plan de tarifa** | El costo del recurso depende el plan de tarifa elegido y del uso. Para obtener más información, consulte los [detalles de los precios](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="retrieve-the-key-and-endpoint"></a>Recuperación de la clave y el punto de conexión

Cuando el recurso de Form Recognizer termine la implementación, búsquelo y selecciónelo en la lista **Todos los recursos** del portal. Encontrará la clave y el punto de conexión en la página **Key and Endpoint** (Clave y punto de conexión) del recurso, en **Administración de recursos**. Guarde ambos en una ubicación temporal antes de continuar.
