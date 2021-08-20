---
title: 'Inicio rápido: Configuración de reglas y acciones en Azure IoT Central'
description: En este inicio rápido se muestra cómo configurar las reglas y las acciones basadas en la telemetría en la aplicación de IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 9357198e140a6ba403fcb74787d31a0940554fa7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459182"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Inicio rápido: Configuración de reglas y acciones para el dispositivo en Azure IoT Central

En este inicio rápido creará una regla de IoT Central que envía un correo electrónico cuando alguien le entrega el teléfono.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar debe completar el inicio rápido anterior [Creación y uso de una aplicación de Azure IoT Central](./quick-deploy-iot-central.md) para conectar la aplicación para smartphone **IoT Plug and Play** a la aplicación IoT Central.

## <a name="create-a-telemetry-based-rule"></a>Crear una regla basada en la telemetría

La aplicación para smartphone envía telemetría que incluye valores del sensor del acelerómetro. Cuando el teléfono está en horizontal sobre la parte trasera, el valor **z** es mayor que `9`; si está sobre la parte delantera, **z** es menor que `-9`.

1. Para agregar una nueva regla basada en telemetría a la aplicación, en el panel izquierdo, seleccione **Reglas**.

1. Para crear una regla, seleccione **Crear una regla**.

1. Escriba **Phone turned over** como nombre para la regla.

1. En la sección **Dispositivos de destino**, seleccione **IoT Plug and Play mobile** (IoT Plug and Play móvil) para **Plantilla de dispositivo**. Esta opción filtra los dispositivos a los que se aplica la regla por tipo de plantilla de dispositivo. Para agregar más criterios de filtro, seleccione **+ Filtro**.

1. En la sección **Condiciones**, se define lo que desencadena la regla. Use la siguiente información para definir una única condición basada en la telemetría del eje z del acelerómetro. Esta regla usa la agregación para que reciba un máximo de un correo electrónico por cada dispositivo cada cinco minutos:

    | Campo            | Value            |
    |------------------|------------------|
    | Agregación de tiempo | Activado, 5 minutos    |
    | Telemetría        | Aceleración / Z |
    | Operador         | es menor que     |
    | Agregación      | Mínima          |
    | Value            | -9               |

    :::image type="content" source="media/quick-configure-rules/rule-target-condition.png" alt-text="Captura de pantalla que muestra la condición de la regla.":::

1. Para agregar una acción de correo electrónico para que se ejecute cuando se desencadene la regla, en la sección **Acciones**, seleccione **+ Correo electrónico**.

1. Use la información de la tabla siguiente para definir la acción y, después, seleccione **Listo**:

    | Configuración      | Value                    |
    |--------------|--------------------------|
    | Nombre para mostrar | El teléfono se movió         |
    | A           | La dirección de correo electrónico propia       |
    | Notas        | ¡El teléfono está boca abajo! |

    > [!TIP]
    > Para recibir una notificación por correo electrónico, la dirección debe ser un [identificador de usuario de la aplicación](howto-manage-users-roles.md) y ese usuario debe haber iniciado sesión en la aplicación al menos una vez.

    :::image type="content" source="media/quick-configure-rules/rule-action.png" alt-text="Captura de pantalla que muestra la acción de correo electrónico agregada a la regla.":::

1. Seleccione **Guardar**. La regla se muestra ahora en la página **Reglas**.

## <a name="test-the-rule"></a>Prueba de la regla

Poco después de guardar la regla, esta se activa. Cuando se cumplan las condiciones definidas en la regla, IoT Central enviará un correo electrónico a la dirección que especificó en la acción.

Para desencadenar la regla, asegúrese de que la aplicación para smartphone envía datos y coloque el smartphone boca abajo en el escritorio. La aplicación ahora envía valores de telemetría del eje z del acelerómetro menores que `-9`. A los cinco minutos, IoT Central le envía un correo electrónico para notificarle que el smartphone está boca abajo.

Una vez terminada la prueba, deshabilite la regla para dejar de recibir las alertas en la bandeja de entrada.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a hacer lo siguiente:

* Crear una regla basada en la telemetría
* Agregar una acción

Para más información sobre la integración de la aplicación IoT Central con otros servicios, consulte:

> [!div class="nextstepaction"]
> [Exportación y procesamiento de los datos desde la aplicación IoT Central](quick-export-data.md).
