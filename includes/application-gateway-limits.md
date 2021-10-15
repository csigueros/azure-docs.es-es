---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 09/09/2021
ms.author: victorh
ms.openlocfilehash: f106ca51487e1c4c306d51161110062ad3582fcd
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129439983"
---
| Resource | Límite | Nota: |
| --- | --- | --- |
| Azure Application Gateway |1000 por suscripción | |
| Configuración de direcciones IP de front-end |2 |1 pública y 1 privada |
| Puertos de front-end |100<sup>1</sup> | |
| Grupos de direcciones de back-end |100<sup>1</sup> | |
| Servidores back-end por grupo |1,200 | |
| Agentes de escucha HTTP |200<sup>1</sup> |Limitado a 100 clientes de escucha activos que enrutan el tráfico. Clientes de escucha activos = número total de clientes de escucha - clientes de escucha no activos.<br>Si se establece una configuración predeterminada dentro de una regla de enrutamiento para enrutar el tráfico (por ejemplo, tiene un cliente de escucha, un grupo de back-end y la configuración de HTTP), también cuenta como cliente de escucha. Para obtener más detalles, consulte las [preguntas más frecuentes sobre Application Gateway](../articles/application-gateway/application-gateway-faq.yml#what-is-considered-an-active-listener-versus-inactive-listener).|
| Reglas de equilibrio de carga HTTP |400<sup>1</sup> | |
| Configuración de HTTP de back-end |100<sup>1</sup> | |
| Instancias por puerta de enlace |V1 SKU: 32<br>V2 SKU: 125 | |
| Certificados SSL |100<sup>1</sup> |Uno por cliente de escucha HTTP |
| Tamaño máximo de certificados SSL |V1 SKU: 10 KB<br>V2 SKU: 16 KB| |
| Certificados de autenticación |100 | |
| Certificados raíz de confianza |100 | |
| Tiempo de espera de solicitud mínimo |1 segundo | |
| Tiempo de espera de solicitud máximo |24 horas | |
| Número de sitios |100<sup>1</sup> |Uno por cliente de escucha HTTP |
| Asignaciones de URL por agente de escucha |1 | |
| Número máximo de reglas basadas en rutas por mapa de direcciones URL|100||
| Configuraciones de redirección |100<sup>1</sup>| |
| Número de conjuntos de reglas de reescritura |400| |
| Número de encabezado o configuración de dirección URL por conjunto de reglas de reescritura|40| |
| Número de condiciones por conjunto de reglas de reescritura|40| |
| Conexiones simultáneas de WebSocket |Puertas de enlace medianas 20k<sup>2</sup><br> Puertas de enlace grandes 50k<sup>2</sup>| |
| Longitud máxima de dirección URL|32 KB| |
| Tamaño de encabezado máximo para HTTP/2 |16 KB| |
| Tamaño máximo de carga de archivos (SKU estándar) |V2: 4 GB<br>V1: 2 GB | |
| Tamaño máximo de carga de archivos (SKU de WAF) |V1 Medio: 100 MB<br>V1 Grande: 500 MB<br>V2: 750 MB<br>V2 (con CRS 3.2 o posterior): 4 GB| |
| Límite de tamaño de cuerpo de WAF (sin archivos)|V1 o V2 (con CRS 3.1 y versiones anteriores): 128 KB<br>V2 (con CRS 3.2 o posterior): 2 MB| |
| Máximo de reglas personalizadas de WAF|100||
| Número máximo de exclusiones de WAF por instancia de Application Gateway|40||

<sup>1</sup> En caso de las SKU con WAF habilitado, debe limitar el número de recursos a 40.

<sup>2</sup> El límite es por instancia de Application Gateway no por recurso de Application Gateway.
