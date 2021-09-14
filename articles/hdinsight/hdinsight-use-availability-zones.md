---
title: Creación de un clúster de Azure HDInsight que usa zonas de disponibilidad
description: Obtenga información sobre cómo crear un clúster de Azure HDInsight que usa zonas de disponibilidad.
ms.service: hdinsight
ms.topic: how-to
ms.custom: references_regions
ms.date: 09/01/2021
ms.openlocfilehash: 1b516db029c5769526f8345d5f6ecdb96c5fad6c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440290"
---
# <a name="create-an-hdinsight-cluster-that-uses-availability-zones-preview"></a>Creación de un clúster de HDInsight que usa zonas de disponibilidad (versión preliminar)

Un clúster de Azure HDInsight consta de varios nodos (nodos principales, nodos de trabajo, nodos de puerta de enlace y nodos de ZooKeeper). De manera predeterminada, en una región que admite zonas de disponibilidad, el usuario no tiene control sobre qué nodos del clúster se aprovisionan en qué zona de disponibilidad. 

Con esta nueva característica de zona de disponibilidad, el usuario ahora puede especificar qué zona de disponibilidad debe hospedar todos los nodos del clúster de HDInsight. Los nodos del clúster están físicamente separados de otras zonas de disponibilidad y están aislados de errores de otras zonas de disponibilidad de la misma región. Este modelo de implementación también proporciona conectividad de red económica y de baja latencia dentro del clúster. 

La replicación de este modelo de implementación en varias zonas de disponibilidad puede proporcionar un mayor nivel de disponibilidad para protegerse frente a errores de hardware.

En este artículo, se muestra cómo crear un clúster de HDInsight dentro de una zona de disponibilidad y cómo usar esta característica para lograr una mayor disponibilidad. 

## <a name="before-you-begin"></a>Antes de empezar
La característica de zona de disponibilidad solo se admite para los clústeres creados después del 15 de junio. La configuración de la zona de disponibilidad no se puede actualizar después de crear el clúster. Tampoco puede actualizar un clúster de zona sin disponibilidad para usar zonas de disponibilidad.

## <a name="prerequisites-and-region-availability"></a>Requisitos previos y disponibilidad por región
Requisitos previos:

 - Los clústeres se deben crear en una red virtual personalizada. 
 - Debe traer su propia base de datos SQL para la base de datos de Ambari y el metastore externo (como el metastore de Hive) para que pueda configurar estas bases de datos en la misma zona de disponibilidad. 

Los clústeres de HDInsight se pueden crear actualmente con zonas de disponibilidad en las siguientes regiones:

 - Este de Australia
 - Sur de Brasil
 - Centro de Canadá
 - Centro de EE. UU.
 - Este de EE. UU.
 - Este de EE. UU. 2
 - Centro de Francia
 - Centro-oeste de Alemania
 - Japón Oriental
 - Norte de Europa
 - Sudeste de Asia
 - Centro-sur de EE. UU.
 - Sur de Reino Unido 2
 - US Gov - Virginia
 - Oeste de Europa
 - Oeste de EE. UU. 2

## <a name="overview-of-availability-zones-for-hdinsight-clusters"></a>Introducción a las zonas de disponibilidad para los clústeres de HDInsight

Las zonas de disponibilidad son ubicaciones físicas únicas dentro de una región. Cada zona de disponibilidad consta de uno o varios centros de datos equipados con alimentación, refrigeración y redes independientes. En Azure, una región contiene una o más zonas de disponibilidad. Esta separación física de las zonas de disponibilidad dentro de una región protege las aplicaciones y los datos frente a los errores del centro de datos. Para más información, consulte [¿Qué son las zonas de disponibilidad en Azure?](../availability-zones/az-overview.md)

Los clústeres de Azure HDInsight se pueden configurar para implementarse dentro de una zona de disponibilidad. Todos los nodos de este clúster de HDInsight, incluidos los dos nodos principales, tres nodos de ZooKeeper, dos nodos de puerta de enlace y los nodos de trabajo, se colocarán en la zona de disponibilidad especificada.  Por ejemplo, hay tres zonas de disponibilidad en Este de EE. UU. Se puede crear un clúster de HDInsight en Este de EE. UU. con todos los nodos en la zona de disponibilidad 1. 

El uso de zonas de disponibilidad con el clúster de HDInsight de esta manera puede proporcionar ventajas tanto de rendimiento como de costos: 

 - Mejor rendimiento debido a la conectividad de red de baja latencia
 - Menor costo: la transferencia de datos dentro de la misma zona de disponibilidad es gratuita. La transferencia de datos entre zonas de disponibilidad incurrirá en costos de red adicionales. 

Si la aplicación requiere alta disponibilidad en varias zonas de disponibilidad, puede crear un clúster de HDInsight principal en una zona de disponibilidad y crear un clúster de HDInsight secundario en otra zona de disponibilidad con un tamaño mínimo para ahorrar costos. Con este diseño, si una de las otras zonas de disponibilidad queda fuera de servicio, este clúster de HDInsight no se verá afectado. Si esta zona de disponibilidad queda fuera de servicio, los clientes deben cambiar los clústeres secundarios de una zona de disponibilidad diferente a la principal, enrutar la carga de trabajo a este nuevo clúster principal y escalar verticalmente rápidamente el tamaño del clúster para permitir el procesamiento de datos.   

## <a name="create-an-hdinsight-cluster-using-availability-zone"></a>Creación de un clúster de HDInsight con zona de disponibilidad
Puede usar una plantilla de Azure Resource Manager (ARM) para iniciar un clúster de HDInsight en una zona de disponibilidad especificada. 

En la sección de recursos, debe agregar una sección de "zonas" y proporcionar en qué zona de disponibilidad desea implementar este clúster. 

```json
   "resources": [
        {
            "type": "Microsoft.HDInsight/clusters",
            "apiVersion": "2018-06-01-preview",
            "name": "[parameters('cluster name')]",
            "location": "East US 2",
            "zones": [
                "1"
            ],
```
 
## <a name="verify-nodes-within-one-availability-zone-across-zones"></a>Comprobación de los nodos dentro de una zona de disponibilidad entre zonas
Cuando el clúster de HDInsight esté listo, puede comprobar la ubicación para ver en qué zona de disponibilidad se implementan.

:::image type="content" source="./media/hdinsight-use-availability-zones/cluster-availability-zone-info.png" alt-text="Captura de pantalla que muestra la información de la zona de disponibilidad en la información general del clúster" border="true":::

**Respuesta de la API Get**: 

```json
 [
        {
            "location": "East US 2",
            "zones": [
                "1"
            ],
```

## <a name="scale-up-the-cluster"></a>Escalado vertical del clúster
Puede escalar verticalmente un clúster de HDInsight con más nodos de trabajo. Los nodos de trabajo recién agregados se colocarán en la misma zona de disponibilidad de este clúster. 

**Limitaciones**: 

 - La característica de zona de disponibilidad en HDInsight no admite clústeres que requieran discos administrados (clúster de Kafka y clúster de HBase habilitado para la característica de escritura acelerada). 

## <a name="best-practices"></a>Procedimientos recomendados

 - Realice una copia de seguridad periódica de las configuraciones en la base de datos de Ambari. 
 - Implemente lógica para enrutar fácilmente la carga de trabajo al clúster secundario.

## <a name="when-az-goes-down-what-to-expect"></a>¿Qué esperar cuando la zona de disponibilidad se queda fuera de servicio?
 - No se puede conectar mediante SSH a este clúster
 - No se puede eliminar, escalar verticalmente o reducir verticalmente este clúster.
 - No se pueden enviar trabajos ni ver el historial de trabajos.
 - Es posible enviar una nueva solicitud de creación de clúster en otra región.