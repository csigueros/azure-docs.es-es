---
title: 'Excel y Apache Hadoop con el controlador de conectividad abierta de bases de datos (ODBC): Azure HDInsight'
description: Aprenda a configurar y usar el controlador ODBC de Microsoft Hive para que Excel consulte datos en un clúster de HDInsight desde Microsoft Excel
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 10bc1d2841e7858697a60dfbd1d093f403617a59
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300082"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Conexión de Excel a Apache Hadoop en Azure HDInsight con el controlador ODBC de Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../includes/hdinsight-selector-odbc-jdbc.md)]

La solución de macrodatos de Microsoft integra componentes de inteligencia empresarial (BI) de Microsoft con clústeres de Apache Hadoop implementados en HDInsight. Un ejemplo es la posibilidad de conectar Excel al almacenamiento de datos de Hive de un clúster de Hadoop. Conéctese con el controlador de conectividad abierta de bases de datos (ODBC) de Microsoft Hive.

Puede conectar los datos asociados a un clúster de HDInsight desde Excel con el complemento Microsoft Power Query para Excel. Para más información, consulte [Conexión de Excel a HDInsight con Power Query](./apache-hadoop-connect-excel-power-query.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar este artículo, debe tener los siguientes elementos:

* Un clúster de Hadoop de HDInsight: Para crear uno, vea [Introducción a HDInsight de Azure](apache-hadoop-linux-tutorial-get-started.md).
* Una estación de trabajo con Office Professional Plus 2010 o posterior, o Excel 2010 o posterior.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalación de Microsoft Hive ODBC Driver

Descargue e instale [Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886). Elija la versión que coincida con la versión de la aplicación en la que va a usar el controlador ODBC.  En este artículo, el controlador se usa para Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Creación de un origen de datos de Apache Hive ODBC

En los siguientes pasos se explica cómo crear un origen de datos de Hive ODBC.

1. En Windows, vaya a **Inicio > Herramientas administrativas de Windows > Orígenes de datos ODBC (32 bits)/(64 bits)** .  Esta acción abre la ventana **Administrador de orígenes de datos ODBC**.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png" alt-text="Administrador de orígenes de datos OBDC" border="true":::

1. Desde la pestaña **DSN del usuario**, seleccione **Agregar** para abrir la ventana **Crear nuevo origen de datos**.

1. Seleccione **Microsoft Hive ODBC Driver** y, luego, seleccione **Finalizar** para abrir la ventana **Microsoft Hive ODBC Driver DSN Setup** (Configuración de DSN de Microsoft Hive ODBC Driver).

1. Escriba o seleccione los valores siguientes:

   | Propiedad | Descripción |
   | --- | --- |
   |  Data Source Name |Asigne un nombre al origen de datos |
   |  Host(s) |Escriba `HDInsightClusterName.azurehdinsight.net`. Por ejemplo, `myHDICluster.azurehdinsight.net`. Nota: se admite `HDInsightClusterName-int.azurehdinsight.net` siempre y cuando la VM de cliente esté emparejada a la misma red virtual. |
   |  Port |Use **443**. (Este puerto se ha cambiado de 563 a 443). |
   |  Base de datos |Use el **valor predeterminado**. |
   |  Mechanism |Seleccione **Servicio HDInsight de Microsoft Azure**. |
   |  Nombre de usuario |Escriba el nombre de usuario HTTP del clúster de HDInsight. El nombre de usuario predeterminado es **admin**. |
   |  Contraseña |Escriba la contraseña del usuario del clúster de HDInsight. Seleccione la casilla **Save Password (Encrypted)** [Guardar contraseña (cifrada)].|

1. Opcional: Seleccione **Opciones avanzadas...**  

   | Parámetro | Descripción |
   | --- | --- |
   |  Use Native Query |Cuando esta opción está seleccionada, el controlador ODBC NO trata de convertir TSQL en HiveQL. Solo debe usarla si está totalmente seguro de que va a enviar instrucciones de HiveQL puras. Al conectarse a SQL Server o a Azure SQL Database, debe dejar esta opción desactivada. |
   |  Rows fetched per block |Al capturar un gran volumen de registros, es posible que sea necesario ajustar este parámetro para garantizar un rendimiento óptimo. |
   |  Default string column length, Binary column length, Decimal column scale |La longitud y precisión del tipo de datos pueden afectar a la forma en que se devuelven los datos. Pueden dar lugar a que se devuelva información incorrecta debido a la pérdida de precisión o al truncamiento. |

    :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png" alt-text="Opciones de configuración avanzada de DSN" border="true":::

1. Seleccione **Probar** para probar el origen de datos. Cuando el origen de datos esté configurado correctamente, el resultado de la prueba mostrará **SUCCESS** (Correcto).

1. Seleccione **Aceptar** para cerrar la ventana Probar.  

1. Seleccione **Aceptar** para cerrar la ventana **Microsoft Hive ODBC Driver DSN Setup**.  

1. Seleccione **Aceptar** para cerrar la ventana **Administrador de orígenes de datos ODBC**.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importación de datos en Excel desde HDInsight

En los pasos siguientes se describe cómo importar datos desde una tabla de Hive a un libro de Excel mediante el origen de datos ODBC creado en la sección anterior.

1. Abra un libro de Excel nuevo o existente.

2. Desde la pestaña **Datos**, vaya a **Obtener datos** > **Desde otros orígenes** > **Desde ODBC** para iniciar la ventana **Desde ODBC**.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png" alt-text="Open Excel data connection wizard" border="true"::: (Abrir el Asistente para la conexión de datos de Excel)

3. De la lista desplegable, seleccione el nombre del origen de datos que creó en la sección anterior y luego seleccione **Aceptar**.

4. Para el primer uso, se abrirá el cuadro de diálogo **Controlador ODBC**. Seleccione **Windows** en el menú izquierdo. Seleccione **Conectar** para abrir la ventana del **Navegador**.

5. Desde **Navegador**, vaya a **HIVE** > **default** > **hivesampletable** y, luego, seleccione **Cargar**. La importación de los datos a Excel tarda un momento.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png" alt-text="HDInsight Excel Hive ODBC navigator" border="true"::: (Navegador de Hive ODBC en Excel para HDInsight)

## <a name="next-steps"></a>Pasos siguientes

En este artículo se proporciona información sobre cómo usar el controlador ODBC de Microsoft Hive para recuperar datos del servicio HDInsight en Excel. De manera similar, puede recuperar datos del servicio HDInsight en la SQL Database. También es posible cargar datos en un servicio HDInsight. Para obtener más información, consulte:

* [Visualización de datos de Apache Hive con Microsoft Power BI en Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualización de datos de Interactive Query Hive con Power BI en Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Conexión de Excel a Apache Hadoop con Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conectarse a Azure HDInsight y ejecutar consultas de Apache Hive con Herramientas de Data Lake para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).