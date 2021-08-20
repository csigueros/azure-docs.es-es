---
title: Comprobación de la preparación del origen de datos a escala (versión preliminar)
description: En este tutorial, comprobará la preparación de los orígenes de datos de Azure antes de registrarlos y examinarlos en Azure Purview.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 05/28/2021
ms.openlocfilehash: d7dc8ab7987f149747df30834f426ce6d119eb5c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105928"
---
# <a name="tutorial-check-data-source-readiness-at-scale-preview"></a>Tutorial: Comprobación de la preparación del origen de datos a escala (versión preliminar)

> [!IMPORTANT]
> Azure Purview se encuentra actualmente en versión preliminar. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

Para analizar orígenes de datos, Azure Purview necesita acceso a ellos. Usa credenciales para obtener este acceso. Una *credencial* es la información de autenticación que Azure Purview puede usar para autenticarse en los orígenes de datos registrados. Hay varias maneras de configurar las credenciales de Azure Purview, entre las que se incluyen: 
- Identidad administrada asignada a la cuenta de Azure Purview.
- Secretos almacenados en Azure Key Vault. 
- Entidades de servicio.

En esta serie de tutoriales de dos partes, le ayudaremos a comprobar y configurar las asignaciones de roles de Azure necesarias y el acceso a la red para varios orígenes de datos de Azure en las suscripciones de Azure a escala. A continuación, puede registrar y examinar los orígenes de datos de Azure en Azure Purview. 

Ejecute el script de [lista de comprobación de preparación de orígenes de datos de Azure Purview](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) después de implementar la cuenta de Azure Purview y antes de registrar y examinar los orígenes de datos de Azure. 

En la parte 1 de esta serie de tutoriales, hará lo siguiente:

> [!div class="checklist"]
>
> * Buscar los orígenes de datos y preparar una lista de suscripciones de orígenes de datos.
> * Ejecutar el script de lista de comprobación de preparación para buscar cualquier control de acceso basado en rol (RBAC) que falte o las configuraciones de red en los orígenes de datos de Azure.
> * En el informe de salida, revise las configuraciones de red que faltan y las asignaciones de roles que requiere Azure Purview Managed Identity (MSI). 
> * Comparta el informe con los propietarios de suscripciones de Azure de datos para que puedan realizar las acciones sugeridas.

## <a name="prerequisites"></a>Requisitos previos

* Suscripciones de Azure donde se encuentran los orígenes de datos. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.
* Una [cuenta de Azure Purview](create-catalog-portal.md).
* Un recurso de Azure Key Vault en cada suscripción que tenga orígenes de datos como Azure SQL Database, Azure Synapse Analytics o Azure SQL Managed Instance.
* El script de [lista de comprobación de preparación de orígenes de datos de Azure Purview](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness).

> [!NOTE]
> La lista de comprobación de preparación de orígenes de datos de Azure Purview solo está disponible para Windows.
> Este script de lista de comprobación de preparación se admite actualmente con MSI de Azure Purview.

## <a name="prepare-azure-subscriptions-list-for-data-sources"></a>Preparación de la lista de suscripciones de Azure para orígenes de datos

Antes de ejecutar el script, cree un archivo .csv (por ejemplo, "C:\temp\Subscriptions.csv) con cuatro columnas:

|Nombre de la columna|Descripción|Ejemplo|
|----|----|----|
|`SubscriptionId`|Identificadores de suscripción de Azure para los orígenes de datos.|12345678-aaaa-bbbb-cccc-1234567890ab|
|`KeyVaultName`|Nombre del almacén de claves existente que se implementa en la suscripción del origen de datos.|ContosoDevKeyVault|
|`SecretNameSQLUserName`|Nombre de un secreto de Azure Key Vault existente que contiene un nombre de usuario de Azure Active Directory (Azure AD) que puede iniciar sesión en Azure Synapse, Azure SQL Database o Azure SQL Managed Instance mediante la autenticación de Azure AD.|ContosoDevSQLAdmin|
|`SecretNameSQLPassword`|Nombre de un secreto de Azure Key Vault existente que contiene una contraseña de Azure AD que puede iniciar sesión en Azure Synapse, Azure SQL Database o Azure SQL Managed Instance mediante la autenticación de Azure AD.|ContosoDevSQLPassword|
   

**Archivo .csv de ejemplo:**
    
:::image type="content" source="./media/tutorial-data-sources-readiness/subscriptions-input.png" alt-text="Captura de pantalla en la que se muestra una lista de suscripciones de ejemplo." lightbox="./media/tutorial-data-sources-readiness/subscriptions-input.png":::

> [!NOTE] 
> Puede actualizar el nombre de archivo y la ruta de acceso en el código, si es necesario.



## <a name="run-the-script-and-install-the-required-powershell-modules"></a>Ejecución del script e instalación de los módulos de PowerShell necesarios 

Siga estos pasos para ejecutar el script desde el equipo Windows:

1. [Descargue el script de lista de comprobación de preparación de orígenes de datos de Azure Purview](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) en la ubicación que prefiera.

2. En el equipo, escriba **PowerShell** en el cuadro de búsqueda de la barra de tareas de Windows. En la lista de búsqueda, haga clic con el botón derecho en **Windows PowerShell** y seleccione **Ejecutar como administrador**.

3. Escriba el comando siguiente en la ventana de PowerShell. (Reemplace `<path-to-script>` por la ruta de acceso del archivo de script extraído).

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. Escriba el comando siguiente para instalar los cmdlets de Azure:

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
6. Si ve el mensaje de advertencia *Se necesita el proveedor de NuGet para continuar*, escriba **Y** y presione **Entrar**.

7. Si ve el mensaje de advertencia *Repositorio que no es de confianza*, escriba **A** y presione **Entrar**.

5. Repita los pasos anteriores para instalar los módulos `Az.Synapse` y `AzureAD`.

PowerShell puede tardar hasta un minuto en instalar los módulos necesarios.


## <a name="collect-other-data-needed-to-run-the-script"></a>Recopilación de otros datos necesarios para ejecutar el script

Antes de ejecutar el script de PowerShell para comprobar la preparación de las suscripciones de origen de datos, obtenga los valores de los argumentos siguientes para usarlos en los scripts:

- `AzureDataType`: elija cualquiera de las opciones siguientes como tipo de origen de datos para comprobar la preparación del tipo de datos en las suscripciones: 
    
    - `BlobStorage`

    - `AzureSQLMI`

    - `AzureSQLDB`
    
    - `ADLSGen2`
    
    - `ADLSGen1`
    
    - `Synapse`
    
    - `All`

- `PurviewAccount`: nombre del recurso de la cuenta de Azure Purview existente.

- `PurviewSub`: identificador de suscripción donde se implementa la cuenta de Azure Purview.

## <a name="verify-your-permissions"></a>Comprobación de los permisos

Asegúrese de que el usuario tenga los siguientes roles y permisos:

Rol o permiso | Ámbito |
|-------|--------|
| **Lector global** | Inquilino de Azure AD |
| **Lector** | Suscripciones de Azure donde se encuentran los orígenes de datos de Azure. |
| **Lector** | Suscripción en la que se creó la cuenta de Azure Purview |
| **Administrador de SQL** (Autenticación de Azure AD) | Grupos dedicados de Azure Synapse, instancias de Azure SQL Database, instancias administradas de Azure SQL |
| Acceso al almacén de claves de Azure | Acceso para obtener o enumerar el secreto del almacén de claves o el usuario del secreto de Key Vault |  


## <a name="run-the-client-side-readiness-script"></a>Ejecución del script de preparación del lado cliente

Complete estos pasos para ejecutar el script:

1. Use el comando siguiente para ir a la carpeta del script. Reemplace `<path-to-script>` por la ruta de acceso de la carpeta del archivo extraído.

   ```powershell
   cd <path-to-script>
   ```

2. Ejecute el comando siguiente para establecer la directiva de ejecución para el equipo local. Escriba **A** para *Sí a todo* cuando se le pida que cambie la directiva de ejecución.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

3. Ejecute el script con los parámetros siguientes. De nuevo, reemplace los marcadores `DataType` , `PurviewName` y `SubscriptionID` .

   ```powershell
   .\purview-data-sources-readiness-checklist.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   Al ejecutar el comando, es posible que aparezca dos veces una ventana emergente en la que se le pide que inicie sesión en Azure y Azure AD con las credenciales de Azure Active Directory.


El informe puede tardar varios minutos en crearse, en función del número de suscripciones y recursos de Azure en el entorno. 

Una vez completado el proceso, revise el informe de salida, que muestra las configuraciones que faltan detectadas en las suscripciones o recursos de Azure. Los resultados pueden aparecer como _Superado_, _No superado_ o _Reconocimiento_. Puede compartir los resultados con los administradores de suscripciones correspondientes de su organización para que puedan configurar las opciones necesarias.


## <a name="more-information"></a>Información adicional

### <a name="what-data-sources-are-supported-by-the-script"></a>¿Qué orígenes de datos son compatibles con el script?

Actualmente, el script admite los orígenes de datos siguientes:

- Azure Blob Storage (BlobStorage)
- Azure Data Lake Storage Gen2 (ADLSGen2)
- Azure Data Lake Storage Gen1 (ADLSGen1)
- Azure SQL Database (AzureSQLDB)
- Azure SQL Managed Instance (AzureSQLMI)
- Grupo dedicado de Azure Synapse (Synapse)

Puede elegir todos o cualquiera de estos orígenes de datos como parámetro de entrada al ejecutar el script.

### <a name="what-checks-are-included-in-the-results"></a>¿Qué comprobaciones se incluyen en los resultados?

#### <a name="azure-blob-storage-blobstorage"></a>Azure Blob Storage (BlobStorage)

- RBAC. Compruebe si MSI de Azure Purview tiene asignado al rol **Lector de datos de Storage Blob** en cada una de las suscripciones situadas debajo del ámbito seleccionado.
- RBAC. Compruebe si MSI de Azure Purview tiene asignado el rol de **lector** en el ámbito seleccionado.
- Punto de conexión de servicio. Compruebe si el punto de conexión de servicio está activado y si **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** está habilitado.
- Redes: Compruebe si se crea un punto de conexión privado para el almacenamiento y se habilita para Blob Storage.

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2 (ADLSGen2)

- RBAC. Compruebe si MSI de Azure Purview tiene asignado al rol **Lector de datos de Storage Blob** en cada una de las suscripciones situadas debajo del ámbito seleccionado.
- RBAC. Compruebe si MSI de Azure Purview tiene asignado el rol de **lector** en el ámbito seleccionado.
- Punto de conexión de servicio. Compruebe si el punto de conexión de servicio está activado y si **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** está habilitado.
- Redes: Compruebe si se crea un punto de conexión privado para el almacenamiento y se habilita para Blob Storage.

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1 (ADLSGen1)

- Redes. Compruebe que el punto de conexión de servicio está habilitado y si **Allow all Azure services to access this Data Lake Storage Gen1 account** (Permitir que todos los servicios de Azure accedan a esta cuenta de Data Lake Storage Gen1) está habilitado.
- Permisos. Compruebe si MSI de Azure Purview tiene permisos de lectura o ejecución.

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL Database (AzureSQLDB)

- Instancias de SQL Server:
  - Red. Compruebe si el punto de conexión público o el punto de conexión privado están habilitados.
  - Firewall. Seleccione si **Permitir que los servicios y recursos de Azure accedan a este servidor** está habilitado.
  - Administración de Azure AD Compruebe si Azure SQL Server tiene la autenticación de Azure AD.
  - Administración de Azure AD Rellene el grupo o usuario administrador de Azure AD de Azure SQL Server.

- Bases de datos SQL:
  - Rol de SQL. Compruebe si MSI de Azure Purview tiene asignado el rol de **db_datareader**.

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL Managed Instance (AzureSQLMI)

- Servidores de SQL Managed Instance:
  - Red. Compruebe si el punto de conexión público o el punto de conexión privado están habilitados.
  - ProxyOverride. Compruebe si Azure SQL Managed Instance se ha configurado como proxy o redireccionamiento.
  - Redes. Compruebe si el grupo de seguridad de red tiene una regla de entrada para permitir AzureCloud a través de los puertos necesarios:  
    - Redireccionamiento: 1433 y 11000-11999  
    o
    - Proxy: 3342
  - Administración de Azure AD Compruebe si Azure SQL Server tiene la autenticación de Azure AD.
  - Administración de Azure AD Rellene el grupo o usuario administrador de Azure AD de Azure SQL Server.

- Bases de datos SQL:
  - Rol de SQL. Compruebe si MSI de Azure Purview tiene asignado el rol de **db_datareader**.

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Grupo dedicado de Azure Synapse (Synapse)

- RBAC. Compruebe si MSI de Azure Purview tiene asignado al rol **Lector de datos de Storage Blob** en cada una de las suscripciones situadas debajo del ámbito seleccionado.
- RBAC. Compruebe si MSI de Azure Purview tiene asignado el rol de **lector** en el ámbito seleccionado.
- Instancias de SQL Server (grupos dedicados):
  - Red: Informe de si el punto de conexión público o el punto de conexión privado están habilitados.
  - Firewall: Seleccione si **Permitir que los servicios y recursos de Azure accedan a este servidor** está habilitado.
  - Administración de Azure AD: Compruebe si Azure SQL Server tiene la autenticación de Azure AD.
  - Administración de Azure AD: Rellene el usuario o grupo o usuario administrador de Azure AD de Azure SQL Server.

- Bases de datos SQL:
  - Rol de SQL. Compruebe si MSI de Azure Purview tiene asignado el rol de **db_datareader**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:
> [!div class="checklist"]
>
> * Ejecute la lista de comprobación de preparación de Azure Purview para comprobar, a escala, si faltan las suscripciones de Azure antes de registrarlas y examinarlas en Azure Purview.

Vaya al siguiente tutorial para aprender a identificar el acceso necesario y a configurar las reglas de autenticación y red necesarias para Azure Purview en los orígenes de datos de Azure:

> [!div class="nextstepaction"]
> [Configuración del acceso a orígenes de datos para MSI de Azure Purview a escala](tutorial-msi-configuration.md)
