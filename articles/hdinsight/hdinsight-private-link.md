---
title: Habilitación de Private Link en un clúster de HDInsight restringido (versión preliminar)
description: Aprenda a conectarse fuera del clúster de HDInsight mediante Azure Private Link.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 9f432d37e58069decdc9a97e55d926aed3b7277f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710030"
---
# <a name="enable-private-link-on-hdinsight-cluster-preview"></a>Habilitación de Private Link en el clúster de HDInsight (versión preliminar)

## <a name="overview"></a>Información general
En este artículo, aprenderá a aprovechar Azure Private Link para conectarse al clúster de HDInsight de forma privada mediante redes de la red troncal de Microsoft. Este artículo es una extensión de nuestro artículo principal [Restricción de la conectividad de clústeres en Azure HDInsight](./hdinsight-restrict-public-connectivity.md), donde nos centramos en restringir la conectividad pública. En el caso de que pueda optar por tener conectividad pública en los clústeres de HDInsight y recursos dependientes y desde ellos, considere la posibilidad de restringir la conectividad del clúster siguiendo las instrucciones del [control del tráfico de red en Azure HDInsight](./control-network-traffic.md).

Private Link se puede aprovechar en escenarios entre redes virtuales en los que el emparejamiento de redes virtuales no está disponible o habilitado. Por ejemplo, si quiere integrar Azure Data Factory con Azure HDInsight, donde es necesario que Azure Data Factory se conecte con los clústeres de HDInsight mediante una red privada (p. ej., Private Link) por motivos de cumplimiento y seguridad.

> [!NOTE]
> Restringir la conectividad pública es un requisito previo para habilitar Private Link y no debe considerarse como la misma funcionalidad.

Private Link es una característica opcional y está deshabilitada de forma predeterminada. La característica solo está disponible cuando la propiedad de red `resourceProviderConnection` se establece en *saliente*, tal como se describe en el artículo [Restricción de la conectividad de clústeres en Azure HDInsight](./hdinsight-restrict-public-connectivity.md).

Cuando `privateLink` está establecido en *habilitado*, se crean [equilibradores de carga estándar](../load-balancer/load-balancer-overview.md) (SLB) y se aprovisiona un servicio Azure Private Link para cada uno de ellos. El servicio Private Link es lo que permite acceder al clúster de HDInsight desde puntos de conexión privados.

## <a name="prerequisites"></a>Prerrequisitos

A diferencia de los equilibradores de carga básicos, los estándar no proporcionan automáticamente la [NAT pública de salida](../load-balancer/load-balancer-outbound-connections.md). Debe proporcionar su propia solución NAT, como una puerta de enlace de NAT o una NAT proporcionada por el [firewall](./hdinsight-restrict-outbound-traffic.md), para conectarse a dependencias de HDInsight públicas de salida. El clúster de HDInsight de todos modos necesita acceso a sus dependencias de salida. Si no se permiten estas dependencias de salida, puede producirse un error en la creación del clúster. También se debe configurar un grupo de seguridad de red en la subred para habilitar la conectividad de salida.

### <a name="1--configure-a-default-network-security-group-nsg-on-the-subnet"></a>1. Configuración de un grupo de seguridad de red (NSG) predeterminado en la subred

Cree y agregue un grupo de seguridad de red en la subred donde se implementará el clúster de HDInsight.

### <a name="2--disable-network-policies-for-private-link-service"></a>2.  Deshabilitación de las directivas de red para el servicio Private Link

Para crear correctamente servicios de vínculo privado, debe [deshabilitar explícitamente las directivas de red para servicios de vínculo privado](../private-link/disable-private-link-service-network-policy.md).

### <a name="3--configure-a-nat-gateway-on-the-subnet"></a>3.  Configuración de una instancia de NAT Gateway en la subred

Puede optar por usar NAT Gateway si no quiere configurar un firewall ni una aplicación virtual de red (NVA) para NAT. En caso contrario, pase al siguiente requisito previo.

Para empezar, simplemente, agregue una puerta de enlace NAT (con una nueva dirección IP pública en la red virtual) a la subred configurada de la red virtual. Esta puerta de enlace es responsable de traducir la dirección IP interna privada a direcciones públicas cuando el tráfico debe salir de la red virtual.

### <a name="4--using-firewall-or-network-virtual-appliance-nvas-for-nat-optional"></a>4.  Uso de un firewall o una aplicación virtual de red (NVA) para NAT (opcional)
Para comenzar con una configuración básica, comience por agregar una nueva subred "AzureFirewallSubnet" a la red virtual. Una vez creada, úsela para configurar un nuevo firewall y agregar las directivas de firewall. Una vez configurado el firewall, use la dirección IP privada de este firewall como `nextHopIpAddress` en la tabla de rutas. Agregue esta tabla de rutas a la subred configurada de la red virtual.
Para obtener más información sobre cómo configurar un firewall, consulte [Control del tráfico de red en Azure HDInsight](./control-network-traffic.md).

En el diagrama siguiente se muestra un ejemplo de la configuración de red necesaria antes de crear un clúster. En este ejemplo, todo el tráfico de salida es forzado a Azure Firewall mediante UDR y se deben "permitir" las dependencias de salida requeridas en el firewall antes de crear un clúster. En el caso de los clústeres de Enterprise Security Package, el emparejamiento de red virtual puede proporcionar la conectividad de red a Azure Active Directory Domain Services.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagrama del entorno de Private Link antes de la creación del clúster":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Administración de puntos de conexión privados para Azure HDInsight

Puede usar [puntos de conexión privados](../private-link/private-endpoint-overview.md) para los clústeres de Azure HDInsight para que los clientes de una red virtual (VNet) puedan acceder de forma segura al clúster mediante [Private Link](../private-link/private-link-overview.md). El tráfico de red entre los clientes de la red virtual y el clúster de HDInsight atraviesa una red troncal de Microsoft, lo que elimina la exposición a la red pública de Internet.

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="Diagrama de la experiencia de administración de puntos de conexión privados":::

Hay dos métodos de aprobación de conexión entre los que puede elegir un consumidor del servicio Private Link (p. ej., Azure Data Factory):
* **Automático**: si el consumidor del servicio tiene permisos de Azure RBAC en el recurso de HDInsight, el consumidor puede elegir el método de aprobación automático. En este caso, cuando el recurso de HDInsight recibe la solicitud, no se requiere ninguna acción por parte del recurso y la conexión se aprueba automáticamente.
* **Manual**: por el contrario, si el consumidor del servicio no tiene permisos de Azure RBAC en el recurso de HDInsight, el consumidor puede elegir el método de aprobación manual. En este caso, la solicitud de conexión aparece en los recursos de HDInsight como Pendiente. El recurso de HDInsight debe aprobar manualmente la solicitud para que se puedan establecer conexiones. 

Para administrar puntos de conexión privados, en la vista de clústeres de Azure Portal, vaya a la sección Redes (versión preliminar), en Seguridad y redes. Aquí podrá ver todas las conexiones existentes, los estados de conexión y los detalles del punto de conexión privado.
También puede aprobar, rechazar o quitar las conexiones existentes. Al crear una conexión privada, puede especificar qué subrecurso de HDInsight (puerta de enlace, nodo principal, etc.) quiere conectar también.

En la siguiente tabla se muestran las distintas acciones del recurso de HDInsight y los estados de conexión resultantes para los puntos de conexión privados. El recurso de HDInsight también puede cambiar el estado de conexión de la conexión de punto de conexión privado en un momento posterior sin la intervención del consumidor. La acción actualizará el estado del punto de conexión en el lado del consumidor.

| Acción del proveedor de servicios | Estado de punto de conexión privado del consumidor del servicio | Descripción |
| --------- | --------- | --------- |
| None | Pending | La conexión se crea manualmente y está pendiente de aprobación por parte del propietario del recurso de Private Link. |
| Aprobación | Aprobado | La conexión se aprobó de forma automática o manual y está lista para usarse. |
| Reject | Rechazada | El propietario del recurso de vínculo privado rechazó la conexión. |
| Remove | Escenario desconectado | El propietario del recurso de Private Link quitó la conexión, el punto de conexión privado se vuelve informativo y debe eliminarse para la limpieza. |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>Configuración de DNS para la conexión mediante puntos de conexión privados

Una vez que configura las redes, puede crear un clúster con la conexión del proveedor de recursos saliente y Private Link habilitado, tal como se muestra en la ilustración siguiente.
Para acceder a clústeres privados, puede usar extensiones de DNS de Private Link y puntos de conexión privados. Cuando el valor de `privateLink` está habilitado, puede crear puntos de conexión privados y configurar la resolución de DNS mediante zonas DNS privadas.
Las entradas de Private Link creadas en la zona DNS pública `azurehdinsight.net` administrada por Azure son las siguientes:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
En la imagen siguiente se muestra un ejemplo de las entradas de DNS privadas configuradas para habilitar el acceso a un clúster desde una red virtual que no está emparejada o no tiene una línea de visión directa para el clúster. Puede usar la zona privada de Azure para reemplazar los nombres de dominio completos `*.privatelink.azurehdinsight.net` y resolver las direcciones IP de puntos de conexión privados en la red del cliente.
Esto solo se muestra para `<clustername>.azurehdinsight.net`, pero también se aplica a otros puntos de conexión del clúster.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagrama de la arquitectura de Private Link":::

## <a name="how-to-create-clusters"></a>¿Cómo se crean clústeres?
### <a name="use-arm-template-properties"></a>Uso de propiedades de plantilla de ARM

El fragmento de código JSON siguiente incluye las dos propiedades de red que tiene que configurar en una plantilla de Resource Manager para crear un clúster de HDInsight privado.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

Para una plantilla completa con muchas de las características de Enterprise Security de HDInsight, incluido Private Link, consulte el artículo sobre la [plantilla de Enterprise Security de HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Uso de Azure PowerShell

Para usar PowerShell, consulte el ejemplo [aquí](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

### <a name="use-azure-cli"></a>Uso de CLI de Azure
Para usar la CLI de Azure, consulte el ejemplo [aquí](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Pasos siguientes

* [Enterprise Security Package para Azure HDInsight](enterprise-security-package.md)
* [Directrices generales e información de seguridad de la empresa en Azure HDInsight](./domain-joined/general-guidelines.md)