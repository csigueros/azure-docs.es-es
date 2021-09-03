---
title: Configuración de reglas y acciones en Azure IoT Central | Microsoft Docs
description: En este artículo paso a paso se muestra cómo puede, como generador, configurar las reglas y las acciones basadas en la telemetría en la aplicación de Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5d36c303f1ebc23b317ccd5dcb08d65f5ceaf30b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746415"
---
# <a name="configure-rules"></a>Configuración de reglas

Las reglas de IoT Central sirven como herramienta de respuesta personalizable que se desencadena en eventos supervisados activamente desde dispositivos conectados. En las secciones siguientes se describe cómo se evalúan las reglas. Puede definir una o varias acciones que tienen lugar cuando se desencadena una regla. En este artículo se describen el correo electrónico, el webhook y los grupos de acción de Azure Monitor. Para información sobre otros tipos de acciones, consulte [Uso de flujos de trabajo para integrar la aplicación de Azure IoT Central con otros servicios en la nube](howto-configure-rules-advanced.md).

## <a name="select-target-devices"></a>Seleccionar dispositivos de destino

Use la sección de dispositivos de destino para seleccionar el tipo de dispositivos que se aplicará a esta regla. Asimismo, los filtros le permiten restringir aún más los dispositivos que deben incluirse. Tenga en cuenta que los filtros usan propiedades en la plantilla de dispositivos para así poder filtrar el conjunto de dispositivos. Los filtros no desencadenan una acción. En la siguiente captura de pantalla, los dispositivos de destino son el tipo de plantilla de dispositivo **Frigorífico**. El filtro indica que la regla solo debe incluir **Frigoríficos** en los que la propiedad **Estado de fabricación** sea igual a **Washington**.

:::image type="content" source="media/howto-configure-rules/filters.png" alt-text="Captura de pantalla que muestra una definición de condición.":::

## <a name="use-multiple-conditions"></a>Uso de varias condiciones

Las reglas se activan según las condiciones. Puede agregar varias condiciones a una regla y especificar si esta debe desencadenarse cuando todas las condiciones son "true" o lo es alguna de las condiciones.  

En la siguiente captura de pantalla, las condiciones comprueban si la temperatura es mayor que 70 &deg;F y la humedad es menor que 10. Cuando alguna de estas instrucciones es "true", la regla se evalúa como "true" y se desencadena una acción.

:::image type="content" source="media/howto-configure-rules/conditions.png" alt-text="Captura de pantalla que muestra el monitor de un refrigerador con las condiciones especificadas para la temperatura y la humedad.":::

> [!NOTE]
> Actualmente solo se admiten las condiciones de telemetría.  

### <a name="use-a-cloud-property-in-a-value-field"></a>Uso de una propiedad en la nube en un campo de valor

Puede hacer referencia a una propiedad en la nube desde la plantilla de dispositivo en el campo **Valor** de una condición. La propiedad en la nube y el valor de telemetría deben tener tipos similares. Por ejemplo, si **Temperatura** es un valor de tipo Double, solo se mostrarán como opciones en la lista desplegable **Valor** las propiedades en la nube de tipo Double.

Si elige un valor de telemetría de tipo de evento, la lista desplegable **Valor** incluye la opción **Cualquiera**. La opción **Cualquier** significa que la regla se desencadena cuando la aplicación recibe un evento de ese tipo, sea cual sea la carga.

## <a name="use-aggregate-windowing"></a>Uso de la opción para agregar funciones de ventana

Puede especificar una agregación de tiempo para desencadenar la regla en función de una ventana de tiempo. Las condiciones de las reglas evalúan las ventanas de tiempo agregadas en los datos de telemetría como ventanas de saltos de tamaño constante. Si hay algún filtro de propiedad en la regla, se aplica al final de la ventana de tiempo. En la captura de pantalla siguiente, la ventana de tiempo es de cinco minutos. Cada cinco minutos, la regla se evalúa en función los últimos cinco minutos de datos de telemetría. Los datos solo se evalúan una vez en la ventana a la que correspondan.

:::image type="content" source="media/howto-configure-rules/tumbling-window.png" alt-text="Diagrama en el que se muestra cómo se definen las ventanas de saltos de tamaño constante.":::

## <a name="create-an-email-action"></a>Creación de una acción de correo electrónico

Cuando crea una acción de correo electrónico, la dirección debe ser un **identificador de usuario** de la aplicación y ese usuario debe haber iniciado sesión en la aplicación al menos una vez. También puede especificar una nota para incluirla en el correo electrónico. IoT Central muestra un ejemplo del aspecto del correo electrónico cuando se desencadena la regla:

:::image type="content" source="media/howto-configure-rules/email-action.png" alt-text="Captura de pantalla que muestra una acción de correo electrónico para una regla.":::

## <a name="create-a-webhook-action"></a>Creación de una acción de webhook

Los webhooks permiten conectar la aplicación de IoT Central a otras aplicaciones y servicios para supervisión remota y notificaciones. Los webhooks notifican automáticamente a otras aplicaciones y servicios a los que se conecta si se activa una regla en la aplicación IoT Central. Su aplicación IoT Central envía una solicitud POST al otro punto de conexión HTTP de la aplicación siempre que se desencadena una regla. La carga útil contiene detalles del dispositivo y del desencadenador de la regla.

En este ejemplo, se conectará a *RequestBin* para obtener notificaciones cuando se active una regla:

1. Abra [RequestBin](https://requestbin.net/).

1. Cree una instancia de RequestBin y copie la **dirección URL de la papelera**.

1. Agregue una acción a la regla:

    :::image type="content" source="media/howto-configure-rules/webhook-create.png" alt-text="Captura de pantalla que muestra la pantalla de creación del webhook.":::

1. Elija la acción de webhook, proporcione un nombre para mostrar y pegue la dirección URL de RequestBin como la dirección **URL de devolución de llamada**.

1. Guarde la regla.

Ahora, cuando se desencadene la regla, verá que aparece una nueva solicitud en RequestBin.

### <a name="payload"></a>Carga

Cuando una regla se desencadena, realiza una solicitud HTTP POST a la dirección URL de devolución de llamada. La solicitud contiene una carga JSON con los detalles de telemetría, dispositivo, regla y aplicación. La carga útil es similar al siguiente fragmento de código JSON:

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```

Si la regla supervisa la telemetría agregada durante un período de tiempo, la carga útil contendrá una sección de telemetría diferente:

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

### <a name="data-format-change-notice"></a>Aviso de cambio de formato de datos

Si tiene uno o varios webhooks creados y guardados antes del **3 de abril de 2020**, elimine el webhook y cree uno nuevo. Los webhooks más antiguos usan un formato de carga en desuso:

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="create-an-azure-monitor-group-action"></a>Creación de una acción de grupo de Azure Monitor

En esta sección se describe cómo usar *grupos de acciones* de [Azure Monitor](../../azure-monitor/overview.md) para asociar varias acciones a una regla de IoT Central. Puede asociar un grupo de acciones a varias reglas. Un [grupo de acciones](../../azure-monitor/alerts/action-groups.md) es una colección de las preferencias de notificación que el propietario de una suscripción de Azure define.

También puede [crear y administrar grupos de acciones en Azure Portal](../../azure-monitor/alerts/action-groups.md) o con un [plantilla de Azure Resource Manager](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md).

Un grupo de acciones puede:

- Enviar notificaciones, como un correo electrónico o SMS, o hacer una llamada de voz.
- Ejecutar de una acción, como llamar a un webhook.

La siguiente captura de pantalla muestra un grupo de acciones que envía notificaciones por correo electrónico y SMS y llama a un webhook:

:::image type="content" source="media/howto-configure-rules/action-group.png" alt-text="Captura de pantalla que muestra un grupo de acciones en Azure Portal.":::

Para usar un grupo de acciones en una regla de IoT Central, dicho grupo debe estar en la misma suscripción de Azure que la aplicación IoT Central.

Cuando agregue una acción a la regla en IoT Central, seleccione **Grupos de acciones de Azure Monitor**.

Elija un grupo de acciones en su suscripción de Azure:

:::image type="content" source="media/howto-configure-rules/choose-action-group.png" alt-text="Captura de pantalla que muestra un grupo de acciones en una regla de IoT Central.":::

Seleccione **Guardar**. El grupo de acciones ahora aparece en la lista de acciones que se ejecuta cuando se desencadena la regla.

En la tabla siguiente se resume la información enviada a los tipos de acciones admitidas:

| Tipo de acción | Formato de salida |
| ----------- | -------------- |
| Email       | Plantilla de correo electrónico estándar de IoT Central |
| sms         | Alerta de Azure IoT Central: ${applicationName} - "${ruleName}" desencadenada en "${deviceName}" el ${triggerDate} ${triggerTime} |
| Voz       | Alerta de Azure I.O.T Central: la regla "${ruleName}" se desencadenó en el dispositivo "${deviceName}" el ${triggerDate} ${triggerTime}, en la aplicación ${applicationName} |
| webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](howto-create-webhooks.md#payload)}} |

El texto siguiente es un mensaje SMS de ejemplo de un grupo de acciones:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="use-rules-with-iot-edge-modules"></a>Usar reglas con módulos de IoT Edge

Una restricción se aplica a las reglas que se aplican a los módulos de IoT Edge. Las reglas en la telemetría de módulos diferentes no se evalúan como reglas válidas. Tome el siguiente ejemplo. La primera condición de la regla se encuentra en la telemetría de la temperatura del módulo A. La segunda condición de la regla se encuentra en la telemetría de la humedad en el módulo B. Dado que las dos condiciones provienen de módulos diferentes, tiene un conjunto no válido de condiciones. La regla no es válida y genera un error al intentar guardarla.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a configurar una regla en su aplicación de Azure IoT Central, puede aprender a [configurar reglas avanzadas](howto-configure-rules-advanced.md) mediante Power Automate o Azure Logic Apps.
