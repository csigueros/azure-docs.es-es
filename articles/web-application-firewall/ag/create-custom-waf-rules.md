---
title: Crear y usar reglas personalizadas de v2
titleSuffix: Azure Web Application Firewall
description: En este artículo se proporciona información sobre cómo crear reglas personalizadas de Firewall de aplicaciones web (WAF) v2 en Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 847a0fc7f1867c2a4ea1f620a60f6236efebf4ce
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358714"
---
# <a name="create-and-use-web-application-firewall-v2-custom-rules-on-application-gateway"></a>Creación y uso de reglas personalizadas de Firewall de aplicaciones web v2 en Application Gateway

El Firewall de aplicaciones web (WAF) v2 de Azure Application Gateway proporciona protección para las aplicaciones web. Esta protección la proporciona el conjunto de reglas básico (CRS) Proyecto de seguridad de aplicación web abierta (OWASP). En algunos casos, es posible que necesite crear reglas personalizadas propias para satisfacer necesidades concretas. Para más información sobre las reglas personalizadas de WAF, vea [Reglas personalizadas para el firewall de aplicaciones web](custom-waf-rules-overview.md).

En este artículo se muestran algunas reglas personalizadas de ejemplo que puede crear y usar con el firewall de aplicaciones web v2. Para obtener información sobre cómo implementar un WAF con una regla personalizada mediante Azure PowerShell, vea [Configuración de reglas personalizadas del firewall de aplicaciones web con Azure PowerShell](configure-waf-custom-rules.md).

Los fragmentos de código JSON que se muestran en este artículo se derivan de un recurso [ApplicationGatewayWebApplicationFirewallPolicies](/azure/templates/microsoft.network/applicationgatewaywebapplicationfirewallpolicies).

>[!NOTE]
> Si la puerta de enlace de aplicaciones no usa el nivel WAF, la opción para actualizar la puerta de enlace de aplicaciones al nivel WAF aparece en el panel derecho.

![Habilitar WAF][fig1]

## <a name="example-1"></a>Ejemplo 1

Sabe que hay un bot denominado *evilbot* que quiere impedir que rastree el sitio web. En este caso, bloqueará *evilbot* en el agente de usuario en los encabezados de solicitud.

Lógica: p

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Y este es el código JSON correspondiente:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

Para ver un WAF implementado mediante esta regla personalizada, vea [Configuración de una regla personalizada del firewall de aplicaciones web con Azure PowerShell](configure-waf-custom-rules.md).

### <a name="example-1a"></a>Ejemplo 1a

Puede lograr lo mismo mediante una expresión regular:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Regex `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Y el código JSON correspondiente:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-2"></a>Ejemplo 2

Quiere permitir el tráfico solo de Estados Unidos mediante el operador GeoMatch y seguir aplicando las reglas administradas:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr `

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator GeoMatch `
   -MatchValue "US" `
   -Transform Lowercase `
   -NegationCondition $True

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name "allowUS" `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Y el código JSON correspondiente:

```json
  {
    "customRules": [
      {
        "name": "allowUS",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "GeoMatch",
            "NegationConditon": false,
            "matchValues": [
              "US"
            ]
          }
        ]
      }
    ]
  }
```



## <a name="example-3"></a>Ejemplo 3

Quiere bloquear todas las solicitudes desde las direcciones IP del intervalo 198.168.5.0/24.

En este ejemplo, bloqueará todo el tráfico que procede de un intervalo de direcciones IP. El nombre de la regla es *myrule1* y la prioridad se establece en 10.

Lógica: p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Este es el código JSON correspondiente:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      }
    ]
  }
```

La regla de CRS correspondiente: `SecRule REMOTE_ADDR "@ipMatch 192.168.5.0/24" "id:7001,deny"`

## <a name="example-4"></a>Ejemplo 4

En este ejemplo, quiere bloquear *evilbot* en User-Agent y el tráfico en el intervalo 192.168.5.0/24. Para lograrlo, puede crear dos condiciones de coincidencia independientes y colocarlas en la misma regla. Esto garantiza que si coinciden *evilbot* en el encabezado User-Agent **y** las direcciones IP del intervalo 192.168.5.0/24, la solicitud se bloquea.

Lógica: p **y** q

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

Este es el código JSON correspondiente:

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 10, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": false, 
              "matchValues": [ 
                "192.168.5.0/24" 
              ] 
            }, 
            { 
              "matchVariable": "RequestHeaders", 
              "selector": "User-Agent", 
              "operator": "Contains", 
              "transforms": [ 
                "Lowercase" 
              ], 
              "matchValues": [ 
                "evilbot" 
              ] 
            } 
        ] 
      } 
    ] 
  } 
```

## <a name="example-5"></a>Ejemplo 5

En este ejemplo, quiere que se produzca el bloqueo si la solicitud está fuera del intervalo de direcciones IP *192.168.5.0/24*, o bien la cadena de agente de usuario no es *chrome* (lo que significa que el usuario no usa el explorador Chrome). Como en esta lógica se usa **o**, las dos condiciones están en reglas independientes como se muestra en el ejemplo siguiente. *myrule1* y *myrule2* deben coincidir para que se bloquee el tráfico.

Lógica: **no** (p **y** q) = **no** p **o no** q.

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

Y el código JSON correspondiente:

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "selector": "User-Agent",
            "operator": "Contains",
            "negateCondition": true,
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "chrome"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-6"></a>Ejemplo 6

Quiere bloquear los ataques por inyección de código SQL personalizados. Como la lógica que se usa aquí es **o**, y todos los valores están en *RequestUri*, todos los valores *MatchValues* pueden estar en una lista separada por comas.

Lógica: p **o** q **o** r

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri 
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1", "drop tables", "'—" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule4 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Código JSON correspondiente:

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 10
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1",
              "drop tables",
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

Versión alternativa de Azure PowerShell:

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
-Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "drop tables" `
   -NegationCondition $False

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block

$variable3 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition3 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable3 `
   -Operator Contains `
   -MatchValue "’—" `
   -NegationCondition $False

$rule3 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule3 `
   -Priority 30 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

Código JSON correspondiente:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "drop tables"
            ]
          }
        ]
      },
      {
        "name": "myrule3",
        "ruleType": "MatchRule",
        "priority": 30,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-7"></a>Ejemplo 7

No es raro ver Azure Front Door implementado delante de Application Gateway.  Para asegurarse de que el tráfico recibido por Application Gateway procede de la implementación de Front Door, el procedimiento recomendado es comprobar si el encabezado `X-Azure-FDID` contiene el valor único esperado.  Para más información, consulte [¿Cómo puedo hacer que Azure Front Door sea el único que tenga acceso a mi back-end?](../../frontdoor/front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-)

Lógica: **not** p

```azurepowershell
$expectedFDID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector X-Azure-FDID

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Equal `
   -MatchValue $expectedFDID `
   -Transform Lowercase `
   -NegationCondition $True

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockNonAFDTraffic `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Y este es el código JSON correspondiente:

```json
  {
    "customRules": [
      {
        "name": "blockNonAFDTraffic",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
                {
                    "variableName": "RequestHeaders",
                    "selector": "X-Azure-FDID"
                }
            ],
            "operator": "Equal",
            "negationConditon": true,
            "matchValues": [
                "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ],
            "transforms": [
                "Lowercase"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="next-steps"></a>Pasos siguientes

Después de crear las reglas personalizadas, puede aprender a ver los registros del firewall de aplicaciones web. Para más información, consulte [Diagnósticos de Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/create-custom-waf-rules/1.png
