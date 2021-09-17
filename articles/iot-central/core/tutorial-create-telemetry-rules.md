---
title: 'Tutorial: Creación y administración de reglas en la aplicación de Azure IoT Central'
description: En este tutorial se muestra cómo las reglas de Azure IoT Central le permiten supervisar los dispositivos casi en tiempo real e invocar automáticamente acciones, como el envío de correo electrónico, cuando la regla se desencadena.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6a9a7d23d4fb8f11c27f279bdef8d2b46274b21e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743401"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Tutorial: Creación de una regla y configuración de las notificaciones en la aplicación de Azure IoT Central

Puede usar Azure IoT Central para supervisar de forma remota los dispositivos conectados. Las reglas de Azure IoT Central le permiten supervisar los dispositivos casi en tiempo real e invocar acciones automáticamente, como el envío de correo electrónico. En este artículo se explica cómo crear reglas para supervisar los datos de telemetría enviados por el dispositivo.

Los dispositivos usan la telemetría para enviar datos numéricos. Cuando los datos de telemetría seleccionados superan un umbral especificado, se desencadena una regla.

En este tutorial se crea una regla que envía un mensaje de correo electrónico cuando la temperatura de un dispositivo de sensor simulado supera los 70&deg; F.

En este tutorial, aprenderá a:

> [!div class="checklist"]
>
> * Crear una regla
> * Adición de una acción de correo electrónico

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este tutorial, necesitará lo siguiente:

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="add-and-customize-a-device-template"></a>Incorporación y personalización de una plantilla de dispositivo

Agregue una plantilla de dispositivo desde el catálogo de dispositivos. En este tutorial se usa la plantilla de dispositivo **ESP32-Azure IoT Kit**:

1. Para agregar una nueva plantilla de dispositivo, seleccione **+ New** (+ Nuevo) en la página **Device templates** (Plantillas de dispositivo).

1. En la página **Select type** (Seleccionar tipo), desplácese hacia abajo hasta que encuentre el icono de **ESP32-Azure IoT Kit** en la sección **Use a preconfigured device template** (Usar una plantilla de dispositivo preconfigurada).

1. Seleccione el icono **ESP32-Azure IoT Kit** y **Next: Revisión**.

1. En la página **Revisar**, seleccione **Crear**.

El nombre de la plantilla que creó es **Sensor Controller**. El modelo incluye componentes como **Sensor Controller**, **SensorTemp** y **Device Information interface** (Interfaz de información del dispositivo). Los componentes definen las funcionalidades de un dispositivo ESP32, como la telemetría, las propiedades y los comandos.

Agregue dos propiedades de la nube a la plantilla de dispositivo **Sensor Controller**:

1. Seleccione **Cloud Properties** (Propiedades de la nube) y, luego, **+ Add cloud property** (+ Agregar propiedad de la nube). Use la información de la tabla siguiente para agregar dos propiedades de la nube a la plantilla de dispositivo:

    | Display Name (Nombre para mostrar)      | Semantic Type (Tipo semántico) | Schema |
    | ----------------- | ------------- | ------ |
    | Fecha de la última revisión | None          | Date   |
    | Nombre del cliente     | None          | String |

1. Haga clic en **Guardar** para guardar los cambios.

Agregue un nuevo formulario a la plantilla de dispositivo para administrar el dispositivo:

1. Seleccione el nodo **Views** (Vistas) y, después, seleccione el icono **Editing device and cloud data** (Editar datos del dispositivo y de la nube) para agregar una vista.

1. Cambie el nombre del formulario a **Manage device** (Administrar dispositivo).

1. Seleccione las propiedades de la nube **Customer Name** (Nombre del cliente) y **Last Service Date** (Fecha de la última revisión), así como la propiedad **Target Temperature** (Temperatura objetivo). Después, seleccione **Add section** (Agregar sección).

1. Seleccione **Save** (Guardar) para guardar la configuración.

Ahora publique la plantilla de dispositivo.

## <a name="create-a-rule"></a>Crear una regla

Para crear una regla de telemetría, la plantilla de dispositivo debe incluir al menos un valor de telemetría. En este tutorial se usa un dispositivo **Sensor Controller** simulado que envía datos de telemetría de temperatura y humedad. La regla supervisa la temperatura notificada por el dispositivo y envía un correo electrónico cada vez que sube de 70 grados.

> [!NOTE]
> Hay un límite de 50 reglas por aplicación.

1. En el panel izquierdo, seleccione **Rules** (Reglas).

1. Si aún no ha creado ninguna regla, consulte la siguiente pantalla:

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="Captura de pantalla que muestra la lista vacía de reglas":::

1. Seleccione **+ Nuevo** para agregar una regla nueva.

1. Escriba el nombre _Temperature monitor_ para identificar la regla y presione Entrar.

1. Seleccione la plantilla de dispositivo **Sensor Controller**. De forma predeterminada, la regla se aplica automáticamente a todos los dispositivos asociados con la plantilla de dispositivo. Para filtrar un subconjunto de los dispositivos, seleccione **+ Filter** (+ Filtro) y use las propiedades de dispositivo para identificar los dispositivos. Para deshabilitar la regla, alterne el botón **Habilitado/Deshabilitado**:

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="Captura de pantalla que muestra la selección de la plantilla de dispositivo en la definición de la regla":::

### <a name="configure-the-rule-conditions"></a>Configuración de las condiciones de la regla

Las condiciones definen los criterios que la regla supervisa. En este tutorial, configurará la regla para que se active cuando la temperatura supere los 70 &deg;F.

1. Seleccione **Temperature** (Temperatura) en la lista desplegable **Telemetry** (Telemetría).

1. A continuación, elija **Is greater than** (Es mayor que) como **Operator** (Operador) y escriba _70_ en **Value** (Valor).

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="Captura de pantalla que muestra la condición de temperatura de la regla":::

1. Opcionalmente, puede establecer un valor de **Time aggregation** (Agregación de tiempo). Al seleccionar una agregación de tiempo, también debe seleccionar un tipo de agregación, como la media o la suma, en la lista desplegable de agregación.

    * Sin la agregación, la regla se desencadena para cada punto de datos de telemetría que cumple la condición. Por ejemplo, si configura la regla para desencadenarse cuando la temperatura está por encima de 70, la regla se desencadena casi al instante cuando la temperatura del dispositivo supere este valor.
    * Con la agregación, la regla se desencadena si el valor agregado de los puntos de datos de telemetría de la ventana de tiempo cumple la condición. Por ejemplo, si configura la regla para desencadenarse cuando la temperatura sea superior a 70 con una agregación de tiempo media de 10 minutos, la regla se desencadena cuando el dispositivo informa de una temperatura media por encima de 70, calculada a lo largo de un intervalo de 10 minutos.

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="Captura de pantalla que muestra la condición de agregado rellenada":::

Puede agregar varias condiciones a una regla seleccionando **+ Condition** (+ Condición). Cuando se agregan varias condiciones, puede especificar si se deben cumplir todas las condiciones o solo alguna para que se desencadene la regla. Si usa la agregación de tiempo con varias condiciones, se deben agregar todos los valores de telemetría.

### <a name="configure-actions"></a>Configuración de acciones

Después de definir la condición, configure las acciones que deben llevarse a cabo cuando se desencadene la regla. Las acciones se invocan cuando todas las condiciones especificadas en la regla se evalúan como verdaderas.

1. Seleccione **+ Email** (+ Correo electrónico) en la sección **Actions** (Acciones).

1. Escriba _Temperature warning_ (Advertencia de temperatura) como nombre para mostrar de la acción, la dirección de correo electrónico en el campo **To** (Para) y _You should check the device!_ (Debe comprobar el dispositivo.) como la nota que va a aparecer en el cuerpo del correo electrónico.

    > [!NOTE]
    > Solo se envían mensajes de correo electrónico a los usuarios que se han agregado a la aplicación y han iniciado sesión al menos una vez. Obtenga más información sobre la [administración de usuarios](howto-administer.md) en Azure IoT Central.

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="Captura de pantalla que muestra la acción de correo electrónico para la regla":::

1. Para guardar la acción, elija **Listo**. Puede agregar varias acciones a una regla.

1. Para guardar la regla, elija **Guardar**. La regla está activa en unos minutos e inicia la supervisión de telemetría que se envía a la aplicación. Cuando se cumple la condición especificada en la regla, la regla desencadena la acción de correo electrónico configurada.

Después de un tiempo, recibirá un mensaje de correo electrónico cuando se desencadene la regla:

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="Captura de pantalla que muestra el correo electrónico de notificación":::

## <a name="delete-a-rule"></a>Eliminar una regla

Si ya no necesita una regla, elimínela; para ello, abra la regla y seleccione **Eliminar**.

## <a name="enable-or-disable-a-rule"></a>Habilitación o deshabilitación de una regla

Elija la regla que quiere habilitar o deshabilitar. Alterne el botón **Habilitado/Deshabilitado** en la regla para habilitar o deshabilitar la regla para todos los dispositivos que tengan el ámbito de esta.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Habilitación o deshabilitación de una regla para dispositivo específicos

Elija la regla que quiere personalizar. Use uno o varios filtros de la sección **Dispositivos de destino** para restringir el ámbito de la regla a los dispositivos que desea supervisar.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

* Crear una regla basada en la telemetría
* Agregar una acción

Ahora que ha definido una regla basada en umbral, el siguiente paso que se recomienda dar es:

> [!div class="nextstepaction"]
> [Configuración de reglas](howto-configure-rules.md)
