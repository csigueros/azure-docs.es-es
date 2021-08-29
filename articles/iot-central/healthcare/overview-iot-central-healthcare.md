---
title: ¿Cuáles son las soluciones de salud de Azure IoT Central? | Microsoft Docs
description: Aprenda a crear soluciones de salud con plantillas de aplicación de Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: b7b66ca25f4813ca718d550ec2225c309f3179d7
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862473"
---
# <a name="what-are-the-iot-central-healthcare-solutions"></a>¿Cuáles son las soluciones de salud de IoT Central?

Aprenda a crear soluciones de salud con Azure IoT Central mediante plantillas de aplicación.

## <a name="what-is-continuous-patient-monitoring-template"></a>¿Qué es la plantilla de supervisión continua de pacientes?

En el espacio de salud de IoT, la supervisión continua de pacientes es uno de los principales facilitadores a la hora de reducir el riesgo de readmisiones, administrar las enfermedades crónicas de un modo más eficaz y mejorar los resultados de los pacientes. La supervisión continua de pacientes puede dividirse en dos categorías principales:

1. **Supervisión en el paciente**: Con ponibles médicos y otros dispositivos en el hospital, los equipos de atención sanitaria pueden supervisar las señales vitales de los pacientes y sus condiciones médicas sin tener que enviar a una enfermera a visitar a un paciente varias veces al día. Los equipos de atención médica pueden detectar el momento en que un paciente necesita atención crítica mediante notificaciones y priorizar su tiempo de forma eficaz.
1. **Supervisión remota de pacientes**: Al usar dispositivos ponibles y resultados notificados por los pacientes (PRO) para supervisar a los pacientes fuera del hospital, se puede reducir el riesgo de nuevo ingreso hospitalario. Se pueden recopilar datos de pacientes con enfermedades crónicas y en rehabilitación para garantizar que los pacientes siguen los planes de atención médica y que las alertas del empeoramiento de los pacientes pueden llegar a los equipos de atención antes de ser críticas.

Esta plantilla de aplicación se puede usar para crear soluciones para ambas categorías de supervisión continua de pacientes. Entre las ventajas se incluye lo siguiente:

* Conectar fácilmente distintos tipos de dispositivos médicos ponibles a una instancia de IoT Central.
* Supervisar y administrar los dispositivos para asegurarse de que están en buen estado.
* Crear reglas personalizadas para los datos de los dispositivos para desencadenar las alertas adecuadas.
* Exportar los datos de estado de los pacientes a Azure API for FHIR, un almacén de datos compatible.
* Exportar la información agregada a las aplicaciones empresariales nuevas o existentes.

>[!div class="mx-imgBorder"] 
>![CPM-dashboard](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Pasos siguientes

Para empezar a crear una solución de supervisión continua de pacientes:

* [Implementar la plantilla de la aplicación](tutorial-continuous-patient-monitoring.md)
* [Consulte una arquitectura de ejemplo](concept-continuous-patient-monitoring-architecture.md)