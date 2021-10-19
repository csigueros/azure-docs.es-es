---
title: Proyectos de GitHub relacionados para Azure Healthcare APIs
description: Enumeración de todos los repositorios de código abierto (GitHub)
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/18/2021
ms.author: ginle
ms.openlocfilehash: 63e3ff442ab45801d079e4f61b8dae98a9b010f4
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166379"
---
# <a name="github-projects"></a>Proyectos de GitHub

Tenemos muchos proyectos de código abierto en GitHub que proporcionan el código fuente y las instrucciones para implementar servicios para varios usos. Puede visitar en cualquier momento nuestros repositorios de GitHub para conocer nuestras características y productos y experimentar con ellos. 

## <a name="healthcare-apis-samples"></a>Ejemplos de API de atención sanitaria

* Este repositorio contiene [ejemplos de](https://github.com/microsoft/healthcare-apis-samples)LAS API de atención sanitaria, incluidos Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;), DICOM, conector de IoT y servicios relacionados con los datos.

## <a name="fhir-server"></a>Servidor FHIR

* [microsoft/fhir-server](https://github.com/microsoft/fhir-server/): servidor FHIR de código abierto, que es la base del servicio FHIR.
* Para obtener información sobre las versiones más recientes, vea [Notas de la versión.](https://github.com/microsoft/fhir-server/releases)
* [microsoft/fhir-server-samples](https://github.com/microsoft/fhir-server-samples): entorno de ejemplo.

## <a name="data-conversion--anonymization"></a>Conversión y anonimización de datos

#### <a name="fhir-converter"></a>Convertidor de FHIR

* [microsoft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter): utilidad de conversión para traducir formatos de datos heredados a FHIR.
* Se integra con el servicio FHIR y el servidor FHIR para Azure mediante la operación de datos $convert.
* Mejoras continuas en el software de código abierto e integración continua con los servidores FHIR.
 
#### <a name="fhir-converter---vs-code-extension"></a>Convertidor de FHIR: extensión de VS Code

* [microsoft/FHIR-Tools-for-Anonymization](https://github.com/microsoft/FHIR-Tools-for-Anonymization): conjunto de herramientas para ayudar con la anonimización de datos (en formato FHIR).
* Se integra con el servicio FHIR, así como con el servidor FHIR para Azure mediante una "exportación no identificada".

#### <a name="fhir-tools-for-anonymization"></a>Herramientas de FHIR para anonimización

* [microsoft/vscode-azurehealthcareapis-tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): extensión de VS Code que contiene una colección de herramientas para trabajar con Azure Healthcare APIs.
* Se publica en Visual Studio Marketplace.
* Se usa para crear plantillas de Liquid que se emplearán en el convertidor de FHIR.

## <a name="iot-connector"></a>Conector de IoT

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integración con IoT Hub e IoT Central

* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): integración con IoT Hub o IoT Central para FHIR con normalización de datos y conversión de FHIR de los datos normalizados.
* Normalización: la información de datos del dispositivo se extrae en un formato común para su posterior procesamiento.
* Conversión de FHIR: los datos normalizados y agrupados se asignan a FHIR. Las observaciones se crean o actualizan según las plantillas configuradas y se vinculan al dispositivo y al paciente.
* [Herramientas para ayudar a crear el mapa de conversación](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): visualice la configuración de asignación para normalizar los datos de entrada del dispositivo y transformarlos en los recursos de FHIR. Los desarrolladores pueden usar esta herramienta para editar y probar las asignaciones de destino del dispositivo y FHIR y exportarlas para cargarlas en el conector de IoT en el Azure Portal.

#### <a name="healthkit-and-fhir-integration"></a>Integración de HealthKit y FHIR

* [microsoft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): biblioteca de Swift que automatiza la exportación de datos de Apple HealthKit a un servidor FHIR.

>[!div class="nextstepaction"]
>[Introducción a las API de Azure Healthcare](healthcare-apis-overview.md)

(FHIR&#174;) es una marca registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7. 