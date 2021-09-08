---
title: Proyectos de GitHub relacionados para Azure API for FHIR
description: Se enumeran todos los repositorios de código abierto (GitHub) para Azure API for FHIR.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780856"
---
# <a name="related-github-projects"></a>Proyectos de GitHub relacionados

Tenemos muchos proyectos de código abierto en GitHub que proporcionan el código fuente y las instrucciones para implementar servicios con distintos fines. Puede visitar en cualquier momento nuestros repositorios de GitHub para conocer nuestras características y productos y experimentar con ellos. 

## <a name="fhir-server"></a>Servidor FHIR
* [microsoft/fhir-server](https://github.com/microsoft/fhir-server/): servidor FHIR de código abierto, que es la base de Azure API for FHIR.
* Puede consultar las versiones más recientes en [Notas de la versión](https://github.com/microsoft/fhir-server/releases).
* [microsoft/fhir-server-samples](https://github.com/microsoft/fhir-server-samples): entorno de ejemplo.

## <a name="data-conversion--anonymization"></a>Conversión y anonimización de datos

#### <a name="fhir-converter"></a>Convertidor de FHIR
* [microsoft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter): utilidad de conversión para traducir formatos de datos heredados a FHIR.
* Se integra con Azure API for FHIR y con el servidor FHIR para Azure en forma de operación $convert-data.
* Mejoras continuas en el software de código abierto e integración continua con los servidores FHIR.
 
#### <a name="fhir-converter---vs-code-extension"></a>Convertidor de FHIR: extensión de VS Code
* [microsoft/FHIR-Tools-for-Anonymization](https://github.com/microsoft/FHIR-Tools-for-Anonymization): conjunto de herramientas para ayudar con la anonimización de datos (en formato FHIR).
* Se integra con Azure API for FHIR y con el servidor FHIR para Azure en forma de "exportación sin identificar".

#### <a name="fhir-tools-for-anonymization"></a>Herramientas de anonimización de FHIR
* [microsoft/vscode-azurehealthcareapis-tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): extensión de VS Code que contiene una colección de herramientas para trabajar con Azure Healthcare APIs.
* Se publica en Visual Studio Marketplace.
* Se usa para crear plantillas de Liquid que se emplearán en el convertidor de FHIR.

## <a name="iot-connector"></a>Conector de IoT

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integración con IoT Hub e IoT Central
* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): integración con IoT Hub o IoT Central para FHIR con normalización de datos y conversión de FHIR de los datos normalizados.
* Normalización: la información de datos del dispositivo se extrae en un formato común para su posterior procesamiento.
* Conversión de FHIR: los datos normalizados y agrupados se asignan a FHIR. Las observaciones se crean o actualizan según las plantillas configuradas y se vinculan al dispositivo y al paciente.
* [Herramientas para ayudar a crear el mapa de conversación](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): visualice la configuración de asignación para normalizar los datos de entrada del dispositivo y transformarlos en los recursos de FHIR. Los desarrolladores pueden usar esta herramienta para editar y probar las asignaciones, la asignación de dispositivos y la asignación de FHIR, y exportarlas para cargarlas en el conector de IoT de Azure Portal.

#### <a name="healthkit-and-fhir-integration"></a>Integración de HealthKit y FHIR
* [microsoft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): biblioteca de Swift que automatiza la exportación de datos de Apple HealthKit a un servidor FHIR.

 