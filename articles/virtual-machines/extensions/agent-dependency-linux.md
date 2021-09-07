---
title: Extensión de máquina virtual de Azure Monitor Dependency para Linux
description: Implemente Dependency Agent de Azure Monitor en la máquina virtual Linux con una extensión de máquina virtual.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/01/2021
ms.openlocfilehash: ce4b7ee2fea9d5b2b7c92b5ade1b3ad146382214
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744981"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Extensión de máquina virtual de Azure Monitor Dependency para Linux

La característica Service Map de Azure Monitor para VM obtiene sus datos de Microsoft Dependency Agent. Microsoft, como editor de la extensión de máquina virtual de Dependency Agent de Azure VM para Linux, es quien presta los servicios de soporte técnico para esta solución. La extensión instala Dependency Agent en las máquinas virtuales de Azure. En este documento se especifican las plataformas compatibles, las configuraciones y las opciones de implementación de la extensión de máquina virtual de Dependency Agent de Azure VM para Linux.

## <a name="prerequisites"></a>Prerrequisitos

### <a name="operating-system"></a>Sistema operativo

La extensión de Dependency Agent de Azure VM para Linux se puede ejecutar en los sistemas operativos compatibles que se indican en la sección [Sistemas operativos compatibles](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) del artículo de implementación de Azure Monitor para VM.

## <a name="extension-schema"></a>Esquema de extensión

El siguiente JSON muestra el esquema de la extensión de Dependency Agent de Azure VM en una máquina virtual Linux de Azure.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Valores de propiedad

| Nombre | Valor/ejemplo |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Implementación de plantilla

Puede implementar las extensiones de VM de Azure con plantillas de Azure Resource Manager. Puede usar el esquema JSON detallado en la sección anterior en una plantilla de Azure Resource Manager para ejecutar la extensión de Dependency Agent de Azure VM durante la implementación de dicha plantilla.

El JSON de una extensión de máquina virtual se puede anidar dentro del recurso de máquina virtual. O bien, puede colocarlo en la raíz o en el nivel superior de una plantilla JSON de Resource Manager. La colocación de la plantilla JSON afecta al valor del nombre y tipo del recurso. Para obtener más información, consulte el artículo sobre cómo [establecer el nombre y el tipo de recursos secundarios](../../azure-resource-manager/templates/child-resource-name-type.md).

En el siguiente ejemplo se da por supuesto que la extensión de Dependency Agent está anidada dentro del recurso de máquina virtual. Cuando se anidan los recursos de extensión, el JSON se coloca en el objeto `"resources": []` de la máquina virtual.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Al colocar el JSON de la extensión en la raíz de la plantilla, el nombre de recurso incluye una referencia a la máquina virtual principal. El tipo refleja la configuración anidada.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Implementación de la CLI de Azure

Puede usar la CLI de Azure para implementar la extensión de máquina virtual de Dependency Agent en una máquina virtual que ya tenga.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5
```

## <a name="automatic-extension-upgrade"></a>Actualización automática de extensiones
Ahora hay disponible una nueva característica para [actualizar automáticamente las versiones secundarias](../automatic-extension-upgrade.md) de la extensión Dependency.

Para habilitar la actualización automática de extensiones para una extensión, debe asegurarse de que la propiedad `enableAutomaticUpgrade` esté establecida en `true` y de que se haya agregado a la plantilla de extensiones. Esta propiedad debe habilitarse individualmente en cada VM o conjunto de escalado de VM. Use uno de los métodos descritos en la sección [habilitación](../automatic-extension-upgrade.md#enabling-automatic-extension-upgrade) para habilitar la característica para la VM o el conjunto de escalado de VM.

Cuando la actualización automática de extensiones está habilitada en una VM o en un conjunto de escalado de VM, la extensión se actualiza automáticamente cada vez que el editor de la extensión publica una nueva versión de esta. La actualización se aplica de forma segura siguiendo los principios de orden de disponibilidad, como se describe [aquí](../automatic-extension-upgrade.md#how-does-automatic-extension-upgrade-work).

La funcionalidad del atributo `enableAutomaticUpgrade` es diferente de la de `autoUpgradeMinorVersion`. Los atributos `autoUpgradeMinorVersion` no desencadenan automáticamente una actualización de una versión secundaria cuando el editor de la extensión publica una nueva versión. El atributo `autoUpgradeMinorVersion` indica si la extensión debe usar una versión secundaria más reciente si hay una disponible en el momento de la implementación. Sin embargo, una vez implementada, la extensión no actualizará las versiones secundarias a menos que se vuelva a implementar, incluso con esta propiedad establecida en true.

Para mantener actualizada la versión de la extensión, se recomienda usar `enableAutomaticUpgrade` con la implementación de la extensión.

> [!IMPORTANT]
> Si agrega `enableAutomaticUpgrade` a la plantilla, asegúrese de usar la versión de API 2019-12-01 o superior.

## <a name="troubleshoot-and-support"></a>Solución de problemas y asistencia

### <a name="troubleshoot"></a>Solución de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal y mediante la CLI de Azure. Para ver el estado de implementación de las extensiones de una máquina virtual determinada, ejecute el comando siguiente con la CLI de Azure:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

El resultado de la ejecución de las extensiones se registra en el archivo siguiente:

```
/opt/microsoft/dependency-agent/log/install.log
```

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, póngase en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/). O bien, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte**. Para obtener más información sobre cómo usar el soporte técnico de Azure, lea las [preguntas más frecuentes del soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).
