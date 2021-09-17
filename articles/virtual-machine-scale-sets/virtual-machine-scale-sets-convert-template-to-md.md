---
title: Conversión de una plantilla de conjunto de escalado para que use un disco administrado
description: Convierta una plantilla de conjunto de escalado de máquinas virtuales de Azure Resource Manager en una plantilla de conjunto de escalado de discos administrados.
keywords: conjuntos de escalado de máquinas virtuales
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 2cf6fc21baae657b28b7d3edfb242ee1bcef407c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690511"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Conversión de una plantilla de conjunto de escalado en una plantilla de conjunto de escalado de un disco administrado

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado uniformes

Los clientes con una plantilla de Resource Manager para crear un conjunto de escalado no sin usar un disco administrado pueden modificarla para usar un disco administrado. En este artículo se muestra cómo hacerlo, con el ejemplo de una solicitud de incorporación de cambios de las [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates), un repositorio basado en la comunidad de las plantillas de Resource Manager de ejemplo. La solicitud de incorporación de cambios completa se puede ver en [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998) y las partes relevantes de la diferencia pueden encontrarse a continuación, junto con una explicación:

## <a name="making-the-os-disks-managed"></a>Administración de los discos de sistema operativo

En la diferencia siguiente, se quitan varias variables relacionadas con las propiedades de la cuenta y el disco de almacenamiento. El tipo de cuenta de almacenamiento ya no es necesario (Standard_LRS es el valor predeterminado), pero puede especificarlo si así lo desea. Standard_LRS y Premium_LRS son los únicos valores compatibles con los discos administrados. El sufijo de la nueva cuenta de almacenamiento, la matriz de cadena única y el recuento de sa se utilizaban en la plantilla antigua para generar nombres de cuenta de almacenamiento. Estas variables no son necesarias en la nueva plantilla, ya que el disco administrado crea automáticamente cuentas de almacenamiento en nombre del cliente. De igual forma, el nombre del contenedor de VHD y el nombre del disco de sistema operativo no son necesarios, porque el disco administrado asigna automáticamente nombres a los discos y contenedores de blobs de almacenamiento subyacentes.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


En la diferencia siguiente, se actualiza la API de proceso a la versión 2016-04-30-preview, que es la primera versión necesaria para la compatibilidad de los discos administrados con conjuntos de escalado. Si lo desea, puede usar discos no administrados en la nueva versión de la API con la sintaxis anterior. Si solo actualiza la versión de la API de proceso, pero no cambia nada más, la plantilla debería seguir funcionando como antes.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

En la diferencia siguiente, el recurso de la cuenta de almacenamiento se quita completamente de la matriz de recursos. El recurso ya no es necesario dado que el disco administrado lo crea automáticamente.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

En la diferencia siguiente, se puede ver que lo que se quita depende de la cláusula a la que se hace referencia desde el conjunto de escalado hasta el bucle que ha creado las cuentas de almacenamiento. En la plantilla antigua, esto garantizaba que las cuentas de almacenamiento se creaban antes de que el conjunto de escalado comenzara la creación, pero esta cláusula no es necesaria con el disco administrado. También se quita la propiedad de contenedores de VHD, junto con la propiedad de nombre de disco de sistema operativo, ya que estas propiedades las administra automáticamente el disco administrado. Se podría agregar `"managedDisk": { "storageAccountType": "Premium_LRS" }` en la configuración de "osDisk" si quisiera discos del sistema operativo Premium. Las únicas máquinas virtuales que pueden usar discos Premium son las que tienen una "s" mayúscula o minúscula en la SKU de la máquina virtual.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

No hay ninguna propiedad explícita en la configuración del conjunto de escalado relativa al uso de un disco administrado o no administrado. El conjunto de escalado sabe cuál se debe usar gracias a las propiedades del perfil de almacenamiento. Por consiguiente, al modificar la plantilla es importante asegurarse de que el perfil de almacenamiento del conjunto de escalado contiene las propiedades correctas.


## <a name="data-disks"></a>Discos de datos.

Con los cambios anteriores, el conjunto de escalado utiliza discos administrados para el disco del SO, pero ¿y para los discos de datos? Para agregar discos de datos, agregue la propiedad "dataDisks" en "storageProfile" al mismo nivel que "osDisk". El valor de la propiedad es una lista JSON de objetos, cada uno de los cuales tiene propiedades "lun" (que deben ser únicas para cada disco de datos de una máquina virtual), "createOption" ("empty" es actualmente la única opción admitida) y "diskSizeGB" (el tamaño del disco en gigabytes; debe ser mayor que 0 y menor que 1024) como en el ejemplo siguiente:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Si especifica `n` discos en esta matriz, cada máquina virtual del conjunto de escalado obtiene `n` discos de datos. Sin embargo, tenga en cuenta que estos discos de datos son dispositivos sin formato. Es responsabilidad del cliente asociar los discos, crearles particiones y formatearlos antes de usarlos. Opcionalmente, también se puede especificar `"managedDisk": { "storageAccountType": "Premium_LRS" }` en cada objeto del disco de datos para indicar que debe ser un disco de datos Premium. Las únicas máquinas virtuales que pueden usar discos Premium son las que tienen una "s" mayúscula o minúscula en la SKU de la máquina virtual.

Para más información acerca del uso de discos de datos con conjuntos de escalado, consulte [este artículo](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Pasos siguientes
Para las plantillas de Azure Resource Manager de ejemplo que usan conjuntos de escalado, busque "vmss" en el [repositorio de GitHub de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates).

Para información general, consulte la [página de destino principal de los conjuntos de escalado](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

