---
title: 'Tutorial: Supervisión continua de pacientes de Azure IoT | Microsoft Docs'
description: En este tutorial se muestra cómo implementar y usar la plantilla de aplicación de supervisión continua de pacientes para IoT Central.
author: philmea
ms.author: philmea
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: dc1cf6a9a250b64b84cacbcf300183b913144b45
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122180171"
---
# <a name="tutorial-deploy-and-walkthrough-the-continuous-patient-monitoring-app-template"></a>Tutorial: Implementación y tutorial de la plantilla de aplicación de supervisión continua de pacientes

:::image type="content" source="media/cpm-architecture.png" alt-text="Arquitectura de la supervisión de pacientes continua":::

## <a name="bluetooth-low-energy-ble-medical-devices"></a>Dispositivos médicos de Bluetooth de bajo consumo

Muchos dispositivos ponibles médicos de los que se usan en las soluciones de IoT para la atención sanitaria son dispositivos Bluetooth de bajo consumo. Estos dispositivos no se pueden comunicar directamente con la nube y deben usar una puerta de enlace para intercambiar datos con la solución en la nube. Esta arquitectura usa una aplicación de teléfono móvil como puerta de enlace.

## <a name="mobile-phone-gateway"></a>Puerta de enlace de teléfono móvil

La función principal de la aplicación de teléfono móvil es recopilar datos de Bluetooth de bajo consumo de dispositivos médicos y comunicarlos a Azure IoT Central. La aplicación también guía a los pacientes a través de la configuración del dispositivo y les permite ver su información sanitaria personal. Otras soluciones podrían utilizar una puerta de enlace de tableta o una puerta de enlace estática de una habitación de hospital. Hay una aplicación móvil de código abierto de ejemplo disponible para Android e iOS que se puede usar como punto de partida para el desarrollo de la aplicación. Para más información, consulte la [aplicación móvil de supervisión continua de pacientes con IoT Central](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

## <a name="export-to-azure-api-for-fhirreg"></a>Exportar a Azure API for FHIR&reg;

Azure IoT Central es compatible con HIPAA y está certificado por HITRUST&reg;. También puede enviar datos de estado de pacientes a otros servicios mediante [Azure API for FHIR](../../healthcare-apis/fhir/overview.md). Azure API for FHIR es una API basada en estándares para datos médicos clínicos. El [conector de IoT de Azure para FHIR](../../healthcare-apis/fhir/iot-fhir-portal-quickstart.md) le permite usar Azure API for FHIR como destino continuo de exportación de datos de IoT Central.

## <a name="machine-learning"></a>Machine Learning

Use los modelos de aprendizaje automático con los datos de FHIR para generar información y permitir la toma de decisiones por parte del equipo de atención sanitaria. Para más información, consulte la [documentación sobre Azure Machine Learning](../../machine-learning/index.yml).

## <a name="provider-dashboard"></a>Panel del proveedor

Use los datos de Azure API for FHIR para crear un panel de información de pacientes o integrarlo directamente en un registro médico electrónico que usen los equipos de atención sanitaria. Estos equipos pueden usar el panel para ayudar a los pacientes e identificar signos de advertencia temprana de deterioro. Para más información, consulte el tutorial [Creación de un panel de proveedor de Power BI](tutorial-health-data-triage.md).

En este tutorial, aprenderá a:

- Elija una plantilla de aplicación.
- Recorrido por la plantilla de aplicación

## <a name="prerequisites"></a>Requisitos previos

- No se necesitan requisitos previos específicos para implementar esta aplicación.
- Puede usar el plan gratuito o una suscripción de Azure.

## <a name="create-continuous-patient-monitoring-application"></a>Creación de una aplicación de supervisión continua de pacientes

1. Navegue al sitio de [Compilación Azure IoT Central](https://aka.ms/iotcentral). Después, inicie sesión con una cuenta Microsoft personal, profesional o educativa. Seleccione **Compilar** en la barra de navegación de la izquierda y la pestaña **Sanidad**: :::image type="content" source="media/app-manager-health.png" alt-text="Plantilla de aplicación":::

1. Seleccione **Creación de una aplicación** en **Supervisión continua de pacientes**.

Para más información, consulte [Creación de una aplicación de Azure IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Recorrido por la aplicación.

Las secciones siguientes le guiarán por las características clave de la aplicación:

### <a name="dashboards"></a>Paneles

Después de implementar la plantilla de la aplicación, irá en primer lugar al **panel de supervisión de pacientes in situ de Lamna**. Lamna Healthcare es un sistema hospitalario ficticio que contiene dos hospitales: de Woodgrove y de Burkville. En el panel del operador del Hospital de Woodgrove, puede:

* Consultar la telemetría de los dispositivos y propiedades como el **nivel de batería** del dispositivo o su estado de **conectividad**.

* Vea el **plan de las plantas** y la ubicación para el dispositivo inteligente de revisión de constantes vitales (Smart Vitals Patch).

* **Vuelva a aprovisionarlo** para un nuevo paciente.

* Vea un ejemplo del **panel de proveedor** que un equipo de atención hospitalaria podría ver para realizar el seguimiento de sus pacientes.

* Cambie el **estado del paciente** del dispositivo para indicar si se está usando para un escenario remoto o con pacientes in situ.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Estado de paciente in situ":::

También puede seleccionar **Go to remote patient dashboard** (Ir al panel de pacientes remotos) para ver el panel del operador del Hospital de Burkville. Este panel contiene un conjunto similar de acciones, telemetría e información. También puede ver varios dispositivos en uso y elegir **actualizar el firmware** de cada uno.

:::image type="content" source="media/lamna-remote.png" alt-text="Panel del operador remoto":::

### <a name="device-templates"></a>Plantillas de dispositivo

Si selecciona **Device templates**, (Plantillas de dispositivo) verá los dos tipos de dispositivo en la plantilla:

- **Smart Vitals Patch** (Revisión inteligente de constantes vitales): este dispositivo representa un parche que mide varias constantes vitales. Se utiliza para supervisar pacientes dentro y fuera del hospital. Si selecciona la plantilla, verá que, además de enviar datos del dispositivo, como el nivel de batería y la temperatura del dispositivo, el parche también envía datos del estado de los pacientes, como la frecuencia respiratoria y la presión arterial.

- **Smart Knee Brace** (Rodillera inteligente): este dispositivo representa una rodillera que los pacientes utilizan al recuperarse de una cirugía de reemplazo de una rodilla. Si selecciona esta plantilla, verá funcionalidades como los datos del dispositivo, el intervalo de movimiento y la aceleración.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Plantilla de parche inteligente":::

### <a name="device-groups"></a>Grupos de dispositivos

Utilice grupos de dispositivos para agrupar lógicamente un conjunto de dispositivos para así realizar consultas u operaciones masivas sobre ellos.

Si selecciona la pestaña de grupos de dispositivos, verá un grupo de dispositivos predeterminado para cada plantilla de dispositivo de la aplicación. También se han creado dos grupos de dispositivos de ejemplo adicionales, llamados **Provision devices** (Aprovisionamiento de dispositivos) y **Devices with outdated firmware** (Dispositivos con firmware sin actualizar). Puede usar estos grupos de dispositivos de ejemplo como entradas para ejecutar algunos [Trabajos](#jobs) de la aplicación.

### <a name="rules"></a>Reglas

Si selecciona **Rules** (Reglas), verá las tres reglas en la plantilla:

- **Brace temperature high**(Temperatura alta de rodillera): esta regla se desencadena cuando la temperatura del dispositivo de la rodillera inteligente es mayor de 95&deg;F en un período de 5 minutos. Utilice esta regla para avisar al equipo de asistencia sanitaria y al paciente, y enfriar el dispositivo de forma remota.

- **Fall detected** (Caída detectada): esta regla se desencadena si se detecta que un paciente ha tenido una caída. Use esta regla para configurar una acción con el fin de implementar un equipo operativo para ayudar al paciente que se ha caído.

- **Patch battery low** (Revisión de poca batería): esta regla se desencadena cuando el nivel de batería del dispositivo desciende por debajo del 10 %. Utilice esta regla para desencadenar una notificación al paciente para que cargue su dispositivo.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Reglas":::

### <a name="jobs"></a>Trabajos

Los trabajos le permiten ejecutar operaciones masivas en un conjunto de dispositivos mediante el uso de [grupos de dispositivos](#device-groups) como entrada. La plantilla de aplicación tiene dos trabajos de ejemplo que un operador puede ejecutar:

* **Update knee brace firmware** (Actualización del firmware de la rodillera): este trabajo busca dispositivos en el grupo de dispositivos **Devices with outdated firmware** (Dispositivos con firmware sin actualizar) y ejecuta un comando para actualizar dichos dispositivos a la versión de firmware más reciente. En este trabajo de ejemplo se supone que los dispositivos pueden controlar el comando **update** y recuperar los archivos de firmware desde la nube.  

* **Re-provision devices** (Reaprovisionamiento de dispositivos): Tiene un conjunto de dispositivos que se han devuelto recientemente al hospital. Este trabajo busca dispositivos en el grupo de dispositivos **Provision devices** (Aprovisionamiento de dispositivos) y ejecuta un comando para volver a aprovisionarlos para el siguiente conjunto de pacientes.

### <a name="devices"></a>Dispositivos

Seleccione la pestaña **Devices**  (Dispositivos) y, a continuación, seleccione una instancia del dispositivo **Smart Knee Brace** (Rodillera inteligente). Hay tres vistas para explorar la información sobre el dispositivo específico que ha seleccionado. Estas vistas se crean y publican al crear la plantilla de dispositivo para el dispositivo. Por lo tanto, estas vistas serán coherentes en todos los dispositivos que se conecten o simulen.

La vista **Dashboard** (Panel) ofrece información general sobre la telemetría y las propiedades del dispositivo orientada a los operadores.

La pestaña **Properties** (Propiedades) le permite editar las propiedades de la nube y leer y escribir las propiedades del dispositivo.

La pestaña **Commands** (Comandos) le permite ejecutar comandos en el dispositivo.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Panel de la rodillera inteligente":::


## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine su plantilla. Para ello, vaya a **Administración > Configuración de la aplicación** y haga clic en **Eliminar**.

:::image type="content" source="media/admin-delete.png" alt-text="Recursos ordenados":::

## <a name="next-steps"></a>Pasos siguientes

Vaya al siguiente artículo para aprender a crear un panel de proveedor que se conecte a la aplicación IoT Central.

> [!div class="nextstepaction"]
> [Creación de un panel de proveedor](tutorial-health-data-triage.md)