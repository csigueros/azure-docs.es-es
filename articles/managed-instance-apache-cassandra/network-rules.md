---
title: Reglas de red de salida necesarias para Azure Managed Instance for Apache Cassandra
description: Obtenga información sobre cuáles son las reglas de red de salida y los FQDN necesarios para Azure Managed Instance for Apache Cassandra.
author: christopheranderson
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: chrande
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6d52f1c72765b3e7d3b7dd171c53c84e85138a20
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005240"
---
# <a name="required-outbound-network-rules"></a>Reglas de red de salida necesarias

El servicio Azure Managed Instance for Apache Cassandra requiere ciertas reglas de red para administrar correctamente el servicio. Asegúrese de que tiene expuestas las reglas adecuadas para mantener el servicio seguro y evitar problemas operativos.

## <a name="virtual-network-service-tags"></a>Etiquetas de servicio de red virtual

Si usa Azure Firewall para restringir el acceso saliente, se recomienda encarecidamente usar [etiquetas de servicio de red virtual](../virtual-network/service-tags-overview.md). A continuación se muestran las etiquetas necesarias para que Azure Managed Instance for Apache Cassandra funcione correctamente.

| Etiqueta de servicio de destino                                                             | Protocolo | Port    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| Almacenamiento | HTTPS | 443 | Necesario para proteger la comunicación entre los nodos y Azure Storage, para la comunicación y configuración del plano de control.|
| AzureKeyVault | HTTPS | 443 | Necesario para proteger la comunicación entre los nodos y Azure Key Vault. Para proteger la comunicación dentro del clúster se usan certificados y claves.|
| EventHub | HTTPS | 443 | Necesario para reenviar registros a Azure. |
| AzureMonitor | HTTPS | 443 | Necesario para reenviar métricas a Azure. |
| AzureActiveDirectory| HTTPS | 443 | Obligatorio para autenticación de Azure Active Directory.|
| AzureResourceManager| HTTPS | 443 | Necesario para recopilar información sobre los nodos de Cassandra y administrarlos (por ejemplo, reiniciar).|
| AzureFrontDoor.Firstparty| HTTPS | 443 | Necesario para las operaciones de registro.|
| GuestAndHybridManagement | HTTPS | 443 |  Necesario para recopilar información sobre los nodos de Cassandra y administrarlos (por ejemplo, reiniciar). |
| ApiManagement  | HTTPS | 443 | Necesario para recopilar información sobre los nodos de Cassandra y administrarlos (por ejemplo, reiniciar). |

> [!NOTE]
> Además de lo anterior, también deberá agregar los siguientes prefijos de dirección, ya que no existe una etiqueta de servicio para el servicio correspondiente: 104.40.0.0/13 13.104.0.0/14 40.64.0.0/10

## <a name="user-defined-routes"></a>Rutas definidas por el usuario

Si usa un firewall de terceros para restringir el acceso saliente, se recomienda encarecidamente configurar [rutas definidas por el usuario (UDR)](../virtual-network/virtual-networks-udr-overview.md#user-defined) para prefijos de dirección de Microsoft, en lugar de intentar permitir la conectividad a través de su propio firewall. Consulte [script de Bash](https://github.com/Azure-Samples/cassandra-managed-instance-tools/blob/main/configureUDR.sh) de muestra para agregar los prefijos de dirección necesarios en rutas definidas por el usuario.

## <a name="azure-global-required-network-rules"></a>Reglas de red obligatorias globales de Azure

Las reglas de red obligatorias y las dependencias de dirección IP son las siguientes:

| Punto de conexión de destino                                                             | Protocolo | Port    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
|snovap\<region\>.blob.core.windows.net:443</br> Or</br> [ServiceTag:](../virtual-network/service-tags-overview.md#available-service-tags) Azure Storage | HTTPS | 443 | Necesario para proteger la comunicación entre los nodos y Azure Storage, para la comunicación y configuración del plano de control.|
|\*.store.core.windows.net:443</br> Or</br> [ServiceTag:](../virtual-network/service-tags-overview.md#available-service-tags) Azure Storage | HTTPS | 443 | Necesario para proteger la comunicación entre los nodos y Azure Storage, para la comunicación y configuración del plano de control.|
|\*.blob.core.windows.net:443</br> Or</br> [ServiceTag:](../virtual-network/service-tags-overview.md#available-service-tags) Azure Storage | HTTPS | 443 | Necesario para proteger la comunicación entre los nodos y Azure Storage, para almacenar copias de seguridad. *Se está revisando la característica de copia de seguridad y el nombre del almacenamiento seguirá un patrón cuando tenga disponibilidad general.*|
|vmc-p-\<region\>.vault.azure.net:443</br> Or</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): Azure KeyVault | HTTPS | 443 | Necesario para proteger la comunicación entre los nodos y Azure Key Vault. Para proteger la comunicación dentro del clúster se usan certificados y claves.|
|management.azure.com:443</br> Or</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): Azure Virtual Machine Scale Sets/Azure Management API | HTTPS | 443 | Necesario para recopilar información sobre los nodos de Cassandra y administrarlos (por ejemplo, reiniciar).|
|\*.servicebus.windows.net:443</br> Or</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): Azure EventHub | HTTPS | 443 | Necesario para reenviar registros a Azure.|
|jarvis-west.dc.ad.msft.net:443</br> Or</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): Azure Monitor | HTTPS | 443 | Necesario para reenviar métricas a Azure. |
|login.microsoftonline.com:443</br> Or</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): Azure AD | HTTPS | 443 | Obligatorio para autenticación de Azure Active Directory.|
| packages.microsoft.com | HTTPS | 443 | Necesario para las actualizaciones de las definiciones y firmas del analizador de seguridad de Azure. |
| azure.microsoft.com | HTTPS | 443 | Necesario para obtener información sobre los conjuntos de escalado de máquinas virtuales |
| \<region\>-dsms.dsms.core.windows.net | HTTPS | 443 | Certificado para el registro |
| gcs.prod.monitoring.core.windows.net | HTTPS | 443 | Punto de conexión de registro necesario para el registro |
| global.prod.microsoftmetrics.com | HTTPS | 443 | Necesario para las métricas |
| shavsalinuxscanpkg.blob.core.windows.net | HTTPS | 443 | Necesario para descargar o actualizar el analizador de seguridad |
| crl.microsoft.com | HTTPS | 443 | Necesario para acceder a certificados públicos de Microsoft |
| global-dsms.dsms.core.windows.net | HTTPS | 443 | Necesario para acceder a certificados públicos de Microsoft |

### <a name="dns-access"></a>Acceso DNS

El sistema usa nombres DNS para acceder a los servicios de Azure que se describen en este artículo para poder usar equilibradores de carga. Por lo tanto, la red virtual debe ejecutar un servidor DNS que pueda resolver esas direcciones. Las máquinas virtuales de la red virtual respetan el servidor de nombres que se comunica mediante el protocolo DHCP. En la mayoría de los casos, Azure configura automáticamente un servidor DNS para la red virtual. Si esto no ocurre en su escenario, los nombres DNS que se describen en este artículo son una buena guía para empezar.

## <a name="internal-port-usage"></a>Uso interno del puerto

Los puertos siguientes solo son accesibles dentro de la red virtual (o redes virtuales emparejadas o ExpressRoute). Las instancias de Managed Instance for Apache Cassandra no tienen una dirección IP pública y no deben ser accesibles en Internet.

| Port | Uso |
| ---- | --- |
| 8443 | Interno |
| 9443 | Interno |
| 7001 | Gossip: se usa para que los nodos de Cassandra se comuniquen entre sí. |
| 9042 | Cassandra: se usa para que los clientes se conecten a Cassandra. |
| 7199 | Interno |

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido acerca de las reglas de red para administrar correctamente el servicio. Más información sobre Azure Managed Instance for Apache Cassandra en los siguientes artículos:

* [¿Qué es Azure Managed Instance for Apache Cassandra? (versión preliminar)](introduction.md)
* [Administración de recursos de Azure Managed Instance for Apache Cassandra mediante la CLI de Azure](manage-resources-cli.md)
