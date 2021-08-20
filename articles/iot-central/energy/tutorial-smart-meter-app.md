---
title: 'Tutorial: Creación de una aplicación de análisis de medidores inteligentes con IoT Central'
description: 'Tutorial: Aprenda a crear una aplicación de supervisión de medidores inteligentes mediante plantillas de aplicación de Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 42e88d322bd4d2b174d7a52e4892970caf5b1a5e
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112963246"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Tutorial: Creación y recorrido de la plantilla de aplicación de supervisión de medidores inteligentes 

Este tutorial le guía por el proceso de creación de la aplicación de supervisión de medidores inteligentes, que incluye un modelo de dispositivo de ejemplo con datos simulados. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Instrucciones sobre cómo crear la aplicación de medidores inteligentes de manera gratuita
> * Tutorial de la aplicación
> * Limpieza de recursos


Si no tiene ninguna suscripción, [cree una cuenta de evaluación gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Requisitos previos
- None
- Le recomendamos que use una suscripción a Azure para la aprueba, aunque no es obligatorio.

## <a name="create-a-smart-meter-monitoring-app"></a>Creación de una aplicación de supervisión de medidores inteligentes 

Puede crear esta aplicación en tres sencillos pasos:

1. Abra la [página principal de Azure IoT Central](https://apps.azureiotcentral.com) y haga clic en **Compilar** para crear una nueva aplicación. 
1. Seleccione la pestaña **Energía** y haga clic en **Crear aplicación** en el icono de la aplicación **Supervisión de medidores inteligentes**.

    > [!div class="mx-imgBorder"]
    > ![Compilar una aplicación](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

1. Con la opción **Crear aplicación** se abrirá el formulario **Nueva aplicación**. Rellene los detalles solicitados tal como se muestra en la ilustración siguiente:
    * **Nombre de la aplicación**: elija un nombre para su aplicación de IoT Central. 
    * **URL**: elija una dirección URL de IoT Central y la plataforma verificará su unicidad.
    * **Prueba gratuita de 7 días**: si ya tiene una suscripción a Azure, se recomienda la configuración predeterminada. Si no tiene ninguna, inicie una evaluación gratuita.
    * **Información de facturación**: la aplicación es gratuita. Los detalles del directorio, la suscripción a Azure y la región son necesarios para aprovisionar los recursos de la aplicación.
    * Haga clic en el botón **Crear** en la parte inferior de la página y la aplicación se creará en un minuto aproximadamente.

        ![Formulario de nueva aplicación](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Información de facturación de un formulario de nueva aplicación](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)

### <a name="verify-the-application-and-simulated-data"></a>Verificación de la aplicación y los datos simulados

La aplicación de medidores inteligentes recién creada es su aplicación y puede modificarla en cualquier momento. Vamos a asegurarnos de que la aplicación se haya implementado y funcione según lo esperado antes de modificarla.

Para verificar la creación de la aplicación y la simulación de datos, vaya al **Panel**. Si puede ver los iconos con algunos datos, significa que la implementación de la aplicación se realizó correctamente. La simulación de datos puede tardar entre 1 y 2 minutos en generar los datos, por lo que deberá esperar un poco. 

## <a name="application-walk-through"></a>Tutorial de la aplicación
Una vez que implemente la plantilla de la aplicación correctamente, esta incluirá un dispositivo de ejemplo de medidores inteligentes, un modelo de dispositivo y un panel. 

Adatum es una compañía energética ficticia que supervisa y administra los medidores inteligentes. En el panel de supervisión de medidores inteligentes, verá propiedades, datos y comandos de ejemplo de los medidores inteligentes. Este permite que los operadores y los equipos de soporte técnico realicen de forma proactiva las actividades siguientes antes de que se produzcan incidentes de soporte técnico: 
* Revisar la información más reciente del medidor y la [ubicación](../core/howto-use-location-data.md) de su instalación en el mapa
* Comprobar de manera proactiva la red del medidor y el estado de la conexión 
* Supervisar las lecturas de voltaje mínimas y máximas para el estado de la red 
* Revisar las tendencias de energía, alimentación y voltaje para detectar cualquier patrón anómalo 
* Realizar un seguimiento del consumo energético total para fines de planificación y facturación
* Realizar operaciones con comandos y controles, como, por ejemplo, volver a conectar el medidor y actualizar la versión del firmware. En la plantilla, los botones de comando muestran las funcionalidades posibles y no envían comandos reales. 

> [!div class="mx-imgBorder"]
> ![Panel de supervisión de medidores inteligentes](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Dispositivos
La aplicación incluye un dispositivo de ejemplo de medidores inteligentes. Para ver los detalles del dispositivo, haga clic en la pestaña **Dispositivos**.

> [!div class="mx-imgBorder"]
> ![Dispositivos de medidores inteligentes](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Haga clic en el vínculo **SM0123456789** del dispositivo de ejemplo para ver los detalles del dispositivo. Puede actualizar las propiedades de escritura del dispositivo en la página **Actualizar propiedades** y visualizar los valores actualizados en el panel.

> [!div class="mx-imgBorder"]
> ![Propiedades de los medidores inteligentes](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Plantilla de dispositivo
Haga clic en la pestaña **Plantillas de dispositivo** para ver el modelo de dispositivo de medidores inteligentes. El modelo tiene una interfaz predefinida para los datos, las propiedades, los comandos y las vistas.

> [!div class="mx-imgBorder"]
> ![Plantillas de dispositivos de medidores inteligentes](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Limpieza de recursos
Si decide no seguir usando esta aplicación, elimínela con los siguientes pasos:

1. En el panel izquierdo, abra la pestaña Administración.
1. Seleccione la configuración de la aplicación y haga clic en el botón Eliminar situado en la parte inferior de la página. 

    > [!div class="mx-imgBorder"]
    > ![Eliminar aplicación](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la arquitectura de las aplicaciones de medidores inteligentes, consulte:

> [!div class="nextstepaction"]
> [Azure IoT Central: arquitectura de aplicaciones de medidores inteligentes](./concept-iot-central-smart-meter-app.md)
