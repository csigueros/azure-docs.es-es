---
title: 'Proyectos GitHub relacionados para el conector de IoT: API de Azure Healthcare'
description: El conector de IoT tiene una sólida biblioteca de código abierto (GitHub) para ingerir mensajes de dispositivo desde dispositivos portátiles populares.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/23/2021
ms.author: jasteppe
ms.openlocfilehash: c7b80efbb032315bfca36511cf07e1f5a1f3b8a4
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061454"
---
# <a name="open-source-projects"></a>Proyectos de código abierto

Consulte nuestros proyectos de código abierto en GitHub que proporcionan código fuente e instrucciones para implementar servicios para varios usos con el conector de IoT. 

## <a name="iot-connector-github-projects"></a>Proyectos de conector GitHub IoT

### <a name="fhir-integration"></a>Integración de FHIR

* [microsoft/iomt-fhir:](https://github.com/microsoft/iomt-fhir)versión de código abierto del servicio administrado del conector ioT de las API de Azure Healthcare. Se puede usar con cualquier servicio Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;) que admita [FHIR R4&#174;](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491)

### <a name="device-and-fhir-destination-mappings"></a>Asignaciones de destino de dispositivos y FHIR

* [Asignador de datos del conector ioMT:](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)herramienta para editar, probar y solucionar problemas de las asignaciones de destino del dispositivo del conector ioT y FHIR. Exporte las asignaciones para cargar en el conector de IoT Azure Portal use con la versión de código abierto.

### <a name="wearables-integration"></a>Integración de wearables

Fitbit

* [microsoft/fitbit-on-fhir:](https://github.com/microsoft/FitbitOnFHIR)lleve fitbit&#174; datos a un servicio FHIR.

HealthKit

* [microsoft/healthkit-on-fhir:](https://github.com/microsoft/healthkit-on-fhir)lleve los datos&#174; HealthKit de Apple&#174; a un servicio FHIR.

* [microsoft/healthkit-to-fhir:](https://github.com/microsoft/healthkit-to-fhir)proporciona una manera sencilla de crear recursos de FHIR a partir de HKObjects

Google Fit en FHIR

* [microsoft/googlefit-on-fhir:](https://github.com/microsoft/googlefit-on-fhir)lleve Google Fit&#174; datos a un servicio FHIR.

Estado Data Sync

* [microsoft/health-data-sync:](https://github.com/microsoft/health-data-sync)una biblioteca de&#174; Swift que simplifica y automatiza la exportación de datos de HealthKit a un almacén externo.

## <a name="next-steps"></a>Pasos siguientes
Aprenda a implementar el conector de IoT en el Azure Portal

>[!div class="nextstepaction"]
>[Implementación del servicio administrado del conector de IoT](deploy-iot-connector-in-azure.md)

(FHIR&#174;) es una marca registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7.
