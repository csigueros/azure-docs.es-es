---
title: Escalado vertical automático de las unidades de procesamiento de Azure Event Hubs
description: Habilite el inflado automático en un espacio de nombres para escalar verticalmente las unidades de procesamiento (nivel estándar).
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: 05ea9a76ec5ece9bf522679c85f3671d600d41e8
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444101"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units-standard-tier"></a>Escalado vertical automático de unidades de procesamiento de Azure Event Hubs (nivel estándar) 
Azure Event Hubs es una plataforma de streaming de datos muy escalable. Por lo tanto, el uso de Event Hubs suele aumentar después de empezar a utilizar el servicio. Tal uso requiere el aumento de las [unidades de procesamiento (TU)](event-hubs-scalability.md#throughput-units) para escalar Event Hubs y administrar velocidades de transferencia más elevadas. La característica de **inflado automático** de Event Hubs realiza el escalado vertical de forma automática mediante el aumento del número de unidades de procesamiento para responder a las necesidades de uso. Al aumentar las TU, se evitan escenarios de limitación en los que nos encontramos con:

* Velocidades de entrada de datos que superan las TU establecidas 
* Velocidades de solicitud de salida de datos que superan las TU establecidas

El servicio Event Hubs aumenta el rendimiento cuando la carga aumenta más allá del umbral mínimo, sin que se produzca ningún problema de las solicitudes con errores de ServerBusy.

> [!NOTE]
> Para más información sobre el nivel **prémium**, consulte [Event Hubs Premium](event-hubs-premium-overview.md).

## <a name="how-auto-inflate-works-in-standard-tier"></a>Funcionamiento del inflado automático en el nivel estándar
El tráfico de Event Hubs se controla mediante unidades de procesamiento (nivel estándar). Para conocer los límites, como las velocidades de entrada y salida por unidad de procesamiento, consulte [Cuotas y límites de Event Hubs](event-hubs-quotas.md). El inflado automático permite empezar poco a poco con las unidades de procesamiento mínimas requeridas elegidas. Después, la característica realiza el escalado automático hasta el límite máximo de unidades de procesamiento que necesite, según el aumento del tráfico. El inflado automático proporciona las siguientes ventajas:

- Un mecanismo de escalado eficaz para empezar poco a poco y escalar verticalmente a medida que aumente el tráfico.
- Escalado automático hasta el límite superior especificado sin problemas de limitación.
- Más control sobre el escalado, ya que se puede controlar el momento y la cantidad que se escala.

> [!NOTE]
> El inflado automático no reduce verticalmente *automáticamente* el número de TU cuando las tasas de entrada o salida bajan por debajo de los límites. 

 ## <a name="enable-auto-inflate-on-a-namespace"></a>Habilitación del inflado automático en un espacio de nombres
Puede habilitar o deshabilitar el inflado automático en un espacio de nombres de Event Hubs de nivel estándar mediante [Azure Portal](https://portal.azure.com) o una [plantilla de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate).

En los espacios de nombres de Event Hubs de nivel prémium, la característica se habilita automáticamente. No se puede deshabilitar. 

> [!NOTE]
> Los espacios de nombres de Event Hubs de nivel Básico no admiten el inflado automático.

## <a name="use-azure-portal"></a>Usar Azure Portal
En Azure Portal, puede habilitar la característica al crear un espacio de nombres de Event Hubs estándar o después de crear el espacio de nombres. También puede establecer las unidades de procesamiento del espacio de nombres y especificar el límite máximo. 

Puede habilitar la característica de inflado automático **al crear un espacio de nombres de Event Hubs**. En la imagen siguiente se muestra cómo habilitar la característica de inflado automático para un espacio de nombres de nivel estándar y cómo configurar las unidades de procesamiento para comenzar con el número máximo de ellas. 

:::image type="content" source="./media/event-hubs-auto-inflate/event-hubs-auto-inflate.png" alt-text="Captura de pantalla de la habilitación del inflado automático en el momento de la creación del centro de eventos para un espacio de nombres de nivel estándar":::

Con esta opción habilitada, puede empezar poco a poco con las unidades de procesamiento y escalarlas verticalmente a medida que sus necesidades de uso aumenten. El límite superior del inflado no afecta inmediatamente al precio, que depende del número de unidades de procesamiento utilizadas por hora.

Para habilitar la característica de inflado automático y modificar su configuración para un centro de eventos existente, siga estos pasos:

1. En la página **Espacio de nombres de Event Hubs**, seleccione **Escalar** en **Configuración** en el menú de la izquierda.
2. En la página **Configuración de escalado**, seleccione la casilla de verificación **Habilitar** (si no se ha habilitado la característica de escalabilidad automática).

    :::image type="content" source="./media/event-hubs-auto-inflate/scale-settings.png" alt-text="Captura de pantalla de la habilitación del inflado automático para un espacio de nombres estándar existente":::
3. Escriba el número **máximo** de unidades de rendimiento o use la barra de desplazamiento para establecer el valor.
4. (opcional) Actualice el número **mínimo** de unidades de procesamiento en la parte superior de esta página.

> [!NOTE]
> Al aplicar la configuración de inflado automático para aumentar las unidades de procesamiento, el servicio Event Hubs emite los registros de diagnóstico que le proporcionan información acerca de por qué y cuándo aumentó el rendimiento. Para habilitar el registro de diagnóstico para un centro de eventos, seleccione **configuración de diagnóstico** en el menú izquierdo de la página del centro de eventos en Azure Portal. Para más información, vea [Configuración de registros de diagnóstico de Azure Event Hubs](monitor-event-hubs-reference.md#resource-logs).


## <a name="use-an-azure-resource-manager-template"></a>Uso de una plantilla de Azure Resource Manager

Puede habilitar el inflado automático durante la implementación de una plantilla de Azure Resource Manager. Por ejemplo, establezca la propiedad `isAutoInflateEnabled` en **True**, y `maximumThroughputUnits` en 10. Por ejemplo:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Para ver la plantilla completa, consulte la plantilla [Create Event Hubs namespace and enable inflate](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate) (Creación de un espacio de nombres de Event Hubs y habilitación del inflado) en GitHub.


## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](./event-hubs-about.md)
