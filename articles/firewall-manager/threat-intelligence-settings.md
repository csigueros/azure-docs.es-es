---
title: Configuración de inteligencia sobre amenazas de Azure Firewall
description: Obtenga información sobre cómo configurar el filtrado basado en inteligencia sobre amenazas para que una directiva de Azure Firewall envíe una alerta y deniegue el tráfico desde y hacia los dominios y las direcciones IP malintencionados.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 819509e08a5604957f01452e5c6ec33c20de2b2a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297494"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Configuración de inteligencia sobre amenazas de Azure Firewall

El filtrado basado en inteligencia sobre amenazas puede configurarse para que una directiva de Azure Firewall envíe una alerta y deniegue el tráfico desde y hacia los dominios y las direcciones IP malintencionados. La direcciones IP y los dominios proceden de la fuente Inteligencia sobre amenazas de Microsoft. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) impulsa la inteligencia sobre amenazas de Microsoft y se usa en numerosos servicios, como Microsoft Defender for Cloud.<br>

Si ha configurado el filtrado basado en inteligencia sobre amenazas, las reglas asociadas se procesan antes que cualquiera de las reglas NAT, reglas de red o reglas de aplicación.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Directiva de inteligencia sobre amenazas":::

## <a name="threat-intelligence-mode"></a>Modo de inteligencia sobre amenazas

Puede configurar la inteligencia sobre amenazas en uno de los tres modos que se describen en la tabla siguiente. De forma predeterminada, el filtrado basado en inteligencia sobre amenazas está habilitado en el modo de alerta.

|Mode |Descripción  |
|---------|---------|
|`Off`     | La característica de inteligencia sobre amenazas no está habilitada para el firewall. |
|`Alert only`     | Cuando se detecte tráfico que intenta atravesar el firewall hacia o desde dominios y direcciones IP malintencionados conocidos, recibirá alertas de alta confianza. |
|`Alert and deny`     | Cuando se detecte tráfico que intenta atravesar el firewall hacia o desde dominios y direcciones IP malintencionados conocidos, el tráfico se bloqueará y recibirá alertas de alta confianza. |

> [!NOTE]
> El modo de inteligencia sobre amenazas se hereda de las directivas principales a las directivas secundarias. Una directiva secundaria debe configurarse con el mismo modo u otro más estricto que la directiva primaria.

## <a name="allowlist-addresses"></a>Direcciones permitidas

La inteligencia sobre amenazas podría desencadenar falsos positivos y bloquear el tráfico que realmente es válido. Puede configurar una lista de direcciones IP permitidas para que la inteligencia sobre amenazas no filtre ninguna de las direcciones, rangos o subredes que especifique.  

![Direcciones permitidas](media/threat-intelligence-settings/allow-list.png)

Puede actualizar la lista de permitidos con varias entradas a la vez mediante la carga de un archivo CSV. El archivo CSV solo puede contener direcciones IP e intervalos. El archivo no puede contener títulos.

> [!NOTE]
> Las direcciones de la lista de permitidos de inteligencia sobre amenazas se heredan de las directivas primarias a las secundarias. Todas las direcciones IP o intervalos agregados a una directiva primaria se aplicarán también a todas las directivas secundarias.

## <a name="logs"></a>Registros

El extracto de registro siguiente muestra una regla desencadenada para el tráfico saliente a un sitio malintencionado:

```json
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Prueba

- **Pruebas de salida**: las alertas de tráfico de salida deben ser un caso poco habitual, ya que significa que se ha puesto en peligro su entorno. Para ayudar a probar que las alertas de salida funcionan, se ha creado un FQDN de prueba que desencadena una alerta. Use **testmaliciousdomain.eastus.cloudapp.azure.com** para las pruebas de salida.

- **Pruebas de entrada**: puede esperar ver las alertas en el tráfico de entrada si se configuran reglas DNAT en el firewall. Esto es cierto incluso si solo se permiten orígenes específicos en la regla DNAT y se deniega el tráfico de otra manera. Azure Firewall no alerta sobre todos los escáneres de puerto conocidos, solo sobre aquellos que se sabe que también participan en actividades malintencionadas.

## <a name="next-steps"></a>Pasos siguientes

- Revise el [informe de inteligencia de seguridad de Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)
