---
title: Permitir las direcciones URL de Azure Portal en el servidor proxy o firewall
description: Para optimizar la conectividad entre la red y Azure Portal y sus servicios, se recomienda agregar estas direcciones URL a la lista de permitidos.
ms.date: 06/21/2021
ms.topic: conceptual
ms.openlocfilehash: 70e2f55a381c38b0a4244f742b7b2c51e6ca81f6
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516368"
---
# <a name="allow-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Permitir las direcciones URL de Azure Portal en el servidor proxy o firewall

Para optimizar la conectividad entre la red y Azure Portal y sus servicios, se recomienda agregar direcciones URL específicas de Azure Portal a la lista de permitidos. De este modo puede mejorar el rendimiento y la conectividad entre la red de área local o de área extensa y la nube de Azure.

Con frecuencia, los administradores de red implementan servidores proxy, firewalls u otros dispositivos, que pueden ayudar con la seguridad y a tener control sobre cómo los usuarios acceden a Internet. Las reglas diseñadas para proteger a los usuarios a veces pueden bloquear o ralentizar el tráfico de Internet empresarial legítimo. Este tráfico incluye comunicaciones entre el usuario y Azure con las direcciones URL enumeradas aquí.

> [!TIP]
> Para obtener ayuda en el diagnóstico de problemas con las conexiones de red a estos dominios, consulte https://portal.azure.com/selfhelp.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Direcciones URL de Azure Portal para la omisión del proxy

Los puntos de conexión de dirección URL que se pueden permitir para Azure Portal son específicos de la nube de Azure donde se implementa la organización. Para permitir que el tráfico de red a estos puntos de conexión omita las restricciones, seleccione la nube y agregue la lista de direcciones URL al servidor proxy o al firewall. No se recomienda agregar direcciones URL adicionales relacionadas con el portal aparte de las indicadas aquí, aunque es posible que desee agregar direcciones URL relacionadas con otros productos y servicios de Microsoft.

#### <a name="public-cloud"></a>[Nube pública](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Gobierno de gobierno de Estados Unidos](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Nube del gobierno de China](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> El tráfico a estos puntos de conexión usa puertos TCP estándar para HTTP (80) y HTTPS (443).
