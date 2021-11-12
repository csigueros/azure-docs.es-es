---
title: Creación mediante programación de paneles de Azure
description: Use un panel de Azure Portal como plantilla para crear paneles de Azure mediante programación. Incluye referencia JSON.
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 15d94cf5efc857adf9bbc0cf98c9e3d34e5bda7d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239834"
---
# <a name="programmatically-create-azure-dashboards"></a>Creación mediante programación de paneles de Azure

Este artículo le guía por el proceso de creación y publicación de paneles de Azure mediante programación. En el documento se hace referencia al panel que se muestra a continuación.

:::image type="content" source="media/azure-portal-dashboards-create-programmatically/sample-dashboard.png" alt-text="Captura de pantalla de un panel de ejemplo en Azure Portal.":::

## <a name="overview"></a>Información general

Los paneles compartidos de [Azure Portal](https://portal.azure.com) son [recursos](../azure-resource-manager/management/overview.md), lo mismo que las máquinas virtuales y las cuentas de almacenamiento. Puede administrar los recursos mediante programación con las [API de REST de Azure Resource Manager](/rest/api/) la [CLI de Azure](/cli/azure) y los [comandos de Azure PowerShell](/powershell/azure/get-started-azureps).

Muchas características se basan en estas API para facilitar la administración de recursos. Cada una de estas API y herramientas ofrece formas de crear, enumerar, recuperar, modificar y eliminar recursos. Puesto que los paneles son recursos, puede elegir la API o la herramienta favorita que vaya a usar.

Independientemente de las herramientas que use, para crear un panel mediante programación, debe construir una representación JSON del objeto de panel. Este objeto contiene información sobre los iconos del panel. Incluye tamaños, posiciones, recursos a los que están enlazados y cualquier personalización del usuario.

La manera más práctica de generar este documento JSON consiste en usar Azure Portal para crear un panel inicial con los iconos deseados. Después, exporte el código JSON y cree una plantilla a partir del resultado que puede modificar para usarla en scripts, programas y herramientas de implementación.

## <a name="create-a-dashboard"></a>Creación de un panel

Para crear un panel, seleccione **Panel** en el menú de [Azure Portal](https://portal.azure.com) y luego **Nuevo panel**.

Para más información, vea [Creación de un panel en Azure Portal](azure-portal-dashboards.md).

## <a name="share-the-dashboard"></a>Uso compartido del panel

Después de configurar el panel, el siguiente paso es publicar el panel con el comando **Compartir**.

Al compartir un panel, tendrá que elegir en qué suscripción y grupo de recursos se va a publicar. Debe tener acceso de escritura a la suscripción y al grupo de recursos que elija. Para más información, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).

Para más información, vea [Uso compartido de paneles de Azure mediante el control de acceso basado en rol de Azure](azure-portal-dashboard-share-access.md).

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Captura de la representación JSON del panel

Compartir el panel solo tarda unos segundos. Cuando haya terminado, el paso siguiente consiste en exportar el código JSON mediante el comando **Descargar**.

:::image type="content" source="media/azure-portal-dashboards-create-programmatically/download-command.png" alt-text="Captura de pantalla del comando para exportar la representación JSON de una plantilla en Azure Portal.":::

## <a name="create-a-template-from-the-json"></a>Creación de una plantilla a partir de JSON

El siguiente paso es crear una plantilla a partir de este JSON. Podrá usar la plantilla mediante programación con las API de administración de recursos adecuadas, las herramientas de línea de comandos o en el portal.

No es necesario comprender perfectamente la estructura JSON del panel para crear una plantilla. En la mayoría de los casos, se quiere conservar la estructura y la configuración de cada icono. Luego, parametrice el conjunto de recursos de Azure a los que señalan los iconos.

Examine el panel JSON exportado y busque todas las repeticiones de los identificadores de recursos de Azure. El panel de ejemplo tiene varios iconos que señalan todos a una única máquina virtual de Azure. Esto se debe a que el panel solo examina este recurso. Si busca "/subscriptions" en el código JSON de ejemplo, incluido al final del documento, encontrará varias repeticiones de este identificador.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Para publicar este panel para cualquier máquina virtual en el futuro, parametrice todas las repeticiones de esta cadena en el JSON.

Hay dos enfoques para las API que crean recursos en Azure:

- Las API imperativas crean un recurso cada vez. Para obtener más información, consulte [Recursos](/rest/api/resources/resources).
- Un sistema de implementación basado en plantillas que crea varios recursos dependientes con una única llamada API. Para obtener más información, vea [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

La implementación basada en plantillas admite la parametrización y las plantillas. En este artículo se usa este enfoque.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Creación mediante programación de un panel a partir de la plantilla mediante una implementación de plantilla

Azure ofrece la capacidad de organizar la implementación de varios recursos. Cree una plantilla de implementación que exprese el conjunto de recursos que se va a implementar, así como las relaciones entre ellos.  El formato JSON de los recursos es el mismo que si estuviera creándolos uno a uno. La diferencia es que el lenguaje de plantilla agrega algunos conceptos como variables, parámetros, funciones básicas, etc. Esta sintaxis extendida solo se admite en el contexto de una implementación de plantilla. No funciona si se usa con las API imperativas descritas anteriormente. Para obtener más información, vea [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](../azure-resource-manager/templates/syntax.md).

La parametrización se debe realizar mediante la sintaxis de parámetros de la plantilla.  Reemplace todas las instancias del identificador del recurso encontrado anteriormente como se muestra aquí.

Ejemplo de propiedad JSON con el identificador de recurso codificado de forma rígida:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Ejemplo de propiedad JSON convertida a una versión con parámetros según los parámetros de plantilla

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Declare los metadatos de plantilla requeridos y los parámetros en la parte superior de la plantilla JSON del modo siguiente:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

Una vez configurada la plantilla, impleméntela con cualquiera de los métodos siguientes:

- [API de REST](/rest/api/resources/deployments)
- [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [CLI de Azure](/cli/azure/group/deployment#az_group_deployment_create)
- [Página de implementación de plantillas de Azure Portal](https://portal.azure.com/#create/Microsoft.Template)

A continuación, verá dos versiones de nuestro JSON del panel de ejemplo. La primera es la versión que hemos exportado desde el portal y que ya estaba enlazada a un recurso. La segunda es la versión de la plantilla que se puede enlazar mediante programación a cualquier máquina virtual e implementar con Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard"></a>Representación JSON del panel de ejemplo

Este ejemplo es similar al que verá si ha seguido este artículo. Las instrucciones han exportado la representación JSON de un panel que ya está implementado. Los identificadores de recursos codificados de forma rígida muestran que este panel señala a una máquina virtual de Azure específica.

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
        "metadata": { }
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

### <a name="template-representation-of-our-example-dashboard"></a>Representación de la plantilla de nuestro panel de ejemplo

La versión de plantilla del panel tiene definidos tres parámetros denominados `virtualMachineName`, `virtualMachineResourceGroup` y `dashboardName`.  Los parámetros permiten que este panel señale a una máquina virtual de Azure diferente cada vez que realice una implementación. Este panel puede configurarse e implementarse mediante programación de modo que señale a cualquier máquina virtual de Azure. Para probar esta característica, copie la plantilla siguiente y péguela en la [página de implementación de plantillas de Azure Portal](https://portal.azure.com/#create/Microsoft.Template).

En este ejemplo se implementa un panel por sí mismo, pero el lenguaje de plantilla le permite implementar varios recursos e incluir uno o más paneles junto a ellos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

Ahora que ha visto un ejemplo del uso de una plantilla con parámetros para implementar un panel, puede probar a implementar la plantilla mediante las [ API REST de Azure Resource Manager](/rest/api/), la [CLI de Azure](/cli/azure) o los [comandos de Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Creación mediante programación de un panel mediante la CLI de Azure

Prepare el entorno para la CLI de Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- En estos ejemplos se utiliza el siguiente panel: [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Asegúrese de reemplazar todo el contenido entre corchetes angulares por valores propios.

Ejecute el comando [az portal dashboard create](/cli/azure/portal/dashboard#az_portal_dashboard_create) para crear un panel en función de la plantilla:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Puede actualizar un panel mediante el comando [az portal dashboard update](/cli/azure/portal/dashboard#az_portal_dashboard_update):

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Consulte los detalles de un panel ejecutando el comando [az portal dashboard show](/cli/azure/portal/dashboard#az_portal_dashboard_show):

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Para ver todos los paneles de la suscripción actual, use [az portal dashboard list](/cli/azure/portal/dashboard#az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

También puede ver todos los paneles de un grupo de recursos:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [usar iconos de markdown en los paneles de Azure para mostrar contenido personalizado](azure-portal-markdown-tile.md).
- Explore todos los comandos de la CLI de Azure para los paneles, vea [az portal dashboard](/cli/azure/portal/dashboard).
- Aprenda a [administrar las preferencias y los valores de Azure Portal](set-preferences.md).
