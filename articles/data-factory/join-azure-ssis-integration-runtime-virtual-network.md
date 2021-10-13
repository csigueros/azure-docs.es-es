---
title: Unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual
description: Aprenda a conectar Azure-SSIS Integration Runtime a una red virtual de Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 023b6898b5ba7d7d5b457f1e0f9c9051d74a1126
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399781"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si usa SQL Server Integration Services (SSIS) en Azure Data Factory, debe conectar el entorno de Azure-SSIS Integration Runtime (IR) a una red virtual de Azure en los escenarios siguientes:

- Quiere conectarse a almacenes de datos locales desde paquetes SSIS que se ejecutan en Azure-SSIS Integration Runtime sin configurar ni administrar un entorno de ejecución de integración autohospedado como proxy. 

- Quiere hospedar una base de datos de catálogo de SSIS (SSISDB) en Azure SQL Database con reglas de firewall de IP y puntos de conexión de servicio de red virtual o en Instancia administrada de SQL con un punto de conexión privado. 

- Quiere conectarse a los recursos de Azure configurados con puntos de conexión de servicio de red virtual desde los paquetes SSIS que se ejecutan en Azure-SSIS Integration Runtime.

- Quiere conectarse a almacenes de datos o recursos configurados con reglas de firewall de IP desde los paquetes SSIS que se ejecutan en Azure-SSIS Integration Runtime.

Azure Data Factory le permite conectar Azure-SSIS IR a una red virtual creada mediante el modelo de implementación clásica o el modelo de implementación con Azure Resource Manager.

> [!IMPORTANT]
> La red virtual clásica está en desuso, así que debe usar la red virtual de Azure Resource Manager.  Si ya usa la red virtual clásica, cambie a la red virtual de Azure Resource Manager en cuanto sea posible.

En el tutorial [Configuración de un entorno de ejecución de integración (IR) de Azure SQL Server Integration Services (SSIS) para unirse a una red virtual](tutorial-deploy-ssis-virtual-network.md) se muestran los pasos mínimos a través de Azure Portal. Este artículo es una extensión del tutorial y en él se describen todas las tareas opcionales:

- Si usa la red virtual (clásica).
- Si trae direcciones IP públicas propias para la instancia de Azure-SSIS IR.
- Si usa un servidor de sistema de nombres de dominio (DNS) propio.
- Si usa un grupo de seguridad de red (NSG) en la subred.
- Si usa Azure ExpressRoute o una ruta definida por el usuario (UDR).
- Si usa una instancia de Azure-SSIS IR personalizada.
- Si usa aprovisionamiento de Azure PowerShell.

## <a name="access-to-on-premises-data-stores"></a>Acceso a los almacenes de datos locales

Si los paquetes SSIS acceden a almacenes de datos locales, puede conectar Azure-SSIS Integration Runtime a una red virtual que esté conectada a la red local. O bien, puede configurar y administrar un entorno de ejecución de integración autohospedado como proxy para Azure-SSIS Integration Runtime. Para más información, consulte [Configuración del entorno de ejecución de integración autohospedado como un proxy para Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). 

Al conectar Azure-SSIS Integration Runtime a una red virtual, recuerde estos puntos, ya que son importantes: 

- Si no hay ninguna red virtual existente conectada a su red local, primero cree una [red virtual de Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) a la que pueda conectarse Azure-SSIS Integration Runtime. Luego, configure una [conexión de puerta de enlace de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) de sitio a sitio o una conexión [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) entre esa red virtual su la red local. 

- Si hay una red de virtual de Azure Resource Manager que ya esté conectada a su red local en la misma ubicación que Azure-SSIS Integration Runtime, puede conectar el entorno de ejecución de integración a esa red virtual. 

- Si hay una red virtual clásica conectada a la red local en una ubicación diferente a la de Azure-SSIS Integration Runtime, puede crear una [red virtual de Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que se conecte Azure-SSIS Integration Runtime. A continuación, configure una conexión de [red virtual clásica a Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Si hay una red virtual de Azure Resource Manager conectada a la red local en una ubicación diferente a la de Azure-SSIS Integration Runtime, puede crear primero una [red virtual de Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que se conecte a Azure-SSIS Integration Runtime. Luego, configure una conexión de [red virtual de Azure Resource Manager a Azure Resource Manager](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hospedaje del catálogo de SSIS en SQL Database

Si hospeda el catálogo de SSIS en Azure SQL Database con puntos de conexión de servicio de red virtual, asegúrese de conectar la instancia de Azure-SSIS Integration Runtime a la misma red virtual y subred.

Si hospeda el catálogo de SSIS en Instancia administrada de SQL con punto de conexión privado, asegúrese de que conecta Azure-SSIS IR a la misma red virtual pero en una subred distinta de la instancia administrada. Para conectar Azure-SSIS Integration Runtime a una red virtual en la que no se encuentra la Instancia administrada de SQL, es aconsejable realizar un emparejamiento de redes virtuales (que se limite a la misma región) o una conexión de red virtual a red virtual. Para más información, consulte [Conexión de una aplicación a Instancia administrada de Azure SQL](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-services"></a>Acceso a servicios de Azure

Si los paquetes SSIS acceden a los recursos de Azure compatibles con los [puntos de conexión de servicio de red virtual](../virtual-network/virtual-network-service-endpoints-overview.md) y quiere proteger el acceso a esos recursos desde Azure-SSIS IR, puede conectar su instancia de Azure-SSIS IR a una subred de red virtual configurada para los puntos de conexión de servicio de red virtual y después agregar una regla de red virtual a los recursos de Azure pertinentes para permitir el acceso desde la misma subred.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Acceso a los orígenes de datos protegidos por la regla de firewall de IP

Si los paquetes SSIS acceden a almacenes de datos o recursos que solo permiten direcciones IP públicas estáticas específicas y desea proteger el acceso a esos recursos desde Azure-SSIS IR, puede asociar [direcciones IP públicas](../virtual-network/virtual-network-public-ip-address.md) a Azure-SSIS IR y conectar este a una red virtual, y después agregar una regla de firewall de IP a los recursos pertinentes para permitir el acceso desde esas direcciones IP. Hay dos formas alternativas de hacerlo: 

- Al crear Azure-SSIS IR, puede traer sus propias direcciones IP públicas y especificarlas por medio de la [Interfaz de usuario de Azure Data Factory Studio](join-azure-ssis-integration-runtime-virtual-network-ui.md) o el [SDK de Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md). Solo la conectividad de Internet de salida de Azure-SSIS IR usará las direcciones IP públicas proporcionadas y otros dispositivos de la subred no las usarán.
- También puede configurar [Virtual Network NAT](../virtual-network/nat-gateway/nat-overview.md) para la subred a la que se unirá Azure-SSIS IR y todas la conectividad de salida en esta subred usará las direcciones IP públicas especificadas.

En todos los casos, la red virtual solo puede implementarse mediante el modelo de implementación de Azure Resource Manager.

## <a name="next-steps"></a>Pasos siguientes

- [Detalles de configuración de la red virtual de Azure-SSIS Integration Runtime](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual con la interfaz de usuario de Azure Data Factory Studio](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual con Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](./tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa Azure SQL Database para hospedar el catálogo de SSIS. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones acerca del uso de Azure SQL Database con puntos de conexión de servicio de red virtual o una Instancia administrada de SQL en una red virtual para hospedar el catálogo SSIS. Muestra cómo conectar Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo obtener información acerca de Azure-SSIS IR. Proporciona descripciones del estado de la información devuelta. 
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Integration Runtime para la integración de SSIS en Azure mediante la adición de nodos.
