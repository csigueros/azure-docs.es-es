---
title: Habilitación de Private Link en el clúster de Azure HDInsight
description: Aprenda a conectarse a un clúster de HDInsight externo mediante Azure Private Link.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 5ed9587c0c8bdb378206db70ca459cc8cd004b45
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215566"
---
# <a name="enable-private-link-on-an-hdinsight-cluster"></a>Habilitación de Private Link en un clúster de HDInsight

En este artículo, aprenderá a usar Azure Private Link para conectarse a un clúster de HDInsight de forma privada mediante redes de la red troncal de Microsoft. Este artículo es una extensión del artículo [Restricción de la conectividad de clústeres en Azure HDInsight](./hdinsight-restrict-public-connectivity.md), que se centra en restringir la conectividad pública. Si quiere conectividad pública con los clústeres de HDInsight o dentro de ellos y los recursos dependientes, considere la posibilidad de restringir la conectividad del clúster siguiendo las instrucciones del [control del tráfico de red en Azure HDInsight](./control-network-traffic.md).

Private Link se puede usar en escenarios entre redes en los que el emparejamiento de redes virtuales no está disponible o habilitado.

> [!NOTE]
> Restringir la conectividad pública es un requisito previo para habilitar Private Link y no debe considerarse como la misma funcionalidad.

El uso de Private Link para conectarse a un clúster de HDInsight es una característica opcional y está deshabilitado de forma predeterminada. La característica solo está disponible cuando la propiedad de red `resourceProviderConnection` se establece en *saliente*, tal como se describe en el artículo [Restricción de la conectividad de clústeres en Azure HDInsight](./hdinsight-restrict-public-connectivity.md).

Cuando `privateLink` se establece en *habilitado*, se crean [equilibradores de carga estándar](../load-balancer/load-balancer-overview.md) (SLB) y se aprovisiona un servicio Azure Private Link para cada uno de ellos. El servicio Private Link es lo que permite acceder al clúster de HDInsight desde puntos de conexión privados.

## <a name="prerequisites"></a>Requisitos previos

A diferencia de los equilibradores de carga básicos, los estándar no proporcionan automáticamente la [NAT pública de salida](../load-balancer/load-balancer-outbound-connections.md). Debe proporcionar su propia solución NAT, como una puerta de enlace de NAT o una NAT proporcionada por el [firewall](./hdinsight-restrict-outbound-traffic.md), para conectarse a dependencias de HDInsight públicas de salida. 

El clúster de HDInsight de todos modos necesita acceso a sus dependencias de salida. Si no se permiten estas dependencias de salida, podría producirse un error en la creación del clúster. 

### <a name="configure-a-default-network-security-group-on-the-subnet"></a>Configuración de un grupo de seguridad de red predeterminado en la subred

Cree y agregue un grupo de seguridad de red (NSG) en la subred donde pretende implementar el clúster de HDInsight. Se requiere un NSG para habilitar la conectividad de salida.

### <a name="disable-network-policies-for-the-private-link-service"></a>Deshabilitación de las directivas de red para el servicio Private Link

Para crear correctamente servicios Private Link, debe [deshabilitar las directivas de red para servicios Private Link](../private-link/disable-private-link-service-network-policy.md) explícitamente.

### <a name="configure-a-nat-gateway-on-the-subnet"></a>Configuración de una puerta de enlace NAT en la subred

Puede optar por usar una puerta de enlace NAT si no desea configurar un firewall o una aplicación virtual de red (NVA) para NAT. Si no, continúe con el requisito previo siguiente.

Para empezar, agregue una puerta de enlace NAT (con una nueva dirección IP pública en la red virtual) a la subred configurada de la red virtual. Esta puerta de enlace es responsable de traducir la dirección IP interna privada a direcciones públicas cuando el tráfico debe salir de la red virtual.

### <a name="configure-a-firewall-optional"></a>Configuración de un firewall (opcional)
Para comenzar con una configuración básica:

1. Agregue una nueva subred con el nombre *AzureFirewallSubnet* a la red virtual. 
1. Use la nueva subred para configurar un nuevo firewall y agregar las directivas de firewall. 
1. Use la dirección IP privada del nuevo firewall como valor `nextHopIpAddress` en la tabla de rutas. 
1. Agregue la tabla de rutas a la subred configurada de la red virtual.

Para más información sobre cómo configurar un firewall, consulte [Control del tráfico de red en Azure HDInsight](./control-network-traffic.md).

En el diagrama siguiente se muestra un ejemplo de la configuración de red necesaria antes de crear un clúster. En este ejemplo, todo el tráfico de salida se dirige obligatoriamente a Azure Firewall a través de una ruta definida por el usuario. Las dependencias de salida necesarias deben permitirse en el firewall antes de la creación del clúster. En el caso de los clústeres de Enterprise Security Package, el emparejamiento de red virtual puede proporcionar la conectividad de red a Azure Active Directory Domain Services.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagrama del entorno de Private Link antes de la creación del clúster.":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Administración de puntos de conexión privados para Azure HDInsight

Puede usar [puntos de conexión privados](../private-link/private-endpoint-overview.md) para los clústeres de Azure HDInsight para que los clientes de una red virtual puedan acceder de forma segura al clúster mediante [Private Link](../private-link/private-link-overview.md). El tráfico de red entre los clientes de la red virtual y el clúster de HDInsight atraviesa la red troncal de Microsoft, lo que elimina la exposición a la red pública de Internet.

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="Diagrama de la experiencia de administración de puntos de conexión privados.":::

Un consumidor del servicio Private Link (p. ej., Azure Data Factory) se puede elegir entre dos métodos de aprobación de conexión:

* **Automático**: si el consumidor del servicio tiene permisos de control de acceso basado en roles (RBAC) de Azure en el recurso de HDInsight, el consumidor puede elegir el método de aprobación automático. En este caso, cuando el recurso de HDInsight recibe la solicitud, no se requiere ninguna acción por parte del recurso y la conexión se aprueba automáticamente.
* **Manual**: si el consumidor del servicio no tiene permisos de RBAC de Azure en el recurso de HDInsight, el consumidor puede elegir el método de aprobación manual. En este caso, la solicitud de conexión aparece en los recursos de HDInsight como **Pendiente**. El recurso de HDInsight debe aprobar manualmente la solicitud para que se puedan establecer conexiones. 

Para administrar puntos de conexión privados, en la vista de clústeres de Azure Portal, vaya a la sección **Redes** en **Seguridad y redes**. Aquí puede ver todas las conexiones existentes, los estados de conexión y los detalles del punto de conexión privado.

También puede aprobar, rechazar o quitar las conexiones existentes. Al crear una conexión privada, puede especificar a qué subrecurso de HDInsight (por ejemplo, puerta de enlace o nodo principal) también desea conectarse.

En la siguiente tabla se muestran las distintas acciones del recurso de HDInsight y los estados de conexión resultantes para los puntos de conexión privados. Un recurso de HDInsight también puede cambiar el estado de conexión de la conexión de punto de conexión privado en un momento posterior sin la intervención del consumidor. La acción actualizará el estado del punto de conexión en el lado del consumidor.

| Acción del proveedor de servicios | Estado de punto de conexión privado del consumidor del servicio | Descripción |
| --------- | --------- | --------- |
| None | Pending | La conexión se crea manualmente y está pendiente de aprobación por parte del propietario del recurso de Private Link. |
| Aprobación | Aprobado | La conexión se aprobó de forma automática o manual y está lista para usarse. |
| Reject | Rechazada | El propietario del recurso de Private Link rechazó la conexión. |
| Remove | Escenario desconectado | El propietario del recurso de Private Link quitó la conexión. El punto de conexión privado se vuelve informativo y debe eliminarse para la limpieza. |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>Configuración de DNS para la conexión mediante puntos de conexión privados

Después de haber configurado las redes, puede crear un clúster con una conexión del proveedor de recursos saliente y Private Link habilitado.

Para acceder a clústeres privados, puede usar extensiones de DNS de Private Link y puntos de conexión privados. Cuando `privateLink` se establece en *habilitado*, puede crear puntos de conexión privados y configurar la resolución de DNS mediante zonas DNS privadas.

Las entradas de Private Link creadas en la zona DNS pública `azurehdinsight.net` administrada por Azure son las siguientes:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
En la imagen siguiente se muestra un ejemplo de las entradas de DNS privadas configuradas para habilitar el acceso a un clúster desde una red virtual que no está emparejada o no tiene una línea de visión directa para el clúster. Puede usar una zona privada de Azure DNS para reemplazar los nombres de dominio completos (FQDN) `*.privatelink.azurehdinsight.net` y resolver las direcciones IP de puntos de conexión privados en la red del cliente. La configuración es solo para `<clustername>.azurehdinsight.net` en el ejemplo, pero también se extiende a otros puntos de conexión de clúster.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagrama de la arquitectura de Private Link.":::

## <a name="create-clusters"></a>Creación de clústeres

El fragmento de código JSON siguiente incluye las dos propiedades de red que tiene que configurar en una plantilla de Azure Resource Manager para crear un clúster de HDInsight privado:

```json
networkProperties: {
    "resourceProviderConnection": "Outbound",
    "privateLink": "Enabled"
}
```

Para una plantilla completa con muchas de las características de Enterprise Security de HDInsight, incluido Private Link, consulte el artículo sobre la [plantilla de Enterprise Security de HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

Para crear un clúster mediante PowerShell, consulte el [ejemplo](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

Para crear un clúster mediante la CLI de Azure, consulte el [ejemplo](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Pasos siguientes

* [Enterprise Security Package para Azure HDInsight](enterprise-security-package.md)
* [Directrices generales e información de seguridad de la empresa en Azure HDInsight](./domain-joined/general-guidelines.md)