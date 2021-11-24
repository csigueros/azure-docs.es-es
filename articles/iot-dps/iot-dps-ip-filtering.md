---
title: Filtros de conexión IP de Microsoft Azure IoT DPS
description: Describe cómo usar el filtrado de IP para bloquear las conexiones de direcciones IP específicas de su instancia de Azure IoT DPS.
author: anastasia-ms
ms.author: v-stharr
ms.service: iot-dps
services: iot-dps
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: 9354b1e3bfc57951cff919b0c14e3bc950939d8f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485311"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Uso de filtros de conexión IP de Azure IoT DPS

La seguridad es un aspecto importante de cualquier solución de IoT. En ciertas ocasiones necesitará especificar explícitamente las direcciones IP desde las que se pueden conectar los dispositivos como parte de la configuración de seguridad. La característica de *filtro IP* para una instancia de Azure IoT Hub Device Provisioning Service (DPS) le permite configurar reglas para rechazar o aceptar tráfico de direcciones IPv4 específicas.

## <a name="when-to-use"></a>Cuándo se usa

Hay dos casos específicos en los que resulta útil bloquear las conexiones a un punto de conexión de DPS para determinadas direcciones IP:

* La instancia de DPS debe recibir tráfico solo de un intervalo concreto de direcciones IP y rechazar todas las demás. Por ejemplo, cuando se usa DPS con [Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) para crear conexiones privadas entre una instancia de DPS y los dispositivos.

* Cuando necesite rechazar el tráfico de direcciones IP que el administrador de DPS haya identificado como sospechosas.

>[!Note]
>Si el filtrado de IP está habilitado, ya no podrá usar Azure Portal para realizar operaciones de servicio (es decir, administrar inscripciones). Para realizar estas operaciones mediante el portal, tendrá que desactivar temporalmente el filtrado de IP, completar el trabajo y, luego, volver a habilitar esta característica. Si quiere usar sus propios clientes y evitar la desactivación del filtro de IP, puede optar por agregar la dirección IP de la máquina a `ipFilterRules` y administrar las inscripciones en DPS mediante la CLI.

## <a name="how-filter-rules-are-applied"></a>Cómo se aplican las reglas de filtro

Las reglas de filtro IP se aplican en el nivel de instancia de DPS. Por lo tanto, las reglas de filtro IP se aplican a todas las conexiones de los dispositivos y aplicaciones de back-end mediante un protocolo admitido.

Cualquier intento de conexión desde una dirección IP que coincida con una regla IP de rechazo en su instancia de DSP recibe un código de estado 401 no autorizado y la descripción. El mensaje de respuesta no menciona la regla IP.

> [!IMPORTANT]
> El rechazo de las direcciones IP puede evitar que otros servicios de Azure interactúen con la instancia de DPS.

## <a name="default-setting"></a>Valor predeterminado

De forma predeterminada, el filtrado de IP está deshabilitado y **Acceso a la red pública** se establece en *Todas las redes*. Esta configuración predeterminada significa que DPS acepta conexiones desde cualquier dirección IP o se ajusta a una regla que acepta el intervalo de direcciones IP 0.0.0.0/0.

:::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-default.png" alt-text="Configuración predeterminada de filtro IP de IoT DPS.":::

## <a name="add-an-ip-filter-rule"></a>Adición de una regla de filtro IP

Para agregar una regla de filtro IP:

1. Vaya a [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service.

4. En el menú **Configuración** de la izquierda, seleccione *Redes*.

5. En *Acceso a la red pública*, seleccione **Selected IP ranges** (Intervalos IP seleccionados).

6. Seleccione **+ Agregar regla de filtro IP**.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-add-rule.png" alt-text="Adición de una regla de filtro IP a una instancia de IoT DPS.":::

7. Rellene los campos siguientes:

    | Campo | Descripción|
    |-------|------------|
    | **Nombre** |Cadena única de hasta 128 caracteres alfanuméricos que no distingue mayúsculas de minúsculas. Solo se aceptan los caracteres alfanuméricos de 7 bits ASCII más `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.|
    | **Intervalo de direcciones** |Una única dirección IPv4 o un bloque de direcciones IP en la notación CIDR. Por ejemplo, en notación CIDR, 192.168.100.0/22 representa las direcciones IPv4 de 1024 de 192.168.100.0 a 192.168.103.255.|
    | **Acción** |Seleccione **Permitir** o **Bloquear**.|

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Después de seleccionar Agregar regla de filtro IP.":::

8. Seleccione **Guardar**. Debería ver una alerta que le informa de que la actualización está en curso.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png" alt-text="Notificación acerca de guardar una regla de filtro IP.":::

    >[!Note]
    > La opción **+ Agregar regla de filtro IP** se deshabilita cuando se alcanza el máximo de 100 reglas de filtro IP.

## <a name="edit-an-ip-filter-rule"></a>Edición de una regla de filtro IP

Para editar una regla existente:

1. Seleccione los datos de la regla de filtro IP que desea cambiar.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-rule-edit.png" alt-text="Edición de una regla de filtro IP.":::

2. Realice el cambio.

3. Seleccione **Guardar**.

## <a name="delete-an-ip-filter-rule"></a>Eliminación de una regla de filtro IP

Para eliminar una regla de filtro IP:

1. Seleccione el icono de eliminación en la fila de la regla de IP que desea eliminar.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-delete-rule.png" alt-text="Eliminación de una regla de filtro IP de IoT DPS.":::

2. Seleccione **Guardar**.

## <a name="ip-filter-rule-evaluation"></a>Evaluación de las reglas de filtro IP

Las reglas de filtro IP se aplican por orden. La primera regla que coincide con la dirección IP determina la acción de aceptación o rechazo.

Por ejemplo, si desea aceptar las direcciones del intervalo 192.168.100.0/22 y rechazar todas las demás, la primera regla de la cuadrícula debe aceptar el intervalo de direcciones 192.168.100.0/22. La siguiente regla debe rechazar todas las direcciones mediante el intervalo 0.0.0.0/0.

Para cambiar el orden de las reglas de filtro IP:

1. Seleccione la regla que desea mover.

2. Arrastre y coloque la regla en la ubicación deseada.

3. Seleccione **Guardar**.

## <a name="update-ip-filter-rules-using-azure-resource-manager-templates"></a>Actualización de reglas de filtro IP mediante plantillas de Azure Resource Manager

Hay dos maneras de actualizar el filtro IP de DPS:

1. Llamar al método de la API REST de recursos de IoT Hub. Para obtener información sobre cómo actualizar las reglas de filtro IP mediante REST, consulte `IpFilterRule` en la [sección Definiciones](/api/iothub/iot-hub-resource/update#definitions) del método [Update de Recurso de IoT Hub](/api/iothub/iot-hub-resource/update).

2. Usar las plantillas de Azure Resource Manager. Para obtener instrucciones sobre su uso, consulte la información sobre [plantillas de Azure Resource Manager](../azure-resource-manager/templates/overview.md). Los ejemplos siguientes muestran cómo crear, editar y eliminar reglas de filtro IP de DPS con plantillas de Azure Resource Manager.

    >[!NOTE]
    >Actualmente, la CLI de Azure y Azure PowerShell no admiten actualizaciones de reglas de filtro IP de DPS.

### <a name="add-an-ip-filter-rule"></a>Adición de una regla de filtro IP

En el ejemplo de plantilla siguiente se crea una nueva regla de filtro IP denominada "AllowAll" que acepta todo el tráfico.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Actualice los atributos de la regla de filtro IP de la plantilla en función de sus requisitos.

| Atributo                | Descripción |
| ------------------------ | ----------- |
| **FilterName**           | Escriba un nombre para la regla de filtro IP. Debe ser una cadena única de hasta 128 caracteres alfanuméricos que no distinga mayúsculas de minúsculas. Solo se aceptan los caracteres alfanuméricos ASCII de 7 bits más  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` . |
| **Acción**               | Los valores aceptados son **Aceptar** o  **Rechazar** como la acción de la regla de filtro IP. |
| **ipMask**               | Proporcione una única dirección IPv4 o un bloque de direcciones IP en la notación CIDR. Por ejemplo, en notación CIDR, 192.168.100.0/22 representa las direcciones IPv4 de 1024 de 192.168.100.0 a 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Actualización de una regla de filtro IP

En el ejemplo de plantilla siguiente se actualiza la regla de filtro IP denominada "AllowAll", que se mostró anteriormente, para rechazar todo el tráfico.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Eliminación de una regla de filtro IP

En el ejemplo de plantilla siguiente se eliminan todas las reglas de filtro IP para la instancia de DPS.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```

## <a name="next-steps"></a>Pasos siguientes

Para explorar aún más la administración de DPS, consulte:

* [Descripción de las direcciones IP de IoT DPS](iot-dps-understand-ip-address.md)
* [Configuración de DPS mediante la CLI de Azure](quick-setup-auto-provision-cli.md)
* [Control de acceso al servicio Azure IoT Hub Device Provisioning](how-to-control-access.md)
