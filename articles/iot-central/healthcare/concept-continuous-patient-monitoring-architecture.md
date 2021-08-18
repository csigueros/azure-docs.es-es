---
title: Arquitectura de supervisión continua de pacientes en Azure IoT Central | Microsoft Docs
description: 'Tutorial: Información sobre una arquitectura de una solución de supervisión continua de pacientes.'
author: dominicbetts
ms.author: dobett
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 196aec4661fc9bc60a62a089276c4c7a9fd300af
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860895"
---
# <a name="continuous-patient-monitoring-architecture"></a>Arquitectura de la supervisión de pacientes continua

En este artículo se describe la arquitectura de una solución creada a partir de la plantilla de la aplicación de **supervisión de pacientes continua**:

Para crear soluciones de supervisión continua de pacientes se puede utilizar la plantilla de aplicación proporcionada y se puede usar la arquitectura que se describe a continuación como guía.

:::image type="content" source="media/cpm-architecture.png" alt-text="Arquitectura de la supervisión de pacientes continua":::

## <a name="details"></a>Detalles

En esta sección se describe cada parte del diagrama de la arquitectura con más detalle:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Dispositivos médicos de Bluetooth de bajo consumo

Muchos dispositivos ponibles médicos de los que se usan en las soluciones de IoT para la atención sanitaria son dispositivos Bluetooth de bajo consumo. Estos dispositivos no se pueden comunicar directamente con la nube y deben usar una puerta de enlace para intercambiar datos con la solución en la nube. Esta arquitectura usa una aplicación de teléfono móvil como puerta de enlace.

### <a name="mobile-phone-gateway"></a>Puerta de enlace de teléfono móvil

La función principal de la aplicación de teléfono móvil es recopilar datos de Bluetooth de bajo consumo de dispositivos médicos y comunicarlos a Azure IoT Central. La aplicación también guía a los pacientes a través de la configuración del dispositivo y les permite ver su información sanitaria personal. Otras soluciones podrían utilizar una puerta de enlace de tableta o una puerta de enlace estática de una habitación de hospital. Hay una aplicación móvil de código abierto de ejemplo disponible para Android e iOS que se puede usar como punto de partida para el desarrollo de la aplicación. Para más información, consulte la [aplicación móvil de supervisión continua de pacientes con IoT Central](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Exportar a Azure API for FHIR&reg;

Azure IoT Central es compatible con HIPAA y está certificado por HITRUST&reg;. También puede enviar datos de estado de pacientes a otros servicios mediante [Azure API for FHIR](../../healthcare-apis/fhir/overview.md). Azure API for FHIR es una API basada en estándares para datos médicos clínicos. El [conector de IoT de Azure para FHIR](../../healthcare-apis/azure-api-for-fhir/iot-fhir-portal-quickstart.md) le permite usar Azure API for FHIR como destino continuo de exportación de datos de IoT Central.

### <a name="machine-learning"></a>Machine Learning

Use los modelos de aprendizaje automático con los datos de FHIR para generar información y permitir la toma de decisiones por parte del equipo de atención sanitaria. Para más información, consulte la [documentación sobre Azure Machine Learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Panel del proveedor

Use los datos de Azure API for FHIR para crear un panel de información de pacientes o integrarlo directamente en un registro médico electrónico que usen los equipos de atención sanitaria. Estos equipos pueden usar el panel para ayudar a los pacientes e identificar signos de advertencia temprana de deterioro. Para más información, consulte el tutorial [Creación de un panel de proveedor de Power BI](tutorial-health-data-triage.md).

## <a name="next-steps"></a>Pasos siguientes

El siguiente paso que se sugiere es obtener [más información sobre cómo implementar una plantilla de aplicación de supervisión continua de pacientes](tutorial-continuous-patient-monitoring.md).
