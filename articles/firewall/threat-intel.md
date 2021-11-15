---
title: Filtrado basado en inteligencia sobre amenazas de Azure Firewall
description: El filtrado basado en inteligencia sobre amenazas puede habilitarse para que el firewall alerte y deniegue el tráfico desde y hacia los dominios y las direcciones IP malintencionados.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/04/2021
ms.author: victorh
ms.openlocfilehash: 90a988e302d3997156dc643f8bacbe6ed6ec786a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852662"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtrado basado en inteligencia sobre amenazas de Azure Firewall

El filtrado basado en inteligencia sobre amenazas puede habilitarse para que el firewall alerte y deniegue el tráfico desde y hacia los dominios y las direcciones IP malintencionados. Las direcciones IP y los dominios proceden de la fuente de inteligencia sobre amenazas de Microsoft, que incluye varios orígenes, incluido el equipo de Microsoft Cyber Security. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) impulsa la inteligencia sobre amenazas de Microsoft y lo utilizan numerosos servicios, incluido Azure Security Center.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Inteligencia sobre amenazas de Firewall" border="false":::

Si ha habilitado el filtrado basado en inteligencia sobre amenazas, las reglas asociadas se procesan antes que cualquiera de las reglas NAT, reglas de red o reglas de aplicación.

Puede optar por registrar solo una alerta cuando se desencadena una regla o puede elegir el modo de alerta y denegación.

De forma predeterminada, el filtrado basado en inteligencia sobre amenazas está habilitado en el modo de alerta. No se puede desactivar esta característica o cambiar el modo hasta que la interfaz del portal esté disponible en su región.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Interfaz del portal del filtrado basado en inteligencia sobre amenazas":::

## <a name="logs"></a>Registros

El siguiente extracto del registro muestra una regla desencadenada:

```
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

- Consulte [Ejemplos de Log Analytics de Azure Firewall](./firewall-workbook.md)
- Consulte el tutorial [Implementación y configuración de Azure Firewall](tutorial-firewall-deploy-portal.md)
- Revise el [informe de inteligencia de seguridad de Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)