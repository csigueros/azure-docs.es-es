---
title: 'Flujo de datos en el conector de IoT: API de Azure Healthcare'
description: Comprender el flujo de datos del conector de IoT. El conector de IoT ingiere, normaliza, agrupa, transforma y conserva los datos de IoMT en el servicio FHIR.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: e442cad97c01c4cd15c931dea45a849cc723cb76
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2021
ms.locfileid: "132954280"
---
# <a name="iot-connector-data-flow"></a>Flujo de datos del conector de IoT

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo se proporciona información general sobre el flujo de datos del conector de IoT. Aprenderá sobre las distintas fases de procesamiento de datos en el conector de IoT que [](https://www.hl7.org/fhir/observation.html) transforman los datos del dispositivo en recursos de observación basados en Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;).

Los datos de dispositivos relacionados con la salud o dispositivos médicos fluyen a través de una ruta de acceso en la que el conector de IoT transforma los datos en FHIR y, a continuación, los datos se almacenan en el servicio FHIR y se accede a ellos desde este. La ruta de acceso a los datos de mantenimiento sigue estos pasos en este orden: ingesta, normalización, agrupación, transformación y persistencia. En este flujo de datos, los datos de mantenimiento se recuperan del dispositivo en el primer paso de la ingesta. Una vez recibidos los datos, se procesan o normalizan por plantillas de esquema seleccionadas por el usuario o creadas por el usuario, por lo que los datos de mantenimiento son más fáciles de procesar y se pueden agrupar. Los datos de mantenimiento se agrupan en tres parámetros Operate. Una vez normalizados y agrupados los datos de mantenimiento, se pueden procesar o transformar a través de asignaciones de destino de FHIR y, a continuación, guardarse o conservarse en el servicio FHIR.

En este artículo se profundiza más en cada paso del flujo de datos. Los pasos siguientes son cómo implementar un conector [de IoT](deploy-iot-connector-in-azure.md) mediante asignaciones de dispositivos (el paso de normalización) y asignaciones de destino de FHIR (el paso de transformación).

En las secciones siguientes se describen las fases por las que pasan los datos de IoMT (Internet de las cosas médicas) una vez recibidos de un centro de eventos en el conector de IoT.

:::image type="content" source="media/iot-data-flow/iot-data-flow.png" alt-text="Flujo de datos de IoMT desde dispositivos IoT a un centro de eventos. Los datos de IoMT ingeridos por el conector de IoT se normalizan, agrupan, transforman y conservan en un servicio FHIR." lightbox="media/iot-data-flow/iot-data-flow.png":::

## <a name="ingest"></a>Ingesta
La ingesta es la primera fase en la que se reciben los datos del dispositivo en el conector de IoT. El punto de conexión de ingesta para los datos del dispositivo se hospeda en [una Azure Event Hubs](../../event-hubs/index.yml). Azure Event Hubs plataforma admite alta escala y rendimiento con capacidad para recibir y procesar millones de mensajes por segundo. También permite que el conector de IoT consuma mensajes de forma asincrónica, lo que elimina la necesidad de que los dispositivos esperen mientras se procesan los datos del dispositivo.

> [!NOTE]
> En este momento el único formato admitido para los datos del dispositivo es JSON.

## <a name="normalize"></a>Normalizar
Normalizar es la siguiente fase en la que los datos del dispositivo se recuperan del centro de eventos anterior y se procesan mediante las asignaciones de dispositivos. Este proceso de asignación da como resultado la transformación de los datos del dispositivo en un esquema normalizado. 

El proceso de normalización no solo simplifica el procesamiento de datos en fases posteriores, sino que también proporciona la capacidad de proyectar un mensaje de entrada en varios mensajes normalizados. Por ejemplo, un dispositivo puede enviar varias constantes vitales con la temperatura del cuerpo, la frecuencia cardíaca, la presión arterial y la frecuencia respiratoria en un solo mensaje. Este mensaje de entrada creará cuatro recursos FHIR independientes. Cada recurso representaría una constante vital diferente, con el mensaje de entrada proyectado en cuatro mensajes normalizados diferentes.

## <a name="group"></a>Grupo
Group es la siguiente fase en la que los mensajes normalizados disponibles en la fase anterior se agrupan mediante tres parámetros diferentes:

* Identidad del dispositivo
* Tipo de medida 
* Período de tiempo

La agrupación de la identidad del dispositivo y el tipo de medida habilita el uso del tipo de medida [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Este tipo proporciona una manera concisa de representar una serie de medidas basadas en el tiempo desde un dispositivo en FHIR. Y el período de tiempo controla la latencia en la que los recursos de observación generados por el conector de IoT se escriben en el servicio FHIR.

> [!NOTE]
> El valor de período de tiempo se establece de manera predeterminada en 15 minutos y no se puede configurar para la versión preliminar.

## <a name="transform"></a>Transformación
En la fase Transformación, los mensajes agrupados normalizados se procesan a través de plantillas de asignación de destino de FHIR. Los mensajes que coinciden con un tipo de plantilla se transforman en recursos de observación basados en FHIR, tal y como se especifica mediante la asignación.

En este momento, [el](https://www.hl7.org/fhir/device.html) recurso dispositivo, junto con su recurso [patient](https://www.hl7.org/fhir/patient.html) asociado, también se recupera del servicio FHIR mediante el identificador de dispositivo presente en el mensaje. Estos recursos se agregan como una referencia al recurso Observación que se va a crear.

> [!NOTE]
> Todas las buscar identidades se almacenan en caché una vez resueltas para reducir la carga en el servicio FHIR. Si planea reutilizar dispositivos con varios pacientes, se recomienda crear un recurso de dispositivo virtual específico para el paciente y enviar el identificador de dispositivo virtual en la carga del mensaje. El dispositivo virtual se puede vincular al recurso de dispositivo real como primario.

Si no existe ningún recurso de dispositivo para un identificador de dispositivo determinado en el servicio FHIR, el resultado depende del valor de establecido en el `Resolution Type` momento de la creación. Cuando se establece en `Lookup`, se omite el mensaje específico y la canalización seguirá procesando otros mensajes entrantes. Si se establece en , el conector de IoT creará un dispositivo sin sistema duro y recursos de pacientes `Create` en el servicio FHIR.  

## <a name="persist"></a>Persist
Una vez que se genera el recurso de Observation FHIR en la fase de transformación, el recurso se guarda en el servicio FHIR. Si el recurso de Observation FHIR es nuevo, se creará en el servicio FHIR. Si el recurso de Observation FHIR ya existía, se actualizará.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a crear asignaciones de destino de dispositivos y FHIR.

> [!div class="nextstepaction"]
> [Asignaciones de dispositivos](how-to-use-device-mappings.md)

> [!div class="nextstepaction"]
> [Asignaciones de destino de FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) es una marca registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7.
