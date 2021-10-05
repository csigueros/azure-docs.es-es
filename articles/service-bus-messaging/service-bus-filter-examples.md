---
title: Establecimiento de filtros de suscripciones en Azure Service Bus | Microsoft Docs
description: En este artículo se proporcionan ejemplos para definir filtros y acciones en las suscripciones a temas de Azure Service Bus.
ms.topic: how-to
ms.date: 09/07/2021
ms.openlocfilehash: d73a8cdade8600a793911acfefc54a8e1e1530b6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754968"
---
# <a name="set-subscription-filters-azure-service-bus"></a>Establecimiento de filtros de suscripciones (Azure Service Bus)
En este artículo se proporcionan algunos ejemplos sobre cómo establecer filtros en las suscripciones a temas de Azure Service Bus. Para obtener información conceptual acerca de los filtros, consulte [Filtros](topic-filters.md).

## <a name="filter-on-system-properties"></a>Filtrado por propiedades del sistema
Para hacer referencia a una propiedad del sistema en un filtro, utilice el siguiente formato: `sys.<system-property-name>`. 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>Filtrado por propiedades de mensaje
Estos son ejemplos de cómo usar las propiedades del mensaje en un filtro. Puede acceder a las propiedades del mensaje mediante `user.property-name` o simplemente `property-name`.

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>Filtrado por propiedades del mensaje con caracteres especiales
Si el nombre de la propiedad del mensaje tiene caracteres especiales, use comillas dobles (`"`) para incluir el nombre de la propiedad. Por ejemplo, si el nombre de la propiedad es `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"`, use la sintaxis siguiente en el filtro. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>Filtrado por propiedades del mensaje con valores numéricos
En los siguientes ejemplos se muestra cómo se pueden usar las propiedades con valores numéricos en los filtros. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>Filtros basados en parámetros
Estos son algunos ejemplos de cómo usar filtros basados en parámetros. En estos ejemplos, `DataTimeMp` es una propiedad del mensaje de tipo `DateTime` y `@dtParam` es un parámetro que se pasa al filtro como un objeto `DateTime`.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>Usar IN y NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Para ver un ejemplo de C#, consulte el [ejemplo de filtros de temas en GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="correlation-filters"></a>Filtros de correlación

### <a name="correlation-filter-using-correlationid"></a>Filtro de correlación con CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Filtra los mensajes con `CorrelationID` establecido en `Contoso`. 

> [!NOTE]
> La clase [CorrelationRuleFilter](/dotnet/api/azure.messaging.servicebus.administration.correlationrulefilter) de .NET se encuentra en el espacio de nombres [Azure.Messaging.ServiceBus.Administration](/dotnet/api/azure.messaging.servicebus.administration). Para obtener código de ejemplo que muestra cómo crear filtros en general mediante .NET, vea [este código en GitHub](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters/Program.cs#L179).


### <a name="correlation-filter-using-system-and-user-properties"></a>Filtro de correlación con propiedades del sistema y del usuario

```csharp
var filter = new CorrelationRuleFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Es equivalente a: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`.





## <a name="next-steps"></a>Pasos siguientes
Consulte los ejemplos siguientes: 

- [.NET: Tutorial básico de envío y recepción con filtros](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET: Filtros de tema](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Plantilla de Azure Resource Manager](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)
