---
title: 'Tutorial: Migración de los datos a una cuenta de Cassandra API en Azure Cosmos DB'
description: En este tutorial aprenderá a copiar datos de Apache Cassandra a la cuenta de Cassandra API en Azure Cosmos DB
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 1f38c0f37daebae0a0320882c7232444819dae39
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587830"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Tutorial: Migración de los datos a una cuenta de Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Como desarrollador, es posible que tenga cargas de trabajo de Cassandra existentes que se ejecutan de forma local o en la nube y puede que desee migrarlas a Azure. Puede migrar estas cargas a una cuenta de Cassandra API en Azure Cosmos DB. Este tutorial proporciona instrucciones sobre las diferentes opciones disponibles para migrar datos de Apache Cassandra a la cuenta de Cassandra API en Azure Cosmos DB.

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Planear la migración
> * Requisitos previos para la migración
> * Migración de los datos mediante el comando `cqlsh` `COPY`
> * Migración de datos mediante Spark

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites-for-migration"></a>Requisitos previos para la migración

* **Cálculo de las necesidades de rendimiento:** Antes de migrar datos a la cuenta de Cassandra API en Azure Cosmos DB, debe calcular las necesidades de rendimiento de la carga de trabajo. En general, puede comenzar con el rendimiento medio requerido por las operaciones CRUD y después incluir el rendimiento adicional necesario para las operaciones de Extracción, transformación y carga, y durante los picos. Necesita los siguientes detalles para planear la migración: 

  * **Tamaño de datos existente o tamaño de datos estimado:** Define el requisito de rendimiento y de tamaño mínimo de la base de datos. Si realiza la estimación de tamaño de los datos para una nueva aplicación, puede asumir que los datos se distribuyen uniformemente entre las filas y calcular el valor multiplicando con el tamaño de los datos. 

  * **Rendimiento necesario:** tasa de rendimiento de las operaciones de lectura (consultar/obtener) y escritura (actualizar/eliminar/insertar) aproximada. Este valor es necesario para calcular las unidades de solicitud necesarios junto con el tamaño de datos de estado estable.  

  * **Obtener el esquema:** conéctese a su clúster de Cassandra existente mediante `cqlsh` y exporte el esquema desde Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Después de identificar los requisitos de carga de trabajo existentes, cree una cuenta de Azure Cosmos DB, base de datos y contenedores según los requisitos de rendimiento recopilados.  

  * **Determinación del cargo de RU para una operación:** Puede determinar las RU mediante el uso de cualquiera de los SDK compatible con Cassandra API. En este ejemplo se muestra la versión de .NET de la obtención de cargos de la unidad de solicitud.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Asignación del rendimiento necesario:** Azure Cosmos DB puede escalar automáticamente el almacenamiento y rendimiento a medida que crecen sus necesidades. Puede estimar sus necesidades de rendimiento mediante el uso de la [Calculadora de unidades de solicitud de Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

* **Crear tablas en la cuenta de Cassandra API:** antes de comenzar la migración de datos, cree previamente todas las tablas de Azure Portal o `cqlsh`. Si va a migrar a una cuenta de Azure Cosmos DB con rendimiento de nivel de base de datos, asegúrese de proporcionar una clave de partición al crear los contenedores.

* **Aumento del rendimiento:** La duración de la migración de datos depende de la cantidad de rendimiento aprovisionado para las tablas de Azure Cosmos DB. Aumente el rendimiento de la duración de la migración. Gracias al mayor rendimiento, puede evitar la limitación de velocidad y realizar la migración en menos tiempo. Después de haber completado la migración, reduzca el rendimiento para ahorrar costos. También se recomienda que tenga la cuenta de Azure Cosmos DB en la misma región que la base de datos de origen. 

* **Habilitación de TLS:** Azure Cosmos DB tiene estándares y requisitos de seguridad estrictos. No olvide habilitar TLS al interactuar con la cuenta. Al usar CQL con SSH, tiene una opción para proporcionar información de TLS.

## <a name="options-to-migrate-data"></a>Opciones para migrar datos

Puede mover los datos de cargas de trabajo existentes de Cassandra a Azure Cosmos DB mediante el comando `cqlsh` `COPY` o con Spark. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Migración de datos mediante el comando COPY de cqlsh

[El comando COPY de CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) se usa para copiar datos locales en la cuenta de Cassandra API en Azure Cosmos DB.

1. Obtenga la información de cadena de conexión de su cuenta de Cassandra API:

   * Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a la cuenta de Azure Cosmos DB.

   * Abra el panel **Cadena de conexión**. Aquí tiene toda la información que necesita para conectarse a su cuenta de Cassandra API desde `cqlsh`.

1. Inicie sesión en `cqlsh` mediante la información de conexión del portal.

1. Use el comando `CQL` `COPY` para copiar datos locales a la cuenta de Cassandra API.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Migración de datos mediante Spark 

Siga estos pasos para migrar datos a la cuenta de Cassandra API en Azure Cosmos DB con Spark:

1. Aprovisione un [clúster de Azure Databricks](cassandra-spark-databricks.md) o un [clúster de Azure HDInsight](cassandra-spark-hdinsight.md). 

1. Mueva los datos al punto de conexión de destino de Cassandra API. Consulte esta [guía paso a paso](cassandra-migrate-cosmos-db-databricks.md) para obtener información sobre la migración con Azure Databricks.

La migración de datos mediante el uso de trabajos de Spark es una opción recomendada si tiene datos que residen en un clúster existente en máquinas virtuales de Azure o cualquier otra nube. Para ello, debe configurar Spark como intermediario para la ingesta de una vez o la ingesta habitual. Puede acelerar la migración mediante el uso de la conectividad de Azure ExpressRoute entre el entorno local y Azure. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede eliminar el grupo de recursos, la cuenta de Azure Cosmos DB y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la máquina virtual, seleccione **Eliminar** y luego confirme el nombre del grupo de recursos que va a eliminar.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo migrar los datos a una cuenta de Cassandra API en Azure Cosmos DB. Ahora puede conocer otros conceptos de Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Niveles de coherencia de datos optimizables en Azure Cosmos DB](../cosmos-db/consistency-levels.md)




