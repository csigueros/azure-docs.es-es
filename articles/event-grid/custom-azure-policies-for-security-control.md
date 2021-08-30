---
title: Configuración de directivas personalizadas de Azure en recursos de Event Grid para mejorar su posición de seguridad
description: Este artículo le ayuda a definir directivas personalizadas de Azure para aplicar controles de seguridad.
ms.topic: how-to
author: jfggdl
ms.author: jafernan
ms.date: 06/24/2021
ms.openlocfilehash: a9018150d75556f729db593ec1a76fb6aa174798
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992345"
---
# <a name="use-custom-azure-policies-to-enforce-security-controls"></a>Uso de las directivas personalizadas de Azure para aplicar controles de seguridad

En este artículo se proporcionan directivas personalizadas de Azure de ejemplo para controlar los destinos que pueden configurarse en las suscripciones a eventos de Event Grid. [Azure Policy](../governance/policy/overview.md) le ayuda a aplicar los estándares de la organización y el cumplimiento normativo para diferentes cuestiones, como la seguridad, el costo, la coherencia de los recursos, la administración, etc. Entre estas cuestiones destacan los estándares de cumplimiento de la seguridad que ayudan a su organización a mantener una posición de seguridad. Para ayudarle con los controles de seguridad, las directivas que se presentan en este artículo contribuyen a evitar la filtración de datos o la entrega de eventos a puntos de conexión o servicios de Azure no autorizados.

> [!NOTE]
> Azure Event Grid proporciona directivas integradas para dominios de cumplimiento y controles de seguridad relacionados con diversos estándares de cumplimiento. Puede encontrar esas directivas integradas en [Azure Security Benchmark](security-controls-policy.md#azure-security-benchmark) de Event Grid.

Para evitar la filtración de datos, las organizaciones pueden limitar los destinos a los que Event Grid puede entregar eventos. Esto se puede hacer mediante la asignación de directivas que permitan la creación o actualización de [suscripciones de eventos](concepts.md#event-subscriptions) que tengan uno de los destinos autorizados en la directiva. El efecto de directiva que se usa para evitar que una solicitud de recurso se realice correctamente es [denegar](../governance/policy/concepts/effects.md#deny).

En las secciones siguientes se muestran definiciones de directivas de ejemplo que aplican una lista de destinos permitidos. Quiere buscar [alias de propiedad](../governance/policy/concepts/definition-structure.md#aliases) que contengan el valor ```destination``` y usarlo como ```field``` para comparar con una lista de destinos permitidos al definir una directiva.

Puede encontrar los alias de propiedad definidos para Event Grid (use el espacio de nombres ```Microsoft.EventGrid```) mediante la ejecución de comandos de la CLI o de PowerShell que se describen [en esta sección del artículo](../governance/policy/concepts/definition-structure.md#aliases).

Para obtener más información sobre cómo definir directivas, consulte el artículo [Estructura de definición de Azure Policy](../governance/policy/concepts/definition-structure.md).

 
## <a name="define-an-azure-policy-with-a-list-of-allowed-destinations-for-a-webhook-destination"></a>Definición de una instancia de Azure Policy con una lista de destinos permitidos para un destino de webhook

La definición de directiva siguiente restringe los destinos de punto de conexión de webhook configurados en una suscripción de eventos para un tema del sistema.

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/systemTopics/eventSubscriptions/destination.WebHook.endpointUrl",
        "in": "[parameters('allowedDestinationEndpointURLs')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedDestinationEndpointURLs": {
      "type": "Array",
      "metadata": {
        "description": "Allowed event destination endpoint URLs.",
        "displayName": "The list of allowed webhook endpoint destinations to which send events"
      },
        "allowedValues": [
          "https://www.your-valid-destination-goes-here-1.com",
          "https://www.your-valid-destination-goes-here-2.com",
          "https://www.your-valid-destination-goes-here-3.com"
        ]
    }
  }
}
```

## <a name="define-an-azure-policy-with-a-list-of-allowed-azure-service-resource-destinations"></a>Definición de una instancia de Azure Policy con una lista de destinos de recursos de servicios de Azure permitidos

La definición de directiva siguiente restringe un destino de Event Hubs específico configurado en una suscripción de eventos para un tema personalizado. Puede usar un enfoque similar para otro tipo de [destinos de servicio de Azure admitidos](event-handlers.md).

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/eventSubscriptions/destination.EventHub.resourceId",
        "in": "[parameters('allowedResourceDestinations')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedResourceDestinations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed event delivery destinations.",
        "displayName": "Allowed event delivery destinations"
      },
        "allowedValues": [
          "/subscriptions/<your-event-subscription>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<event-hubs-namespace-name>/eventhubs/<your-event-hub-name>"
        ]
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información sobre Azure Policy, consulte los artículos siguientes: 
    - [¿Qué es Azure Policy?](../governance/policy/overview.md)
    - [Estructura de definición de Azure Policy](../governance/policy/concepts/definition-structure.md).
    - [Comprender los efectos de Azure Policy](../governance/policy/concepts/effects.md).
    - [Descripción del ámbito en Azure Policy](../governance/policy/concepts/scope.md).
    - [Uso de la extensión de Azure Policy para Visual Studio Code](../governance/policy/how-to/extension-for-vscode.md).
    - [Creación de directivas mediante programación](../governance/policy/how-to/programmatically-create.md).
- Para obtener más información sobre Azure Event Grid, consulte los artículos de la sección Conceptos, como la [Terminología de Event Grid](concepts.md).