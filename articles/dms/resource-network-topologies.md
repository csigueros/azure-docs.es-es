---
title: Topologías de red para migraciones de Instancia administrada de SQL
titleSuffix: Azure Database Migration Service
description: Obtenga información sobre las configuraciones de origen y destino para migrar Instancia administrada de Azure SQL mediante Azure Database Migration Service.
author: pochiraju
ms.author: rajpo
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 4a4cdce5ae20147bbf25a82401c9993a620320e5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363038"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Topologías de red para migraciones de Instancia administrada de Azure SQL mediante Azure Database Migration Service

En este artículo se tratan diversas topologías de red con las que Azure Database Migration Service puede trabajar para proporcionar una experiencia de migración completa desde instancias de SQL Server a Instancia administrada de Azure SQL.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Instancia administrada de Azure SQL configurada para cargas de trabajo híbridas 

Use esta topología si su Instancia administrada de Azure SQL está conectada a la red local. Este enfoque proporciona el enrutamiento de red más simplificado y genera el máximo rendimiento de los datos durante la migración.

![Topología de red para cargas de trabajo híbridas](media/resource-network-topologies/hybrid-workloads.png)

**Requisitos**

- En este escenario, Instancia administrada de SQL y la instancia de Azure Database Migration Service se crean en la misma instancia de Microsoft Azure Virtual Network, pero usan diferentes subredes.  
- La red virtual que se utiliza en este escenario también está conectada a la red local mediante [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>Instancia administrada de SQL aislada de la red local

Use esta topología de red si su entorno requiere uno o varios de los siguientes escenarios:

- Instancia administrada de SQL está aislada de la conectividad local pero la instancia de Azure Database Migration Service está conectada a la red local.
- Si se han implementado directivas de control de acceso basado en rol de Azure (RBAC de Azure) y tiene que limitar el acceso de los usuarios a la misma suscripción que hospeda SQL Managed Instance.
- Las redes virtuales que se usan para Instancia administrada de SQL y Azure Database Migration Service se encuentran en suscripciones distintas.

![Topología de red para Instancia administrada aislada de la red local](media/resource-network-topologies/mi-isolated-workload.png)

**Requisitos**

- La red virtual que utiliza Azure Database Migration Service para este escenario también debe estar conectada a la red local mediante [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- Configure el [emparejamiento de red virtual](../virtual-network/virtual-network-peering-overview.md) entre la red virtual que se usa para Instancia administrada de SQL y Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migraciones de nube a nube: red virtual compartida

Use esta topología si el servidor SQL Server de origen se hospeda en una VM de Azure y comparte la misma red virtual con Instancia administrada de SQL y Azure Database Migration Service.

![Topología de red para migraciones de nube a nube con una red virtual compartida](media/resource-network-topologies/cloud-to-cloud.png)

**Requisitos**

- No hay requisitos adicionales.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migraciones de nube a nube: red virtual aislada

Use esta topología de red si su entorno requiere uno o varios de los siguientes escenarios:

- Instancia administrada de SQL se aprovisiona en una red virtual aislada.
- Si se han implementado directivas de control de acceso basado en rol de Azure (RBAC de Azure) y tiene que limitar el acceso de los usuarios a la misma suscripción que hospeda SQL Managed Instance.
- Las redes virtuales que se usan para Instancia administrada de SQL y Azure Database Migration Service se encuentran en suscripciones distintas.

![Topología de red para migraciones de nube a nube con una red virtual aislada](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisitos**

- Configure el [emparejamiento de red virtual](../virtual-network/virtual-network-peering-overview.md) entre la red virtual que se usa para Instancia administrada de SQL y Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Reglas de seguridad de entrada

| **NOMBRE**   | **PUERTO** | **PROTOCOLO** | **ORIGEN** | **DESTINO** | **ACCIÓN** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Any      | Any          | SUBRED DMS | Any             | Allow      |

## <a name="outbound-security-rules"></a>Reglas de seguridad de entrada

| **NOMBRE**                  | **PUERTO**                                              | **PROTOCOLO** | **ORIGEN** | **DESTINO**           | **ACCIÓN** | **Motivo de la regla**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceBus                | 443, ServiceTag: ServiceBus                           | TCP          | Any        | Any                       | Allow      | Comunicación de planos de administración mediante Service Bus. <br/>(Si está habilitado el emparejamiento de Microsoft, es posible que no necesite esta regla).                                                             |
| Storage                   | 443, ServiceTag: Storage                              | TCP          | Any        | Any                       | Allow      | Plano de administración con Azure Blob Storage. <br/>(Si está habilitado el emparejamiento de Microsoft, es posible que no necesite esta regla).                                                             |
| Diagnóstico               | 443, ServiceTag: AzureMonitor                         | TCP          | Any        | Any                       | Allow      | DMS usa esta regla para recopilar información de diagnóstico para solucionar problemas. <br/>(Si está habilitado el emparejamiento de Microsoft, es posible que no necesite esta regla).                                                  |
| Servidor de origen SQL         | 1433 (o puerto TCP IP en el que SQL Server escucha) | TCP          | Any        | Espacio de direcciones local | Allow      | Conectividad del origen de SQL Server desde DMS <br/>(Si dispone de conectividad de sitio a sitio, es posible que no necesite esta regla).                                                                                       |
| Instancia con nombre de SQL Server | 1434                                                  | UDP          | Any        | Espacio de direcciones local | Allow      | Conectividad del origen de las instancias con nombre desde DMS <br/>(Si dispone de conectividad de sitio a sitio, es posible que no necesite esta regla).                                                                        |
| Recurso compartido de SMB                 | 445 (si el escenario lo necesita)                             | TCP          | Any        | Espacio de direcciones local | Allow      | Recurso compartido de red SMB para DMS para almacenar los archivos de copia de seguridad de base de datos de las migraciones a Azure SQL Database MI y servidores SQL Server en Azure VM <br/>(Si dispone de conectividad de sitio a sitio, es posible que no necesite esta regla). |
| DMS_subnet                | Any                                                   | Any          | Any        | DMS_Subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Consulte también

- [Migración de SQL Server a Instancia administrada de SQL](./tutorial-sql-server-to-managed-instance.md)
- [Introducción a los requisitos previos para usar Azure Database Migration Service](./pre-reqs.md)
- [Creación de una red virtual mediante Azure Portal](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de Azure Database Migration Service, consulte el artículo [¿Qué es Azure Database Migration Service?](dms-overview.md).
- Para obtener información actualizada acerca de la disponibilidad regional de Azure Database Migration Service, consulte la página [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).