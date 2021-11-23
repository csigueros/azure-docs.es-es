---
title: Versiones mensuales de las API de Azure Healthcare
description: En este artículo se proporcionan detalles sobre las características y mejoras mensuales de las API de Azure Healthcare.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/22/2021
ms.author: cavoeg
ms.openlocfilehash: 304fdc3ceea4c26e861ba54e2dd90f98fd7bc225
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936531"
---
# <a name="release-notes-azure-healthcare-apis"></a>Notas de la versión: API de Azure Healthcare

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general. 

Las API de Azure Healthcare son un conjunto de servicios de API administrados basados en estándares y marcos abiertos para el sector sanitario. Le permiten crear soluciones sanitarias escalables y seguras al reunir conjuntos de datos de información sanitaria protegida (PHI) y conectarlos de un extremo a otro con herramientas de aprendizaje automático, análisis e inteligencia artificial. En este documento se proporcionan detalles sobre las características y mejoras realizadas en las API de Azure Healthcare, incluidos los diferentes tipos de servicio (servicio FHIR, servicio DICOM y conector de IoT) que funcionan sin problemas entre sí.

## <a name="october-2021"></a>Octubre de 2021

### <a name="azure-healthcare-apis-feature-enhancements"></a>Mejoras en las características de las API de atención sanitaria de Azure

| Mejoras &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Información relacionada           |
|------------- | -----------------------------: |
|Herramienta Generador de datos de prueba |Hemos actualizado las API de atención sanitaria GitHub ejemplos para incluir una herramienta [de](https://github.com/microsoft/healthcare-apis-samples/blob/main/docs/HowToRunPerformanceTest.md) generador de datos de prueba mediante datos de Synthea. Esta herramienta es una mejora en los proyectos de prueba públicos de código [abierto,](https://github.com/ShadowPic/PublicTestProjects)basados en Apache JMeter, que se pueden implementar en Azure AKS para pruebas de rendimiento. |

### <a name="fhir-service"></a>Servicio FHIR

#### <a name="feature-enhancements"></a>**Mejoras de las características**

|Mejoras | Información relacionada |
|------------------------ | -------------------------------: |
|Se ha agregado compatibilidad [con _sort](././../healthcare-apis/fhir/overview-of-search.md#search-result-parameters) en cadenas y dateTime. |[#2169](https://github.com/microsoft/fhir-server/pull/2169)  |

#### <a name="bug-fixes"></a>**Correcciones de errores**

|Corrección de errores | Información relacionada |
|------------------------ | -------------------------------: |
|Se ha corregido [un problema por el](././../healthcare-apis/fhir/fhir-rest-api-capabilities.md#conditional-delete) que la eliminación condicional podía dar lugar a un bucle infinito. | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |
|Se ha resuelto el error 500 posiblemente causado por un cuerpo de transacción con formato mal en una agrupación POST. Hemos agregado una comprobación de que la dirección URL se rellena en las solicitudes de [agrupación](././..//healthcare-apis/fhir/fhir-features-supported.md#rest-api) de transacciones. | [#2255](https://github.com/microsoft/fhir-server/pull/2255) |

### <a name="dicom-service"></a>**Servicio DICOM**

|Compatibilidad agregada | Información relacionada |
|------------------------ | -------------------------------: |
|Regions | Sur de Brasil y Centro de Canadá. Para más información sobre las regiones de Azure y las zonas de disponibilidad, consulte [Servicios de Azure que admiten zonas de disponibilidad.](./../availability-zones/az-region.md) |
|Etiquetas de consulta extendida |Tipos DateTime (DT) y Time (TM) Value Representation (VR) |

|Corrección de errores | Información relacionada |
|------------------------ | -------------------------------: |
|Corrección implementada en los nombres de área de trabajo. |Se ha habilitado el servicio DICOM para trabajar con áreas de trabajo que tienen nombres que comienzan por una letra. |

## <a name="september-2021"></a>Septiembre de 2021

### <a name="fhir-service"></a>Servicio FHIR

#### <a name="feature-enhancements"></a>**Mejoras de las características**

|Mejoras | Información relacionada |
|:------------------- | -------------------------------: |

|Se ha agregado compatibilidad con la revisión condicional. | [Revisión condicional](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
|:------------------- | -------------------------------:|
|Revisión condicional | [#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Se ha agregado un evento de auditoría de revisión condicional. | [#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|Permitir revisión JSON en agrupaciones | [Revisión json en agrupaciones](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
|:------------------- | -------------------------------:|
|Permite agrupaciones de historial de búsqueda con solicitudes de revisión. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Se ha habilitado la revisión JSON en agrupaciones que usan recursos binarios. |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |
|Se han agregado nuevos subtipos [OperationName de](./././azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details) eventos de auditoría| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

| Ejecución de un trabajo de reindexación | [Mejoras de reindexación](./././fhir/how-to-run-a-reindex.md)|
|:------------------- | -------------------------------:|
|Se han [agregado límites para los parámetros de reindexación.](./././azure-api-for-fhir/how-to-run-a-reindex.md#performance-considerations) |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Mensaje de error actualizado para volver a indexar los límites de parámetros. |[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Se ha agregado la comprobación final del recuento de reindexación. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|

#### <a name="bug-fixes"></a>**Correcciones de errores**

|Errores de revisión resueltos | Información relacionada |
|:------------------- | --------------------------------: |

| Captura más amplia de excepciones durante la aplicación de la revisión | [#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|:------------------- | -----------: |
|Corrección del historial con PATCH en PATCH3 |[#2177](https://github.com/microsoft/fhir-server/pull/2177) |

|Errores de búsqueda personalizada | Información relacionada |
|:------------------- | -------------------------------: |
|Soluciona el error de eliminación con parámetros de búsqueda personalizada |[#2133](https://github.com/microsoft/fhir-server/pull/2133) |
|Se ha agregado lógica de reintento al eliminar el parámetro Search. | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Establecer el número máximo de elementos en las opciones de búsqueda en SearchParameterDefinitionManager |[#2141](https://github.com/microsoft/fhir-server/pull/2141) |
|Mejor excepción si hay una expresión no válida en un parámetro de búsqueda |[#2157](https://github.com/microsoft/fhir-server/pull/2157) |

|Se ha SQL la reindexación por lotes si se produce un error en un recurso | Información relacionada |
|:------------------- | -------------------------------: |
|Actualiza SQL lógica de reintento de reindexación por lotes |[#2118](https://github.com/microsoft/fhir-server/pull/2118) |

|GitHub problemas cerrados | Información relacionada |
|:------------------- | -------------------------------: |
|Mensaje de error poco claro para la creación condicional sin identificador |[#2168](https://github.com/microsoft/fhir-server/issues/2168) |

### <a name="dicom-service"></a>**Servicio DICOM**

|Corrección de errores | Información relacionada |
|:------------------- | -------------------------------: |

|Corrección implementada para resolver problemas de ordenación de paginación de QIDO |  [#989](https://github.com/microsoft/dicom-server/pull/989) |
|:------------------- | -------------------------------: |

### <a name="iot-connector"></a>**Conector de IoT**

|Corrección de errores | Información relacionada |
|:------------------- | -------------------------------: |
| Mejoras normalizadas del conector de IoT con cálculos para admitir y mejorar la normalización de los datos de mantenimiento. | Vea: [Uso de asignaciones de dispositivos](./../healthcare-apis/iot/how-to-use-device-mappings.md) y [funciones calculadas](./../healthcare-apis/iot/how-to-use-calculated-functions-mappings.md)  |

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre las características y correcciones de errores en Azure API for FHIR, consulte

>[!div class="nextstepaction"]
>[Notas de la versión: Azure API for FHIR](./azure-api-for-fhir/release-notes.md)
