---
title: Conceptos de clasificación de trabajos para Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Obtenga información sobre los conceptos de clasificación de enrutador de trabajos de Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: c31c0831f625f65075f3c1f5cde2647ebc73ce64
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308434"
---
# <a name="job-classification-concepts"></a>Conceptos de clasificación de trabajos

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

El enrutador de trabajos de Azure Communication Services usa un proceso denominado **clasificación** cuando se envía un trabajo. En este artículo se describen las distintas formas de clasificar un trabajo y el efecto que este proceso tiene sobre él.

## <a name="job-classification-overview"></a>Introducción a la clasificación de trabajos

El enrutador de trabajos usa dos métodos principales para clasificar un trabajo: estático o dinámico. Si la aplicación que realiza la llamada tiene conocimientos sobre el identificador de cola, la prioridad o los selectores de trabajo, el trabajo se puede enviar sin una directiva de clasificación; conocida como **clasificación estática**. Si prefiere dejar que el enrutador de trabajos decida el identificador de cola, se puede usar una directiva de clasificación para modificar las propiedades del trabajo; conocida como **clasificación dinámica**.

Al enviar un trabajo mediante el SDK del enrutador de trabajo, el proceso de clasificación dará como resultado el envío de un evento a la suscripción de Event Grid de Azure Communication Services. Los eventos generados como parte del ciclo de vida de la clasificación proporcionan información sobre las acciones que está llevando a cabo el enrutador de trabajos. Por ejemplo, una clasificación correcta producirá un **RouterJobClassified** y aquella que genere un error producirá un **RouterJobClassificationFailed**.

El proceso de clasificación de un trabajo implica, de forma opcional, establecer las siguientes propiedades:

- Identificador de cola
- Priority
- Selectores de trabajo

## <a name="static-classification"></a>Clasificación estática

El envío de un trabajo con un identificador de cola, prioridad y selectores de trabajo definidos previamente le permite empezar a trabajar rápidamente. El enrutador de trabajos no modificará estas propiedades después de enviar el trabajo a menos que lo actualice especificando una directiva de clasificación antes de la asignación a un trabajo. Puede actualizar la propiedad de directiva de clasificación de un trabajo después del envío, lo que desencadenará el proceso de clasificación dinámica.

> [!NOTE]
> Tiene la opción de invalidar el resultado de la clasificación dinámica mediante el SDK del enrutador de trabajos para actualizar manualmente las propiedades del trabajo. Por ejemplo, podría especificar un identificador de cola estático inicialmente, a continuación, actualizar el trabajo con un identificador de directiva de clasificación para que se clasifique de forma dinámica y, después, invalidar el identificador de cola.

## <a name="dynamic-classification"></a>Clasificación dinámica

Si especifica una directiva de clasificación al enviar un trabajo, el enrutador de trabajos podrá asignar de forma dinámica el identificador de cola, la prioridad y modificar potencialmente los selectores de trabajo. Este tipo de clasificación es beneficioso, ya que la aplicación que realiza la llamada no necesita tener conocimiento de propiedades de trabajos, como el identificador de cola en entorno de ejecución.

### <a name="queue-selectors"></a>Selectores de cola

Una directiva de clasificación puede hacer referencia a `QueueSelector`, que el proceso de clasificación usa para determinar qué identificador de cola se elegirá para un trabajo determinado. Los siguientes tipos `QueueSelector` existen en el enrutador de trabajos y son opciones aplicables al proceso de selección de cola durante la clasificación:

**QueueLabelSelector**: al crear una cola de enrutadores de trabajo, puede especificar etiquetas para ayudar al proceso de selección de cola durante la clasificación del trabajo. Este tipo de selector usa una colección de tipos `LabelSelectorAttachment` para ofrecer la máxima flexibilidad a la hora de seleccionar la cola durante el proceso de clasificación. Use este selector para permitir que el proceso de clasificación de trabajos seleccione el identificador de cola en función de sus etiquetas. Para obtener más información, consulte la [siguiente](#using-labels-and-selectors-in-classification) sección.

**QueueIdSelector**: este selector permitirá el uso de uno de los muchos motores de reglas para determinar el identificador de cola del trabajo en función del resultado de la regla. Para obtener más información, consulte la página de [Conceptos de RouterRule](router-rule-concepts.md).

### <a name="worker-selectors"></a>Selectores de trabajo

Un selector de trabajo de una directiva de clasificación contiene una colección de tipos `LabelSelectorAttachment`, que el proceso de clasificación usa para adjuntar selectores de trabajo a un trabajo en función de sus etiquetas. Para obtener más información, consulte la [siguiente](#using-labels-and-selectors-in-classification) sección.

### <a name="prioritization-rule"></a>Regla de priorización

La prioridad de un trabajo se puede resolver durante la clasificación mediante uno de los muchos motores de reglas; parecido al funcionamiento de `QueueIdSelector`. Para obtener más información, consulte la página de [Conceptos de RouterRule](router-rule-concepts.md).

## <a name="using-labels-and-selectors-in-classification"></a>Uso de etiquetas y selectores en la clasificación

El enrutador de trabajos usa las "etiquetas" del par clave-valor de un trabajo o una cola para tomar distintas decisiones sobre el enrutamiento. Cuando usa un `LabelSelectorAttachment` en un `QueueSelector`, tiene función de filtro. Cuando se usa en el contexto de `WorkerSelectors`, adjunta selectores al conjunto inicial que se creó con el trabajo. Se pueden usar los siguientes tipos `LabelSelectorAttachment`:

**Selector de etiquetas estático**: adjunta siempre el `LabelSelector` determinado.

**Selector de etiquetas condicional**: evaluará una condición definida por una regla.  Si se resuelve en `true`, se aplicará la colección de selectores especificada.

**Selector de etiquetas de paso a través**: usa una clave y `LabelOperator` para comprobar la existencia de la clave. Este selector se puede usar en `QueueLabelSelector` para que coincida con una cola basada en el conjunto de etiquetas. Cuando se usa con el `WorkerSelectors`, el par clave-valor del trabajo se adjunta al `WorkerSelectors` del trabajo.

**Selector de etiquetas de regla**: obtiene una colección de selectores de uno de los muchos motores de reglas. Para obtener más información, consulte la página de [Conceptos de RouterRule](router-rule-concepts.md).

**Selector de etiquetas de asignación ponderada**: colección de objetos `WeightedAllocation` que especifican una ponderación basada en porcentajes y una colección de selectores que se aplicarán en función de la asignación de ponderación. Por ejemplo, puede que desee que el 30 % de los trabajos vaya a "Contoso" y el 70 % a "Fabrikam".

## <a name="reclassifying-a-job"></a>Volver a clasificar un trabajo
Una vez que se ha clasificado un trabajo, se puede volver a clasificar de las siguientes maneras:

1. Puede actualizar las etiquetas de trabajos, lo que hará que el enrutador de trabajos evalúe las nuevas etiquetas con la directiva de clasificación anterior.
2. Puede actualizar el identificador de directiva de clasificación de un trabajo, lo que hará que el enrutador de trabajos procese el trabajo existente con la nueva directiva.
3. Un **desencadenador** de directiva de excepciones puede realizar la **acción** de solicitar que se vuelva a clasificar un trabajo 

> [!NOTE]
> El SDK del enrutador de trabajo incluye un método `UpdateJobLabels` que simplemente actualiza las etiquetas sin hacer que el enrutador de trabajos ejecute el proceso de reclasificación.
