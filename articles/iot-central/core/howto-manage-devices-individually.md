---
title: Administración individual de dispositivos en una aplicación de Azure IoT Central | Microsoft Docs
description: Aprenda a administrar dispositivos de manera individual en la aplicación de Azure IoT Central. Cree, elimine y actualice dispositivos.
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0ca2190bf5994edec56dce638d7361d90d2da2de
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810634"
---
# <a name="manage-individual-devices-in-your-azure-iot-central-application"></a>Administración de dispositivos individuales en la aplicación de Azure IoT Central

En este artículo se describe cómo puede administrar dispositivos en la aplicación de Azure IoT Central. Puede hacer lo siguiente:

- Usar la página **Devices** (Dispositivos) para ver, agregar y eliminar los dispositivos conectados a una aplicación de Azure IoT Central.
- Mantenga actualizados los metadatos del dispositivo cambiando los valores almacenados en las propiedades del dispositivo desde sus vistas.
- Controlar el comportamiento de los dispositivos mediante la actualización de una configuración en un dispositivo específico desde sus vistas.

Para obtener información sobre cómo administrar grupos personalizados de dispositivos, consulte [Tutorial: Uso de grupos de dispositivos para analizar la telemetría de dispositivo](tutorial-use-device-groups.md).

## <a name="view-your-devices"></a>Visualización de los dispositivos

Para ver un dispositivo determinado:

1. Elija **Dispositivos** en el panel izquierdo. Aquí puede ver una lista de las plantillas de dispositivo y una lista de todos los dispositivos a los que puede acceder su organización.

1. Elija una plantilla.

1. En el panel derecho de la página **Dispositivos**, verá una lista de los dispositivos creados con esa plantilla a los que puede acceder su organización. Elija un dispositivo para ver la página de detalles de ese dispositivo:

    :::image type="content" source="media/howto-manage-devices-individually/device-list.png" alt-text="Captura de pantalla que muestra la lista de dispositivos.":::

    > [!TIP]
    > Puede usar la herramienta de filtro de esta página para ver los dispositivos de una organización específica.

## <a name="add-a-device"></a>Agregar un dispositivo

Para agregar un dispositivo real a una aplicación de Azure IoT Central:

1. Elija **Dispositivos** en el panel izquierdo.

1. Elija la plantilla de dispositivo desde la que va a crear un dispositivo.

1. Elija + **Nuevo**.

1. Escriba un nombre de dispositivo y un identificador, o bien acepte el valor predeterminado. La longitud máxima del nombre de un dispositivo es de 148 caracteres. La longitud máxima del identificador de un dispositivo es de 128 caracteres.

1. Cambie el botón de alternancia **Simulated** **On** (Activado) u **Off** (Desactivado). Un dispositivo real hace referencia a un dispositivo físico que se conecta a la aplicación de Azure IoT Central. Un dispositivo simulado tiene datos de ejemplo generados por Azure IoT Central, que los pone a su disposición.

1. Si la aplicación usa organizaciones, elija la organización a la que pertenece el dispositivo.

    > [!TIP]
    > Puede establecer una [organización predeterminada](howto-create-organizations.md#default-organization) para que aparezca en la lista desplegable de la organización.

1. Seleccione **Crear**.

1. El dispositivo aparece ahora en la lista de dispositivos de esta plantilla. Selecciónelo para ver su página de detalles, que contiene todas las vistas del dispositivo.

## <a name="change-organization"></a>Cambio de organización

Para trasladar un dispositivo a otra organización, debe tener acceso a las organizaciones de origen y de destino. Para trasladar un dispositivo:

1. Elija **Dispositivos** en el panel izquierdo.

1. Seleccione el dispositivo que se trasladará en la lista de dispositivos.

1. Seleccione **Organización**.

1. Seleccione la nueva organización para el dispositivo:

    :::image type="content" source="media/howto-manage-devices-individually/change-device-organization.png" alt-text="Captura de pantalla en la que se muestra cómo trasladar un dispositivo a una nueva organización.":::

1. Seleccione **Guardar**.

## <a name="migrate-devices-to-a-template"></a>Migración de dispositivos a una plantilla

Si para registrar dispositivos inicia la importación en **All devices** (Todos los dispositivos), los dispositivos se crean sin asociación con ninguna plantilla. Los dispositivos deben estar asociados con una plantilla para explorar los datos y otros detalles sobre ellos. Siga estos pasos para asociar dispositivos con una plantilla:

1. Elija **Dispositivos** en el panel izquierdo.

1. Elija **All devices** (Todos los servicios) en el panel izquierdo:

    :::image type="content" source="media/howto-manage-devices-individually/unassociated-devices-1.png" alt-text="Captura de pantalla que muestra los dispositivos no asociados.":::

1. Use el filtro de la cuadrícula para determinar si el valor de la columna **Plantilla de dispositivo** es **Sin asociar** en cualquiera de los dispositivos.

1. Seleccione los dispositivos que quiere asociar con una plantilla:

1. Seleccione **Migrate** (Migrar):

    :::image type="content" source="media/howto-manage-devices-individually/unassociated-devices-2.png" alt-text="Captura de pantalla que muestra cómo asociar un dispositivo.":::

1. Elija la plantilla en la lista de plantillas disponibles y seleccione **Migrate** (Migrar).

1. Los dispositivos seleccionados están asociados con la plantilla de dispositivo que eligió.

## <a name="delete-a-device"></a>Eliminar un dispositivo

Para eliminar ya sea un dispositivo real o simulado de la aplicación de Azure IoT Central:

1. Elija **Dispositivos** en el panel izquierdo.

1. Elija la plantilla de dispositivo del dispositivo que desea eliminar.

1. Use las herramientas de filtro para filtrar y buscar los dispositivos. Active la casilla situada junto al dispositivo que desea eliminar.

1. Elija **Eliminar**. Puede realizar un seguimiento del estado de la eliminación en el panel Device Operations (Operaciones de dispositivo).

## <a name="change-a-property"></a>Cambio de una propiedad

Las propiedades de la nube son los metadatos de dispositivo asociados al dispositivo, como la ciudad y el número de serie. Las propiedades de la nube solo existen en la aplicación IoT Central y no se sincronizan con sus dispositivos. Las propiedades que se pueden escribir controlan el comportamiento de un dispositivo y permiten establecer el estado de un dispositivo de forma remota, por ejemplo, estableciendo la temperatura objetivo de un dispositivo de termostato.  Las propiedades del dispositivo las establece el dispositivo y son de solo lectura en IoT Central. Puede ver y actualizar las propiedades en las vistas de **Detalles del dispositivo**.

1. Elija **Dispositivos** en el panel izquierdo.

1. Elija la plantilla de dispositivo del dispositivo cuyas propiedades desea modificar y seleccione el dispositivo de destino.

1. Elija la vista que contiene las propiedades del dispositivo. Esta vista le permite especificar valores y seleccionar **Guardar** en la parte superior de la página. Aquí verá las propiedades del dispositivo y sus valores actuales. Tanto las propiedades de la nube como las propiedades que se pueden escribir tienen campos editables, mientras que las propiedades del dispositivo son de solo lectura. En el caso de las propiedades que se pueden escribir, puede ver su estado de sincronización en la parte inferior del campo.

1. Modifique las propiedades con los valores que necesite. Puede modificar varias propiedades a la vez y actualizarlas todas al mismo tiempo.

1. Elija **Guardar**. Si guardó las propiedades que se pueden escribir, los valores se envían al dispositivo. Cuando el dispositivo confirma el cambio de la propiedad que se puede escribir, el estado vuelve a **synced** (sincronizado). Si guardó una propiedad de la nube, el valor se actualiza.

## <a name="next-steps"></a>Pasos siguientes

Ahora que aprendió a administrar dispositivos de manera individual, el paso siguiente sugerido es aprender a [administrar dispositivos de manera masiva en la aplicación de Azure IoT Central](howto-manage-devices-in-bulk.md).
