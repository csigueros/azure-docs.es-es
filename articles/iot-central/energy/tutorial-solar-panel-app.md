---
title: 'Tutorial: Creación de una aplicación de supervisión de paneles solares con Azure IoT Central'
description: 'Tutorial: Aprenda a crear una aplicación de paneles solares mediante plantillas de aplicación de Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 369195e8dfce2440c2e34151e7de6b2d23edbad1
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112963264"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Tutorial: Creación y tutorial de la plantilla de aplicación de supervisión de paneles solares 

Este tutorial le guía por el proceso de creación de una aplicación de supervisión de paneles solares, que incluye un modelo de dispositivo de ejemplo con datos simulados. En este tutorial, aprenderá a:


> [!div class="checklist"]
> * Crear una aplicación de paneles solares gratis
> * Recorrido por la aplicación.
> * Limpieza de recursos


Si no tiene ninguna suscripción, [cree una cuenta de evaluación gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Requisitos previos

No hay requisitos previos para completar este tutorial. Se recomienda una suscripción de Azure, pero no es necesaria.


## <a name="create-a-solar-panel-monitoring-app"></a>Creación de una aplicación de supervisión de paneles solares 

Puede crear esta aplicación en tres sencillos pasos:

1. Vaya a [Azure IoT Central](https://apps.azureiotcentral.com). Para crear una nueva aplicación, seleccione **Build** (Compilar). 

1. Seleccione la pestaña **Energy** (Sector energético). En **Solar panel monitoring** (Supervisión de paneles solares), seleccione **Create app** (Crear aplicación). 

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de las opciones de compilación de Azure IoT Central.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. En el cuadro de diálogo **New application** (Nueva aplicación), rellene los detalles solicitados y, a continuación, seleccione **Create** (Crear):
    * **Nombre de la aplicación**: elija un nombre para la aplicación de Azure IoT Central. 
    * **URL**: elija una dirección URL de Azure IoT Central. La plataforma comprueba su unicidad.
    * **Pricing plan** (Plan de precios): si ya tiene una suscripción de Azure, se recomienda la configuración predeterminada. Si no tiene una suscripción de Azure, comience con la evaluación gratuita.
    * **Billing info** (Información de facturación): la aplicación es gratuita. Los detalles del directorio, la suscripción de Azure y la región son necesarios para aprovisionar los recursos de la aplicación.
        ![Captura de pantalla de la nueva aplicación.](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Captura de pantalla de la información de facturación.](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Verificación de la aplicación y los datos simulados

Puede modificar la nueva aplicación de paneles solares en cualquier momento. Por ahora, asegúrese de que la aplicación está implementada y funciona según lo esperado antes de modificarla.

Para verificar la creación de la aplicación y la simulación de datos, vaya al **Panel**. Si puede ver los iconos con algunos datos, significa que la implementación de la aplicación se realizó correctamente. La simulación de datos puede tardar unos minutos en generar los datos. 

## <a name="application-walk-through"></a>Tutorial de la aplicación
Después de implementar correctamente la plantilla de la aplicación, querrá explorar la aplicación un poco más. Observe que se incluyen el dispositivo medidor inteligente de ejemplo, el modelo de dispositivo y el panel.

Adatum es una compañía energética ficticia que supervisa y administra paneles solares. En el panel de supervisión de paneles solares, verá propiedades, datos y comandos de ejemplo de los paneles solares. Este panel permite al usuario o al equipo de soporte técnico realizar las siguientes actividades de forma proactiva, antes de que los problemas requieran soporte técnico adicional:
* Revisar la información más reciente del panel y la [ubicación](../core/howto-use-location-data.md) de su instalación en el mapa.
* Comprobar el estado del panel y el estado de la conexión.
* Revisar las tendencias de generación de energía y temperatura para detectar cualquier patrón anómalo.
* Realizar un seguimiento de la generación total de energía para fines de planeamiento y facturación.
* Activar un panel y actualizar la versión del firmware, si es necesario. En la plantilla, los botones de comando muestran las funcionalidades posibles y no envían comandos reales.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla del panel de la plantilla de supervisión de paneles solares.](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Dispositivos
La aplicación incluye un dispositivo de ejemplo de paneles solares. Para ver los detalles del dispositivo, seleccione **Devices** (Dispositivos).

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de los dispositivos de la plantilla de supervisión de paneles solares.](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

Seleccione el dispositivo de ejemplo, **SP0123456789**. En la pestaña **Update Properties** (Actualizar propiedades), puede actualizar las propiedades de escritura del dispositivo y visualizar los valores actualizados en el panel. 

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de la pestaña para actualizar propiedades de la plantilla de supervisión de paneles solares.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Plantilla de dispositivo
Para ver el modelo de dispositivo del panel solar, seleccione la pestaña **Device templates** (Plantillas de dispositivo). El modelo tiene interfaces predefinidas para datos, propiedades, comandos y vistas.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de las plantillas de dispositivo de la plantilla de supervisión de paneles solares.](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Limpieza de recursos
Si decide no seguir usando esta aplicación, elimínela con los siguientes pasos:

1. En el panel izquierdo, seleccione **Administration** (Administración).
1. Seleccione **Application settings** > **Delete** (Configuración de la aplicación > Eliminar). 

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la opción de administración de la plantilla de supervisión de paneles solares.](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Pasos siguientes
 
> [!div class="nextstepaction"]
> [Azure IoT Central: arquitectura de aplicaciones de panel solar](./concept-iot-central-solar-panel-app.md)

