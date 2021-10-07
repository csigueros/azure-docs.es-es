---
title: 'Funciones de Bicep: date'
description: Describe las funciones que se usarán en un archivo de Bicep para trabajar con fechas.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 45e0180a11209b39b4c37f16f9940686c042ee89
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124794127"
---
# <a name="date-functions-for-bicep"></a>Funciones de fecha para Bicep

En este artículo se describen las funciones de Bicep para trabajar con fechas.

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Agrega una duración de tiempo a un valor base. Se esperaba el formato ISO 8601.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| base | Sí | string | Valor datetime inicial para la suma. Utilice el [formato de marca de tiempo ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Sí | string | Valor de hora que se suma a la base. Puede ser un valor negativo. Utilice el [formato de duración ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | No | string | El formato de salida del resultado de fecha y hora. Si no se indica, se usa el formato del valor base. Use [cadenas de formato estándar](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [cadenas de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Valor devuelto

Valor datetime resultado de sumar el valor de duración al valor base.

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestran distintas formas de sumar valores de hora.

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

Cuando el ejemplo anterior se implementa con una hora base de `2020-04-07 14:53:14Z`, la salida es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| add3YearsOutput | String | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | String | 3/29/2020 2:53:14 PM |
| add1HourOutput | String | 4/7/2020 3:53:14 PM |

En el ejemplo siguiente se muestra cómo establecer la hora de inicio de una programación de Automation.

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Devuelve el valor de fecha y hora (UTC) actual en el formato especificado. Si no se proporciona el formato, se usa ISO 8601 (`yyyyMMddTHHmmssZ`). **Esta función solo puede utilizarse en el valor predeterminado para un parámetro.**

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| format |No |string |El valor codificado por el identificador URI para convertir en una cadena. Use [cadenas de formato estándar](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [cadenas de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Observaciones

Solo puede usar esta función dentro de una expresión para el valor predeterminado de un parámetro. El uso de esta función en cualquier otro lugar de un archivo de Bicep genera un error. La función no se permite en otras partes del archivo de Bicep porque devuelve un valor diferente cada vez que se le llama. La implementación del mismo archivo de Bicep con los mismos parámetros no produciría de forma confiable los mismos resultados.

Si usa la [opción de revertir en caso de error](../templates/rollback-on-error.md) a una implementación correcta anterior y dicha implementación incluye un parámetro que usa utcNow, el parámetro no se vuelve a evaluar. En su lugar, el valor del parámetro de la implementación anterior se reutiliza automáticamente en la implementación de reversión.

Tenga cuidado al volver a implementar un archivo de Bicep que se base en la función utcNow para un valor predeterminado. Si vuelve a implementar y no proporciona un valor para el parámetro, la función se vuelve a evaluar. Si desea actualizar un recurso existente en lugar de crear uno nuevo, pase el valor de parámetro de la implementación anterior.

### <a name="return-value"></a>Valor devuelto

El valor de fecha y hora UTC actual.

### <a name="examples"></a>Ejemplos

En el siguiente ejemplo se muestran formatos diferentes para el valor de fecha y hora.

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

El resultado del ejemplo anterior varía para cada implementación, pero será similar a:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 05/03/2019 |
| utcCustomOutput | string | 3 5 |

El ejemplo siguiente muestra cómo usar un valor de la función cuando se establece un valor de etiqueta.

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

## <a name="next-steps"></a>Pasos siguientes

* Puede encontrar una descripción de las secciones de un archivo de Bicep en [Nociones sobre la estructura y la sintaxis de los archivos de Bicep](./file.md).
