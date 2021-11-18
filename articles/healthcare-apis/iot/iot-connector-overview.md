---
title: ¿Qué es el conector de IoT? - API de Azure Healthcare
description: En este artículo, aprenderá los pasos que realiza el conector de IoT antes de almacenar datos de IoMT en el servicio FHIR.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: b74b3c1aefe2c7dd05421385e672385196aab15e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719436"
---
# <a name="what-is-iot-connector"></a>¿Qué es el conector de IoT?

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

El conector de IoT es una característica opcional de las API de Azure Healthcare que proporciona la capacidad de ingerir datos de dispositivos de Internet de las cosas médicas (IoMT).

En este artículo se proporciona información general sobre el flujo de datos en el conector de IoT. Aprenderá sobre las distintas fases de procesamiento de datos dentro del servicio de conector de IoT [](https://www.hl7.org/fhir/observation.html) que transforma los datos del dispositivo en recursos de observación basados en Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;).

A continuación se muestran las distintas fases por las que pasan los datos una vez que los recibe el conector de IoT.

## <a name="ingest"></a>Ingesta

La ingesta es la primera fase en la que se reciben los datos del dispositivo en el servicio del conector de IoT. El punto de conexión de ingesta de los datos del dispositivo se hospeda en un centro de eventos de Azure. [La plataforma Azure Event Hubs](../../event-hubs/index.yml) admite una gran escala y rendimiento con la capacidad de recibir y procesar millones de mensajes por segundo. También permite que el servicio del conector de IoT consuma mensajes de forma asincrónica, lo que elimina la necesidad de que los dispositivos esperen mientras se procesan los datos del dispositivo.

> [!NOTE]
> En este momento el único formato admitido para los datos del dispositivo es JSON.

## <a name="normalize"></a>Normalizar

Normalizar es la siguiente fase en la que los datos del dispositivo se recuperan del centro de eventos de Azure anterior y se procesan mediante plantillas de asignación de dispositivos. Este proceso de asignación da como resultado la transformación de los datos del dispositivo en un esquema normalizado. El proceso de normalización no solo simplifica el procesamiento de datos en fases posteriores, sino que también proporciona para la proyección un mensaje de entrada en varios mensajes normalizados. Por ejemplo, un dispositivo puede enviar varias constantes vitales con la temperatura del cuerpo, la frecuencia cardíaca, la presión arterial y la frecuencia respiratoria en un solo mensaje. Este mensaje de entrada creará cuatro recursos FHIR independientes. Cada recurso representaría una constante vital diferente, con el mensaje de entrada proyectado en cuatro mensajes normalizados diferentes.

## <a name="group"></a>Grupo

Group es la siguiente fase en la que los mensajes normalizados disponibles en la fase anterior se agrupan mediante tres parámetros diferentes: 

* Identidad del dispositivo
* Tipo de medida 
* Período de tiempo

La agrupación de la identidad del dispositivo y el tipo de medida habilita el uso del tipo de medida [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Este tipo proporciona una manera concisa de representar una serie de medidas basadas en el tiempo desde un dispositivo en FHIR, mientras que el período de tiempo controla la latencia en la que los recursos de observación generados por el conector de IoT se escriben en el servicio FHIR.

> [!NOTE]
> El valor de período de tiempo se establece de manera predeterminada en 15 minutos y no se puede configurar para la versión preliminar.

## <a name="transform"></a>Transformación

En la fase De transformación, los mensajes agrupados normalizados se procesan a través de plantillas de asignación de destino de FHIR. Los mensajes que coinciden con un tipo de plantilla se transforman en recursos de observación basados en FHIR, tal y como se especifica mediante la asignación.

En este momento, el recurso dispositivo, junto con su recurso patient asociado, también se recupera del servicio FHIR mediante el identificador de dispositivo presente en el mensaje. Estos recursos se agregan como una referencia al recurso Observación que se va a crear.

> [!NOTE]
>Todas las buscar identidades se almacenan en caché una vez resueltas para reducir la carga en el servicio FHIR. Si planea volver a usar dispositivos con varios pacientes, se recomienda crear un recurso de dispositivo virtual específico para el paciente y enviar el identificador de dispositivo virtual en la carga del mensaje. El dispositivo virtual se puede vincular al recurso de dispositivo real como primario.

Si no existe ningún recurso de dispositivo para un identificador de dispositivo determinado en el servicio FHIR, el resultado depende del valor de Tipo de resolución establecido en el momento de la creación. Cuando se establece en Buscar, se omite el mensaje específico y la canalización seguirá procesando otros mensajes entrantes. Si se establece en Crear, el servicio del conector de IoT creará un dispositivo sin sistema duro y recursos de pacientes en el servicio FHIR.

## <a name="persist"></a>Persist

Una vez que se genera el recurso de Observation FHIR en la fase de transformación, el recurso se guarda en el servicio FHIR. Si el recurso de FHIR es nuevo, se creará en el servicio. Si el recurso de FHIR ya existe, se actualizará.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las asignaciones de conectores de IoT, consulte estas guías:

>[!div class="nextstepaction"]
>[Uso de asignaciones de dispositivos](how-to-use-device-mappings.md)

>[!div class="nextstepaction"]
>[Uso de asignaciones de destino de FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso hl7.
