---
title: Migración de los datos de Cassandra a Cassandra API de Azure Cosmos DB mediante Blitzz
description: Aprenda a migrar datos de la base de datos de Apache Cassandra a Cassandra API de Azure Cosmos DB mediante Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 9370d2fb6723325bf927389fce516b95506c93fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780027"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migración de los datos de Cassandra a una cuenta de Cassandra API de Azure Cosmos DB mediante Blitzz
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Cassandra API en Azure Cosmos DB se ha convertido en una excelente opción para cargas de trabajo empresariales que se ejecutan en Apache Cassandra por diversos motivos como, por ejemplo: 

* **No hay sobrecarga de administración y supervisión**: evita la sobrecarga que supone administrar y supervisar innumerables configuraciones en el sistema operativo, JVM, los archivos yaml y sus interacciones.

* **Importante ahorro de costos**: puede ahorrar costos con Azure Cosmos DB, como en el costo de las máquinas virtuales, del ancho de banda y de las licencias aplicables. Además, no tiene que administrar los centros de datos, los servidores, el almacenamiento en SSD, las redes y los costos de electricidad. 

* **Posibilidad de usar código y herramientas existentes**: Azure Cosmos DB proporciona compatibilidad de nivel de protocolo de conexión con SDK y herramientas existentes de Cassandra. Esta compatibilidad garantiza que pueda usar el código base existente con Cassandra API de Azure Cosmos DB con cambios triviales.

Existen varias maneras de migrar las cargas de trabajo de base de datos de una plataforma a otra. [Blitzz](https://www.blitzz.io) es una herramienta que ofrece una forma segura y confiable de realizar una migración sin tiempo de inactividad desde una variedad de bases de datos a Azure Cosmos DB. En este artículo se describen los pasos necesarios para migrar datos de la base de datos de Apache Cassandra a Cassandra API de Azure Cosmos DB mediante Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Ventajas del uso de Blitzz para la migración

La solución de migración de Blitzz sigue un enfoque paso a paso para migrar cargas de trabajo operativas complejas. Estos son algunos de los aspectos clave del plan de migración sin tiempo de inactividad de Blitzz:

* Ofrece la migración automática de la lógica de negocios (tablas, índices y vistas) desde la base de datos de Apache Cassandra a Azure Cosmos DB. No es necesario crear los esquemas manualmente.

* Blitzz ofrece una replicación de base de datos de gran volumen en paralelo. Permite que las plataformas de origen y de destino estén sincronizadas durante la migración mediante una técnica denominada captura de datos modificados (CDC). Mediante el uso de CDC, Blitzz extrae continuamente un flujo de cambios de la base de datos de origen (Apache Cassandra) y los aplica a la base de datos de destino (Azure Cosmos DB).

* Es tolerante a errores y proporciona una entrega exacta de los datos, incluso durante un error de hardware o software en el sistema.

* Protege los datos durante el tránsito mediante diversas metodologías de seguridad, como TLS o el cifrado.

## <a name="steps-to-migrate-data"></a>Pasos para migrar los datos

En esta sección se describen los pasos necesarios para configurar Blitzz y migrar datos de la base de datos de Apache Cassandra a Azure Cosmos DB.

1. Agregue un certificado de seguridad en el equipo donde va a instalar el replicante de Blitzz. El replicante de Blitzz necesita este certificado para establecer una conexión TLS con la cuenta de Azure Cosmos DB especificada. Puede agregar el certificado con los pasos siguientes:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. Puede obtener la instalación de Blitzz y los archivos binarios solicitando una demostración en el [sitio web de Blitzz](https://www.blitzz.io). También puede enviar un [correo electrónico](mailto:success@blitzz.io) al equipo.

   :::image type="content" source="./media/migrate-data-blitzz/blitzz-replicant-download.png" alt-text="Descarga de la herramienta replicante de Blitzz":::

   :::image type="content" source="./media/migrate-data-blitzz/replicant-files.png" alt-text="Archivos de replicante de Blitzz":::

1. En el terminal de la CLI, configure las opciones de la base de datos de origen. Abra el archivo de configuración con el comando **`vi conf/conn/cassandra.yml`** y agregue una lista separada por comas de las direcciones IP de los nodos de Cassandra, el número de puerto, el nombre de usuario, la contraseña y cualquier otro detalle necesario. A continuación se puede ver un ejemplo del contenido del archivo de configuración:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   :::image type="content" source="./media/migrate-data-blitzz/open-connection-editor-cassandra.png" alt-text="Apertura de editor de conexiones de Cassandra":::

   :::image type="content" source="./media/migrate-data-blitzz/cassandra-connection-configuration.png" alt-text="Configuración de la conexión de Cassandra":::

   Después de rellenar los detalles de configuración, guarde y cierre el archivo.

1. También puede configurar el archivo de filtro de base de datos de origen. El archivo de filtro especifica qué esquemas o tablas se van a migrar. Abra el archivo de configuración con el comando **`vi filter/cassandra_filter.yml`** y escriba los siguientes detalles de configuración:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   Después de rellenar los detalles del filtro de base de datos, guarde y cierre el archivo.

1. A continuación, realizará la configuración de la base de datos de destino. Antes de definir la configuración, [cree una cuenta de Cassandra API de Azure Cosmos DB](manage-data-dotnet.md#create-a-database-account) y, a continuación, cree un espacio de claves y una tabla para almacenar los datos migrados. Dado que va a migrar de Apache Cassandra a Cassandra API en Azure Cosmos DB, puede usar la misma clave de partición que usó con Apache Cassandra.

1. Antes de migrar los datos, aumente el rendimiento del contenedor hasta el valor necesario para que la aplicación se migre rápidamente. Por ejemplo, puede aumentar el rendimiento a 100 000 RU. El escalado del rendimiento antes de iniciar la migración le ayudará a reducir el tiempo de migración.

   :::image type="content" source="./media/migrate-data-blitzz/scale-throughput.png" alt-text="Escalado del rendimiento del contenedor de Azure Cosmos":::

   Reduzca el rendimiento una vez completada la migración. En función de la cantidad de datos almacenados y de las unidades de solicitud (RU) necesarias para cada operación, puede calcular el rendimiento necesario después de la migración de datos. Para más información sobre cómo calcular las unidades de solicitud necesarias, consulte los artículos [Aprovisionar rendimiento en contenedores y bases de datos](../set-throughput.md) y [Estimación de RU/s mediante Capacity Planner de Azure Cosmos DB](../estimate-ru-with-capacity-planner.md).

1. Obtenga en el panel **Cadena de conexión** los valores de **Punto de contacto, Puerto, Nombre de usuario** y **Contraseña principal** correspondientes a la cuenta de Azure Cosmos. Usará estos valores en el archivo de configuración.

1. En el terminal de la CLI, configure las opciones de la base de datos de destino. Abra el archivo de configuración con el comando **`vi conf/conn/cosmosdb.yml`** y agregue una lista separada por comas del identificador URI del host, el número de puerto, el nombre de usuario, la contraseña y otros parámetros necesarios. En el ejemplo siguiente se muestra el contenido del archivo de configuración:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. A continuación, migre los datos mediante Blitzz. Puede ejecutar el replicante de Blizz en modo **completo** o **de instantánea**:

   * **Modo completo**: en este modo, el replicante continúa ejecutándose después de la migración y escucha los cambios en el sistema Apache Cassandra de origen. Si detecta cambios, se replican en la cuenta de Azure Cosmos de destino en tiempo real.

   * **Modo de instantánea**: en este modo, puede realizar la migración de esquemas y una única replicación de datos. Esta opción no admite la replicación en tiempo real.

   Mediante el uso de los dos modos anteriores, la migración se puede realizar sin tiempo de inactividad. 

1. Para migrar los datos, desde el terminal de la CLI del replicante de Blitzz, ejecute el siguiente comando:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   La interfaz de usuario del replicante muestra el progreso de la replicación. Una vez realizada la migración del esquema y la operación de instantánea, se muestra un progreso del 100 %. Una vez completada la migración, puede validar los datos en la base de datos de Azure Cosmos de destino.

   :::image type="content" source="./media/migrate-data-blitzz/cassandra-data-migration-output.png" alt-text="Salida de la migración de datos de Cassandra":::


1. Dado que ha usado el modo completo para la migración, puede realizar operaciones como insertar, actualizar o eliminar datos en la base de datos de Apache Cassandra de origen. Después, asegúrese de que se replican en tiempo real en la base de datos de Azure Cosmos de destino. Tras la migración, asegúrese de reducir el rendimiento configurado para el contenedor de Azure Cosmos.

1. Puede detener el replicante en cualquier momento y reiniciarlo con el modificador **--resume**. La replicación se reanuda en el punto en el que se detuvo sin poner en peligro la coherencia de los datos. El siguiente comando muestra cómo usar el modificador de reanudación.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

Para más información sobre la migración de datos a un destino en tiempo real, consulte la [demostración del replicante de Blitzz](https://www.youtube.com/watch?v=fsUhF9LUZmM).

## <a name="next-steps"></a>Pasos siguientes

* [Aprovisionamiento del rendimiento en contenedores y bases de datos](../set-throughput.md) 
* [Procedimientos recomendados de las claves de partición](../partitioning-overview.md#choose-partitionkey)
* [Estimación de RU/s mediante Capacity Planner de Azure Cosmos DB](../estimate-ru-with-capacity-planner.md)
