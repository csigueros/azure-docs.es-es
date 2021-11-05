---
title: Modificación de un tipo de nodo de clúster administrado de Service Fabric
description: En este artículo se explica cómo modificar un tipo de nodo de clúster administrado.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: bd39c3810b1ecc1c6174e80a6719f2422aa72bb4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090846"
---
# <a name="service-fabric-managed-cluster-node-types"></a>Tipos de nodo de clúster administrado de Service Fabric

Cada tipo de nodo de un clúster administrado de Service Fabric se encuentra respaldado por un conjunto de escalado de máquinas virtuales. Con los clústeres administrados, puede realizar los cambios necesarios a través del proveedor de recursos de clúster administrado de Service Fabric. El proveedor de clústeres administrados crea y abstrae todos los recursos subyacentes del clúster en su nombre. Esto ayuda a simplificar la implementación y administración de tipos de nodo de clúster, evitar errores de operación, tales como eliminar un nodo de inicialización, y ayuda a aplicar los procedimientos recomendados, tales como validar una SKU de máquina virtual segura.

En el resto de este documento se explica cómo ajustar varias opciones desde la creación de un tipo de nodo, el ajuste del número de instancias de tipo de nodo, la habilitación de actualizaciones automáticas de imágenes del sistema operativo, el cambio de la imagen del sistema operativo y la configuración de las propiedades de selección de ubicación. Este documento también se centrará en el uso de las plantillas de Azure Portal o Azure Resource Manager para realizar cambios.

> [!NOTE]
> No podrá modificar el tipo de nodo mientras haya un cambio en curso. Se recomienda dejar que se complete cualquier cambio solicitado antes de realizar otro.


## <a name="add-or-remove-a-node-type-with-portal"></a>Adición o eliminación de un tipo de nodo con el portal

En este tutorial, aprenderá a agregar o quitar un tipo de nodo utilizando el portal.

**Para agregar un tipo de nodo:**
1) Inicie sesión en [Azure Portal](https://portal.azure.com/).

2) Vaya a la página de Información general del recurso de clúster. 
![Ejemplo de página de Información general][overview]

3) Seleccione`Node types` bajo la `Settings` sección ![Visualización de los tipos de nodo][addremove]

4) Haga clic en `Add`en la parte superior, rellene la información necesaria y, a continuación, haga clic en Agregar en la parte inferior, eso es todo.


**Para quitar un tipo de nodo:**
1) Inicie sesión en [Azure Portal](https://portal.azure.com/).

2) Vaya a la página de Información general del recurso de clúster. 
![Ejemplo de página de Información general][overview]

3) Seleccione`Node types` bajo la `Settings` sección ![Visualización de los tipos de nodo][addremove]

4) Seleccione el `Node Type` que desea quitar y haga clic en `Delete`en la parte superior.

> [!NOTE]
> No es posible quitar un tipo de nodo principal si es el único tipo de nodo principal del clúster.


## <a name="add-a-node-type-with-a-template"></a>Adición de un tipo de nodo con una plantilla

**Para agregar un tipo de nodo mediante una plantilla de ARM**

Agregue otro tipo de recurso `Microsoft.ServiceFabric/managedclusters/nodetypes` con los valores necesarios y realice una implementación de clúster para que la configuración surja efecto.

* El valor de apiVersion del recurso de clúster administrado de Service Fabric debe ser **2021-05-01** o posterior.

```json
          {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeType2Name'))]",
            "location": "[resourcegroup().location]",
            "dependsOn": [
              "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
            ],
            "properties": {
                "isPrimary": false,
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                "vmSize": "[parameters('nodeType2VmSize')]",
                "vmInstanceCount": "[parameters('nodeType2VmInstanceCount')]",
                "dataDiskSizeGB": "[parameters('nodeType2DataDiskSizeGB')]",
                "dataDiskType": "[parameters('nodeType2managedDataDiskType')]"
           }
```
Para obtener un ejemplo de configuración de dos tipos de nodo, consulte nuestra [plantilla de ARM de ejemplo de dos tipos de nodo.](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/SF-Managed-Standard-SKU-2-NT)


## <a name="scale-a-node-type-manually-with-portal"></a>Escalado manual de un tipo de nodo con el portal

En este tutorial, aprenderá a modificar el número de nodos para un tipo de nodo utilizando el portal.

1) Inicie sesión en [Azure Portal](https://portal.azure.com/).

2) Vaya a la página de Información general del recurso de clúster. 
![Ejemplo de página de Información general][overview]

3) Seleccione`Node Types` en la sección `Settings`. 

4) Seleccione el `Node type name`que desea modificar.

5) Ajuste el `Node count` al nuevo valor que desea y seleccione`Apply` en la parte inferior. En esta captura de pantalla, el valor era `3`y se ajustó a `5`.
![Ejemplo que muestra un aumento del número de nodos][adjust-node-count]

6) En `Provisioning state`, el estado se mostrará como`Updating` hasta que se haya completado la operación. Una vez finalizada la operación, mostrará `Succeeded` de nuevo.
![Ejemplo que muestra la actualización de un tipo de nodo][node-type-updating]


## <a name="scale-a-node-type-manually-with-a-template"></a>Escalado manual de un tipo de nodo utilizando una plantilla

Para ajustar el número de nodos para un tipo de nodo mediante una plantilla de ARM, ajuste la `vmInstanceCount`propiedad con el nuevo valor y realice una implementación de clúster para que la configuración surja efecto.

* El valor de apiVersion del recurso de clúster administrado de Service Fabric debe ser **2021-05-01** o posterior.

> [!NOTE]
> El proveedor de clústeres administrados bloqueará los ajustes de escala y devolverá un error si la solicitud de escalado infringe los mínimos necesarios.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                ...
                "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
                ...
            }
        }
}
```

## <a name="enable-automatic-os-image-upgrades"></a>Habilitar las actualizaciones automáticas de las imágenes del sistema operativo

Puede elegir habilitar las actualizaciones automáticas de imágenes del sistema operativo en las máquinas virtuales que ejecutan los nodos de clúster administrados. Aunque los recursos del conjunto de escalado de máquinas virtuales se administran en su nombre con los clústeres administrados, es su elección habilitar actualizaciones automáticas de imágenes de sistema operativo para los nodos del clúster. Al igual que con los clústeres de [Service fabric clásicos](service-fabric-best-practices-infrastructure-as-code.md#virtual-machine-os-automatic-upgrade-configuration), los nodos de clústeres administrados no se actualizan de forma predeterminada, con el fin de evitar interrupciones imprevistas en el clúster.

Para habilitar las actualizaciones automáticas del sistema operativo:

* Utilice la versión `2021-05-01` (o posterior) de los recursos *Microsoft.ServiceFabric/managedclusters* y *Microsoft.ServiceFabric/managedclusters/nodetypes*.
* Establezca la propiedad `enableAutoOSUpgrade` del clúster en *true*.
* Establezca la propiedad del recurso nodeTypes del clúster `vmImageVersion` en *latest*.

Por ejemplo:

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Una vez habilitado, Service Fabric comenzará a consultar y realizar el seguimiento de las versiones de imagen del sistema operativo en el clúster administrado. Si hay disponible una nueva versión del sistema operativo, se actualizarán los tipos de nodo de clúster (conjuntos de escalado de máquinas virtuales), de uno en uno. Las actualizaciones en tiempo de ejecución de Service Fabric solo se realizan después de confirmar que no hay actualizaciones de imágenes de sistema operativo de nodo de clúster en curso.

Si se produce un error en una actualización, Service Fabric lo volverá a intentar después de 24 horas, con un máximo de tres reintentos. Al igual que las actualizaciones de Service Fabric clásico (no administradas), las aplicaciones o los nodos incorrectos pueden bloquear la actualización de la imagen del sistema operativo.

Para más información sobre las actualizaciones de imágenes, consulte [Actualizaciones automáticas de imágenes de sistema operativo con conjuntos de escalado de máquinas virtuales de Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="modify-the-os-image-for-a-node-type-with-portal"></a>Modificación de la imagen del sistema operativo para un tipo de nodo utilizando el portal

En este tutorial, aprenderá a modificar la imagen del sistema operativo para un tipo de nodo utilizando el portal.

1) Inicie sesión en [Azure Portal](https://portal.azure.com/).

2) Vaya a la página de Información general del recurso de clúster. 
![Ejemplo de página de Información general][overview]

3) Seleccione`Node Types` en la sección `Settings`. 

4) Seleccione el `Node type name`que desea modificar.

5) Ajuste el `OS Image` al nuevo valor que desea y seleccione`Apply` en la parte inferior. ![Ejemplo que muestra cómo cambiar la imagen del sistema operativo] [change-os-image]!

6) Ahora `Provisioning state` mostrará un estado de `Updating` y continuará con un dominio de actualización a la vez. Una vez finalizada la operación, mostrará `Succeeded` de nuevo.
![Ejemplo que muestra la actualización de un tipo de nodo][node-type-updating]


## <a name="modify-the-os-image-for-a-node-type-with-a-template"></a>Modificación de la imagen del sistema operativo para un tipo de nodo utilizando una plantilla

Para modificar la imagen del sistema operativo utilizada para un tipo de nodo mediante una plantilla de ARM, ajuste la `vmImageSku`propiedad con el nuevo valor y realice una implementación de clúster para que la configuración surja efecto. El proveedor de clústeres administrados modificará la imagen de cada instancia por dominio de actualización.

* El valor de apiVersion del recurso de clúster administrado de Service Fabric debe ser **2021-05-01** o posterior.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                ...
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                ...
            }
        }
}
```

## <a name="configure-placement-properties-for-a-node-type-with-portal"></a>Configuración de las propiedades de selección de ubicación para un tipo de nodo utilizando el portal

En este tutorial, aprenderá a modificar la propiedad de selección de ubicación para un tipo de nodo utilizando el portal.

1) Inicie sesión en [Azure Portal](https://portal.azure.com/).

2) Vaya a la página de Información general del recurso de clúster. 
![Ejemplo de página de Información general][overview]

3) Seleccione`Node Types` en la sección `Settings`. 

4) Seleccione el `Node type name`que desea modificar.

5) En la sección `Placement properties` agregue el nombre y el valor que desee y, a continuación, seleccione `Apply` en la parte inferior. En esta captura de pantalla, `Name``SSD_Premium` se utilizó con `Value` de `true` .
![Ejemplo que muestra cómo agregar una propiedad de selección de ubicación][nodetype-placement-property]

6) En `Provisioning state`, el estado se mostrará como`Updating` hasta que se haya completado la operación. Una vez finalizada la operación, mostrará `Succeeded` de nuevo.
![Ejemplo que muestra la actualización de un tipo de nodo][node-type-updating]

Ahora puede utilizar esa [propiedad de selección de ubicación asegurarse de que ciertas cargas de trabajo solo se ejecuten en determinados tipos de nodos en el clúster](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). 

## <a name="configure-placement-properties-for-a-node-type-with-a-template"></a>Configuración de las propiedades de selección de ubicación para un tipo de nodo utilizando una plantilla

Para ajustar las propiedades de selección de ubicación para un tipo de nodo mediante una plantilla de ARM, ajuste la `placementProperties`propiedad con el nuevo valor o valores y realice una implementación de clúster para que la configuración surja efecto. En el ejemplo siguiente se muestran tres valores que se establecen para un tipo de nodo.

* El valor de apiVersion del recurso de clúster administrado de Service Fabric debe ser **2021-05-01** o posterior.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "placementProperties": {
                    "PremiumSSD": "true",
                    "NodeColor": "green",
                    "SomeProperty": "5"
            }
        }
}
```
Ahora puede utilizar esa [propiedad de selección de ubicación asegurarse de que ciertas cargas de trabajo solo se ejecuten en determinados tipos de nodos en el clúster](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). 

## <a name="modify-the-vm-sku-for-a-node-type"></a>Modificación de la SKU de máquina virtual para un tipo de nodo

El clúster administrado de Service Fabric no admite la modificación local de la SKU de máquina virtual, pero sigue siendo muy sencillo. Para ello, debe hacer lo siguiente:
* [Cree un nuevo tipo de nodo](how-to-managed-cluster-modify-node-type.md#add-or-remove-a-node-type-with-portal) con la SKU de máquina virtual que necesita.
* Migre la carga de trabajo. Una forma es utilizar una [propiedad de selección de ubicación asegurarse de que ciertas cargas de trabajo solo se ejecuten en determinados tipos de nodos en el clúster](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). 
* [Borre el tipo de nodo anterior](how-to-managed-cluster-modify-node-type.md#add-or-remove-a-node-type-with-portal)


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Escalado automático de un nodo de clúster administrado de Service Fabric](how-to-managed-cluster-autoscale.md)
> [!div class="nextstepaction"]
> [Opciones de configuración del clúster administrado de Service Fabric](how-to-managed-cluster-configuration.md)
> [!div class="nextstepaction"]
> [Implementación de una aplicación en un clúster administrado de Service Fabric](./tutorial-managed-cluster-deploy-app.md)


[overview]: ./media/how-to-managed-cluster-modify-node-type/sfmc-overview.png
[node-type-updating]: ./media/how-to-managed-cluster-modify-node-type/sfmc-adjust-node-type-updating.png
[adjust-node-count]: ./media/how-to-managed-cluster-modify-node-type/sfmc-adjust-node-counts.png
[change-nodetype-os-image]: ./media/how-to-managed-cluster-modify-node-type/sfmc-change-os-image.png
[nodetype-placement-property]: ./media/how-to-managed-cluster-modify-node-type/sfmc-nodetype-placement-property.png
[addremove]: ./media/how-to-managed-cluster-modify-node-type/sfmc-addremove-node-type.png

