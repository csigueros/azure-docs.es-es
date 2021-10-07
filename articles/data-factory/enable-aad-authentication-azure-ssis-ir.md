---
title: Habilitar la autenticación de Azure Active Directory para el entorno de ejecución de integración de Azure SSIS
description: En este artículo se describe cómo habilitar la autenticación de Azure Active Directory con el sistema especificado o la identidad administrada asignada por el usuario para Azure Data Factory para crear un entorno de ejecución de integración de Azure-SSIS.
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 09/27/2021
ms.openlocfilehash: f887d3370364f899a1e7afd527c9a146b20bd637
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081422"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Habilitar la autenticación de Azure Active Directory para Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este artículo se muestra cómo habilitar la autenticación de Azure Active Directory (Azure AD) con el sistema especificado o la identidad administrada asignada por el usuario para Azure Data Factory (ADF) y usarla en lugar de los métodos de autenticación convencionales (como la autenticación de SQL) para:

- Crear un entorno de ejecución de integración de Azure-SSIS que, a su vez, aprovisionará la base de datos del catálogo de SSIS (SSISDB) en el servidor de Azure SQL Database o la Instancia administrada en su nombre.

- Conectarse a varios recursos de Azure al ejecutar paquetes SSIS en Azure-SSIS IR.

Para más información sobre la identidad administrada de ADF, consulte [Identidad administrada de Data Factory](./data-factory-service-identity.md).

> [!NOTE]
> - En este escenario, la autenticación de Azure AD con el sistema especificado o la identidad administrada asignada por el usuario para su instancia de ADF solo se usa en el aprovisionamiento y en las operaciones de inicio posteriores de Azure-SSIS IR que, a su vez, aprovisionará o conectará SSISDB. Para las ejecuciones de paquetes de SSIS, la instancia de Azure-SSIS IR se conectará con SSISDB para capturar los paquetes mediante la autenticación de SQL con cuentas totalmente administradas (*AzureIntegrationServiceDbo* y *AzureIntegrationServiceWorker*) que se crean durante el aprovisionamiento de SSISDB.
>
> - Si ya ha creado una instancia de Azure-SSIS IR con la autenticación de SQL, no puede volver a configurarla para usar la autenticación de Azure AD con PowerShell en este momento, pero puede hacerlo en Azure Portal o la aplicación ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-authentication-on-azure-sql-database"></a>Habilite la autenticación de Azure AD para Azure SQL Database

Azure SQL Database admite la creación de una base de datos con un usuario de Azure AD. En primer lugar, debe crear un grupo de Azure AD con el sistema especificado o la identidad administrada asignada por el usuario para ADF como miembro. A continuación, debe establecer un usuario de Azure AD como administrador de Active Directory para el servidor de Azure SQL Database y luego conectarse desde SQL Server Management Studio (SSMS) con ese usuario. Por último, deberá crear un usuario contenido que represente al grupo de Azure AD, de modo que Azure-SSIS IR pueda usar el sistema especificado o la identidad administrada asignada por el usuario para ADF para crear SSISDB en su nombre.

### <a name="create-an-azure-ad-group-with-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-member"></a>Cree un grupo de Azure AD con el sistema especificado o la identidad administrada asignada por el usuario para ADF como miembro

Puede utilizar un grupo de Azure AD existente o crear uno nuevo con Azure AD PowerShell.

1. Instale el módulo de [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

2. Inicie sesión con `Connect-AzureAD`, ejecute el siguiente cmdlet para crear el grupo y guárdelo en una variable:

   ```powershell
   $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                             -MailEnabled $false `
                             -SecurityEnabled $true `
                             -MailNickName "NotSet"
   ```

   El resultado es similar al del siguiente ejemplo, que también muestra el valor de la variable:

   ```powershell
   $Group

   ObjectId DisplayName Description
   -------- ----------- -----------
   6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
   ```

3. Agregue el sistema especificado o la identidad administrada asignada por el usuario para ADF al grupo. Puede seguir el artículo [Identidad administrada de Data Factory](./data-factory-service-identity.md) para obtener el identificador de objeto del sistema especificado o la identidad administrada asignada por el usuario (por ejemplo, 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, pero no use el id. de aplicación de la identidad administrada para este propósito).

   ```powershell
   Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
   ```

   También puede comprobar la pertenencia al grupo más adelante.

   ```powershell
   Get-AzureAdGroupMember -ObjectId $Group.ObjectId
   ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database"></a>Configurar la autenticación de Azure AD para Azure SQL Database

Puede [configurar y administrar la autenticación de Azure AD para Azure SQL Database](../azure-sql/database/authentication-aad-configure.md) siguiendo estos pasos:

1. En Azure Portal, seleccione **Todos los servicios** -> **Servidores SQL** en el panel de navegación izquierdo.

2. Seleccione el servidor de Azure SQL Database que quiera configurar con la autenticación de Azure AD.

3. En la sección **Configuración** de la hoja, haga clic en **Administrador de Active Directory**.

4. En la barra de comandos, seleccione **Establecer administrador**.

5. Elija una cuenta de usuario de Azure AD para que sea el administrador del servidor y, después, haga clic en **Seleccionar**.

6. En la barra de comandos, seleccione **Guardar**.

### <a name="create-a-contained-user-in-azure-sql-database-representing-the-azure-ad-group"></a>Cree un usuario contenido en Azure SQL Database que represente al grupo de Azure AD

Para el siguiente paso, necesita [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

1. Inicie SSMS.

2. En el cuadro de diálogo **Conectar al servidor**, escriba el nombre de su servidor en el campo **Nombre del servidor**.

3. En el campo **Autenticación**, seleccione **Active Directory - Universal compatible con MFA** (también puede usar los otros dos tipos de autenticación de Active Directory; consulte [Configuración y administración de la autenticación de Azure AD para Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)).

4. En el campo **Nombre de usuario**, escriba el nombre de la cuenta de Azure AD que estableció como administrador del servidor, por ejemplo, testuser@xxxonline.com.

5. Seleccione **Conectar** y complete el proceso de inicio de sesión.

6. En el **Explorador de objetos**, expanda la carpeta **Bases de datos** -> **Bases de datos del sistema**.

7. Haga clic con el botón derecho en la base de datos **maestra** y seleccione **Nueva consulta**.

8. En la ventana de consulta, escriba el siguiente comando T-SQL y seleccione **Ejecutar** en la barra de herramientas.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   El comando debería completarse correctamente y crear el usuario contenido para representar al grupo.

9. Borre la ventana de consulta, escriba el siguiente comando T-SQL y seleccione **Ejecutar** en la barra de herramientas.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   El comando debería completarse correctamente y conceder al usuario contenido la capacidad de crear una base de datos (SSISDB).

10. Si la SSISDB se ha creado mediante la autenticación de SQL y desea cambiar a la autenticación de Azure AD, con el fin de que Azure-SSIS IR tenga acceso a ella, primero debe asegurarse de que los pasos señalados anteriormente para conceder permisos a la base de datos maestra (**master**) se han realizado correctamente. Después, haga clic con el botón derecho en la base de datos **SSISDB** y seleccione **Nueva consulta**.

    1. En la ventana de consulta, escriba el siguiente comando T-SQL y seleccione **Ejecutar** en la barra de herramientas.

       ```sql
       CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
       ```

       El comando debería completarse correctamente y crear el usuario contenido para representar al grupo.

    2. Borre la ventana de consulta, escriba el siguiente comando T-SQL y seleccione **Ejecutar** en la barra de herramientas.

       ```sql
       ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
       ```

       El comando debería completarse correctamente y conceder al usuario contenido la capacidad de acceder a SSISDB.

## <a name="enable-azure-ad-authentication-on-azure-sql-managed-instance"></a>Habilite la autenticación de Azure AD para Azure SQL Managed Instance

Azure SQL Managed Instance admite la creación de una base de datos con sistema especificado o la identidad administrada asignada por el usuario para ADF directamente. No necesita unir el sistema especificado o la identidad administrada asignada por el usuario para ADF a un grupo de Azure AD ni crear un usuario contenido que represente dicho grupo en Azure SQL Managed Instance.

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Configuración de la autenticación de Azure AD para Instancia administrada de Azure SQL

Siga los pasos que aparecen en [Aprovisionamiento de un administrador de Azure AD para Azure SQL Managed Instance](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

### <a name="add-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-user-in-azure-sql-managed-instance"></a>Agregue el sistema especificado o la identidad administrada asignada por el usuario para ADF como usuario de Azure SQL Managed Instance

Para el siguiente paso, necesita [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

1. Inicie SSMS.

2. Conéctese a Azure SQL Managed Instance con una cuenta de SQL Server que sea **sysadmin**. Esta es una limitación temporal que se quitará una vez que el soporte para las entidades de seguridad (inicios de sesión) del servidor de Azure AD de Azure SQL Managed Instance estén disponibles con carácter general. Si intenta usar una cuenta de administrador de Azure AD para crear el inicio de sesión, verá el siguiente error: *Mens. 15247, nivel 16, estado 1, línea 1 El usuario no tiene permiso para realizar esta acción*.

3. En el **Explorador de objetos**, expanda la carpeta **Bases de datos** -> **Bases de datos del sistema**.

4. Haga clic con el botón derecho en la base de datos **maestra** y seleccione **Nueva consulta**.

5. En la ventana de consulta, ejecute el siguiente script de T-SQL para agregar el sistema especificado o la identidad administrada asignada por el usuario para ADF como usuario.

   ```sql
   CREATE LOGIN [{your managed identity name}] FROM EXTERNAL PROVIDER
   ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your managed identity name}]
   ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your managed identity name}]
   ```

   Si usa la identidad administrada del sistema para ADF, el *nombre de la identidad administrada* debe ser el nombre de ADF. Si usa una identidad administrada asignada por el usuario para ADF, el *nombre de la identidad administrada* debe ser el nombre de la identidad administrada asignada por el usuario especificado.
    
   El comando debería completarse correctamente y conceder al sistema o identidad administrada asignada por el usuario para ADF la capacidad de crear una base de datos (SSISDB).

6. Si la SSISDB se ha creado mediante la autenticación de SQL y desea cambiar a la autenticación de Azure AD, con el fin de que Azure-SSIS IR tenga acceso a ella, primero debe asegurarse de que los pasos señalados anteriormente para conceder permisos a la base de datos maestra (**master**) se han realizado correctamente. Después, haga clic con el botón derecho en la base de datos **SSISDB** y seleccione **Nueva consulta**.

   1. En la ventana de consulta, escriba el siguiente comando T-SQL y seleccione **Ejecutar** en la barra de herramientas.

      ```sql
      CREATE USER [{your managed identity name}] FOR LOGIN [{your managed identity name}] WITH DEFAULT_SCHEMA = dbo
      ALTER ROLE db_owner ADD MEMBER [{your managed identity name}]
      ```

      El comando debería completarse correctamente y conceder al sistema o identidad administrada asignada por el usuario para ADF la capacidad de acceder a SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Aprovisionar Azure-SSIS IR en Azure Portal o ADF

Al aprovisionar la aplicación Azure-SSIS IR en Azure Portal/ADF, en la página **Configuración de implementación**, seleccione la casilla **Crear catálogo de SSIS (SSISDB) hospedado por un servidor de Azure SQL Database o una instancia administrada para almacenar los proyectos, paquetes, entornos y registros de ejecución** y, a continuación, active la casilla **Usar autenticación de AAD con la identidad administrada del sistema para Data Factory** o **Usar autenticación de AAD con una identidad administrada asignada por el usuario para Data Factory** para elegir un método de autenticación de Azure AD para Azure-SSIS IR para acceder al servidor de base de datos que hospeda SSISDB.

Para más información, consulte [Creación de una instancia de Azure-SSIS IR en ADF](./create-azure-ssis-integration-runtime.md).

## <a name="provision-azure-ssis-ir-with-powershell"></a>Aprovisionamiento de Azure-SSIS IR con PowerShell

Para aprovisionar el entorno de ejecución para la integración de SSIS en Azure con PowerShell, haga lo siguiente:

1. Instale el módulo [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018).

2. En el script, no establezca el parámetro `CatalogAdminCredential`. Por ejemplo:

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                         -DataFactoryName $DataFactoryName `
                                         -Name $AzureSSISName `
                                         -Description $AzureSSISDescription `
                                         -Type Managed `
                                         -Location $AzureSSISLocation `
                                         -NodeSize $AzureSSISNodeSize `
                                         -NodeCount $AzureSSISNodeNumber `
                                         -Edition $AzureSSISEdition `
                                         -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                         -CatalogServerEndpoint $SSISDBServerEndpoint `
                                         -CatalogPricingTier $SSISDBPricingTier

   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName
   ```

## <a name="run-ssis-packages-using-azure-ad-authentication-with-the-specified-systemuser-assigned-managed-identity-for-your-adf"></a>Ejecución de paquetes SSIS mediante autenticación de Azure AD con el sistema especificado o la identidad administrada asignada por el usuario para ADF

Al ejecutar paquetes SSIS en Azure-SSIS IR, puede usar la autenticación de Azure AD el sistema especificado o la identidad administrada asignada por el usuario para que ADF se conecte a varios recursos de Azure. Actualmente, admitimos la autenticación de AD con el sistema especificado o la identidad administrada asignada por el usuario para ADF en los siguientes administradores de conexiones.

- [Administrador de conexiones OLEDB](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [Administrador de conexiones ADO.NET](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Administrador de conexiones de Azure Storage](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
