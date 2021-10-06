---
title: Configuración del acceso a orígenes de datos para MSI de Azure Purview a escala
description: En este tutorial, configurará valores MSI de Azure en las suscripciones de origen de datos de Azure.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 09/27/2021
ms.openlocfilehash: 101d18cdecdc4fc7d4fb33b824500350f53b7b3f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213401"
---
# <a name="tutorial-configure-access-to-data-sources-for-azure-purview-msi-at-scale"></a>Tutorial: Configuración del acceso a orígenes de datos para MSI de Azure Purview a escala

Para examinar orígenes de datos, Azure Purview necesita acceder a ellos. Este tutorial está pensado para propietarios de suscripciones de Azure y administradores de orígenes de datos de Azure Purview. Le ayudará a identificar el acceso necesario y configurar las reglas de autenticación y red necesarias para Azure Purview en los orígenes de datos de Azure.

En la parte 2 de esta serie de tutoriales, hará lo siguiente:

> [!div class="checklist"]
>
> * Buscar los orígenes de datos y preparar una lista de suscripciones de orígenes de datos.
> * Ejecutar un script para configurar cualquier control de acceso basado en rol (RBAC) que falte o las configuraciones de red necesarias en los orígenes de datos de Azure.
> * Revisar el informe de salida.

## <a name="prerequisites"></a>Requisitos previos

* Suscripciones de Azure donde se encuentran los orígenes de datos. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.
* Una [cuenta de Azure Purview](create-catalog-portal.md).
* Un recurso de Azure Key Vault en cada suscripción que tenga orígenes de datos como Azure SQL Database, Azure Synapse Analytics o Azure SQL Managed Instance.
* El script de [configuración MSI de Azure Purview](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-MSI-Configuration).

> [!NOTE]
> El script de configuración MSI de Azure Purview solo está disponible para Windows.
> Este script es compatible actualmente con Identidad administrada de Azure Purview (MSI).

> [!IMPORTANT]
> Se recomienda encarecidamente probar y comprobar todos los cambios que realiza el script en el entorno de Azure antes de implementarlo en el entorno de producción.

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

1. [Descargue el script de configuración MSI de Azure Purview](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-MSI-Configuration) en la ubicación que prefiera.

2. En el equipo, escriba **PowerShell** en el cuadro de búsqueda de la barra de tareas de Windows. En la lista de búsqueda, mantenga presionado (o haga clic con el botón derecho) en **Windows PowerShell** y seleccione **Ejecutar como administrador**.

3. Escriba el comando siguiente en la ventana de PowerShell. (Reemplace `<path-to-script>` por la ruta de acceso del archivo de script extraído).

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. Escriba el comando siguiente para instalar los cmdlets de Azure:

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
5. Si ve el mensaje de advertencia *Se necesita el proveedor de NuGet para continuar*, escriba **Y** y presione **Entrar**.

6. Si ve el mensaje de advertencia *Repositorio que no es de confianza*, escriba **A** y presione **Entrar**.

7. Repita los pasos anteriores para instalar los módulos `Az.Synapse` y `AzureAD`.

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

Asegúrese de que el usuario tiene los siguientes roles y permisos:

Como mínimo, necesita los permisos siguientes para ejecutar el script en el entorno de Azure:

Rol | Ámbito | ¿Por qué es necesario? |
|-------|--------|--------|
| **Lector global** | Inquilino de Azure AD | Para leer la pertenencia a grupos de usuarios administradores de Azure SQL y MSI de Azure Purview |
| **Administrador global** | Inquilino de Azure AD | Para asignar el rol **Lector de directorios** a instancias administradas de Azure SQL |
| **Colaborador** | Suscripción o grupo de recursos donde se crea la cuenta de Azure Purview | Para leer el recurso de la cuenta de Azure Purview y crear un recurso y un secreto de Key Vault |
| **Propietario o Administrador de acceso de usuarios** | Grupo de administración o suscripción donde se encuentran los orígenes de datos de Azure | Para asignar RBAC |
| **Colaborador** | Grupo de administración o suscripción donde se encuentran los orígenes de datos de Azure | Para configurar la red |
| **Administrador de SQL** (Autenticación de Azure AD) | Instancias de Azure SQL Server o instancias administradas de Azure SQL | Para asignar el rol **db_datareader** a Azure Purview |
| Acceso al almacén de claves de Azure | Acceso para obtener o enumerar el secreto de Key Vault para la autenticación de Azure SQL Database, Azure SQL Managed Instance o Azure Synapse |  


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
   .\purview-msi-configuration.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   Al ejecutar el comando, es posible que aparezca dos veces una ventana emergente en la que se le pide que inicie sesión en Azure y Azure AD con las credenciales de Azure Active Directory.

El informe puede tardar varios minutos en crearse, en función del número de suscripciones y recursos de Azure en el entorno. 

Es posible que se le pida que inicie sesión en las instancias de Azure SQL Server si las credenciales del almacén de claves no coinciden. Puede proporcionar las credenciales o presionar **Entrar** para omitir el servidor específico. 

Cuando se complete el proceso, puede ver el informe de salida para revisar los cambios. 


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

### <a name="what-configurations-are-included-in-the-script"></a>¿Qué configuraciones se incluyen en el script?

Este script puede ayudarle a completar de forma automática las tareas siguientes:

#### <a name="azure-blob-storage-blobstorage"></a>Azure Blob Storage (BlobStorage)

- RBAC. Asigne el rol **Lector** de Azure RBAC a MSI de Azure Purview en el ámbito seleccionado. Compruebe la asignación. 
- RBAC. Asigne el rol **Lector de datos de Storage Blob** de Azure RBAC a MSI de Azure Purview en cada una de las suscripciones situadas debajo del ámbito seleccionado. Compruebe las asignaciones.
- Redes. Notifique si se crea un punto de conexión privado para el almacenamiento y se habilita para Blob Storage.
- Punto de conexión de servicio. Si el punto de conexión privado está desactivado, compruebe si el punto de conexión de servicio está habilitado y habilite **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento**.

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2 (ADLSGen2)

- RBAC. Asigne el rol **Lector** de Azure RBAC a MSI de Azure Purview en el ámbito seleccionado. Compruebe la asignación. 
- RBAC. Asigne el rol **Lector de datos de Storage Blob** de Azure RBAC a MSI de Azure Purview en cada una de las suscripciones situadas debajo del ámbito seleccionado. Compruebe las asignaciones.
- Redes. Notifique si se crea un punto de conexión privado para el almacenamiento y se habilita para Blob Storage.
- Punto de conexión de servicio. Si el punto de conexión privado está desactivado, compruebe si el punto de conexión de servicio está habilitado y habilite **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento**.

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1 (ADLSGen1)

- Redes. Compruebe que el punto de conexión de servicio está habilitado y habilite **Allow all Azure services to access this Data Lake Storage Gen1 account** (Permitir que todos los servicios de Azure accedan a esta cuenta de Data Lake Storage Gen1) en Data Lake Storage.
- Permisos. Asigne acceso de lectura/ejecución a MSI de Azure Purview. Compruebe el acceso. 

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL Database (AzureSQLDB)

- Instancias de SQL Server:
  - Red. Notifique si el punto de conexión público o el punto de conexión privado están habilitados.
  - Firewall. Si el punto de conexión privado está desactivado, compruebe las reglas de firewall y habilite **Permitir que los servicios y recursos de Azure accedan a este servidor**.
  - Administración de Azure AD. Habilite la autenticación de Azure AD para Azure SQL Database.

- Bases de datos SQL:
  - Rol de SQL. Asigne el rol **db_datareader** a MSI de Azure Purview.

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL Managed Instance (AzureSQLMI)

- Servidores de SQL Managed Instance:
  - Red. Compruebe que el punto de conexión público o el punto de conexión privado están activados. Notifique si el punto de conexión público está desactivado.
  - ProxyOverride. Compruebe que Azure SQL Managed Instance se ha configurado como proxy o redireccionamiento.
  - Redes. Actualice las reglas de NSG para permitir el acceso entrante de AzureCloud a las instancias de SQL Server por medio de los puertos necesarios:  
    - Redireccionamiento: 1433 y 11000-11999
    
    o 
    - Proxy: 3342
    
    Compruebe este acceso. 
  - Administración de Azure AD. Habilite la autenticación de Azure AD para Azure SQL Managed Instance.
  
- Bases de datos SQL:
  - Rol de SQL. Asigne el rol **db_datareader** a MSI de Azure Purview.

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Grupo dedicado de Azure Synapse (Synapse)

- RBAC. Asigne el rol **Lector** de Azure RBAC a MSI de Azure Purview en el ámbito seleccionado. Compruebe la asignación. 
- RBAC. Asigne el rol **Lector de datos de Storage Blob** de Azure RBAC a MSI de Azure Purview en cada una de las suscripciones situadas debajo del ámbito seleccionado. Compruebe las asignaciones.
- Instancias de SQL Server (grupos dedicados):
  - Red. Notifique si el punto de conexión público o el punto de conexión privado están activados.
  - Firewall. Si el punto de conexión privado está desactivado, compruebe las reglas de firewall y habilite **Permitir que los servicios y recursos de Azure accedan a este servidor**.
  - Administración de Azure AD. Habilite la autenticación de Azure AD para Azure SQL Database.

- Bases de datos SQL:
  - Rol de SQL. Asigne el rol **db_datareader** a MSI de Azure Purview.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:
> [!div class="checklist"]
>
> * Identifique el acceso necesario y configure las reglas de autenticación y red necesarias para Azure Purview en los orígenes de datos de Azure.

Vaya al siguiente tutorial para aprender a [registrar y examinar varios orígenes en Azure Purview](register-scan-azure-multiple-sources.md).
