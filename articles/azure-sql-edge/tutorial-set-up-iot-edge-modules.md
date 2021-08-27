---
title: Configuración de módulos de IoT Edge en Azure SQL Edge
description: En la segunda parte de este tutorial de tres partes de Azure SQL Edge para predecir impurezas de mineral de hierro, configurará módulos y conexiones de IoT Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: dab213cf240dd955f1aea2a545b5c25efcdef349
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751338"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Configuración de módulos y conexiones de IoT Edge

En la segunda parte de este tutorial de tres partes para predecir impurezas de mineral de hierro en Azure SQL Edge, configurará los siguientes módulos de IoT Edge:

- Azure SQL Edge
- Módulo IoT Edge del generador de datos

## <a name="specify-container-registry-credentials"></a>Especificación de las credenciales de registro de contenedor

Debe especificar las credenciales en los registros de contenedor que hospedan las imágenes de módulo. Se pueden encontrar en el registro de contenedor que se creó en el grupo de recursos. Vaya a la sección **Claves de acceso**. Anote los campos siguientes:

- Nombre de registro
- Servidor de inicio de sesión
- Nombre de usuario
- Contraseña

Ahora, especifique las credenciales del contenedor en el módulo IoT Edge.

1. Vaya al centro de IoT que creó en el grupo de recursos.

2. En la sección **IoT Edge**, en **Administración automática de dispositivos**, haga clic en **Id. de dispositivo**. Para este tutorial, el identificador es `IronOrePredictionDevice`.

3. Seleccione la sección **Establecer módulos**.

4. En **Credenciales de Container Registry**, escriba los valores siguientes:

   | _Campo_   | _Valor_       |
   | -------   | -------       |
   | Nombre      | Nombre de registro |
   | Dirección   | Servidor de inicio de sesión  |
   | Nombre de usuario | Nombre de usuario      |
   | Contraseña  | Contraseña      |

## <a name="build-push-and-deploy-the-data-generator-module"></a>Creación, inserción e implementación del módulo del generador de datos

1. Clone los [archivos de proyecto](https://github.com/microsoft/sqlsourabh/tree/main/SQLEdgeSamples/IoTEdgeSamples/IronOreSilica) en la máquina.
2. Abra el archivo **IronOre_Silica_Predict.sln** con Visual Studio 2019.
3. Actualice los detalles del registro de contenedor en **deployment.template.json**.
   ```json
   "registryCredentials":{
        "RegistryName":{
            "username":"",
            "password":""
            "address":""
        }
    }
   ```
4. Actualice el archivo **modules.json** para especificar el registro de contenedor de destino (o el repositorio para el módulo).
   ```json
   "image":{
        "repository":"samplerepo.azurecr.io/ironoresilicapercent",
        "tag":
    }
   ```
5. Ejecute el proyecto en modo de depuración o de lanzamiento para asegurarse de que el proyecto se ejecuta sin problemas.
6. Inserte el proyecto en el registro de contenedor; para ello, haga clic con el botón derecho en el nombre del proyecto y, a continuación, seleccione **Compilación e inserción de módulos IoT Edge**.
7. Implemente el módulo del generador de datos como módulo de IoT Edge en el dispositivo perimetral.

## <a name="deploy-the-azure-sql-edge-module"></a>Implementación del módulo de Azure SQL Edge

1. Implemente el módulo Azure SQL Edge; para ello, haga clic en **+ Agregar** y, después, en **Módulo de Marketplace**.

2. En la hoja **Marketplace de módulos IoT Edge**, busque *Azure SQL Edge* y seleccione *Desarrollador de Azure SQL Edge*.

3. Haga clic en el módulo *Azure SQL Edge* recién agregado en **Módulos IoT Edge** para configurar el módulo Azure SQL Edge. Para obtener más información sobre las opciones de configuración, consulte [Implementación de Azure SQL Edge](./deploy-portal.md).

4. Agregue la variable de entorno `MSSQL_PACKAGE` a la implementación del módulo *Azure SQL Edge* y especifique la dirección URL de SAS del archivo dacpac de base de datos creado en el paso 8 de la [primera parte](tutorial-deploy-azure-resources.md) de este tutorial.

5. Haga clic en **Update** (Actualizar).

6. En la página **Establecer módulos en el dispositivo**, haga clic en **Siguiente: Rutas >** .

7. En el panel de rutas de la página **Establecer módulos en el dispositivo**, especifique las rutas del módulo para la comunicación del centro de IoT Edge como se describe a continuación. Asegúrese de actualizar los nombres de los módulos en las definiciones de ruta siguientes.

   ```syntax
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Por ejemplo:

   ```syntax
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. En la página **Establecer módulos en el dispositivo**, haga clic en **Siguiente: Revisar y crear >**

8. En la página **Establecer módulos en el dispositivo**, haga clic en **Crear**.

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Cree e inicie el trabajo de streaming de T-SQL en Azure SQL Edge.

1. Abra Azure Data Studio.

2. En la pestaña **Bienvenida**, inicie una nueva conexión con los detalles siguientes:

   |_Campo_|_Valor_|
   |-------|-------|
   |Tipo de conexión| Microsoft SQL Server|
   |Server|Dirección IP pública mencionada en la VM que se creó para esta demostración.|
   |Nombre de usuario|sa|
   |Contraseña|Contraseña segura que se usó al crear la instancia de Azure SQL Edge.|
   |Base de datos|Valor predeterminado|
   |Grupo del servidor|Valor predeterminado|
   |Nombre (opcional)|Puede proporcionar un nombre opcional.|

3. Haga clic en **Conectar**

4. En la pestaña del menú **Archivo**, abra un cuaderno nuevo o use el método abreviado de teclado CTRL+N.

5. En la nueva ventana Consulta, ejecute el siguiente script para crear el trabajo de streaming de T-SQL. Antes de ejecutar el script, asegúrese de cambiar las siguientes variables.
   - *SQL_SA_Password:* El valor MSSQL_SA_PASSWORD especificado durante la implementación del módulo Azure SQL Edge.

   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max)

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]
      WITH ( FORMAT_TYPE = JSON)
   End


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub]
      With(
         LOCATION = N'edgehub://'
      )
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. Utilice la siguiente consulta para comprobar que los datos del módulo de generación de datos se estén transmitiendo a la base de datos.

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


En este tutorial, hemos implementado el módulo generador de datos y el módulo SQL Edge. Después, hemos creado un trabajo de streaming para transmitir los datos generados por el módulo de generación de datos a SQL.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación del modelo de ML en Azure SQL Edge mediante ONNX](tutorial-run-ml-model-on-sql-edge.md)
