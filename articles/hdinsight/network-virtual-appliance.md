---
title: Configuración de una aplicación virtual de red en Azure HDInsight
description: Aprenda a configurar otras características adicionales para el dispositivo virtual de red en Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: f92dc402323e8285f9a2c23ba9e4a229d72718a0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254780"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configuración de una aplicación virtual de red en Azure HDInsight

> [!Important]
> La siguiente información **solo** es necesaria si desea configurar una aplicación virtual de red (NVA) distinta a [Azure Firewall](./hdinsight-restrict-outbound-traffic.md).

La etiqueta de FQDN de Azure Firewall está configurada automáticamente para permitir el tráfico para muchos de los FQDN comunes más importantes. Si usa otra aplicación virtual de red, tendrá que configurar algunas características adicionales. Tenga en cuenta los siguientes factores al configurar la aplicación virtual de red:

* Los servicios compatibles con puntos de conexión de servicio se pueden configurar con estos, lo que da lugar a la omisión de la aplicación virtual de red, normalmente por motivos de costo o rendimiento.
* Si ResourceProviderConnection se establece en *outbound*, puede usar puntos de conexión privados para los servidores de almacenamiento y SQL Server para los metastores y no es necesario agregarlos a NVA.
* Las dependencias de dirección IP son para tráfico que no sea HTTP/HTTPS (tráfico TCP y UDP).
* Los puntos de conexión HTTP/HTTPS de FQDN se pueden aprobar en el dispositivo NVA.
* Asigne la tabla de rutas que creó a la subred de HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dependencias compatibles con los puntos de conexión de servicio

Opcionalmente, puede habilitar uno o varios de los siguientes puntos de conexión de servicio, lo que provocará que se omita el NVA. Esta opción puede ser útil para grandes cantidades de transferencias de datos con el fin de ahorrar costos y también para optimizar el rendimiento. 

| **Punto de conexión** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Dependencias de dirección IP

| **Punto de conexión** | **Detalles** |
|---|---|
| Las direcciones IP se publican [aquí](hdinsight-management-ip-addresses.md) | Estas direcciones IP sirven para el proveedor de recursos de HDInsight y deben incluirse en el UDR para evitar el enrutamiento asimétrico. Esta regla solo es necesaria si ResourceProviderConnection está establecido en *Inbound*. Si ResourceProviderConnection se establece en *Outbound*, estas direcciones IP no son necesarias en el UDR.  |
| Direcciones IP privadas de AAD-DS | Solo se necesitan para los clústeres ESP, si las redes virtuales no están emparejadas.|


### <a name="fqdn-httphttps-dependencies"></a>Dependencias HTTP/HTTPS de FQDN

Puede obtener la lista de dependencias de FQDN (principalmente de Azure Storage y Azure Service Bus) para configurar la aplicación virtual de red [en este repositorio](https://github.com/Azure-Samples/hdinsight-fqdn-lists/). Para la lista regional, consulte [aquí](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/main/Public). Estas dependencias las usa el proveedor de recursos (RP) de HDInsight para crear y supervisar o administrar clústeres de manera correcta. Incluyen registros de telemetría y diagnóstico, metadatos de aprovisionamiento, configuraciones relacionadas con el clúster, scripts, etc. Esta lista de dependencias de FQDN podría cambiar con la publicación de futuras actualizaciones de HDInsight.

La lista siguiente solo proporciona algunos FQDN que pueden ser necesarios para las actualizaciones de seguridad o las validaciones de certificados del sistema operativo *después* de crear el clúster y durante la vigencia de las operaciones del clúster.

| **FQDN de dependencias en tiempo de ejecución**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Pasos siguientes

* [Uso del firewall para restringir el tráfico de salida](./hdinsight-restrict-outbound-traffic.md)
* [Arquitectura de red virtual de Azure HDInsight](hdinsight-virtual-network-architecture.md)
