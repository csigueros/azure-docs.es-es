---
title: 'Habilitación de la creación automática de temas en Apache Kafka: Azure HDInsight'
description: Obtenga información sobre cómo configurar Apache Kafka en HDInsight para crear automáticamente los temas. Puede configurar Kafka estableciendo `auto.create.topics.enable` en true mediante Ambari. O durante la creación del clúster mediante las plantillas de Resource Manager o PowerShell.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 946655910f17bb3a12237490f0570cbcff2e76b0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620509"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Configuración de Apache Kafka en HDInsight para crear automáticamente los temas

De manera predeterminada, Apache Kafka en HDInsight no habilita la creación automática de temas. Puede habilitar la creación automática de temas de clústeres existentes con Apache Ambari. También puede habilitar la creación automática de temas al crear un clúster de Kafka mediante una plantilla de Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Interfaz de usuario web de Apache Ambari

Para habilitar la creación automática de temas en un clúster existente mediante la interfaz de usuario web de Ambari, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), seleccione el clúster de Kafka.

1. Desde **Paneles de clúster**, seleccione **Inicio de Ambari**.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png" alt-text="Imagen Azure Portal con el panel de clúster seleccionado" border="true":::

    Cuando se le solicite, realice la autenticación mediante las credenciales de inicio de sesión (administrador) del clúster. En su lugar, puede conectarse a Ambari directamente desde `https://CLUSTERNAME.azurehdinsight.net/`, donde `CLUSTERNAME` es el nombre del clúster de Kafka.

1. Seleccione el servicio Kafka en la lista de la parte izquierda de la página.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-list.png" alt-text="Servicio de Apache Ambari: pestaña de lista" border="true":::

1. Seleccione Configuraciones en el medio de la página.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-config.png" alt-text="Servicio de Apache Ambari: pestaña de configuración" border="true":::

1. En el campo Filtro, escriba un valor de `auto.create`.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png" alt-text="Apache Ambari: campo de filtro de búsqueda" border="true":::

    Esta configuración filtra la lista de propiedades y muestra el valor `auto.create.topics.enable`.

1. Cambie el valor de `auto.create.topics.enable` a `true` y, luego, seleccione **Guardar**. Agregue una nota y, a continuación, seleccione de nuevo **Guardar**.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png" alt-text="Imagen de la entrada de auto.create.topics.enable" border="true":::

1. Seleccione el servicio Kafka, seleccione __Reiniciar__ y luego seleccione __Restart all affected__ (Reiniciar todos los afectados). Cuando se le solicite, seleccione __Confirm Restart All__ (Confirmar reiniciar todo).

    :::image type="content" source="./media/apache-kafka-auto-create-topics/restart-all-affected.png" alt-text="&quot;Reinicio de todas las entradas afectadas en Apache Ambari&quot;" border="true":::

> [!NOTE]  
> También puede establecer valores de Ambari mediante la API de REST de Ambari. Esto suele ser más difícil, ya que tiene que hacer varias llamadas de REST para recuperar la configuración actual, modificarla, etc. Para obtener más información, consulte el documento [Manage HDInsight clusters using the Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md) (Administrar clústeres de HDInsight con la API REST de Apache Ambari).

## <a name="resource-manager-templates"></a>Plantillas de Resource Manager

Al crear un clúster de Kafka mediante una plantilla de Azure Resource Manager, puede establecer directamente `auto.create.topics.enable` al agregarla en `kafka-broker`. El siguiente fragmento de código JSON muestra cómo establecer este valor en `true`:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
        "gateway": {
            "restAuthCredential.isEnabled": true,
            "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
            "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
        },
        "kafka-broker": {
            "auto.create.topics.enable": "true"
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a habilitar la creación automática de temas para Apache Kafka en HDInsight. Para más información sobre cómo trabajar con Kafka, vea los siguientes vínculos:

* [Análisis de registros de Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Réplica de datos entre clústeres de Apache Kafka](apache-kafka-mirroring.md)
