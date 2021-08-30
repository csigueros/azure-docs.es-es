---
title: Proyectos de GitHub relacionados para Azure Healthcare APIs
description: Enumeración de todos los repositorios de código abierto (GitHub)
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: be855812af2e7721355c5cb7cfce59156ee5c346
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780147"
---
# <a name="github-projects"></a>Proyectos de GitHub

Tenemos muchos proyectos de código abierto en GitHub que proporcionan el código fuente y las instrucciones para implementar servicios para varios usos. Siempre puede visitar nuestros repositorios de GitHub para aprender y experimentar con nuestras características y productos. 

## <a name="fhir-server"></a>Servidor FHIR
* [microsoft/fhir-server](https://github.com/microsoft/fhir-server/): servidor FHIR de código abierto, que es la base del servicio FHIR.
* Para ver las versiones más recientes, consulte las [notas de la versión](https://github.com/microsoft/fhir-server/releases).
* [microsoft/fhir-server-samples](https://github.com/microsoft/fhir-server-samples): un entorno de ejemplo.

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
* Publicado en Visual Studio Marketplace
* Se usa para crear plantillas de Liquid que se emplearán en el convertidor de FHIR.

## <a name="iot-connector"></a>Conector de IoT

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integración con IoT Hub e IoT Central
* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): integración con IoT Hub o IoT Central para FHIR con normalización de datos y conversión de FHIR de los datos normalizados.
* Normalización: la información de datos del dispositivo se extrae en un formato común para su posterior procesamiento.
* Conversión de FHIR: los datos normalizados y agrupados se asignan a FHIR. Las observaciones se crean o actualizan según las plantillas configuradas y se vinculan al dispositivo y al paciente.
* [Herramientas para ayudar a crear el mapa de conversación](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): visualice la configuración de asignación para normalizar los datos de entrada del dispositivo y transformarlos en los recursos de FHIR. Los desarrolladores pueden usar esta herramienta para editar y probar las asignaciones, la asignación de dispositivos y la asignación de FHIR, y exportarlas para cargarlas en el conector de IoT de Azure Portal.

#### <a name="healthkit-and-fhir-integration"></a>Integración de HealthKit y FHIR
* [microsoft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): biblioteca de Swift que automatiza la exportación de datos de Apple HealthKit a un servidor FHIR.

 