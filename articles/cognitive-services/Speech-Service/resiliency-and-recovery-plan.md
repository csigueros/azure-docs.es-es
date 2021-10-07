---
title: Procedimiento de copia de seguridad y recuperación de recursos de personalización de voz
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo prepararse para las interrupciones del servicio con Habla personalizada y Voz personalizada.
services: cognitive-services
author: masakiitagaki
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: mitagaki
ms.openlocfilehash: 0f540025561b6e452371a74093133bf3e1183b1b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744117"
---
# <a name="back-up-and-recover-speech-customization-resources"></a>Copia de seguridad y recuperación de recursos de personalización de voz

El servicio Voz está [disponible en distintas regiones](./regions.md). Las claves de suscripción del servicio están vinculadas a una sola región. Al adquirir una clave, se selecciona una región específica donde residen los datos, el modelo y las implementaciones.

Los conjuntos de datos para los recursos de datos creados por el cliente, como los modelos de voz personalizados y las fuentes de voz personalizadas, también están **disponibles solo dentro de la región implementada por el servicio**. Estos recursos son:

**Habla personalizada**
-   Datos de audio/texto de entrenamiento
-   Datos de audio/texto de prueba
-   Modelos de voz personalizados
-   Datos de registro

**Voz personalizada**
-   Datos de audio/texto de entrenamiento
-   Datos de audio/texto de prueba
-   Cuentes de voz personalizadas

Aunque algunos clientes usan nuestros puntos de conexión predeterminados para transcribir audio o voces estándar para la síntesis de voz, otros clientes crean recursos para la personalización.

Los propios repositorios hacen una copia de seguridad de estos recursos de forma periódica y automática, por lo que **no se producirá ninguna pérdida de datos** si una región deja de estar disponible. Sin embargo, debe tomar medidas para garantizar la continuidad del servicio en caso de que se interrumpa una región.

## <a name="how-to-monitor-service-availability"></a>Supervisión de la disponibilidad del servicio

Si usa nuestros puntos de conexión predeterminados, debe configurar el código de cliente para supervisar los errores y, si los errores persisten, debe redirigirlos a otra región de su elección donde tenga una suscripción de servicio.

Siga estos pasos para configurar el cliente para que supervise si hay errores:

1.  Busque la [lista de puntos de conexión disponibles regionalmente en nuestra documentación](./rest-speech-to-text.md).
2.  Seleccione en la lista una región primaria y una o varias regiones secundarias o de copia de seguridad.
3. Desde Azure Portal, cree recursos del servicio Voz para cada región.
    -  Si ha establecido una cuota específica, también puede considerar la posibilidad de establecer la misma cuota en las regiones de copia de seguridad. Puede encontrar más detalles en [Cuotas y límites del servicio de voz](./speech-services-quotas-and-limits.md).

4.  Tenga en cuenta que cada región tiene su propio servicio de token STS. Para la región primaria y las regiones de reserva, el archivo de configuración de cliente debe conocer lo siguiente:
    -  Puntos de conexión del servicio Voz regional
    -  [Clave de suscripción regional y código de región](./rest-speech-to-text.md)

5.  Configure el código para supervisar los errores de conectividad (normalmente, los tiempos de espera de conexión y los errores de falta de disponibilidad del servicio). Este es el código de ejemplo en C#: [GitHub: Agregar ejemplo para mostrar un posible candidato para cambiar de región](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/fa6428a0837779cbeae172688e0286625e340942/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L965).

    1.  Dado que las redes experimentan errores transitorios, en caso de que se produzcan problemas de conectividad únicos, la sugerencia es volver a intentarlo.
    2.  Para la persistencia, redirija el tráfico al nuevo servicio de token STS y al punto de conexión del servicio Voz. Para texto a voz, haga referencia al código de ejemplo: [GitHub: región de conmutación de voz pública de TTS](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L880).

La recuperación de errores regionales para este tipo de uso puede ser instantánea y a un coste muy bajo. Lo único que se necesita es el desarrollo de esta funcionalidad del lado cliente. La pérdida de datos que se producirá suponiendo que no haya ninguna copia de seguridad de la secuencia de audio será mínima.

## <a name="custom-endpoint-recovery"></a>Recuperación de puntos de conexión personalizados

Los recursos de datos, los modelos o las implementaciones de una región no se pueden hacer visibles ni accesibles en ninguna otra región.

Debe crear recursos del servicio Voz en una región principal y una secundaria siguiendo los mismos pasos que se usan para los puntos de conexión predeterminados.

### <a name="custom-speech"></a>Habla personalizada

El servicio Habla personalizada no admite la conmutación automática por error. Se recomiendan los pasos siguientes para prepararse para la conmutación por error manual o automática implementada en el código de cliente. En estos pasos, replicará modelos personalizados en una región secundaria. Con esta preparación, el código de cliente puede cambiar a una región secundaria cuando se produce un error en la región primaria.

1.  Cree el modelo personalizado en una región principal (primaria).
2.  Ejecute la [API de copia de modelos](https://eastus2.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) para replicar el modelo personalizado en todas las regiones preparadas (secundarias).
3.  Vaya a Speech Studio para cargar el modelo copiado y crear un nuevo punto de conexión en la región secundaria. Vea cómo implementar un nuevo modelo en [Entrenamiento e implementación de un modelo de Habla personalizada](./how-to-custom-speech-train-model.md).
    -  Si ha establecido una cuota específica, considere también la posibilidad de establecer la misma cuota en las regiones de copia de seguridad. Puede encontrar más detalles en [Cuotas y límites del servicio de voz](./speech-services-quotas-and-limits.md).
4.  Configure el cliente para conmutar por error en errores persistentes como con el uso de puntos de conexión predeterminados.

El código de cliente puede supervisar la disponibilidad de los modelos implementados en la región primaria y redirigir el tráfico de audio a la región secundaria cuando se produce un error en la región primaria. Si no necesita una conmutación por error en tiempo real, puede seguir estos pasos para prepararse para una conmutación por error manual.

#### <a name="offline-failover"></a>Conmutación por error sin conexión

Si no necesita una conmutación por error en tiempo real, puede decidir importar los datos o crear e implementar los modelos en la región secundaria más adelante, con el conocimiento de que estas tareas tardarán tiempo en completarse.

#### <a name="failover-time-requirements"></a>Requisitos de tiempo de conmutación por error

En esta sección se proporcionan instrucciones generales sobre el tiempo. Los tiempos se registraron para calcular la conmutación por error sin conexión mediante un [conjunto de datos de prueba representativo](https://github.com/microsoft/Cognitive-Custom-Speech-Service).

-   Carga de datos en una nueva región: **15 minutos**
-   Creación de modelos acústicos o de lenguaje: **6 horas (en función del volumen de datos)**
-   Evaluación del modelo: **30 min**
-   Implementación del punto de conexión: **10 min**
-   Llamada API de copia de modelo: **10 min**
-   Reconfiguración e implementación de código de cliente: **en función del sistema del cliente**

No obstante, es aconsejable crear claves para una región primaria y secundaria para modelos de producción con requisitos en tiempo real.

### <a name="custom-voice"></a>Voz personalizada

Voz personalizada no admite la conmutación automática por error. Controle los errores de síntesis en tiempo real con estas dos opciones.

**Opción 1: Conmutación por error a la voz pública en la misma región.**

Cuando se produce un error en la síntesis de voz personalizada en tiempo real, conmute por error a una voz pública (código de ejemplo de cliente: [GitHub: conmutación por error de voz personalizada a voz pública](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L899)).

Compruebe las [voces públicas disponibles](./language-support.md#neural-voices). También puede cambiar el código de ejemplo anterior si desea conmutar por error a una voz diferente o a otra región.

**Opción 2: Conmutación por error a una voz personalizada en otra región.**

1.  Cree e implemente la voz personalizada en una región principal (primaria).
2.  Copie el modelo de voz personalizado en otra región (la región secundaria) en [Speech Studio](https://speech.microsoft.com).
3.  Vaya a Speech Studio y cambie al recurso Voz en la región secundaria. Cargue el modelo copiado y cree un nuevo punto de conexión.
    -   La implementación del modelo de voz suele finalizar **en 3 minutos**.
    -   Nota: El punto de conexión adicional está sujeto a cargos adicionales. [Compruebe los precios del hospedaje de modelos aquí](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

4.  Configure el cliente para conmutar por error a la región secundaria. Vea el código de ejemplo en C#: [GitHub: conmutación por error de voz personalizada a la región secundaria](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L920).