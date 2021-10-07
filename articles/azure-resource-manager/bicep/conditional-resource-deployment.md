---
title: Implementación condicional con Bicep
description: Se describe cómo implementar un recurso de forma condicional en Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: be7e52cb093eb9920be5b6e8e9971b7f882f7f06
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793842"
---
# <a name="conditional-deployment-in-bicep"></a>Implementación condicional en Bicep

En algunas ocasiones, debe implementar un recurso o módulo en Bicep de manera condicional. Use la palabra clave `if` para especificar si el recurso está implementado. El valor de la condición se resuelve como true o false. Cuando el valor es true, el recurso se crea. Cuando el valor es false, el recurso no se crea. El valor solo se puede aplicar a todo el recurso o módulo.

> [!NOTE]
> La implementación condicional no se aplica en cascada a los [recursos secundarios](child-resource-name-type.md). Si desea implementar condicionalmente un recurso y sus recursos secundarios, debe aplicar la misma condición a cada tipo de recurso.

### <a name="microsoft-learn"></a>Microsoft Learn

Para más información sobre las condiciones y para obtener una guía práctica, consulte [Creación de plantillas de Bicep flexibles mediante condiciones y bucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) en **Microsoft Learn**.

## <a name="deploy-condition"></a>Condición de implementación

Puede pasar un valor de parámetro que indica si un recurso está implementado. En el ejemplo siguiente se implementa una zona DNS de forma condicional.

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

En el ejemplo siguiente se implementa un módulo de forma condicional.

```bicep
param deployZone bool

module dnsZone 'dnszones.bicep' = if (deployZone) {
  name: 'myZoneModule'
}
```

Las condiciones se pueden usar con declaraciones de dependencia. En el caso de las [dependencias explícitas](resource-declaration.md#set-resource-dependencies), Azure Resource Manager lo quita automáticamente de las dependencias necesarias cuando el recurso no se implementa. En el caso de las dependencias implícitas, se permite hacer referencia a una propiedad de un recurso condicional, pero puede producirse un error de implementación.

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

## <a name="next-steps"></a>Pasos siguientes

* Puede encontrar un módulo de Microsoft Learn sobre condiciones y bucles en [Creación de plantillas de Bicep flexibles mediante condiciones y bucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/).
* Puede encontrar recomendaciones sobre cómo crear archivos de Bicep en [Procedimientos recomendados para Bicep](best-practices.md).
* Para crear varias instancias de un recurso, consulte [Iteración de recursos en Bicep](loop-resources.md).
