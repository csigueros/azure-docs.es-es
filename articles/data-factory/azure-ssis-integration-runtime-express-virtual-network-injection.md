---
title: Configuración de una red virtual para la inserción express de Azure-SSIS Integration Runtime
description: Obtenga información sobre cómo configuración de una red virtual para la inserción express de Azure-SSIS Integration Runtime
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1d904e33f830f36e41acd0afd32dc7dd7dfaf2c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093226"
---
# <a name="express-virtual-network-injection-method-preview"></a>Método de inserción rápida de la red virtual (versión preliminar)

Al usar SQL Server Integration Services (SSIS) en Azure Data Factory (ADF), hay dos métodos para unir el entorno de ejecución de integración de SSIS (IR) de Azure a una red virtual: estándar y express. Si usa el método express, debe configurar la red virtual para cumplir estos requisitos: 

- Asegúrese de que *Microsoft.Batch* es un proveedor de recursos registrado en la suscripción de Azure que tiene la red virtual idónea para la conexión a la misma de la instancia de Azure-SSIS IR. Para obtener instrucciones detalladas, consulte la sección sobre cómo [registrar Azure Batch como proveedor de recursos](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

- Asegúrese de que no existe ningún bloqueo de recursos en la red virtual.

- Seleccione una subred adecuada en la red virtual a la que Azure-SSIS IR unirse. Para obtener más información, consulte la sección [Seleccionar una subred](#subnet) a continuación.

- Asegúrese de que al usuario que crea Azure-SSIS IR se le conceden los permisos de control de acceso basado en rol (RBAC) necesarios para unirse a la red virtual o subred.  Para obtener más información, consulte la sección [Selección de permisos de red virtual](#perms) a continuación.

En función de su escenario específico, puede configurar opcionalmente lo siguiente:

- Si desea usar una dirección IP pública estática para el tráfico saliente de su Azure-SSIS IR, consulte la sección [Configuración de una dirección IP pública estática](#ip) a continuación.

- Si desea usar su propio servidor de sistema de nombres de dominio (DNS) en la red virtual, consulte la sección [Configuración de un servidor DNS](#dns) personalizado a continuación.

- Si desea usar un grupo de seguridad de red (NSG) para limitar el tráfico saliente en la subred, consulte la sección [Configuración de un grupo de seguridad de red](#nsg) a continuación.

- Si desea usar rutas definidas por el usuario (UDR) para auditar e inspeccionar el tráfico saliente, consulte la sección [Configuración de UDR](#udr) a continuación.

Este diagrama muestra las conexiones necesarias para Azure-SSIS Integration Runtime:

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-express.png" alt-text="Diagrama que muestra las conexiones necesarias para el Azure-SSIS IR inserción de red virtual express.":::

## <a name="select-a-subnet"></a><a name="subnet"></a>Seleccione una subred

Para habilitar la inserción de red virtual express, debe seleccionar una subred adecuada a la que Azure-SSIS IR unirse:

- No seleccione GatewaySubnet, ya que está dedicado para puertas de enlace de red virtual.

- Asegúrese de que la subred seleccionada tiene direcciones IP disponibles al menos dos veces el número de Azure-SSIS IR nodo. Estos son necesarios para evitar interrupciones al implementar revisiones o actualizaciones para su Azure-SSIS IR. Azure también reserva algunas direcciones IP que no se pueden usar en cada subred. La primera y la última dirección IP están reservadas para el cumplimiento del protocolo, mientras que otras tres direcciones están reservadas para los servicios de Azure. Para más información, consulte la sección [Restricciones de direcciones IP de subred.](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

- No use una subred que esté ocupada exclusivamente por otros servicios de Azure (por ejemplo, Azure SQL Managed Instance, App Service, etc.).

- La subred seleccionada debe delegarse al servicio *Microsoft.Batch/batchAccounts.* Para obtener más información, consulte el artículo [Introducción a la delegación de subredes.](../virtual-network/subnet-delegation-overview.md) Para obtener instrucciones detalladas, consulte la sección [Delegación de una subred a Azure Batch](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet).

## <a name="select-virtual-network-permissions"></a><a name="perms"></a>Selección de permisos de red virtual

Para habilitar la inserción de red virtual express, al usuario que crea Azure-SSIS IR se le deben conceder los permisos de RBAC necesarios para unirse a la red virtual o subred. Tiene dos opciones:

- Usar el rol integrado *Colaborador de red*. Este rol requiere el permiso _Microsoft.Network /\*_ , que tiene un ámbito mucho mayor del necesario.

- Crear un rol personalizado que incluya solo el permiso *Microsoft.Network/virtualNetworks/subnets/join/action*.

Para obtener instrucciones detalladas, consulte la sección [Concesión de permisos de red](azure-ssis-integration-runtime-virtual-network-configuration.md#grantperms) virtual.

## <a name="configure-a-static-public-ip-address"></a><a name="ip"></a>Configuración de una dirección IP pública estática

Si desea usar una dirección IP pública estática para el tráfico saliente de su Azure-SSIS IR, para que pueda permitirla en los firewalls, debe configurar la [traducción de direcciones de red virtual (NAT)](../virtual-network/nat-gateway/nat-overview.md) para configurarla.

## <a name="configure-a-custom-dns-server"></a><a name="dns"></a>Configuración de un servidor DNS personalizado

Si desea usar su propio servidor DNS en la red virtual para resolver los nombres de host privados, asegúrese de que también puede resolver nombres de host globales de Azure (por ejemplo, el nombre de Azure Blob Storage denominado `<your storage account>.blob.core.windows`).

Se recomienda configurar su propio servidor DNS para reenviar solicitudes DNS sin resolver a la dirección IP de las resoluciones recursivas de Azure (*168.63.129.16*).

Para más información, consulte la sección [Resolución de nombres de servidor DNS.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

En la actualidad, Azure-SSIS IR usar su propio servidor DNS, debe configurarlo con una configuración personalizada estándar siguiendo estos pasos:

1. Descargue un script de instalación personalizada ([main.cmd](https://expressvnet.blob.core.windows.net/customsetup/main.cmd)) + el archivo asociado ([setupdnsserver.ps1](https://expressvnet.blob.core.windows.net/customsetup/setupdnsserver.ps1)).

1. Reemplace "your-dns-server-ip" en main.cmd por la dirección IP de su propio servidor DNS.

1. Upload main.cmd + setupdnsserver.ps1 en su propio contenedor de blobs de Azure Storage para la instalación personalizada estándar y escriba su URI de SAS al aprovisionar Azure-SSIS IR, consulte el artículo [Personalización de Azure-SSIS IR.](how-to-configure-azure-ssis-ir-custom-setup.md)

> [!NOTE]
> Use un nombre de dominio completo (FQDN) para el nombre de host privado (por ejemplo, use `<your_private_server>.contoso.com` en lugar de `<your_private_server>`). Como alternativa, puede utilizar una configuración personalizada estándar en Azure-SSIS IR para anexar automáticamente su propio sufijo DNS (por ejemplo `contoso.com`) a un nombre de dominio de una sola etiqueta no calificado y convertirlo en un FQDN antes de usarlo en consultas DNS (consulte la sección [Ejemplos de instalación personalizada estándar](how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples)). 

## <a name="configure-an-nsg"></a><a name="nsg"></a>Configuración de un NSG

Si desea usar un grupo de seguridad de red en la subred unida por la Azure-SSIS IR, permita el siguiente tráfico saliente: 

| Protocolo de transporte | Source | Puertos de origen | Destination | Puertos de destino | Comentarios | 
|--------------------|--------|--------------|-------------|-------------------|----------| 
| TCP | *VirtualNetwork* | * | *DataFactoryManagement* | *443* | Se requiere para que Azure-SSIS IR acceso a los servicios de ADF.<br/><br/>Por ahora, su tráfico saliente solo usa el punto de conexión público de ADF. | 
| TCP | *VirtualNetwork* | * | *Sql/VirtualNetwork* | *1433, 11000-11999* | (Opcional) Solo es necesario si usa Azure SQL Database servidor o Instancia administrada para hospedar el catálogo de SSIS (SSISDB).<br/><br/>Si el Azure SQL Database servidor o Instancia administrada está configurado con un punto de conexión público o un punto de conexión de servicio de red virtual, use la etiqueta de servicio *Sql* como destino.<br/><br/>Si el servidor de Azure SQL Database o instancia administrada está configurado con un punto de conexión privado, use la etiqueta de servicio *VirtualNetwork* como destino.<br/><br/>Si la directiva de conexión del servidor está establecida en *Proxy*, en lugar de *Redirigir*, solo se requiere el puerto *1433*. | 
| TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *443* | (Opcional) Solo es necesario si usa un Azure Storage blobs para almacenar el script o los archivos de instalación personalizados estándar.<br/><br/>Si Azure Storage está configurado con un punto de conexión público o un punto de conexión de servicio de red virtual, use la etiqueta de servicio de *Storage* como destino.<br/><br/>Si Azure Storage está configurado con un punto de conexión privado, use la etiqueta de servicio *VirtualNetwork* como destino. | 
| TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *445* | (Opcional) Solo es necesario si necesita acceder a Azure Files.<br/><br/>Si Azure Storage está configurado con un punto de conexión público o un punto de conexión de servicio de red virtual, use la etiqueta de servicio de *Storage* como destino.<br/><br/>Si Azure Storage está configurado con un punto de conexión privado, use la etiqueta de servicio *VirtualNetwork* como destino. | 

## <a name="configure-udrs"></a><a name="udr"></a>Configuración de UDR

Si desea auditar o inspeccionar el tráfico saliente desde su Azure-SSIS IR, puede usar [rutas definidas por el usuario (UDR)](../virtual-network/virtual-networks-udr-overview.md) para redirigirlo a un dispositivo de firewall local [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) a través de una tunelización forzada que anuncia una ruta de protocolo de puerta de enlace de borde (BGP) *0.0.0.0/0* a la red virtual, a una aplicación virtual de red (NVA) configurada como firewall o a un servicio [Azure Firewall](../firewall/overview.md).

Mediante las instrucciones de la sección Anterior [Configuración de un grupo de seguridad de red](#nsg), debe implementar reglas similares en el dispositivo o servicio del firewall para permitir el tráfico saliente desde la Azure-SSIS IR:

- Si usa Azure Firewall:
  - Debe abrir el puerto *443* para el tráfico TCP saliente con la etiqueta de servicio *DataFactoryManagement* como destino.

  - Si usa Azure SQL Database server/Instancia administrada para hospedar SSISDB, debe abrir los puertos *1433, 11000-11999* para el tráfico TCP saliente con la etiqueta de servicio *Sql/VirtualNetwork* como destino.

  - Si usa un contenedor de blobs Azure Storage para almacenar el script o los archivos de instalación personalizada estándar, debe abrir el puerto *443* para el tráfico TCP saliente con la etiqueta de servicio *Storage/VirtualNetwork* como destino.

  - Si necesita acceder a Azure Files, debe abrir el puerto *445* para el tráfico TCP saliente con la etiqueta de servicio *Storage/VirtualNetwork* como destino.

- Si usa otro dispositivo o servicio de firewall:
  - Debe abrir el *puerto 443* para el tráfico TCP saliente con *0.0.0.0/0* o el siguiente FQDN específico del entorno de Azure como destino:

    | Entorno de Azure | FQDN |
    |-------------------|------|
    | <b>Azure público</b> | _\*.frontend.clouddatahub.net_ |
    | <b>Azure Government</b> | _\*.frontend.datamovement.azure.us_ |
    | <b>Azure China 21Vianet</b> | _\*.frontend.datamovement.azure.cn_ |

  - Si usa el servidor de Azure SQL Database o una instancia administrada para hospedar SSISDB, debe abrir los puertos *1433, 11000-11999* para el tráfico TCP saliente con *0.0.0.0/0* o el FQDN del servidor de Azure SQL Database o instancia administrada como destino.

  - Si usa un contenedor de blobs para almacenar el script o los archivos de instalación personalizada estándar, debe abrir el puerto *443* para el tráfico TCP saliente con *0.0.0.0/0* o el FQDN de Azure Blob Storage como destino. Azure Storage.

  - Si necesita acceder a Azure Files, debe abrir el puerto *445* para el tráfico TCP saliente con *0.0.0.0/0* o el FQDN de Azure Files como destino.

## <a name="next-steps"></a>Pasos siguientes

- [Conexión de Azure-SSIS IR a una red virtual mediante la interfaz de usuario de ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Conexión de Azure-SSIS IR a una red virtual mediante Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa el servidor de Azure SQL Database para hospedar SSISDB. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones sobre el uso de un servidor de Azure SQL Database configurado con un punto de conexión de servicio de red virtual, una regla de firewall de IP, un punto de conexión privado o Azure SQL Managed Instance que se conecta a una red virtual para hospedar SSISDB. Muestra cómo conectar Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
