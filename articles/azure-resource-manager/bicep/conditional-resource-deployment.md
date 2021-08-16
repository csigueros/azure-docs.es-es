---
title: Implementación condicional con Bicep
description: Se describe cómo implementar un recurso de forma condicional en Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 9636444af81b000443dc72cf6e3fc1d8d6da5093
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537084"
---
# <a name="conditional-deployment-in-bicep"></a>Implementación condicional en Bicep

En algunas ocasiones, es necesario implementar opcionalmente un recurso en Bicep. Use la palabra clave `if` para especificar si el recurso está implementado. El valor de la condición se resuelve como true o false. Cuando el valor es true, el recurso se crea. Cuando el valor es false, el recurso no se crea. El valor solo se puede aplicar a todo el recurso.

> [!NOTE]
> La implementación condicional no se aplica en cascada a los [recursos secundarios](child-resource-name-type.md). Si desea implementar condicionalmente un recurso y sus recursos secundarios, debe aplicar la misma condición a cada tipo de recurso.

## <a name="deploy-condition"></a>Condición de implementación

Puede pasar un valor de parámetro que indica si un recurso está implementado. En el ejemplo siguiente se implementa una zona DNS de forma condicional.

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

Las condiciones se pueden usar con declaraciones de dependencia. Si el identificador del recurso condicional se especifica en el elemento `dependsOn` de otro recurso (dependencia explícita), la dependencia se omite si la condición se evalúa como falsa en el momento de implementación de la plantilla. Si la condición se evalúa como verdadera, se respeta la dependencia. Se permite hacer referencia a una propiedad de un recurso condicional (dependencia implícita), pero, en algunos casos, se puede producir un error en tiempo de ejecución.

## <a name="new-or-existing-resource"></a>Recurso nuevo o existente

Puede usar la implementación condicional para crear un recurso nuevo o usar uno existente. En el ejemplo siguiente se muestra cómo implementar una cuenta de almacenamiento nueva o usar una existente.

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

Cuando el parámetro `newOrExisting` está establecido en **new**, la condición se evalúa como true. Se implementa la cuenta de almacenamiento. Sin embargo, cuando `newOrExisting` está establecido **existing**, la condición se evalúa como false y no se implementa la cuenta de almacenamiento.

Para una plantilla de ejemplo completo que usa el elemento `condition`, consulte [VM with a new or existing Virtual Network, Storage, and Public IP](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-new-or-existing-conditions) (Máquina virtual con una red virtual nueva o existente, almacenamiento y dirección IP pública).

## <a name="runtime-functions"></a>Funciones en tiempo de ejecución

Si usa una función [reference](./bicep-functions-resource.md#reference) o [list](./bicep-functions-resource.md#list) con un recurso que se implementa de forma condicional, se puede evaluar la función incluso si el recurso no está implementado. Se genera un error si la función hace referencia a un recurso que no existe.

Use el operador de [expresión condicional ?:](./operators-logical.md#conditional-expression--) para asegurarse de que la función solo se evalúa en las condiciones cuando se implementa el recurso. En la plantilla de ejemplo siguiente se muestra cómo usar esta función con expresiones que solo son válidas bajo ciertas condiciones.

```bicep
param vmName string
param location string
param logAnalytics string = ''

resource vmName_omsOnboarding 'Microsoft.Compute/virtualMachines/extensions@2017-03-30' = if (!empty(logAnalytics)) {
  name: '${vmName}/omsOnboarding'
  location: location
  properties: {
    publisher: 'Microsoft.EnterpriseCloud.Monitoring'
    type: 'MicrosoftMonitoringAgent'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    settings: {
      workspaceId: ((!empty(logAnalytics)) ? reference(logAnalytics, '2015-11-01-preview').customerId : json('null'))
    }
    protectedSettings: {
      workspaceKey: ((!empty(logAnalytics)) ? listKeys(logAnalytics, '2015-11-01-preview').primarySharedKey : json('null'))
    }
  }
}

output mgmtStatus string = ((!empty(logAnalytics)) ? 'Enabled monitoring for VM!' : 'Nothing to enable')
```

Puede establecer un [recurso como dependiente](./resource-declaration.md#set-resource-dependencies) en un recurso condicional exactamente como lo haría con cualquier otro recurso. Cuando un recurso condicional no está implementado, Azure Resource Manager lo quita automáticamente de las dependencias necesarias.

## <a name="next-steps"></a>Pasos siguientes

* Puede encontrar un módulo de Microsoft Learn sobre condiciones y bucles en [Creación de plantillas de Bicep flexibles mediante condiciones y bucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/).
* Puede encontrar recomendaciones sobre cómo crear archivos de Bicep en [Procedimientos recomendados para Bicep](best-practices.md).
* Para crear varias instancias de un recurso, consulte [Iteración de recursos en Bicep](loop-resources.md).
