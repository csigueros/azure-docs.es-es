---
title: Base de datos de Apache Ambari personalizada en Azure HDInsight
description: Aprenda a crear clústeres de HDInsight con su propia base de datos de Apache Ambari personalizada.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: d2ab2609338daff846797834be7694a8b1f220e6
ms.sourcegitcommit: 9caa850a2b26773e238f8ba6f4ca151c47260915
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2021
ms.locfileid: "113600946"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Configuración de clústeres de HDInsight con una base de datos de Ambari personalizada

Apache Ambari simplifica la administración y la supervisión de un clúster de Apache Hadoop, ya que proporciona una interfaz de usuario web y una API REST fáciles de usar. Ambari se incluye en los clústeres de HDInsight y, además, se usa para supervisar el clúster y realizar cambios en la configuración.

En la creación de clústeres normal, como se describe en otros artículos como [Configuración de clústeres en HDInsight](hdinsight-hadoop-provision-linux-clusters.md), Ambari se implementa en una [instancia S0 de Azure SQL Database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) que administra HDInsight y no es accesible para los usuarios.

La característica de bases de datos personalizadas de Ambari permite implementar un nuevo clúster y configurar Ambari en una base de datos externa que usted administra. La implementación se realiza mediante una plantilla de Azure Resource Manager. Esta característica tiene las siguientes ventajas:

- Personalización: puede elegir el tamaño y la capacidad de procesamiento de la base de datos. Si tiene clústeres grandes que procesan cargas de trabajo de uso intensivo, una base de datos de Ambari con especificaciones menores podría convertirse en un cuello de botella para las operaciones de administración.
- Flexibilidad: puede escalar la base de datos según sea necesario para satisfacer sus necesidades.
- Control: puede administrar las copias de seguridad y la seguridad de su base de datos para que se ajusten a los requisitos de su organización.

En el resto de este artículo se tratan los puntos siguientes:

- requisitos para usar la característica de base de datos personalizada de Ambari;
- los pasos necesarios para aprovisionar clústeres de HDInsight con su propia base de datos externa para Apache Ambari.

## <a name="custom-ambari-db-requirements"></a>Requisitos de la base de datos personalizada de Ambari

Puede implementar una base de datos personalizada de Ambari con todos los tipos de clúster y versiones. Varios clústeres no pueden usar la misma base de datos de Ambari.

La base de datos personalizada de Ambari tiene los siguientes requisitos:

- El nombre de la base de datos no puede contener guiones ni espacios
- Debe tener una base de datos y un servidor de Azure SQL Database existentes.
- La base de datos que se proporciona para la configuración de Ambari debe estar vacía. No debe haber ninguna tabla en el esquema DBO predeterminado.
- El usuario utilizado para conectarse a la base de datos debe tener permisos SELECT, CREATE TABLE e INSERT en la base de datos.
- Active la opción [Permitir el acceso a servicios de Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) en el servidor donde hospedará Ambari.
- Las direcciones IP de administración del servicio HDInsight deben permitirse en la regla de firewall. Consulte [Direcciones IP de administración de HDInsight](hdinsight-management-ip-addresses.md) para obtener una lista de las direcciones IP que se deben agregar a la regla de firewall en el nivel de servidor.

Al hospedar la base de datos de Apache Ambari en una base de datos externa, recuerde los puntos siguientes:

- Los costos adicionales de la base de datos de Azure SQL que contiene Ambari son su responsabilidad.
- Realice una copia de la base de datos personalizada de Ambari periódicamente. Azure SQL Database genera las copias de seguridad automáticamente, pero el período de tiempo de retención de dichas copias de seguridad varía. Para más información, consulte [más información sobre copias de seguridad automáticas de SQL Database](../azure-sql/database/automated-backups-overview.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Implementación de clústeres con una base de datos de Ambari personalizada

Para crear un clúster de HDInsight que use su propia base de datos externa de Ambari, use la [plantilla de inicio rápido de la base de datos personalizada de Ambari](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.hdinsight/hdinsight-custom-ambari-db).

Edite los parámetros en el archivo `azuredeploy.parameters.json` para especificar información sobre el nuevo clúster y la base de datos que contendrá Ambari.

Puede comenzar la implementación mediante la CLI de Azure. Reemplace `<RESOURCEGROUPNAME>` por el grupo de recursos en el que quiere implementar el clúster.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```


> [!WARNING]
> Use la siguiente máquina virtual recomendada de base de datos SQL y nodo principal para el clúster de HDInsight. No use la base de datos de Ambari (S0) predeterminada en ningún entorno de producción. 
>


## <a name="database-and-headnode-sizing"></a>Tamaño de base de datos y nodo principal

En la tabla siguiente se proporcionan instrucciones sobre el nivel de Azure SQL Database que se debe seleccionar según el tamaño del clúster de HDInsight.

| Número de nodos de trabajo | Nivel de base de datos requerido | Máquina virtual de nodo principal necesaria |
|---|---|---|
| menor o igual que 4 | S0 | 4 núcleos/28 GB de RAM o superior |
| mayor que 4 y menor o igual que 8 | S1 | 4 núcleos/28 GB de RAM o superior |
| mayor que 8 y menor o igual que 16 | S2 | 4 núcleos/28 GB de RAM o superior |
| mayor que 16 y menor o igual que 32 | S3 | 8 núcleos/56 GB de RAM o superior |
| mayor que 32 y menor o igual que 64 | S4 | 8 núcleos/56 GB de RAM o superior |
| mayor que 64 y menor o igual que 128 | P2 | 16 núcleos/112 GB de RAM o superior |
| mayor que 128 | Ponerse en contacto con el servicio de soporte técnico | Ponerse en contacto con el servicio de soporte técnico |

## <a name="next-steps"></a>Pasos siguientes

- [Uso de repositorios de metadatos externos en Azure HDInsight](hdinsight-use-external-metadata-stores.md)
