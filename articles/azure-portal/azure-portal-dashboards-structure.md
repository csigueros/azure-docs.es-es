---
title: Estructura de los paneles de Azure
description: Recorra la estructura JSON de un panel de Azure mediante un panel de ejemplo. Incluye una referencia a las propiedades de los recursos.
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 4f005d6b232a7bba15d55055d49ac1c7adf49866
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239711"
---
# <a name="the-structure-of-azure-dashboards"></a>Estructura de los paneles de Azure

Este documento le guía a través de la estructura de un panel de Azure con el panel siguiente como ejemplo:

:::image type="content" source="media/azure-portal-dashboards-structure/sample-dashboard.png" alt-text="Captura de pantalla de un panel de ejemplo en Azure Portal.":::

Como los [paneles de Azure compartidos son recursos](../azure-resource-manager/management/overview.md), este panel se puede representar como JSON.  La siguiente representación JSON representa el panel visualizado anteriormente.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>Propiedades de recursos comunes

Vamos a desglosar las secciones relevantes de JSON.  Las propiedades de nivel superior, __id__, __name__, __type__, __location__ y __tags__ se comparten entre todos los tipos de recursos de Azure. Es decir, no tienen mucho que ver con el contenido del panel.

### <a name="id"></a>id

El identificador de recurso de Azure está sujeto a las [convenciones de nomenclatura de recursos de Azure](/azure/architecture/best-practices/resource-naming). Cuando el portal crea un panel, elige generalmente un identificador en forma de identificador único global, pero se puede usar cualquier nombre válido cuando se crea mediante programación.

### <a name="name"></a>Nombre

El nombre es el segmento del identificador de recurso que no incluye la suscripción, el tipo de recurso o la información del grupo de recursos. Básicamente, es el último segmento del identificador de recurso.

### <a name="type"></a>Tipo

Todos los paneles son del tipo __Microsoft.Portal/dashboards__.

### <a name="location"></a>Ubicación

A diferencia de otros recursos, los paneles no tienen un componente de tiempo de ejecución.  Para los paneles, la ubicación indica la ubicación geográfica principal que almacena la representación JSON del panel. El valor debe ser uno de los códigos de ubicación que se pueden recuperar mediante la [API de ubicaciones en el recurso de suscripciones](/rest/api/resources/subscriptions).

### <a name="tags"></a>Etiquetas

Las etiquetas son una característica común de los recursos de Azure que le permiten organizar el recurso por pares de nombre y valor arbitrarios. Los paneles incluyen una etiqueta especial denominada __hidden-title__. Si el panel tiene esta propiedad rellenada, ese valor se utiliza como el nombre para mostrar del panel en el portal. No se puede cambiar el nombre de los identificadores de recursos de Azure, pero sí el de las etiquetas. Esta etiqueta ofrece un modo de tener un nombre para mostrar que pueda cambiar para el panel.

`"tags": { "hidden-title": "Created via API" }`

### <a name="properties"></a>Propiedades

El objeto properties contiene dos propiedades, __lenses__ y __metadata__. La propiedad __lenses__ contiene información sobre los iconos del panel.  La propiedad __metadata__ existe para posibles características futuras.

### <a name="lenses"></a>lenses

La propiedad __lenses__ contiene el panel. El objeto lens de este ejemplo contiene una propiedad única denominada "0". lenses son un concepto de agrupación que no está implementado actualmente en los paneles. Por ahora, todos los paneles tienen esta propiedad en el objeto lens, denominada "0".

### <a name="parts"></a>Partes

El objeto que hay debajo de "0" contiene dos propiedades, __order__ y __parts__.  En la versión actual de los paneles, __order__ es siempre 0. La propiedad __parts__ contiene un objeto que define los elementos individuales (también denominados iconos) del panel.

El objeto __parts__ contiene una propiedad para cada elemento, donde el nombre de la propiedad es un número. El número no es significativo.

Cada objeto part individual tiene las propiedades __position__ y __metadata__.

### <a name="position"></a>Posición

La propiedad __position__ contiene información sobre el tamaño y la ubicación del elemento expresado como __x__, __y__, __rowSpan__ y __colSpan__. Los valores se expresan en términos de unidades de cuadrícula. Estas unidades de cuadrícula están visibles cuando el panel está en modo de personalización tal como se muestra aquí. Si desea que un icono tenga una anchura de dos unidades de cuadrícula, una altura de una unidad de cuadrícula y una ubicación en la esquina superior izquierda del panel, entonces, el objeto position tendrá el siguiente aspecto:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

:::image type="content" source="media/azure-portal-dashboards-structure/grid-units.png" alt-text="Captura de pantalla en la que se muestran las unidades de cuadrícula de un panel en Azure Portal.":::

### <a name="metadata"></a>Metadatos

Cada elemento tiene una propiedad metadata, un objeto solo tiene una propiedad necesaria que se llama __type__. Esta cadena indica al portal qué icono debe mostrar. El panel de ejemplo utiliza estos tipos de iconos:

1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart`: se usa para mostrar las métricas de supervisión
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart`: se usa para mostrar con texto o imágenes y con el formato básico para listas, vínculos, etc.
1. `Extension[azure]/HubsExtension/PartType/VideoPart`: se usa para mostrar vídeos de YouTube, Channel9 y cualquier otro tipo de vídeo que funcione en una etiqueta de vídeo HTML.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart`: se usa para mostrar el nombre y el estado de una máquina virtual de Azure.

Cada tipo de elemento tiene su propia configuración. Las posibles propiedades de configuración se denominan __inputs__, __settings__ y __asset__. 

### <a name="inputs"></a>Entradas

El objeto inputs generalmente contiene información que enlaza un icono con una instancia del recurso.  El elemento de máquina virtual en el panel de ejemplo contiene una sola entrada que usa el identificador de recurso de Azure para expresar el enlace.  Este formato de identificador de recurso es coherente en todos los recursos de Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```

El elemento de gráfico de métricas tiene una sola entrada que expresa el recurso al que se va a enlazar, junto con información sobre las métricas que se van a mostrar. Esta es la entrada para el icono que muestra las métricas Entrada de red y Salida de red.

```json
"inputs":
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="settings"></a>Configuración

El objeto settings contiene los elementos configurables de un elemento.  En el panel de ejemplo, el elemento Markdown usa valores para almacenar el contenido de marcado personalizado, junto con un título y un subtítulo configurables.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Del mismo modo, el icono de vídeo tiene su propia configuración que contiene un puntero al vídeo para que se reproduzca, una configuración de reproducción automática e información de título opcional.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="asset"></a>Recurso

Los iconos que están enlazados a objetos del portal de primera clase administrables (denominados recursos) expresan esta relación mediante el objeto asset.  En el panel de ejemplo, el icono de la máquina virtual contiene esta descripción del recurso.  La propiedad __idInputName__ indica al portal que la entrada del identificador contiene el identificador único para el activo, en este caso, el identificador del recurso. La mayoría de los tipos de recursos tienen activos definidos en el portal.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a crear un panel en [Azure Portal](azure-portal-dashboards.md) o [mediante programación](azure-portal-dashboards-create-programmatically.md).
- Aprenda a [usar iconos de markdown en los paneles de Azure para mostrar contenido personalizado](azure-portal-markdown-tile.md).
