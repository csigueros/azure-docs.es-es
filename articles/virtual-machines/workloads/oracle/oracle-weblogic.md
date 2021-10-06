---
title: ¿Cuáles son las soluciones para ejecutar Oracle WebLogic Server en máquinas virtuales de Azure?
description: Obtenga información sobre cómo ejecutar Oracle WebLogic Server en Microsoft Azure Virtual Machines.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 03/23/2021
ms.author: rezar
ms.custom:
- devx-track-java
- devx-track-javaee
ms.openlocfilehash: efe8b2f2befda9b7117d7839073cfb8323d67110
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360426"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>¿Cuáles son las soluciones para ejecutar Oracle WebLogic Server en máquinas virtuales de Azure?

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux 

En esta página se describen las soluciones para ejecutar Oracle WebLogic Server (WLS) en las máquinas virtuales de Azure. Oracle y Microsoft conjuntamente desarrollan estas soluciones y les prestan soporte técnico.

También puede ejecutar WLS en Azure Kubernetes Service. Las soluciones para ello se describen en [este artículo de Microsoft](./weblogic-aks.md).

WLS es un servidor de aplicaciones Java líder en el que se ejecutan algunas de las aplicaciones de Java de empresa más fundamentales de todo el mundo. WLS constituye la base de middleware para el conjunto de software de Oracle. Oracle y Microsoft están comprometidos con ofrecer a los clientes de WLS la posibilidad y flexibilidad de ejecutar cargas de trabajo en Azure como una plataforma de nube líder.

Las soluciones de Azure WLS están diseñadas para facilitar lo máximo posible la migración de las aplicaciones de Java a las máquinas virtuales de Azure. Las soluciones consiguen esto al generar recursos implementados para los escenarios de aprovisionamiento en la nube más comunes. Las soluciones aprovisionan automáticamente la red virtual, el almacenamiento y los recursos de Java, WLS y Linux. WebLogic Server se instala con un mínimo esfuerzo. Las soluciones pueden configurar la seguridad con un grupo de seguridad de red, el equilibrio de carga con Azure App Gateway, la autenticación con Azure Active Directory, los registros centralizados con ELK y el almacenamiento en caché distribuido con Oracle Coherence. También puede conectarse automáticamente a la base de datos existente, como Azure PostgreSQL, Azure SQL y Oracle DB en la nube de Oracle o Azure. 

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Puede usar Azure Portal para implementar WebLogic Server en Azure":::

Hay cuatro ofertas disponibles para satisfacer distintos escenarios: [un solo nodo sin un servidor de administración](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls), [un solo nodo con un servidor de administración](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin), [un clúster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) y [un clúster dinámico](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster). Las ofertas están disponibles de forma gratuita. Los vínculos y descripciones de dichas ofertas se incluyen a continuación.

_Estas ofertas son de tipo "traiga su propia licencia"_ . Se supone que ya adquirió las licencias adecuadas con Oracle y que tiene licencias adecuadas para ejecutar ofertas en Azure.

Las ofertas admiten una variedad de versiones de sistema operativo, Java y WLS a través de imágenes base (como WebLogic Server 14 y JDK 11 en Oracle Linux 7.6). Estas imágenes base también están disponibles en Azure por su cuenta. Las imágenes base resultan adecuadas para los clientes que requieren implementaciones de Azure complejas y personalizadas. El conjunto actual de imágenes base está disponible en el \[Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1).

_Si está interesado en trabajar estrechamente en los escenarios de migración con el equipo de ingeniería que desarrolla estas ofertas, seleccione el botón [PONERSE EN CONTACTO CONMIGO](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ en la [página de información general de la oferta de Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Los administradores de programas, los arquitectos y los ingenieros se pondrán en contacto con usted en breve para iniciar una colaboración estrecha. La oportunidad de colaborar en un escenario de migración es gratuita mientras las ofertas se encuentren en desarrollo activo.

## <a name="oracle-weblogic-server-single-node"></a>Nodo único de Oracle WebLogic Server

[Esta oferta](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls) aprovisiona una máquina virtual única e instala WLS en ella. No crea un dominio ni inicia el servidor de administración. La oferta de nodo único resulta útil en escenarios con una configuración de dominio muy personalizada.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server con servidor de administración

[Esta oferta](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) aprovisiona una máquina virtual única e instala WLS en ella. Crea un dominio e inicia el servidor de administración. Puede administrar el dominio y comenzar a usar las implementaciones de aplicaciones inmediatamente.

## <a name="oracle-weblogic-server-cluster"></a>Clúster de Oracle WebLogic Server

[Esta oferta](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) crea un clúster de alta disponibilidad de máquinas virtuales de WLS. El servidor de administración y todos los servidores administrados se inician de manera predeterminada. Puede administrar el clúster y comenzar a trabajar con las aplicaciones de alta disponibilidad inmediatamente.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Clúster dinámico de Oracle WebLogic Server

[Esta oferta](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster) crea un clúster dinámico escalable y de alta disponibilidad de máquinas virtuales de WLS. El servidor de administración y todos los servidores administrados se inician de manera predeterminada.

Las soluciones habilitarán una amplia gama de arquitecturas de implementación listas para producción con cierta facilidad. Puede satisfacer la mayoría de los casos de migración de la manera más productiva posible al permitir una mayor atención al desarrollo de aplicaciones empresariales.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Las implementaciones complejas de WebLogic Server están habilitadas en Azure":::

De manera adicional a lo que las soluciones aprovisionan automáticamente, los clientes tienen total flexibilidad para personalizar sus implementaciones. Probablemente, además de la implementación de aplicaciones, los clientes integrarán más recursos de Azure a sus implementaciones. Se recomienda a los clientes que se [pongan en contacto con el equipo de desarrollo](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) y proporcionen comentarios para mejorar aún más las soluciones.

## <a name="next-steps"></a>Pasos siguientes

Explore las ofertas de Azure.

> [!div class="nextstepaction"]
> [Nodo único de Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server con servidor de administración](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Clúster de Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Clúster dinámico de Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
