---
title: 'Copia de datos de Blob Storage en SQL Database: Azure'
description: En este tutorial se muestra cómo usar la actividad de copia en una canalización de Azure Data Factory para copiar datos de Almacenamiento de blobs en Base de datos SQL de Azure.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3ae57d6efbdff7c1b54c02b97a0b121b9a14b416
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638793"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Tutorial: Copia de datos de Blob Storage en SQL Database mediante Data Factory
> [!div class="op_single_selector"]
> * [Introducción y requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Asistente para copia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Plantilla de Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API de .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte el [tutorial de la actividad de copia](../quickstart-create-data-factory-dot-net.md).

En este tutorial, creará una factoría de datos con una canalización para copiar datos de Blob Storage a SQL Database.

La actividad de copia realiza el movimiento de datos en Azure Data Factory. Funciona con un servicio disponible de forma global que puede copiar datos entre varios almacenes de datos de forma segura, confiable y escalable. Consulte el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para obtener más información sobre la actividad de copia.  

> [!NOTE]
> Para información general más detallada sobre el servicio Data Factory, consulte el artículo [Introducción al servicio Azure Data Factory](data-factory-introduction.md) .
>
>

## <a name="prerequisites-for-the-tutorial"></a>Requisitos previos para el tutorial
Antes de comenzar este tutorial, debe cumplir los siguientes requisitos previos:

* **Suscripción de Azure**.  Si no tiene una suscripción, puede crear una cuenta de prueba gratuita en tan solo un par de minutos. Consulte el artículo [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) para obtener información.
* **Cuenta de Azure Storage**. Almacenamiento de blobs se usará como un almacén de datos de **origen** en este tutorial. Si no tiene una cuenta de almacenamiento de Azure, consulte la sección [Crear una cuenta de almacenamiento](../../storage/common/storage-account-create.md) para ver los pasos para su creación.
* **Azure SQL Database**. Usará Azure SQL Database como almacén de datos de **destino** en este tutorial. Si no dispone de una base de datos de Azure SQL Database que pueda usar en el tutorial, consulte [Creación y configuración de una base de datos en Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md) para crear una.
* **SQL Server 2012/2014 o Visual Studio 2013**. Usará SQL Server Management Studio o Visual Studio para crear una base de datos de ejemplo y ver los datos de resultados de la base de datos.  

## <a name="collect-blob-storage-account-name-and-key"></a>Obtención del nombre y la clave de la cuenta de Almacenamiento de blobs
Para realizar este tutorial necesitará el nombre de cuenta y la clave de cuenta de su cuenta de Almacenamiento de Azure. Anote el **nombre de cuenta** y la **clave de cuenta** para su cuenta de Azure Storage.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Haga clic en **Todos los servicios** en el menú de la izquierda y seleccione **Cuentas de almacenamiento**.

    :::image type="content" source="media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png" alt-text="Examinar: Cuentas de almacenamiento":::
3. En la hoja **Cuentas de almacenamiento**, seleccione la **cuenta de Azure Storage** que desea usar en este tutorial.
4. Seleccione **Claves de acceso** en **CONFIGURACIÓN**.
5. Haga clic en el botón **copiar** (imagen) que se encuentra junto al cuadro de texto **Nombre de cuenta de almacenamiento** y guárdelo y péguelo en algún lugar (por ejemplo: en un archivo de texto).
6. Repita el paso anterior para copiar o anotar la **clave 1**.

    :::image type="content" source="media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png" alt-text="Clave de acceso de almacenamiento":::
7. Haga clic en **X** para cerrar todas las hojas.

## <a name="collect-sql-server-database-user-names"></a>Recopilación de nombres de usuario, bases de datos y servidores SQL Server
Necesitará los nombres del servidor SQL lógico, la base de datos y el usuario para realizar este tutorial. Anote los nombres del **servidor**, la **base de datos** y el **usuario** de Azure SQL Database.

1. En **Azure Portal**, haga clic en **Todos los servicios** a la izquierda y seleccione **Bases de datos SQL**.
2. En la **hoja de bases de datos SQL**, seleccione la **base de datos** que desea usar en este tutorial. Anote el **nombre de la base de datos**.  
3. En la hoja **SQL Database**, haga clic en **Propiedades** en **CONFIGURACIÓN**.
4. Anote los valores de **NOMBRE DEL SERVIDOR** e **INICIO DE SESIÓN DE ADMINISTRADOR DE SERVIDOR**.
5. Haga clic en **X** para cerrar todas las hojas.

## <a name="allow-azure-services-to-access-sql-server"></a>Procedimiento para permitir que los servicios de Azure accedan a SQL Server
Asegúrese de que la configuración **Permitir el acceso a los servicios de Azure** está **ACTIVADA** para el servidor, de tal modo que el servicio Data Factory pueda acceder a este servidor. Para comprobar y activar esta configuración, realice los siguientes pasos:

1. Haga clic en el concentrador **Todos los servicios** a la izquierda y haga clic en **Servidores SQL**.
2. Seleccione el servidor y haga clic en **Firewall** en **CONFIGURACIÓN**.
3. En la hoja **Configuración de firewall**, haga clic en **ACTIVADA** para **Permitir el acceso a los servicios de Azure**.
4. Haga clic en **X** para cerrar todas las hojas.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparación de Blob Storage y SQL Database
Ahora, prepare su almacenamiento de blobs de Azure e instancia de Azure SQL Database para el tutorial realizando los pasos siguientes:  

1. Inicie el Bloc de notas. Copie el texto siguiente y guárdelo como **emp.txt** en la carpeta **C:\ADFGetStarted** de su disco duro.

    ```
    John, Doe
    Jane, Doe
    ```
2. Use herramientas como el [Explorador de Azure Storage](https://storageexplorer.com/) para crear el contenedor **adftutorial** y cargar el archivo **emp.txt** en el contenedor.

3. Use el siguiente script de SQL para crear la tabla **emp** en su base de datos de Azure SQL.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Si tiene SQL Server 2012/2014 instalado en el equipo**: siga las instrucciones de [Administración de Azure SQL Database con SQL Server Management Studio](../../azure-sql/database/single-database-manage.md) para conectarse a su servidor y ejecutar el script de SQL.

    Si el cliente no tiene permiso para acceder al servidor SQL lógico, tendrá que configurar el firewall del servidor para permitir el acceso desde su máquina (dirección IP). Vea [este artículo](../../azure-sql/database/firewall-configure.md) para conocer los pasos que se deben seguir para configurar el firewall del servidor.

## <a name="create-a-data-factory"></a>Crear una factoría de datos
Ha completado los requisitos previos. Puede crear una factoría de datos de una de las siguientes formas. Haga clic en una de las opciones de la lista desplegable de la parte superior o en los vínculos siguientes para realizar el tutorial.     

* [Asistente para copia](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Plantilla de Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [API de .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> La canalización de datos de este tutorial copia datos de un almacén de datos de origen a un almacén de datos de destino. No transforma los datos de entrada para generar datos de salida. Para ver un tutorial acerca de cómo transformar datos mediante Azure Data Factory, consulte [Tutorial: Compilación de la primera canalización para transformar datos mediante el clúster de Hadoop](data-factory-build-your-first-pipeline.md).
>
> Puede encadenar dos actividades (ejecutar una después de otra) haciendo que el conjunto de datos de salida de una actividad sea el conjunto de datos de entrada de la otra actividad. Para más información, consulte [Programación y ejecución en Data Factory](data-factory-scheduling-and-execution.md).