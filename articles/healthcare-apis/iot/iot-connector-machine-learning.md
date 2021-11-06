---
title: 'Conector de IoT y Azure Machine Learning Service: API de Azure Healthcare'
description: En este artículo, aprenderá a usar el conector de IoT y el servicio Azure Machine Learning.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 8150734243fda78805b5ef4cbf3ab318222b21cb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894859"
---
# <a name="iot-connector-and-azure-machine-learning-service"></a>Conector de IoT y Azure Machine Learning Service

En este artículo, exploraremos el uso del conector de IoT y Azure Machine Learning Service.

## <a name="iot-connector-and-azure-machine-learning-service-reference-architecture"></a>Arquitectura de referencia del conector de IoT Azure Machine Learning Service

El conector de IoT permite que los dispositivos IoT integren sin problemas Recursos Rápidos de Interoperabilidad en Salud servicios de&#174; (FHIR). Esta arquitectura de referencia está diseñada para acelerar la adopción de proyectos de Internet de las cosas médicas (IoMT). Esta solución usa Azure Databricks para el Machine Learning (ML). Sin embargo, Azure ML Services con Kubernetes o una solución de ML asociado podría caber en el entorno Machine Learning puntuación.

Los cuatro colores de línea muestran las distintas partes del recorrido de datos.

- **Azul** = Datos de IoT para el servicio FHIR.
- **Verde** = ruta de acceso de datos para puntuar datos de IoT
- **Rojo** = ruta de acceso de acceso de datos para informar a los médicos del riesgo de los pacientes. El objetivo de la ruta de acceso activa es estar lo más cerca posible de en tiempo real.
- **Naranja** = Ruta de acceso templada para los datos. Seguir respaldando a los médicos en la atención a los pacientes. Las solicitudes de datos se suelen desencadenar manualmente o según una programación de actualización.

:::image type="content" source="media/iot-concepts/iot-connector-machine-learning.png" alt-text="Captura de pantalla del conector de IoT y la arquitectura Machine Learning Service." lightbox="media/iot-concepts/iot-connector-machine-learning.png":::

**Ingesta de datos: pasos del 1 al 5**

1. Datos del dispositivo IoT o de la puerta de enlace de dispositivos enviados a Azure IoT Hub/Azure IoT Edge.
2. Datos de Azure IoT Edge enviados a Azure IoT Hub.
3. Copia de datos de dispositivos IoT sin procesar enviados a un entorno de almacenamiento seguro para la administración de dispositivos.
4. La carga de IOMT de PHI se mueve Azure IoT Hub al conector de IoT. Varios servicios de Azure se representan mediante un icono de conector de IoT.
5. Tres partes al número 5: a. El conector de IoT solicita el recurso patient del servicio FHIR. b. El servicio FHIR devuelve el recurso patient al conector de IoT. c. Observación de pacientes de IoT es un registro en el servicio FHIR.

**Machine Learning y ruta de datos de IA: pasos del 6 al 11**

6. Flujo de datos no desagrupados normalizado enviado a La función de Azure (ML entrada).
7. Azure Function (ML entrada) solicita al recurso Patient que se combine con la carga de IoMT.
8. La carga de IoMT con PHI se envía al centro de eventos para su distribución Machine Learning proceso y almacenamiento.
9. La carga de IOMT de PHI se envía a Azure Data Lake Storage Gen 2 para puntuar la observación durante períodos de tiempo más largos.
10. La carga de IOMT de PHI se envía a Azure Databricks para ventanas, ajuste de datos y puntuación de datos.
11. El Azure Databricks solicita más datos de pacientes de Data Lake según sea necesario. a. Azure Databricks también envía una copia de los datos puntuados al lago de datos.

**Coordinación de notificaciones y atención: pasos 12 a 18**

**Ruta de acceso de acceso de acceso**

12. Azure Databricks envía una carga a una función de Azure (ML salida).
13. Recurso RiskAssessment o Flag enviado al servicio FHIR. a. Para cada ventana de observación, se envía un recurso RiskAssessment al servicio FHIR. b. Para las ventanas de observación en las que la evaluación de riesgos está fuera del intervalo aceptable, también se debe enviar un recurso Flag al servicio FHIR.
14. Datos puntuados enviados al repositorio de datos para el enrutamiento al equipo de atención adecuado. Azure SQL Server es el repositorio de datos que se usa en este diseño debido a su interacción nativa con Power BI.
15. Power BI El panel se actualiza con la salida de evaluación de riesgos en menos de 15 minutos.

**Ruta de acceso activa**

16. Power BI actualiza el panel en la programación de actualización de datos. Normalmente, más de 15 minutos entre actualizaciones.
17. Rellene la aplicación Care Team con los datos actuales.
18. Coordinación de la atención a través Microsoft Teams para el sector sanitario aplicación de pacientes.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido sobre el conector de IoT y la Machine Learning servicio. Para obtener información general sobre el conector de IoT, consulte

>[!div class="nextstepaction"]
>[Introducción al conector de IoT](iot-connector-overview.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso de HL7.
