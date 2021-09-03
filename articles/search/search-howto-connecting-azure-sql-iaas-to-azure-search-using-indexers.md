---
title: Conexión del indizador a SQL Server en máquinas virtuales de Azure
titleSuffix: Azure Cognitive Search
description: Habilite conexiones cifradas y configure el firewall para permitir conexiones a SQL Server en una máquina virtual de Azure a partir de un indexador de Búsqueda cognitiva de Azure.
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: 4aa60a5433f13ca6b942204bbdb8eb4425d0ddf7
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2021
ms.locfileid: "112983219"
---
# <a name="indexer-connections-to-sql-server-on-an-azure-virtual-machine"></a>Conexiones del indizador a SQL Server en una máquina virtual de Azure

Al configurar un [indizador de Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq) para extraer contenido de una base de datos en una máquina virtual de Azure, se requieren pasos adicionales para las conexiones seguras. 

La conexión de Azure Cognitive Search a SQL Server en una máquina virtual es una conexión pública de Internet. Para que las conexiones seguras se realicen correctamente, complete los pasos siguientes:

+ Obtenga un [Certificado de una entidad de certificación](https://en.wikipedia.org/wiki/Certificate_authority#Providers) para el nombre de dominio completo de la instancia de SQL Server de la máquina virtual

+ Instale el certificado en la máquina virtual y, a continuación, habilite y configure las conexiones cifradas en la máquina virtual siguiendo las instrucciones de este artículo.

## <a name="enable-encrypted-connections"></a>Habilitación de conexiones cifradas

Búsqueda cognitiva de Azure requiere un canal cifrado para todas las solicitudes del indexador a través una conexión pública a Internet. En esta sección se enumeran los pasos necesarios para realizar este trabajo.

1. En las propiedades del certificado compruebe que el nombre de sujeto es el nombre de dominio completo (FQDN) de la máquina virtual de Azure. Para ver las propiedades, puede utilizar una herramienta como CertUtils o el complemento Certificados. El FQDN se puede obtener de la sección Essentials de la hoja del servicio VM, en el campo **Etiqueta de dirección IP pública/nombre de DNS** de [Azure Portal](https://portal.azure.com/).
  
   + En el caso de las máquinas virtuales creadas mediante la plantilla de **Resource Manager** más reciente, el nombre de dominio completo tiene el formato `<your-VM-name>.<region>.cloudapp.azure.com`.

   + En el caso de las VM anteriores creadas como una VM **clásica**, el FQDN tiene el formato `<your-cloud-service-name.cloudapp.net>`.

1. Configure SQL Server para que use el certificado mediante el Editor del registro (regedit). 

   Aunque el Administrador de configuración de SQL Server se usa a menudo para esta tarea, no se puede utilizar para este escenario. No encontrará el certificado importado, ya que el FQDN de la VM de Azure no coincide con el FQDN que determina la VM (identifica el dominio como el equipo local o el dominio de red al que está conectado). Si los nombres no coinciden, utilice regedit para especificar el certificado.

   + En regedit, vaya a esta clave del Registro: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.

     La parte `[MSSQL13.MSSQLSERVER]` varía en función de la versión y del nombre de la instancia. 

   + Establezca el valor de la clave del **Certificado** en la **huella digital** del certificado TLS/SSL que importó a la VM.

     Hay varias maneras de obtener la huella digital, y algunas de ellas son mejores que otras. Si la copia desde el complemento **Certificados** de MMC, es probable que elija un carácter inicial invisible [como se describe en este artículo](https://support.microsoft.com/kb/2023869/), lo que provoca un error al intentar establecer una conexión. Existen varias soluciones para corregir este problema. La más fácil es borrarlo y, luego, volver a escribir el primer carácter de la huella digital para quitar el carácter inicial del campo de valor de clave en regedit. Como alternativa, se puede utilizar otra herramienta para copiar la huella digital.

1. Otorgue permisos a la cuenta de servicio. 

    Asegúrese de que a la cuenta de servicio de SQL Server se le concede el permiso adecuado en la clave privada del certificado TLS/SSL. Si pasa por alto este paso, SQL Server no se iniciará. Puede usar el complemento **Certificados** o **CertUtils** para esta tarea.

1. Reinicie el servicio SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configuración de la conectividad de SQL Server en la máquina virtual

Después de configurar la conexión cifrada requerida por Búsqueda cognitiva de Azure, existen pasos adicionales de configuración intrínsecos a SQL Server en las máquinas virtuales de Azure. Si aún no lo ha hecho, el paso siguiente es finalizar la configuración mediante cualquiera de estos artículos:

+ En el caso de una máquina virtual de **Resource Manager** , consulte [Conexión a una máquina virtual de SQL Server en Azure (Resource Manager)](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md). 

+ En el caso de una máquina virtual **clásica** , consulte [Conexión a una máquina virtual de SQL Server en Azure (implementación clásica)](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect).

En concreto, consulte en ambos artículos la sección dedicada a la "conexión a través de Internet".

## <a name="configure-the-network-security-group-nsg"></a>Configuración del grupo de seguridad de red (NSG)

No es extraño configurar el NSG y el correspondiente punto de conexión o lista de control de acceso (ACL) de Azure para que se pueda acceder a la máquina virtual de Azure desde otras partes. Lo más probable es que ya haya realizado esta operación para la lógica de su aplicación se conecte a la máquina virtual de SQL Azure. Esto es igual para una conexión de Búsqueda cognitiva de Azure a la máquina virtual de SQL Azure. 

Los vínculos siguientes proporcionan instrucciones para la configuración de NSG en las implementaciones de VM. Siga estas instrucciones para incluir en la ACL un punto de conexión de Búsqueda cognitiva de Azure por su dirección IP.

> [!NOTE]
> Para más información, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/network-security-groups-overview.md)

+ En el caso de una máquina virtual de **Resource Manager** , consulte [Administración de grupos de seguridad de red con Azure Portal](../virtual-network/tutorial-filter-network-traffic.md).

+ En el caso de una máquina virtual **clásica** , consulte [Creación de grupos de seguridad de red (clásicos) en PowerShell](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps).

La dirección IP puede plantear ciertos problemas, que se solucionan fácilmente si se conoce el problema y las posibles soluciones. En las restantes secciones encontrará recomendaciones para el control de los problemas relacionados con las direcciones IP de la ACL.

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Restricción del acceso a Azure Cognitive Search

Se recomienda encarecidamente restringir el acceso a la dirección IP del servicio de búsqueda y el rango de direcciones IP de la  [etiqueta de servicio](../virtual-network/service-tags-overview.md#available-service-tags)`AzureCognitiveSearch` en la ACL en lugar de abrir las máquinas virtuales de SQL Azure a todas las solicitudes de conexión.

Puede averiguar la dirección IP haciendo ping en el FQDN (por ejemplo, `<your-search-service-name>.search.windows.net`) del servicio de búsqueda. Aunque la dirección IP del servicio de búsqueda podría cambiar, no es probable que lo haga. La dirección IP tiende a ser estática durante la vigencia del servicio.

Puede averiguar el intervalo de direcciones IP de la [etiqueta de servicio](../virtual-network/service-tags-overview.md#available-service-tags) `AzureCognitiveSearch` mediante el uso de [archivos JSON descargables](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) o a través de la [API de detección de etiquetas de servicio](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). El intervalo de direcciones IP se actualiza semanalmente.

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Inclusión de las direcciones IP del portal de Búsqueda cognitiva de Azure

Si usa el Azure Portal para crear un indizador, debe conceder acceso de entrada del portal a la máquina virtual SQL Azure. Una regla de entrada en el Firewall requiere que proporcione la dirección IP del portal.

Para obtener la dirección IP del portal, haga ping `stamp2.ext.search.windows.net`, que es el dominio del administrador de tráfico. Se agotará el tiempo de espera de la solicitud, pero la dirección IP estará visible en el mensaje de estado. Por ejemplo, en el mensaje "Pinging azsyrie.northcentralus.cloudapp.azure.com [52.252.175.48]", la dirección IP es "52.252.175.48".

> [!NOTE]
> Los clústeres de diferentes regiones se conectan a diferentes administradores de tráfico. Independientemente del nombre de dominio, la dirección IP devuelta por ping es la correcta que se va a usar al definir una regla de Firewall de entrada para el Azure Portal en su región.

## <a name="next-steps"></a>Pasos siguientes

Dejando a un lado la configuración, ya puede especificar un servicio SQL Server en la máquina virtual de Azure como origen de los datos de un indexador de Búsqueda cognitiva de Azure. Para más información, consulte [Conexión de Azure SQL Database a Azure Cognitive Search mediante indizadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).