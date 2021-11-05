---
title: Información general sobre la directiva de Azure Firewall Manager
description: Más información sobre las directivas de Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: victorh
ms.openlocfilehash: 98524c2c5c73ab7a75395464911585f80bcd092c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024303"
---
# <a name="azure-firewall-manager-policy-overview"></a>Información general sobre la directiva de Azure Firewall Manager

Directiva de Firewall, el método recomendado para configurar Azure Firewall. Es un recurso global que se puede utilizar en varias instancias de Azure Firewall en centros virtuales protegidos y redes virtuales de centros. Las directivas funcionan entre regiones y suscripciones.

![Directiva de Azure Firewall Manager](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Creación y asociación de una directiva

Una directiva se puede crear y administrar de varias maneras, entre las que se incluyen Azure Portal, API REST, plantillas, Azure PowerShell y CLI.

También puede migrar las reglas clásicas existentes de Azure Firewall mediante el portal o mediante Azure PowerShell para crear las directivas. Para más información, consulte [Cómo migrar configuraciones de Azure Firewall a la directiva de Azure Firewall](migrate-to-policy.md). 

Las directivas se pueden asociar a uno o varios centros o redes virtuales. El firewall puede estar en cualquier suscripción asociada a su cuenta y en cualquier región.

## <a name="classic-rules-and-policies"></a>Directivas y reglas clásicas

Azure Firewall admite tanto reglas clásicas como directivas, pero la configuración recomendada son las directivas. En la tabla siguiente se comparan las directivas y las reglas clásicas:


| Asunto | Directiva  | Reglas clásicas |
| ------- | ------- | ----- |
|Contains     |Reglas NAT, de red y de aplicación, configuración de proxy DNS y DNS personalizado, grupos de IP y configuración de Inteligencia sobre amenazas (incluida la lista de permitidos), IDPS, inspección de TLS, categorías web, filtrado de direcciones URL|Reglas NAT, de red y de aplicación, configuración de proxy DNS y DNS personalizado, grupos de IP y configuración de inteligencia sobre amenazas (incluida la lista de permitidos)|
|Protege     |Centros virtuales y redes virtuales|Solo redes virtuales|
|Experiencia del portal     |Administración centralizada mediante Firewall Manager|Experiencia de firewall independiente|
|Compatibilidad con varios firewalls     |La directiva de firewall es un recurso independiente que se puede usar entre firewalls.|Exporte e importe manualmente reglas o use soluciones de administración de terceros |
|Precios     |Facturado en función de la asociación del firewall. Consulte [Precios](#pricing).|Gratuito|
|Mecanismos de implementación admitidos     |Portal, API REST, plantillas, Azure PowerShell y CLI|Portal, API REST, plantillas, PowerShell y CLI. |

## <a name="standard-and-premium-policies"></a>Directivas Estándar y Premium

Azure Firewall admite las directivas Estándar y las Premium. En la tabla siguiente se resume la diferencia entre ambas:


|Tipo de directiva|Compatibilidad de características  | Compatibilidad con la SKU de Firewall|
|---------|---------|----|
|Directiva Estándar    |Reglas NAT, reglas de red, reglas de aplicación<br>DNS personalizado, proxy DNS<br>Grupos de IP<br>Categorías web<br>Información sobre amenazas|Estándar o Premium|
|Directiva Premium    |Admite todas las características de la directiva Estándar, además de:<br><br>Inspección de TLS<br>Categorías web<br>Filtrado de direcciones URL<br>IDPS|Premium


## <a name="hierarchical-policies"></a>Directivas jerárquicas

Las nuevas directivas se pueden crear desde cero o heredar de directivas ya existentes. La herencia permite a DevOps crear directivas de firewall locales sobre la directiva base asignada de la organización.

Las directivas creadas con directivas primarias no vacías heredan todas las colecciones de reglas de la directiva primaria. Las colecciones de reglas de red heredadas de una directiva primaria siempre se priorizan por encima de las colecciones de reglas de red definidas como parte de una nueva directiva. La misma lógica también se aplica a las colecciones de reglas de aplicación. Las colecciones de reglas de red siempre se procesan antes que las colecciones de reglas de aplicación independientemente de la herencia.

El modo de inteligencia sobre amenazas también se hereda de la directiva primaria. Puede establecer el modo de inteligencia sobre amenazas en un valor diferente para invalidar este comportamiento, pero no puede desactivarlo. Solo es posible invalidarlo con un valor más estricto. Por ejemplo, si la directiva primaria está establecida en **solo Alerta**, puede configurar esta directiva local para **Alertar y denegar**.

Igual que en el modo de inteligencia sobre amenazas, la lista de permitidos de inteligencia sobre amenazas se hereda de la directiva primaria. La directiva secundaria puede agregar direcciones IP adicionales a la lista de permitidos.

Las colecciones de reglas NAT no se heredan porque son específicas de un firewall determinado.

Mediante la herencia, los cambios en la directiva primaria se aplican automáticamente a las directivas secundarias de firewall asociadas.



## <a name="pricing"></a>Precios

Las directivas se facturan en función de las asociaciones del firewall. Una directiva con una o cero asociaciones de firewall es gratuita. Una directiva con varias asociaciones de firewall se factura según una tarifa fija. Para más información, consulte [Precios de Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo implementar una instancia de Azure Firewall, consulte [Tutorial: Proteja su red en la nube con la versión preliminar de Azure Firewall Manager en Azure Portal](secure-cloud-network.md).
