---
title: 'Proyectos GitHub relacionados para el conector de IoT: API de Azure Healthcare'
description: Enumeración de todos los repositorios de código abierto (GitHub) para el conector de IoT
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/18/2021
ms.author: jasteppe
ms.openlocfilehash: 0092482af2f9f4f6f9a090a00cc387ae901e5b68
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130187784"
---
# <a name="open-source-projects"></a>Proyectos de código abierto

Consulte nuestros proyectos de código abierto en GitHub que proporcionan código fuente e instrucciones para implementar servicios para varios usos con el conector de IoT. 

## <a name="iot-connector-github-projects"></a>Proyectos de conectores GitHub IoT

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integración con IoT Hub e IoT Central

* [microsoft/iomt-fhir:](https://github.com/microsoft/iomt-fhir)integración con IoT Hub o IoT Central a Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;) con normalización de datos y conversión de FHIR de los datos normalizados
* Normalización: la información de datos del dispositivo se extrae en un formato común para su posterior procesamiento.
* Conversión de FHIR: los datos normalizados y agrupados se asignan a FHIR. Las observaciones se crean o actualizan según las plantillas configuradas y se vinculan al dispositivo y al paciente.

#### <a name="device-and-fhir-destination-mappings-authoring-and-troubleshooting"></a>Creación y solución de problemas de asignaciones de destino de dispositivos y FHIR

* [Herramientas para ayudar a crear asignaciones](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)de destino de dispositivos y FHIR: visualice la configuración de asignación para normalizar los datos de entrada del dispositivo y transformarlos en recursos de FHIR. Los desarrolladores pueden usar esta herramienta para editar y probar asignaciones de dispositivos y asignaciones de destino de FHIR. Exporte para cargarlos en el conector de IoT en el Azure Portal.

#### <a name="healthkit-and-fhir-integration"></a>Integración de HealthKit y FHIR

* [microsoft/healthkit-on-fhir:](https://github.com/microsoft/healthkit-on-fhir)una biblioteca swift que automatiza la exportación de datos de Apple HealthKit a un servicio FHIR.

## <a name="next-steps"></a>Pasos siguientes
Aprenda a implementar el conector de IoT en el Azure Portal

>[!div class="nextstepaction"]
>[Implementación del servicio administrado del conector de IoT](deploy-iot-connector-in-azure.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso hl7.