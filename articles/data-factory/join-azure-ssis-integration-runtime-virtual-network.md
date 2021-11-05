---
title: Unión de un entorno de ejecución de integración de Azure-SSIS a una red virtual
description: Aprenda a unir un entorno de ejecución de integración de Azure-SSIS a una red virtual.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9766ed3ed870e41c44a2dd84bbaad578ec54509c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087882"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network"></a>Unión de un entorno de ejecución de integración de Azure-SSIS a una red virtual

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Al usar SQL Server Integration Services (SSIS) en Azure Data Factory (ADF), debe unir un entorno de integración (IR) de Azure-SSIS a una red virtual de Azure en los escenarios siguientes:

- Quiere conectarse a almacenes/recursos de datos locales desde paquetes SSIS que se ejecutan en Azure-SSIS IR sin configurar ni administrar un entorno de ejecución de integración autohospedado como proxy.

- Quiere utilizar un servidor de Azure SQL Database configurado con un punto de conexión de servicio de red virtual, una regla de firewall de IP, un punto de conexión privado o Azure SQL Managed Instance que se conecta a una red virtual para hospedar una base de datos del catálogo SSISDB.

- Quiere acceder a los almacenes de datos o recursos de Azure configurados con un punto de conexión privado, una regla de firewall de IP o un punto de conexión de servicio de red virtual desde paquetes SSIS que se ejecutan en Azure-SSIS IR.

- Quiere conectarse a otros almacenes de datos o recursos configurados con reglas de firewall de IP desde los paquetes SSIS que se ejecutan en Azure-SSIS IR.

ADF le permite unir Azure-SSIS IR a una red virtual creada mediante el modelo de implementación clásica o el modelo de implementación de Azure Resource Manager.

> [!IMPORTANT]
> La red virtual clásica está en desuso, así que debe usar la red virtual de Azure Resource Manager. Si ya usa la red virtual clásica, cambie a la red virtual de Azure Resource Manager en cuanto sea posible.

En el tutorial [Configuración de Azure-SSIS IR para unirse a una red virtual](tutorial-deploy-ssis-virtual-network.md) se muestran los pasos mínimos con el método de inserción de red virtual exprés a través Azure Portal o la interfaz de usuario de ADF. En este artículo y otros, como el artículo [Configuración de una red virtual para insertar Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md), se amplía el tutorial y se describen todos los pasos opcionales:

- Si utiliza el método de inserción de red virtual estándar.
- Si utiliza la red virtual clásica.
- Si trae sus propias direcciones IP públicas estáticas (BYOIP) para Azure-SSIS IR.
- Si utiliza su propio servidor de sistema de nombres de dominio (DNS).
- Si utiliza un grupo de seguridad de red (NSG).
- Si utiliza rutas definidas por el usuario (UDR).
- Si usa una instancia de Azure-SSIS IR personalizada.
- Si utiliza Azure PowerShell para aprovisionar Azure-SSIS IR.

## <a name="access-to-on-premises-data-stores"></a>Acceso a los almacenes de datos locales

Si los paquetes SSIS acceden a almacenes de datos locales, puede conectar Azure-SSIS Integration Runtime a una red virtual que esté conectada a la red local. O bien, puede configurar y administrar un entorno de ejecución de integración autohospedado como proxy para Azure-SSIS IR. Para obtener más información, consulte [Configuración del entorno de ejecución de integración autohospedado como un proxy para Azure-SSIS IR](self-hosted-integration-runtime-proxy-ssis.md). 

Al conectar Azure-SSIS Integration Runtime a una red virtual, recuerde estos puntos, ya que son importantes: 

- Si no hay ninguna red virtual existente conectada a su red local, primero cree una [red virtual de Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) a la que pueda conectarse Azure-SSIS Integration Runtime. Luego, configure una [conexión de puerta de enlace de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) de sitio a sitio o una conexión [ExpressRoute de Azure](../expressroute/expressroute-howto-linkvnet-classic.md) desde esa red virtual a su red local. 

- Si hay una red de virtual de Azure Resource Manager que ya esté conectada a su red local en la misma ubicación que Azure-SSIS IR, puede conectar el entorno de ejecución de integración a esa red virtual. 

- Si hay una red virtual clásica conectada a la red local en una ubicación diferente a la de Azure-SSIS Integration Runtime, puede crear una [red virtual de Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que se conecte Azure-SSIS Integration Runtime. A continuación, configure una conexión de [red virtual clásica a Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Si hay una red virtual de Azure Resource Manager conectada a la red local en una ubicación diferente a la de Azure-SSIS Integration Runtime, puede crear primero una [red virtual de Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que se conecte a Azure-SSIS Integration Runtime. Luego, configure una conexión de [red virtual de Azure Resource Manager a Azure Resource Manager](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). 

## <a name="hosting-ssisdb-in-azure-sql-database-server-or-managed-instance"></a>Hospedar una base de datos de SSIS en un servidor o instancia administrada de Azure SQL Database

Si hospeda la base de datos de SSIS en Azure SQL Database con puntos de conexión de servicio de red virtual, asegúrese de unir la instancia de Azure-SSIS IR a la misma red virtual y subred.

Si hospeda la base de datos de SSIS en una Instancia administrada de Azure SQL con punto de conexión privado, asegúrese de que conecta Azure-SSIS IR a la misma red virtual pero en una subred distinta de la instancia administrada. Para unir la instancia de Azure-SSIS IR a una red virtual diferente de aquella de la instancia administrada, le recomendamos realizar un emparejamiento de redes virtuales (que se limite a la misma región) o una conexión de red virtual a red virtual. Para más información, consulte [Conexión de una aplicación a Instancia administrada de Azure SQL](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-data-stores"></a>Acceso a almacenes de datos de Azure

Si los paquetes SSIS acceden a recursos o almacenes de Azure compatibles con los [puntos de conexión de servicio de red virtual](../virtual-network/virtual-network-service-endpoints-overview.md) y quiere proteger el acceso a esos recursos desde Azure-SSIS IR, puede conectar su instancia de Azure-SSIS IR a una subred de red virtual configurada para los puntos de conexión de servicio de red virtual y después agregar una regla de red virtual a los recursos de Azure pertinentes para permitir el acceso desde la misma subred.

## <a name="access-to-other-cloud-data-stores"></a>Acceso a otros almacenes de datos en la nube

Si los paquetes SSIS acceden a otros almacenes de datos o recursos en la nube que solo permiten direcciones IP públicas estáticas específicas y desea proteger el acceso a esos recursos desde Azure-SSIS IR, puede asociar [direcciones IP públicas](../virtual-network/virtual-network-public-ip-address.md) a Azure-SSIS IR y conectar este a una red virtual, y después agregar una regla de firewall de IP en el firewall de los recursos pertinentes para permitir el acceso desde esas direcciones IP. Tiene dos opciones para hacerlo: 

- Al crear Azure-SSIS IR, puede traer sus propias direcciones IP públicas estáticas y asociarlas a su Azure-SSIS IR a través de la [interfaz de usuario de ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md) o [Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md). Solo la conectividad saliente a Internet desde Azure-SSIS IR usará estas direcciones IP públicas y el resto de recursos de la subred no las usarán.

- También puede configurar una [traducción de direcciones de red virtual (NAT)](../virtual-network/nat-gateway/nat-overview.md) en la subred a la que se une Azure-SSIS IR y toda la conectividad saliente a Internet desde esta subred usará una dirección IP pública especificada.

En todos los casos, la red virtual solo puede implementarse mediante el modelo de implementación de Azure Resource Manager.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de una red virtual para insertar Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Método rápido de inserción de la red virtual](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Método estándar de inserción de red virtual](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Unión de Azure-SSIS IR a una red virtual mediante la interfaz de usuario de ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Unión de Azure-SSIS IR a una red virtual mediante Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa el servidor de Azure SQL Database para hospedar SSISDB. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones sobre el uso de un servidor de Azure SQL Database configurado con un punto de conexión de servicio de red virtual, una regla de firewall de IP, un punto de conexión privado o Azure SQL Managed Instance que se conecta a una red virtual para hospedar SSISDB. Muestra cómo conectar Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
