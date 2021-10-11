---
title: 'Tutorial: Aprovisionamiento de dispositivos en centros de conectividad con equilibrio de carga mediante Azure IoT Hub Device Provisioning Service'
description: En este tutorial se indica cómo Device Provisioning Service (DPS) permite el aprovisionamiento automático de dispositivos en instancias de IoT Hub con equilibrio de carga en Azure Portal.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 090d11866f3d3605a3703fd7ca39f820bbeb7187
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273257"
---
# <a name="tutorial-provision-devices-across-load-balanced-iot-hubs"></a>Tutorial: Aprovisionamiento de dispositivos en instancias de IoT Hub con equilibrio de carga

En este tutorial se muestra cómo aprovisionar dispositivos para varias instancias de IoT Hub con equilibrio de carga mediante el servicio Device Provisioning. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Usar Azure Portal para aprovisionar un segundo dispositivo en un segundo centro de IoT 
> * Agregar una entrada de la lista de inscripción en el segundo dispositivo
> * Establecer la directiva de asignación del servicio Device Provisioning para una **distribución uniforme**
> * Vincular la nueva instancia de IoT Hub al servicio Device Provisioning

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Este tutorial se basa en el tutorial anterior, [Provision the device to an IoT hub using the Azure IoT Hub Device Provisioning Service](tutorial-provision-device-to-hub.md) (Aprovisionamiento del dispositivo en un centro de IoT mediante el servicio IoT Hub Device Provisioning).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Usar Azure Portal para aprovisionar un segundo dispositivo en un segundo centro de IoT

Siga los pasos del tutorial [Provision the device to an IoT hub using the Azure IoT Hub Device Provisioning Service](tutorial-provision-device-to-hub.md) para aprovisionar un segundo dispositivo en otro centro de IoT (Aprovisionamiento del dispositivo en un centro de IoT mediante el servicio IoT Hub Device Provisioning).

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Agregar una entrada de la lista de inscripción en el segundo dispositivo

La lista de inscripción indica al servicio Device Provisioning el método de atestación (el método para confirmar la identidad de un dispositivo) que se usando con el dispositivo. El siguiente paso es agregar una entrada de la lista de inscripción para el segundo dispositivo. 

1. En la página del servicio Device Provisioning, haga clic en **Manage enrollments** (Administrar inscripciones). Aparece la página **Add enrollment list entry** (Agregar entrada de la lista de inscripción). 
2. Haga clic en **Agregar** en la parte superior de la página.
2. Rellene los campos y, a continuación, haga clic en **Guardar**.

## <a name="set-the-device-provisioning-service-allocation-policy"></a>Establecimiento de la directiva de asignación del servicio Device Provisioning

La directiva de asignación es un valor del servicio Device Provisioning que determina cómo se asignan los dispositivos a una instancia de IoT Hub. Hay tres directivas de asignación admitidas: 

1. **Latencia más baja**: los dispositivos se aprovisionan en una instancia de IoT Hub en función del centro con la latencia más baja en el dispositivo.
2. **Distribución uniformemente ponderada** (predeterminada): las instancias de IoT Hub vinculadas tienen la misma probabilidad de tener dispositivos aprovisionados para ellos. Esta es la configuración predeterminada. Si va a aprovisionar dispositivos para un único centro de IoT Hub, puede mantener esta configuración. 
3. **Configuración estática a través de la lista de inscripción**: la especificación de la instancia de IoT deseada en la lista de inscripción tiene prioridad sobre la directiva de asignación del nivel del servicio Device Provisioning.

Siga estos pasos para establecer la directiva de asignación:

1. Para establecer la directiva de asignación, en la página del servicio Device Provisioning haga clic en **Manage allocation policy** (Administrar directiva de asignación).
2. Establezca la directiva de asignación en **Distribución uniformemente ponderada**.
3. Haga clic en **Save**(Guardar).

## <a name="link-the-new-iot-hub-to-the-device-provisioning-service"></a>Vincular la nueva instancia de IoT Hub al servicio Device Provisioning

Vincule el servicio Device Provisioning e IoT Hub para que el primero pueda registrar dispositivos en el segundo.

1. En la página **Todos los recursos**, haga clic en la instancia de Device Provisioning Service que creó anteriormente.
2. En la página del servicio Device Provisioning, haga clic en **Linked IoT hubs** (Centros de IoT vinculados).
3. Haga clic en **Agregar**.
4. En la página **Add link to IoT hub** (Agregar vínculo a centro de IoT), utilice los botones de radio para especificar si el centro de IoT vinculado se encuentra en la suscripción actual o en una suscripción diferente. A continuación, elija el nombre del centro de IoT en el cuadro **Centro de IoT**.
5. Haga clic en **Save**(Guardar).

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Usar Azure Portal para aprovisionar un segundo dispositivo en un segundo centro de IoT 
> * Agregar una entrada de la lista de inscripción en el segundo dispositivo
> * Establecer la directiva de asignación del servicio Device Provisioning para una **distribución uniforme**
> * Vincular la nueva instancia de IoT Hub al servicio Device Provisioning

## <a name="next-steps"></a>Pasos siguientes

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps]()
-->
