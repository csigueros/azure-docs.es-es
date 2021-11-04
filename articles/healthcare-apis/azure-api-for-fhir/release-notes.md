---
title: Azure API for FHIR versiones mensuales
description: En este artículo se proporcionan detalles sobre las Azure API for FHIR y las mejoras mensuales.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/01/2021
ms.author: cavoeg
ms.openlocfilehash: 201de8f7bbae01c708757ee2290f6005979410c4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477544"
---
# <a name="release-notes-azure-api-for-fhir"></a>Notas de la versión: Azure API for FHIR

Azure API for FHIR proporciona una implementación totalmente administrada Microsoft FHIR Server para Azure. El servidor es una implementación del estándar [FHIR](https://hl7.org/fhir). En este documento se proporcionan detalles sobre las características y mejoras realizadas en Azure API for FHIR.

## <a name="september-2021"></a>Septiembre de 2021 

### <a name="features-and-enhancements"></a>**Características y mejoras**

|Mejoras | Descripción |
|:------------------- | -----------:|

|Se ha agregado compatibilidad con la revisión condicional. | [Revisión condicional](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
| :----------------------------------- | ------: |
|Revisión condicional |[#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Se ha agregado un evento de auditoría de revisión condicional. |[#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|Permitir revisión JSON en agrupaciones | [Revisión json en agrupaciones](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
| :----------------------------------- | ------: |
|Permitir agrupaciones de historial de búsqueda con solicitudes de revisión |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Habilitación de la revisión JSON en agrupaciones mediante recursos binarios |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |

|Nuevos subtipos de eventos de auditoría| Descripción|
| :----------------------------------- | ------: |
|Se han agregado nuevos [subtipos OperationName de auditoría.](././../azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|[Mejoras de volver a indexar:](how-to-run-a-reindex.md) | Descripción|
| :----------------------------------- | ------: |
|Se han [agregado límites para los parámetros de reindexación.](how-to-run-a-reindex.md)|[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Mensaje de error de actualización para volver a indexar los límites de parámetros|[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Agrega la comprobación final del recuento de reindexación. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|


### <a name="bug-fixes"></a>**Correcciones de errores**

|Se han resuelto los errores de revisión.| Descripción|
| :----------------------------------- | ------: |
|Captura más amplia de excepciones durante la aplicación de la revisión |[#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|Corrige el historial con PATCH en STU3| [#2177](https://github.com/microsoft/fhir-server/pull/2177)|

|Errores de búsqueda personalizada| Descripción|
| :----------------------------------- | ------: |
|Soluciona el error de eliminación con parámetros de Custom Search| [#2133](https://github.com/microsoft/fhir-server/pull/2133)|
|Se ha agregado lógica de reintento al eliminar el parámetro de búsqueda.| [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Establecer el número máximo de elementos en las opciones de búsqueda en SearchParameterDefinitionManager| [#2141](https://github.com/microsoft/fhir-server/pull/2141)|
|Mejor excepción en caso de una expresión no válida en el parámetro de búsqueda|[#2157](https://github.com/microsoft/fhir-server/pull/2157)|

|Error de reintento 503 resuelto| Descripción|
| :----------------------------------- | ------: |
|Reintente el error 503 desde Cosmos DB |[#2106](https://github.com/microsoft/fhir-server/pull/2106)|
|Corrige el procesamiento de 429s desde StoreProcedures|[#2165](https://github.com/microsoft/fhir-server/pull/2165)|

|GitHub problemas cerrados| Descripción|
| :----------------------------------- | ------: |
|No se puede crear el parámetro de búsqueda personalizado para el dispositivo médico CarePlan |[#2146](https://github.com/microsoft/fhir-server/issues/2146) |
|Mensaje de error poco claro para la creación condicional sin identificador| [#2168](https://github.com/microsoft/fhir-server/issues/2168)|

### <a name="iot-connector-for-fhir-preview"></a>Conector de IoT para FHIR (versión preliminar)

|Corrección de errores| Descripción|
| :-----------------------------------| ------: |
|Vínculo corregido a la documentación de Azure del conector de IoT|Vínculo roto en el portal Azure API for FHIR web |

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre las características y correcciones de errores en las API de Azure Healthcare (servicio FHIR, servicio DICOM y conector de IoT), consulte

>[!div class="nextstepaction"]
>[Notas de la versión: API de Azure Healthcare](../release-notes.md)
