---
title: 'Tutorial de Data Factory: primera canalización de datos '
description: En este tutorial de Azure Data Factory se muestra cómo crear y programar una factoría de datos que procese los datos mediante el script de Hive en un clúster de Hadoop.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 06d5821fd6841f0fce5b657a49ecbca6fe232be1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557800"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Tutorial: Compilación de la primera canalización para procesar datos mediante el clúster de Hadoop
> [!div class="op_single_selector"]
> * [Introducción y requisitos previos](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Plantilla de Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, consulte [Guía de inicio rápido: Creación de una factoría de datos mediante Data Factory](../quickstart-create-data-factory-dot-net.md).

En este tutorial, se crea una instancia de Azure Data Factory con una canalización de datos. La canalización transforma los datos de entrada mediante la ejecución de un script de Hive en un clúster de Azure HDInsight (Hadoop) para generar datos de salida.  

Este artículo se proporciona información general del tutorial y se indican sus requisitos previos. Cuando se cumplan los requisitos previos, puede realizar el tutorial mediante uno de los siguientes SDK o herramientas: Visual Studio, PowerShell, plantilla de Resource Manager o API REST. Seleccione una de las opciones de la lista desplegable del principio o los vínculos del final de este artículo para realizar el tutorial mediante una de estas opciones.    

## <a name="tutorial-overview"></a>Información general del tutorial
En este tutorial, realizará los siguientes pasos:

1. Crear una **factoría de datos**. Una factoría de datos puede contener una o varias canalizaciones de datos que mueven y procesan datos.

    En este tutorial, se crea una canalización de la factoría de datos.
2. Crear una **canalización**. Una canalización puede tener una o varias actividades (ejemplos: actividad de copia, actividad de Hive en HDInsight). Este ejemplo usa la actividad de Hive de HDInsight que ejecuta un script de Hive en un clúster de Hadoop de HDInsight. El script crea primero una tabla que hace referencia a los datos de blog sin procesar almacenados en Azure Blob Storage y, después, divide los datos sin procesar por año y mes.

    En este tutorial, la canalización usa Actividad de Hive para transformar datos, para lo que ejecuta una consulta de Hive en un clúster de Hadoop de Azure HDInsight.
3. Cree **servicios vinculados**. Un servicio vinculado se crea para vincular un almacén de datos o servicio de proceso a la factoría de datos. Un almacén de datos como Azure Storage contiene los datos de entrada y salida de las actividades de la canalización. Un servicio de proceso como un clúster de Hadoop de HDInsight procesa y transforma los datos.

    En este tutorial se crean dos servicios vinculados: **Azure Storage** y **Azure HDInsight**. El servicio vinculado Azure Storage vincula una cuenta de Azure Storage que contiene los datos de entrada/salida con la factoría de datos. El servicio vinculado Azure HDInsight vincula un clúster de Azure HDInsight que se utiliza para transformar los datos con la factoría de datos.
3. Crear **conjuntos de datos** de entrada y salida. Un conjunto de datos de entrada representa la entrada para una actividad de la canalización y un conjunto de datos de salida representa la salida de la actividad.

    En este tutorial, los conjuntos de datos de entrada y salida especifican las ubicaciones de los datos de entrada y salida en Azure Blob Storage. El servicio vinculado Azure Storage especifica qué cuenta de Azure Storage se usa. Un conjunto de datos de entrada especifica el lugar en que se encuentran los archivos de entrada, mientras que un conjunto de datos de salida especifica el lugar en que se colocan los archivos de salida.


Para obtener información general detallada de Azure Data Factory, consulte el artículo [Introducción a Azure Data Factory](data-factory-introduction.md).

Este es la **vista de diagrama** de la factoría de datos de ejemplo creada en este tutorial. **MyFirstPipeline** tiene una actividad del tipo Hive que consume el conjunto de datos **AzureBlobInput** como entrada y genera el conjunto de datos **AzureBlobOutput** como salida.

:::image type="content" source="media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png" alt-text="Vista de diagrama en el tutorial de Data Factory":::


En este tutorial, la carpeta **inputdata** del contenedor de blobs de Azure **adfgetstarted** contiene un archivo llamado input.log. Este archivo de registro tiene entradas de tres meses: enero, febrero y marzo de 2016. Aquí están las filas de ejemplo para cada mes en el archivo de entrada.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Cuando la canalización procesa el archivo mediante la actividad de Hive de HDInsight, esta actividad ejecuta un script de Hive en el clúster de HDInsight que divide los datos de entrada por año y mes. El script crea tres carpetas de salida que contienen un archivo con entradas de cada mes.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

De las líneas de ejemplo mostradas anteriormente, la primera de ellas (con 2016-01-01) se escribirá en el archivo 000000_0 en la carpeta month=1. De igual manera, la segunda se escribirá en el archivo de la carpeta month=2 y la tercera en el archivo de la carpeta month=3.  

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, debe cumplir los siguientes requisitos previos:

1. **Suscripción de Azure** : si no tiene ninguna, puede crear una cuenta de evaluación gratuita en un par de minutos. Consulte el artículo [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) sobre cómo puede obtener una cuenta de evaluación gratuita.
2. **Azure Storage** : para almacenar los datos de este tutorial usará una cuenta de Azure Storage. Si no dispone de una cuenta de almacenamiento de Azure, vea el artículo [Creación de una cuenta de almacenamiento](../../storage/common/storage-account-create.md) . Después de haber creado la cuenta de almacenamiento, anote el **nombre de la cuenta** y la **clave de acceso**. Para más información sobre cómo recuperar las claves de acceso de las cuentas de almacenamiento, consulte [Administración de claves de acceso de cuentas de almacenamiento](../../storage/common/storage-account-keys-manage.md).
3. Descargue y revise el archivo de consulta de Hive (**HQL**) ubicado en: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Esta consulta transforma los datos de entrada para generar datos de salida.
4. Descargue y revise el archivo de entrada de ejemplo (**input.log**) ubicado en: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Crear un contenedor de blobs denominado **adfgetstarted** en Azure Blob Storage.
6. Cargue el archivo **partitionweblogs.hql** en la carpeta **script** del contenedor **adfgetstarted**. Use herramientas como el [Explorador de Microsoft Azure Storage](https://storageexplorer.com/).
7. Cargue el archivo **input.log** en la carpeta **inputdata** del contenedor **adfgetstarted**.

Una vez que se cumplan los requisitos previos, seleccione uno de los siguientes SDK o herramientas para realizar el tutorial:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Plantilla de Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio proporciona una forma de generar factorías de datos mediante GUI. Mientras que las opciones de PowerShell, la plantilla de Resource Manager y la API de REST proporcionan una forma de generar factorías de datos mediante scripts/programación.

> [!NOTE]
> En este tutorial, la canalización de datos transforma los datos de entrada para generar datos de salida. No copia los datos de un almacén de datos de origen a un almacén de datos de destino. Para ver un tutorial acerca de cómo copiar datos mediante Azure Data Factory, consulte [Tutorial: Copia de datos de Blob Storage en SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Puede encadenar dos actividades (ejecutar una después de otra) haciendo que el conjunto de datos de salida de una actividad sea el conjunto de datos de entrada de la otra actividad. Para más información, consulte [Programación y ejecución en Data Factory](data-factory-scheduling-and-execution.md).
