---
title: Versiones mensuales de las API de Azure Healthcare
description: En este artículo se proporcionan detalles sobre las características y mejoras mensuales de las API de Azure Healthcare.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/01/2021
ms.author: cavoeg
ms.openlocfilehash: 40afc404e3121ba4b35da895d804c475f9b28d95
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719363"
---
# <a name="release-notes-azure-healthcare-apis"></a>Notas de la versión: API de Azure Healthcare

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general. 

Las API de Azure Healthcare son un conjunto de servicios de API administrados basados en estándares y marcos abiertos para el sector sanitario. Le permiten crear soluciones sanitarias escalables y seguras al reunir conjuntos de datos de información sanitaria protegida (PHI) y conectarlos de un extremo a otro con herramientas de aprendizaje automático, análisis e inteligencia artificial. En este documento se proporcionan detalles sobre las características y mejoras realizadas en las API de Azure Healthcare, incluidos los diferentes tipos de servicio (servicio FHIR, servicio DICOM y conector de IoT) que funcionan sin problemas entre sí.

## <a name="september-2021"></a>Septiembre de 2021

### <a name="fhir-service"></a>Servicio FHIR

#### <a name="feature-enhancements"></a>**Mejoras de las características**

|Mejoras | Descripción |
|:------------------- | -----------:|

|Se ha agregado compatibilidad con la revisión condicional. | [Revisión condicional](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
|:------------------- | -----------:|
|Revisión condicional | [#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Agregar evento de auditoría de revisión condicional | [#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|Permitir revisión JSON en agrupaciones | [Revisión json en agrupaciones](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
|:------------------- | -----------:|
|Permite agrupaciones de historial de búsqueda con solicitudes de revisión |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Habilitación de la revisión JSON en agrupaciones mediante recursos binarios |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |
|Se han agregado nuevos subtipos [OperationName de](./././azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details) eventos de auditoría| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

| Ejecución de un trabajo de reindexación | [Mejoras de reindexación](./././fhir/how-to-run-a-reindex.md)|
|:------------------- | -----------:|
|Se han [agregado límites para los parámetros de reindexación.](./././azure-api-for-fhir/how-to-run-a-reindex.md#performance-considerations)|[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Actualización del mensaje de error para volver a indexar los límites de parámetros|[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Agrega la comprobación de recuento de reindexación final. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|

#### <a name="bug-fixes"></a>**Correcciones de errores**

|Errores de revisión resueltos | Descripción |
|:------------------- | -----------:|

| Captura más amplia de excepciones durante la aplicación de la revisión | [#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|:------------------- | -----------:|
|Corrección del historial con PATCH en PATCH3 |[#2177](https://github.com/microsoft/fhir-server/pull/2177) |

|Errores de búsqueda personalizada |Descripción |
|:------------------- | -----------:|
|Soluciona el error de eliminación con parámetros de búsqueda personalizada |[#2133](https://github.com/microsoft/fhir-server/pull/2133) |
|Se ha agregado lógica de reintento al eliminar el parámetro Search. | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Establecer el número máximo de elementos en las opciones de búsqueda en SearchParameterDefinitionManager |[#2141](https://github.com/microsoft/fhir-server/pull/2141) |
|Mejor excepción si hay una expresión no válida en un parámetro de búsqueda |[#2157](https://github.com/microsoft/fhir-server/pull/2157) |

|Se ha SQL la reindexación por lotes si se produce un error en un recurso |Descripción |
|:------------------- | -----------:|
|Actualizaciones de SQL lógica de reintento de reindexación por lotes |[#2118](https://github.com/microsoft/fhir-server/pull/2118) |

|GitHub problemas cerrados |Descripción |
|:------------------- | -----------:|
|Mensaje de error poco claro para la creación condicional sin identificador |[#2168](https://github.com/microsoft/fhir-server/issues/2168) |

### <a name="dicom-service"></a>**Servicio DICOM**

|Corrección de errores | Descripción |
|:------------------- | -----------:|

|Corrección implementada para resolver problemas de ordenación de paginación de QIDO |  [#989](https://github.com/microsoft/dicom-server/pull/989) |
|:------------------- | -----------:|

### <a name="iot-connector"></a>**Conector de IoT**

|Corrección de errores | Descripción |
|:------------------- | -----------:|
| Mejoras normalizadas del conector de IoT con cálculos para admitir y mejorar la normalización de datos de mantenimiento. | Vea: [Uso de asignaciones de dispositivos](./../healthcare-apis/iot/how-to-use-device-mappings.md) y [funciones calculadas](./../healthcare-apis/iot/how-to-use-calculated-functions-mappings.md)  |

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre las características y correcciones de errores Azure API for FHIR, consulte

>[!div class="nextstepaction"]
>[Notas de la versión: Azure API for FHIR](./azure-api-for-fhir/release-notes.md)
