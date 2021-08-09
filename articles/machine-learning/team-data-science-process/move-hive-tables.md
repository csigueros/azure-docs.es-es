---
title: 'Creación de tablas de Hive y carga de datos desde Blob Storage: Proceso de ciencia de datos en equipo'
description: Use consultas de Hive para crear tablas de Hive y cargar datos desde Azure Blob Storage. Cree particiones de las tablas de Hive y use el formato Columnas de filas optimizadas (ORC) para mejorar el rendimiento de las consultas.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 69781bcd950b5dd18f4beb99414ded7afdcd00e8
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111422199"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Creación de tablas de Hive y carga de datos desde Azure Blob Storage

En este artículo se presentan consultas genéricas de Hive que crean tablas de Hive y cargan datos desde Azure Blob Storage. También se ofrecen algunas instrucciones acerca de las particiones de tablas de subárbol y de cómo utilizar el formato ORC para mejorar el rendimiento de las consultas.

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ha:

* Creado una cuenta de Azure Storage. Si necesita instrucciones, consulte [Acerca de las cuentas de Azure Storage](../../storage/common/storage-introduction.md).
* Aprovisionado un clúster de Hadoop personalizado con el servicio HDInsight.  Si necesita instrucciones, consulte [Configuración de clústeres en HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Habilitado el acceso remoto para el clúster, iniciado sesión y abierto la consola de la línea de comandos de Hadoop. Si necesita instrucciones, consulte [Administración de clústeres de Apache Hadoop](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carga de datos en Azure Blob Storage
Si creó una máquina virtual de Azure siguiendo las instrucciones dadas en [Configuración de una máquina virtual de Azure para análisis avanzado](../../machine-learning/data-science-virtual-machine/overview.md), este archivo de script debió descargarse en el directorio *C:\\Users\\\<user name\>\\Documents\\Data Science Scripts* de la máquina virtual. Para que estas consultas de Hive estén listas para su envío, solo tiene que proporcionar un esquema de datos y la configuración de Azure Blob Storage en los campos adecuados.

Se supone que los datos de las tablas de Hive están en un formato tabular **sin comprimir** y que se han cargado los datos en el contenedor predeterminado (o en otro adicional) de la cuenta de almacenamiento que usa el clúster Hadoop.

Si desea practicar con el grupo **NYC Taxi Trip Data**, necesita hacer lo siguiente:

* **Descargue** los 24 archivos de [NYC Taxi Trip Data](https://www.andresmh.com/nyctaxitrips) (12 archivos de carreras y 12 archivos de tarifas).
* **Descomprima** todos los archivos en archivos .csv.
* **Cargue** los archivos en el contenedor predeterminado (o adecuado) de la cuenta de Azure Storage; las opciones de este tipo de cuenta aparecen en el tema [Uso de Azure Storage con clústeres de Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). En esta [página](hive-walkthrough.md#upload)encontrará el proceso de cargar los archivos .csv en el contenedor predeterminado de la cuenta de almacenamiento.

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Envío de consultas de Hive
Las consultas de subárbol se pueden enviar mediante:

* [Enviar consultas de Hive a través de línea de comandos de Hadoop en el nodo principal del clúster de Hadoop](#headnode)
* [Enviar consultas de Hive con el Editor de Hive](#hive-editor)
* [Enviar consultas de Hive con los comandos de Azure PowerShell](#ps)

Las consultas de Hive son similares a SQL. Los usuarios familiarizados con SQL pueden encontrar la [hoja de referencia rápida Hive para usuarios de SQL](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) de gran utilidad.

Al enviar una consulta de subárbol, también puede controlar el destino del resultado de las consultas de subárbol, ya sea en la pantalla o en un archivo local del nodo principal o en un blob de Azure.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Envío de consultas de Hive mediante la línea de comandos de Hadoop en el nodo principal del clúster de Hadoop
Si la consulta es compleja, enviar consultas de Hive directamente en el nodo principal del clúster de Hadoop normalmente permite obtener respuestas más rápidas que si se efectúa el envío mediante un editor de Hive o scripts de Azure PowerShell.

Inicie sesión en el nodo principal del clúster de Hadoop, abra la línea de comandos de Hadoop en el escritorio del nodo principal y escriba el comando `cd %hive_home%\bin`.

Los usuarios disponen de tres maneras de enviar consultas de Hive en la línea de comandos de Hadoop:

* Directamente
* Con archivos ".hql"
* Con la consola de comandos de Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Envíe consultas de subárbol directamente en la línea de comandos de Hadoop.
Los usuarios pueden ejecutar el comando como `hive -e "<your hive query>;` para enviar consultas de Hive sencillas directamente en la línea de comandos de Hadoop. Este es un ejemplo, donde el cuadro rojo muestra el comando que envía la consulta de subárbol y el cuadro verde muestra el resultado de la consulta de subárbol.

![Comando para enviar una consulta de Hive con la salida de la consulta de Hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Envío de consultas de Hive en archivos ".hql"
Cuando la consulta de subárbol es más complicada y tiene varias líneas, no resulta práctico modificar consultas en la línea de comandos o la consola de comandos de subárbol. Una alternativa es usar un editor de texto en el nodo principal del clúster de Hadoop para guardar las consultas de Hive en un archivo ".hql" de un directorio local del nodo principal. Luego la consulta de Hive del archivo ".hql" puede enviarse mediante el argumento `-f`, como se indica a continuación:

```console
hive -f "<path to the '.hql' file>"
```

![Consulta de Hive en un archivo ".hql"](./media/move-hive-tables/run-hive-queries-3.png)

**Suprimir la impresión de pantalla del estado de progreso de las consultas de subárbol**

De forma predeterminada, después de enviar la consulta de Hive en la línea de comandos de Hadoop, el progreso del trabajo de asignación/reducción se imprimirá en pantalla. Para suprimir la impresión de la pantalla del progreso del trabajo de asignación/reducción, puede usar un argumento `-S` ("S" en mayúsculas) en la línea de comandos del modo indicado a continuación:

```console
hive -S -f "<path to the '.hql' file>"
hive -S -e "<Hive queries>"
```

#### <a name="submit-hive-queries-in-hive-command-console"></a>Envíe consultas de subárbol en la consola de comandos de subárbol.
Los usuarios también pueden especificar en primer lugar la consola de comandos de Hive al ejecutar el comando `hive` en la línea de comandos de Hadoop y, a continuación, enviar consultas de Hive en la consola de comandos de Hive. A continuación se muestra un ejemplo: En este ejemplo, los dos cuadros de color rojo resaltan los comandos que se utilizan para escribir en la consola de comandos de subárbol y la consulta de subárbol enviada en la consola de comandos de subárbol, respectivamente. El cuadro verde resalta el resultado de la consulta de subárbol.

![Abra la consola de comandos de Hive y escriba el comando view Hive query output](./media/move-hive-tables/run-hive-queries-2.png)

Los ejemplos anteriores generan directamente los resultados de la consulta de subárbol en pantalla. Los usuarios también pueden escribir la salida en un archivo local del nodo principal o en un blob de Azure. A continuación, los usuarios pueden utilizar otras herramientas para analizar más el resultado de las consultas de Hive.

**Genere los resultados de consulta de subárbol en un archivo local.**
Para generar los resultados de consultas de Hive en un directorio local del nodo principal, los usuarios tienen que enviar la consulta de Hive de la línea de comandos de Hadoop de la siguiente manera:

```console
hive -e "<hive query>" > <local path in the head node>
```

En el ejemplo siguiente, el resultado de la consulta de Hive se escribe en un archivo `hivequeryoutput.txt` del directorio `C:\apps\temp`.

![Captura de pantalla que muestra la salida de la consulta de Hive en una ventana de línea de comandos de Hadoop.](./media/move-hive-tables/output-hive-results-1.png)

**Generar los resultados de consulta de subárbol en un blob de Azure**

Los usuarios también pueden generar resultados de consulta de Hive en un blob de Azure, dentro del contenedor predeterminado del clúster de Hadoop. La consulta de Hive para esto es la siguiente:

```console
insert overwrite directory wasb:///<directory within the default container> <select clause from ...>
```

En el ejemplo siguiente, el resultado de la consulta de Hive se escribe en un directorio de blob `queryoutputdir` dentro del contenedor predeterminado del clúster de Hadoop. En este caso, solo necesita proporcionar el nombre del directorio, sin el nombre del blob. Se produce un error si proporciona los nombres de directorio y de blob, como `wasb:///queryoutputdir/queryoutput.txt`.

![Captura de pantalla que muestra el comando anterior en la ventana de la línea de comandos de Hadoop.](./media/move-hive-tables/output-hive-results-2.png)

Si abre el contenedor predeterminado del clúster de Hadoop mediante herramientas como el Explorador de Azure Storage, verá el resultado de la consulta de Hive como se muestra en la ilustración siguiente. Puede aplicar el filtro (resaltado mediante un cuadro rojo) para recuperar solo el blob con letras especificadas en los nombres.

![Explorador de Azure Storage mostrando la salida de la consulta de Hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Envío de consultas de Hive con el editor de Hive
Otra manera de usar la consola de consultas (Editor de Hive) es escribir una dirección URL con el formato *https:\//\<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* en un explorador web. Debe haber iniciado sesión para ver esta consola; así pues, tiene que escribir sus credenciales de Hadoop aquí.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Envío de consultas de Hive con comandos de Azure PowerShell
Los usuarios pueden también usar PowerShell para enviar consultas de Hive. Para obtener instrucciones, consulte [Envío de trabajos de Hive mediante PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Creación de tablas y base de datos de Hive
Las consultas de Hive se comparten en el [repositorio de GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) y se pueden descargar desde allí.

Esta es la consulta de subárbol que crea una tabla de subárbol.

```hiveql
create database if not exists <database name>;
CREATE EXTERNAL TABLE if not exists <database name>.<table name>
(
    field1 string,
    field2 int,
    field3 float,
    field4 double,
    ...,
    fieldN string
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");
```

Estas son las descripciones de los campos que los usuarios necesitan para conectar y otras configuraciones:

* **\<database name\>** : nombre de la base de datos que los usuarios desean crear. Si solo quiere usar la base de datos predeterminada, se puede omitir la consulta "*create database...* ".
* **\<table name\>** : nombre de la tabla que los usuarios quieren crear en la base de datos especificada. Si los usuarios desean usar la base de datos predeterminada, puede hacer referencia directamente a la tabla *\<table name\>* sin \<database name\>.
* **\<field separator\>** : el separador que separa los campos del archivo de datos que se cargará en la tabla de Hive.
* **\<line separator\>** : separador que delimita las líneas del archivo de datos.
* **\<storage location\>** : la ubicación de Azure Storage para guardar los datos de tablas de Hive. Si los usuarios no especifican *LOCATION \<storage location\>* , la base de datos y las tablas se almacenan de forma predeterminada en el directorio *hive/warehouse/* del contenedor predeterminado del clúster Hive. Si un usuario desea especificar la ubicación de almacenamiento, esta debe estar dentro del contenedor predeterminado para la base de datos y las tablas. Se debe hacer referencia a esta ubicación como ubicación relativa al contenedor predeterminado del clúster con el formato *"wasb:///\<directory 1>/'* o *"wasb:///\<directory 1>/\<directory 2>/"* , etc. Después de ejecutar la consulta, se crean los directorios relativos dentro del contenedor predeterminado.
* **TBLPROPERTIES("skip.header.line.count"="1")** : si el archivo de datos tiene una línea de encabezado, los usuarios deben agregar esta propiedad **al final** de la consulta *create table*. De lo contrario, se carga la línea de encabezado como registro en la tabla. Si el archivo de datos no tiene una línea de encabezado, se puede omitir esta configuración en la consulta.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Carga de datos en tablas de Hive
Esta es la consulta de subárbol que carga datos en una tabla de subárbol.

```hiveql
LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;
```

* **\<path to blob data\>** : si el archivo de blob que se va a cargar en la tabla de Hive se encuentra en el contenedor predeterminado del clúster Hadoop de HDInsight, *\<path to blob data\>* debe tener el formato *"wasb:///\<directory in this container>/\<blob file name>"* . El archivo blob también puede estar en un contenedor adicional del clúster de Hadoop de HDInsight. En este caso, *\<path to blob data\>* debe tener el formato *"wasb://\<container name>@\<storage account name>.blob.core.windows.net/\<blob file name>"* .

  > [!NOTE]
  > Los datos blob que se van a cargar en la tabla de subárbol tienen que estar en el contenedor adicional o predeterminado de la cuenta de almacenamiento para el clúster de Hadoop. De lo contrario, la consulta *LOAD DATA* genera un error indicando que no puede obtener acceso a los datos.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Temas avanzados: tabla con particiones y datos de Hive de almacén en formato ORC
Si los datos son grandes, crear particiones de la tabla será beneficioso para las consultas que solo necesitan examinar algunas particiones de la tabla. Por ejemplo, es razonable crear particiones de los datos de registro de un sitio web por fechas.

Además de crear particiones de tablas de subárbol, también es beneficioso almacenar los datos de subárbol en el formato ORC. Para obtener más información acerca de la aplicación de formato ORC, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">El uso de archivos ORC mejora el rendimiento cuando Hive está leyendo, escribiendo y procesando datos</a>.

### <a name="partitioned-table"></a>Tabla con particiones
Esta es la consulta de subárbol que crea una tabla con particiones y carga datos en ella.

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
(field1 string,
...
fieldN string
)
PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
    lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
    PARTITION (<partitionfieldname>=<partitionfieldvalue>);
```

Al consultar tablas con particiones, se recomienda agregar la condición de partición al **comienzo** de la cláusula `where`, lo que mejora la eficacia de la búsqueda.

```hiveql
select
    field1, field2, ..., fieldN
from <database name>.<partitioned table name>
where <partitionfieldname>=<partitionfieldvalue> and ...;
```

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Almacenamiento de datos de Hive en formato ORC
Los usuarios no pueden cargar datos directamente desde Blob Storage en tablas de Hive que se almacenan en el formato ORC. Estos son los pasos que los usuarios deben seguir para cargar datos desde blobs de Azure a tablas de Hive almacenadas en formato ORC.

Cree una tabla externa **STORED AS TEXTFILE** y cargue datos del almacenamiento de blobs en la tabla.

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
(
    field1 string,
    field2 int,
    ...
    fieldN date
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
    lines terminated by '<line separator>' STORED AS TEXTFILE
    LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;
```

Cree una tabla interna con el mismo esquema que la tabla externa del paso 1, con el mismo delimitador de campo, y almacene los datos de subárbol en el formato ORC.

```hiveql
CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
(
    field1 string,
    field2 int,
    ...
    fieldN date
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;
```

Seleccionar datos desde la tabla externa del paso 1 e insertarlos en la tabla de ORC

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name>
    SELECT * FROM <database name>.<external textfile table name>;
```

> [!NOTE]
> Si la tabla TEXTFILE *\<database name\>.\<external textfile table name\>* tiene particiones, en el PASO 3, el comando `SELECT * FROM <database name>.<external textfile table name>` selecciona la variable de partición como un campo en el conjunto de datos devuelto. Su inserción en *\<database name\>.\<ORC table name\>* produce un error dado que *\<database name\>.\<ORC table name\>* no tiene la variable de partición como un campo en el esquema de tabla. En este caso, debe seleccionar de manera específica los campos que se van a insertar en *\<database name\>.\<ORC table name\>* de la siguiente manera:
>
>

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
    SELECT field1, field2, ..., fieldN
    FROM <database name>.<external textfile table name>
    WHERE <partition variable>=<partition value>;
```

Es seguro quitar *\<external text file table name\>* cuando se utiliza la consulta siguiente después de que todos los datos se hayan insertado en *\<database name\>.\<ORC table name\>* :

```hiveql
    DROP TABLE IF EXISTS <database name>.<external textfile table name>;
```

Después de seguir este procedimiento, debe tener una tabla con datos en el formato ORC lista para su uso.  
