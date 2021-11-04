---
title: Procedimiento para llamar a Text Analytics for Health
titleSuffix: Azure Cognitive Services
description: Aprenda a extraer y etiquetar la información médica de textos clínicos no estructurados con Text Analytics para el estado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: d94e07b312bda98c6317e8a2b020510ffda800f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093173"
---
# <a name="how-to-use-text-analytics-for-health"></a>Uso de Text Analytics para el estado

> [!IMPORTANT] 
> Text Analytics para el sector sanitario es una funcionalidad que se proporciona "tal cual" y "con todos los errores". Text Analytics para el estado no está previsto ni está disponible para su uso como dispositivo médico, soporte clínico, herramienta de diagnóstico u otra tecnología que se pretenda usar en el diagnóstico, la cura, la mitigación, el tratamiento o la prevención de la enfermedad u otras condiciones, y Microsoft no concede ninguna licencia o derecho para usar esta funcionalidad con este fin. Esta funcionalidad no está diseñada o prevista para implementarse como un sustituto del asesoramiento médico profesional o la opinión de la atención sanitaria, el diagnóstico, el tratamiento o la opinión clínica de un profesional sanitario y no debe usarse como tal. El cliente es el único responsable de cualquier uso de Text Analytics para el estado. El cliente debe obtener una licencia por separado de todos y cada uno de los vocabularios fuente que va a usar en virtud de los términos establecidos en el [Apéndice al contrato de licencia del metatesauro de UMLS](https://www.nlm.nih.gov/research/umls/knowledge_sources/metathesaurus/release/license_agreement_appendix.html) o cualquier vínculo equivalente futuro. El cliente es responsable de garantizar el cumplimiento de esos términos de licencia, incluidas las restricciones geográficas u otras restricciones aplicables.


Text Analytics for Health se puede usar para extraer y etiquetar información médica pertinente de textos no estructurados, como notas del médico, resúmenes de altas, documentos clínicos e historiales sanitarios electrónicos.  Hay dos maneras de utilizar este servicio: 

* La API basada en web y las bibliotecas cliente (asincrónicas)
* Un [contenedor de Docker](use-containers.md) (sincrónica)

## <a name="features"></a>Características

Text Analytics for Health realiza el reconocimiento de entidades con nombre (NER), la extracción de relaciones, la negación de entidades y la vinculación de entidades en el texto en inglés para revelar información en texto clínico y biomédico sin estructurar. Consulte las [categorías de entidad](../concepts/health-entity-categories.md) devueltas por Text Analytics para el estado a fin de obtener una lista completa de las entidades admitidas. Para obtener información sobre las puntuaciones de confianza, vea la [nota de transparencia](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

> [!TIP]
> Si quiere empezar a usar esta característica, puede seguir el [artículo de inicio rápido](../quickstart.md). También puede realizar solicitudes de ejemplo mediante [Language Studio](../../language-studio.md) sin necesidad de escribir código.

## <a name="determine-how-to-process-the-data-optional"></a>Determinación de cómo procesar los datos (opcional)

### <a name="specify-the-text-analytics-for-health-model"></a>Especificación del modelo de Text Analytics for Health

De manera predeterminada, Text Analytics for Health usará el modelo de IA más reciente disponible en el texto. También puede configurar las solicitudes de API para usar una versión específica del modelo. El modelo que especifique se usará para realizar operaciones proporcionadas por Text Analytics for Health.

| Versiones admitidas | Versión más reciente |
|--|--|
| `2021-05-15` | `2021-05-15`   |

### <a name="text-analytics-for-health-container"></a>Text Analytics para el contenedor de estado

El [contenedor de Text Analytics for Health](use-containers.md) usa versiones de modelo distintas a las de la API REST y las bibliotecas cliente. Solo hay una versión de modelo disponible por cada imagen de contenedor.

| Punto de conexión                        | Etiqueta de la imagen de contenedor                     | Versión del modelo |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.016230002-onprem-amd64` (más reciente)            | `2021-05-15`  |
| `/entities/health`              | `3.0.015370001-onprem-amd64`            | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |

### <a name="input-languages"></a>Idiomas de entrada

Actualmente, Text Analytics for Health solo [admite](../language-support.md) el idioma inglés. 

## <a name="submitting-data"></a>Envío de datos

Para enviar una solicitud de API, necesitará el punto de conexión y la clave del recurso de idioma.

> [!NOTE]
> Puede encontrar la clave y el punto de conexión de su recurso de idioma en Azure Portal. Los encontrará en la página **Key and endpoint** (Clave y punto de conexión) del recurso, en **Resource management** (Administración de recursos). 

El análisis se realiza tras la recepción de la solicitud. Para obtener información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo, consulte los límites de datos a continuación.

Si envía una solicitud mediante la API REST o la biblioteca cliente, los resultados se devolverán de forma asincrónica. Si usa el contenedor de Docker, se devolverán de forma sincrónica.  


## <a name="getting-results-from-the-feature"></a>Obtención de resultados de la característica

En función de la solicitud de API y los datos que envíe a Text Analytics for Health, obtendrá lo siguiente:

[!INCLUDE [Text Analytics for health features](../includes/features.md)]


Cuando se usa esta característica de forma asincrónica, los resultados de la API están disponibles durante 24 horas desde el momento en que se ingiere la solicitud y se indican en la respuesta. Después de este período de tiempo, los resultados se purgan y ya no están disponibles para la recuperación.

## <a name="data-limits"></a>Límites de datos

> [!NOTE]
> * Si necesita analizar documentos con un tamaño superior al límite, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. Para obtener mejores resultados, divida el texto entre oraciones.
> * Un documento es una sola cadena de caracteres de texto.  

| Límite | Value |
|------------------------|---------------|
| Tamaño máximo de un documento individual | 5120 caracteres medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Tamaño máximo de la solicitud completa | 1 MB |
| Número máximo de documentos por solicitud | 10 para la API basada en web y 1000 para el contenedor. |

Si un documento supera el límite de caracteres, la API no lo procesará y devolverá un error de documento no válido. Si una solicitud de API tiene varios documentos, la API seguirá procesándolos si están dentro del límite de caracteres.

### <a name="rate-limits"></a>Límites de frecuencia

El límite de velocidad variará en función del [plan de tarifa](https://aka.ms/unifiedLanguagePricing). Estos límites son los mismos en ambas versiones de la API. Estos límites de velocidad no se aplican a Text Analytics para el contenedor de estado, que no tiene un límite de velocidad establecido.

| Nivel          | Solicitudes por segundo | Solicitudes por minuto |
|---------------|---------------------|---------------------|
| S / Varios servicios | 1000                | 1000                |
| F0         | 100                 | 300                 |

## <a name="see-also"></a>Consulte también

* [Text Analytics información general sobre el mantenimiento](../overview.md)
* [Categorías de entidad de Text Analytics for Health](../concepts/health-entity-categories.md)
