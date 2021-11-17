---
title: Azure API for FHIR versiones mensuales
description: En este artículo se proporcionan detalles sobre las Azure API for FHIR y las mejoras mensuales.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: cavoeg
ms.openlocfilehash: 0acbf2efdb65ff5376da9be918eaead2169cd5d6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547919"
---
# <a name="release-notes-azure-api-for-fhir"></a>Notas de la versión: Azure API for FHIR

Azure API for FHIR proporciona una implementación totalmente administrada Microsoft FHIR Server para Azure. El servidor es una implementación del estándar [FHIR](https://hl7.org/fhir). En este documento se proporcionan detalles sobre las características y mejoras realizadas en Azure API for FHIR.


## <a name="october-2021"></a>Octubre de 2021

### <a name="bug-fixes"></a>**Correcciones de errores**

| Error de bucle infinito | Información relacionada          |
| ----------------- | ----------------------------: |
|Se ha corregido [un problema por el](./././../azure-api-for-fhir/fhir-rest-api-capabilities.md#conditional-delete) que la eliminación condicional podía dar lugar a un bucle infinito. | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |

## <a name="september-2021"></a>Septiembre de 2021 

### <a name="features-and-enhancements"></a>**Características y mejoras**

|Mejoras &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |Información relacionada |
|------------------- | --------------- |

|Se ha agregado compatibilidad con la revisión condicional. | [Revisión condicional](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
| ----------------------------------- | ------: |
|Revisión condicional |[#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Se ha agregado un evento de auditoría de revisión condicional. |[#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|Permitir revisión JSON en agrupaciones | [Revisión json en agrupaciones](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
| ----------------------------------- | ------: |
|Permite agrupaciones de historial de búsqueda con solicitudes de revisión. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Se ha habilitado la revisión JSON en agrupaciones que usan recursos binarios. |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |

|Nuevos subtipos de eventos de auditoría |Información relacionada |
| ----------------------------------- | ---------------: |
|Se han agregado nuevos [subtipos OperationName de auditoría.](././../azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|[Mejoras de reindexación](how-to-run-a-reindex.md) |Información relacionada |
| ----------------------------------- | ---------------: |
|Se han [agregado límites para los parámetros de reindexación.](how-to-run-a-reindex.md) |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Se ha actualizado el mensaje de error para volver a indexar los límites de parámetros. |[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Se ha agregado la comprobación final del recuento de reindexación. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|


### <a name="bug-fixes"></a>**Correcciones de errores**

|Errores de revisión resueltos |Información relacionada |
| :----------------------------------- | ---------------: |
|Captura más amplia para las excepciones al aplicar la revisión. |[#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|Corrige el historial con PATCH en PATCH3.| [#2177](https://github.com/microsoft/fhir-server/pull/2177)|

|Errores de búsqueda personalizada |Información relacionada |
| ----------------------------------- | ---------------: |
|Soluciona el error de eliminación con parámetros de Búsqueda personalizada. | [#2133](https://github.com/microsoft/fhir-server/pull/2133)|
|Se ha agregado lógica de reintento al eliminar el parámetro Search. | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Establezca el número máximo de elementos en las opciones de búsqueda en SearchParameterDefinitionManager. | [#2141](https://github.com/microsoft/fhir-server/pull/2141)|
|Proporciona una excepción mejor si hay una expresión no válida en el parámetro de búsqueda. |[#2157](https://github.com/microsoft/fhir-server/pull/2157)|

|Se ha resuelto el error 503 de reintento |Información relacionada |
| ----------------------------------- | ---------------: |
|Vuelva a intentar el error 503 desde Cosmos db. |[#2106](https://github.com/microsoft/fhir-server/pull/2106)|
|Corrige el procesamiento de 429 desde StoreProcedures. |[#2165](https://github.com/microsoft/fhir-server/pull/2165)|

|GitHub problemas cerrados |Información relacionada |
| ----------------------------------- | ---------------: |
|No se puede crear el parámetro de búsqueda personalizado para el dispositivo médico CarePlan. |[#2146](https://github.com/microsoft/fhir-server/issues/2146) |
|Mensaje de error poco claro para la creación condicional sin identificador. | [#2168](https://github.com/microsoft/fhir-server/issues/2168)|

### <a name="iot-connector-for-fhir-preview"></a>Conector de IoT para FHIR (versión preliminar)

|Correcciones &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|Información relacionada |
| ----------------------------------- | ---------------: |
|Se ha corregido el vínculo roto.| Vínculo actualizado a la documentación de Azure del conector de IoT en Azure API for FHIR portal. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre las características y correcciones de errores en las API de Azure Healthcare (servicio FHIR, servicio DICOM y conector de IoT), consulte

>[!div class="nextstepaction"]
>[Notas de la versión: API de Azure Healthcare](../release-notes.md)
