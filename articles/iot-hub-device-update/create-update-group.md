---
title: Creación de un grupo de dispositivos en Device Update para Azure IoT Hub | Microsoft Docs
description: Creación de un grupo de dispositivos en Device Update para Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 6c9f1294f1d2f80689cdb417ad16357cc5fbcece
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110089333"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Creación de grupos de dispositivos en Device Update para IoT Hub
Device Update para IoT Hub permite implementar una actualización en un grupo de dispositivos IoT.

## <a name="prerequisites"></a>Prerrequisitos

* [Acceso a una instancia de IoT Hub con Device Update para IoT Hub habilitado](create-device-update-account.md). Se recomienda usar un nivel S1 (estándar), o superior, para su instancia de IoT Hub. 
* Un dispositivo IoT (o simulador) aprovisionado para Device Update en IoT Hub.
* [Al menos una actualización se importó correctamente para el dispositivo aprovisionado.](import-update.md)
* Instale e inicie el agente de Device Update en el dispositivo IoT como una [identidad de nivel de dispositivo o de módulo](device-update-agent-provisioning.md).

## <a name="add-a-tag-to-your-devices"></a>Adición de una etiqueta a los dispositivos  

Device Update para IoT Hub permite implementar una actualización en un grupo de dispositivos IoT. Para crear un grupo, el primer paso consiste en agregar una etiqueta al conjunto de dispositivos de destino en IoT Hub. Las etiquetas solo se pueden agregar correctamente al dispositivo una vez que se ha conectado a Device Update.

En la documentación siguiente se describe cómo agregar y actualizar una etiqueta.

### <a name="programmatically-update-device-twin"></a>Actualización del dispositivo gemelo mediante programación

Puede actualizar el dispositivo gemelo con la etiqueta adecuada mediante RegistryManager después de inscribir el dispositivo en Device Update. 
[Obtenga información sobre cómo agregar etiquetas con una aplicación de .NET de ejemplo.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[Obtenga información sobre las propiedades de las etiquetas](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>Formato de etiqueta de Device Update

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Uso de trabajos

Es posible programar un trabajo en varios dispositivos para agregar o actualizar una etiqueta de Device Update siguiendo [estos](../iot-hub/iot-hub-devguide-jobs.md) ejemplos. El dispositivo gemelo o el módulo gemelo se pueden actualizar (si el agente de Device Update se configura como una identidad de módulo) mediante Jobs. [Obtenga más información](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > Esta acción entra en conflicto con la cuota de mensajes de IoT Hub actual y se recomienda cambiar solo 50 000 etiquetas del dispositivo o módulo gemelo como máximo a la vez. De lo contrario, es posible que tenga que comprar más unidades de IoT Hub si supera la cuota diaria de mensajes de IoT Hub. Los detalles se pueden encontrar en [Cuotas y limitación](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling).

### <a name="direct-twin-updates"></a>Actualizaciones directas de gemelos

Las etiquetas también se pueden agregar o actualizar directamente en el dispositivo gemelo o en el módulo gemelo.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a su instancia de IoT Hub.

2. En "Dispositivos IoT" o en "IoT Edge", en el panel de navegación izquierdo, busque el dispositivo IoT y vaya al dispositivo gemelo o al módulo de actualización de dispositivos y, después, a su módulo gemelo (estará disponible si el agente de Device Update está configurado como una identidad de módulo).

3. En el dispositivo gemelo o en el módulo gemelo, elimine todos los valores de etiqueta de Device Update existentes, para lo que debe establecerlos en NULL.

4. Agregue un nuevo valor de etiqueta de Device Update, como se muestra a continuación. [Ejemplo de documento JSON de dispositivo gemelo con etiquetas.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Limitaciones

* Puede agregar cualquier valor a la etiqueta excepto "Sin categoría", que es un valor reservado.
* El valor de etiqueta no puede tener más de 255 caracteres.
* El valor de etiqueta puede contener caracteres alfanuméricos y los caracteres especiales ".", "-", "_" y "~".
* Los nombres de etiqueta y grupo distinguen mayúsculas de minúsculas.
* Un dispositivo solo puede tener una etiqueta con el nombre ADUGroup. Cualquier adición posterior de una etiqueta con ese nombre reemplazará el valor existente del nombre de etiqueta ADUGroup.
* Un dispositivo solo puede pertenecer a un grupo.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Creación de un grupo de dispositivos mediante la selección de una etiqueta de IoT Hub existente

1. Vaya a [Azure Portal](https://portal.azure.com).

2. Seleccione la instancia de IoT Hub que conectó previamente a la instancia de Device Update.

3. Seleccione la opción Actualizaciones del dispositivo en Administración de dispositivos automática en la barra de navegación izquierda.

4. Seleccione la pestaña Grupos en la parte superior de la página. Podrá ver el número de dispositivos conectados a Device Update que todavía no están agrupados.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Captura de pantalla de dispositivos no agrupados." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Seleccione el botón Agregar para crear un grupo.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Captura de pantalla de la adición de grupos de dispositivos." lightbox="media/create-update-group/add-group.png":::

6. Seleccione una etiqueta de IoT Hub de la lista y, a continuación, seleccione Crear grupo de actualización.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Captura de pantalla de selección de etiquetas." lightbox="media/create-update-group/select-tag.png":::

7. Una vez creado el grupo, verá que se han actualizado la lista de grupos y el gráfico de Update Compliance.  En el gráfico de Update Compliance se muestra el recuento de dispositivos en los distintos estados de compatibilidad: On latest update (Actualización más reciente), Nuevas actualizaciones disponibles, Actualizaciones en curso y Devices not yet Grouped (Dispositivos aún sin agrupar). [Obtenga más información sobre Update Compliance.](device-update-compliance.md)
   :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Captura de pantalla de la vista de Update Compliance." lightbox="media/create-update-group/updated-view.png":::

8. Debería ver el grupo recién creado y todas las actualizaciones disponibles para los dispositivos del nuevo grupo. Puede implementar la actualización en el nuevo grupo desde esta vista mediante un clic en el nombre de la actualización. Para obtener más detalles, consulte el paso siguiente: Implementación de la actualización.

## <a name="view-device-details-for-the-group-you-created"></a>Visualización de los detalles del dispositivo para el grupo que ha creado

1. Navegue hasta el grupo recién creado y haga clic en el nombre del grupo.

2. Junto con las propiedades de Device Update, se mostrará una lista de los dispositivos que forman parte del grupo. En esta vista, también puede ver la información de Update Compliance para todos los dispositivos que son miembros del grupo. En el gráfico de Update Compliance se muestra el recuento de dispositivos en los distintos estados de compatibilidad: On latest update (Actualización más reciente), Nuevas actualizaciones disponibles y Actualizaciones en curso.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Captura de pantalla de la vista de detalles del grupo de dispositivos." lightbox="media/create-update-group/group-details.png":::

3. También puede hacer clic en cada dispositivo individual de un grupo para que el sistema lo redirija a la página de detalles del dispositivo en IoT Hub.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Captura de pantalla de la vista detalles del dispositivo." lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Pasos siguientes 

[Implementación de la actualización](deploy-update.md)

[Obtenga más información sobre los grupos de dispositivos.](device-update-groups.md)

[Obtenga información sobre Update Compliance.](device-update-compliance.md)
