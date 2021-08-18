---
title: Acceso a almacenes de datos y recursos compartidos de archivos con la autenticación de Windows
description: Aprenda a configurar el catálogo de SSIS en Azure SQL Database y Azure-SSIS Integration Runtime en Azure Data Factory para ejecutar paquetes que accedan a los almacenes de datos y los recursos compartidos de archivos con la autenticación de Windows.
ms.date: 10/27/2020
ms.topic: conceptual
ms.service: data-factory
ms.subservice: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: a0777b13eb394ac221bc85679c2ea05925b156c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733004"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Acceso a los almacenes de datos y los recursos compartidos de archivos con la autenticación de Windows desde paquetes SSIS en Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Puede usar la autenticación de Windows para acceder a almacenes de datos, como instancias de SQL Server, recursos compartidos de archivos, Azure Files, etc., desde paquetes SSIS que se ejecuten en su instancia de Azure-SSIS Integration Runtime (IR) en Azure Data Factory (ADF). Los almacenes de datos pueden estar en el entorno local, hospedarse en Azure Virtual Machines (VM) o ejecutarse en Azure como servicios administrados. Si están en el entorno local, deberá unir la instancia de Azure SSIS IR a una red virtual (Microsoft Azure Virtual Network) conectada a la red local; consulte [Unión de una instancia de Azure-SSIS IR a una red Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md). Hay cuatro métodos para acceder a los almacenes de datos con la autenticación de Windows desde los paquetes SSIS que se ejecutan en Azure-SSIS IR:

| Método de conexión | Ámbito efectivo | Paso de configuración | Método de acceso en paquetes | Número de conjuntos de credenciales y recursos conectados | Tipo de recursos conectados | 
|---|---|---|---|---|---|
| Configuración de un contexto de ejecución de nivel de actividad | Por ejecución de actividad de paquete SSIS | Configure la propiedad **Autenticación de Windows** para definir un contexto de "Ejecución/Ejecutar como" cuando se ejecutan paquetes SSIS como actividades de ejecución de paquetes SSIS en canalizaciones de ADF.<br/><br/> Para más información, consulte [Configuración de la actividad de ejecución de paquetes SSIS](./how-to-invoke-ssis-package-ssis-activity.md). | Acceda a los recursos directamente desde los paquetes; por ejemplo, use la ruta UNC para obtener acceso a los recursos compartidos de archivos o a Azure Files: `\\YourFileShareServerName\YourFolderName` o `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName`. | Compatibilidad con solo un conjunto de credenciales para todos los recursos conectados | - Recursos compartidos de archivos en las máquinas virtuales de Azure o locales<br/><br/> - Azure Files, consulte [Montaje de un recurso compartido de archivos de Azure y acceso al recurso compartido en Windows](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -Instancias de SQL Server locales/máquinas virtuales de Azure con autenticación de Windows<br/><br/> - Otros recursos con autenticación de Windows |
| Configuración de un contexto de ejecución a nivel de catálogo | Por instancia de Azure-SSIS IR, pero se invalida cuando se configura un contexto de ejecución de nivel de actividad (véase más arriba) | Ejecute el procedimiento almacenado `catalog.set_execution_credential` de SSISDB para configurar un contexto de "Ejecución/Ejecutar como".<br/><br/> Para obtener más información, vea el resto de este artículo a continuación. | Acceda a los recursos directamente desde los paquetes; por ejemplo, use la ruta UNC para obtener acceso a los recursos compartidos de archivos o a Azure Files: `\\YourFileShareServerName\YourFolderName` o `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName`. | Compatibilidad con solo un conjunto de credenciales para todos los recursos conectados | - Recursos compartidos de archivos en las máquinas virtuales de Azure o locales<br/><br/> - Azure Files, consulte [Montaje de un recurso compartido de archivos de Azure y acceso al recurso compartido en Windows](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -Instancias de SQL Server locales/máquinas virtuales de Azure con autenticación de Windows<br/><br/> - Otros recursos con autenticación de Windows |
| Credenciales persistentes a través del comando `cmdkey` | Por instancia de Azure-SSIS IR, pero se invalida cuando se configura un contexto de ejecución de nivel de actividad o catálogo (véase más arriba) | Ejecute el comando `cmdkey` en un script de configuración personalizado (`main.cmd`) al aprovisionar la instancia de Azure-SSIS IR; por ejemplo, si usa recursos compartidos de archivos, Azure Files o SQL Server:<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey` o<br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> Para obtener más información, consulte [Instalación personalizada del entorno de ejecución para la integración de SSIS en Azure](./how-to-configure-azure-ssis-ir-custom-setup.md). | Acceda a los recursos directamente desde los paquetes; por ejemplo, use la ruta UNC para obtener acceso a los recursos compartidos de archivos o a Azure Files: `\\YourFileShareServerName\YourFolderName` o `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName`. | Compatibilidad con varios conjuntos de credenciales para los distintos recursos conectados | - Recursos compartidos de archivos en las máquinas virtuales de Azure o locales<br/><br/> - Azure Files, consulte [Montaje de un recurso compartido de archivos de Azure y acceso al recurso compartido en Windows](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -Instancias de SQL Server locales/máquinas virtuales de Azure con autenticación de Windows<br/><br/> - Otros recursos con autenticación de Windows |
| Montaje de unidades en tiempo de ejecución del paquete (sin persistencia) | Por paquete | Execute el comando `net use` en la tarea Ejecutar proceso que se agrega al principio del flujo de control en los paquetes, por ejemplo, `net use D: \\YourFileShareServerName\YourFolderName` | Acceso a recursos compartidos de archivos a través de las unidades asignadas | Compatibilidad con varias unidades para diferentes recursos compartidos de archivos | - Recursos compartidos de archivos en las máquinas virtuales de Azure o locales<br/><br/> - Azure Files, consulte [Montaje de un recurso compartido de archivos de Azure y acceso al recurso compartido en Windows](../storage/files/storage-how-to-use-files-windows.md) |
|||||||

> [!WARNING]
> Si no usa ninguno de los métodos anteriores para acceder a almacenes de datos con autenticación de Windows, los paquetes que dependen de la autenticación de Windows no pueden acceder a ellos y se produce un error en tiempo de ejecución. 

En el resto de este artículo se describe cómo configurar el catálogo de SSIS (SSISDB) hospedado en SQL Database o una instancia administrada de SQL para ejecutar paquetes en Azure-SSIS IR que usan la autenticación de Windows para acceder a los almacenes de datos. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Solo se puede usar un conjunto de credenciales

Al usar la autenticación de Windows en un paquete SSIS, solo puede usar un conjunto de credenciales. Las credenciales de dominio que proporcione al seguir los pasos de este artículo se aplican a todas las ejecuciones de paquetes, tanto interactivas como programadas, en la instancia de Azure-SSIS IR hasta que las cambie o las quite. Si el paquete tiene que conectarse a varios almacenes de datos con diferentes conjuntos de credenciales, debería considerar los métodos alternativos anteriores.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Proporcionar credenciales de dominio para la autenticación de Windows

Para proporcionar credenciales de dominio que permitan que los paquetes usen la autenticación de Windows para acceder a los almacenes de datos en el entorno local, realice estos pasos:

1. Con SQL Server Management Studio (SSMS) u otra herramienta, conéctese a SQL Database o a la instancia administrada de SQL que hospedan SSISDB. Para más información, consulte [Conectarse a SSISDB en Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Con SSISDB como base de datos actual, abra una ventana de consulta.

3. Ejecute el siguiente procedimiento almacenado y proporcione las credenciales de dominio adecuadas:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Ejecute los paquetes de SSIS. Los paquetes usan las credenciales que proporcionó para acceder a los almacenes de datos en el entorno local con la autenticación de Windows.

### <a name="view-domain-credentials"></a>Ver las credenciales de dominio

Para ver las credenciales de dominio activas, haga lo siguiente:

1. Con SSMS u otra herramienta, conéctese a SQL Database o a la instancia administrada de SQL que hospedan SSISDB. Para más información, consulte [Conectarse a SSISDB en Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Con SSISDB como base de datos actual, abra una ventana de consulta.

3. Ejecute el siguiente procedimiento almacenado y compruebe el resultado:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Borrar las credenciales de dominio
Para borrar y eliminar las credenciales que proporcionó tal como se describe en este artículo, haga lo siguiente:

1. Con SSMS u otra herramienta, conéctese a SQL Database o a la instancia administrada de SQL que hospedan SSISDB. Para más información, consulte [Conectarse a SSISDB en Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Con SSISDB como base de datos actual, abra una ventana de consulta.

3. Ejecute el siguiente procedimiento almacenado:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Conexión a SQL Server local

Para comprobar que puede conectarse a una instancia local de SQL Server, haga lo siguiente:

1. Para ejecutar esta prueba, busque un equipo que no esté unido a ningún dominio.

2. En el equipo que no esté unido a ningún dominio, ejecute el siguiente comando para iniciar SSMS con las credenciales de dominio que quiera usar:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. En SSMS, compruebe que puede conectarse a SQL Server en el entorno local.

### <a name="prerequisites"></a>Requisitos previos

Para acceder a una instancia local de SQL Server desde paquetes que se ejecutan en Azure, lleve a cabo los pasos siguientes:

1.  En el Administrador de configuración de SQL Server, habilite el protocolo TCP/IP.

2. Permita el acceso a través de Firewall de Windows. Para más información, consulte [Configurar Firewall de Windows para permitir el acceso a SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Una su instancia de Azure-SSIS IR a una red Microsoft Azure Virtual Network que esté conectada a la instancia local de SQL Server.  Para obtener más información, consulte [Unión de una instancia de Azure-SSIS IR a una red Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md).

4. Use el procedimiento almacenado `catalog.set_execution_credential` de SSISDB para proporcionar credenciales, como se describe en este artículo.

## <a name="connect-to-a-file-share-on-premises"></a>Conexión a un recurso compartido de archivos en el entorno local

Para comprobar que puede conectarse a un recurso compartido de archivos local, haga lo siguiente:

1. Para ejecutar esta prueba, busque un equipo que no esté unido a ningún dominio.

2. En el equipo que no está unido a ningún dominio, ejecute los siguientes comandos. Estos comandos abren una ventana del símbolo del sistema con las credenciales de dominio que quiera usar y, seguidamente, obtiene un listado de directorios para probar la conectividad con el recurso compartido de archivos.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Compruebe que se devuelve la lista de directorios del recurso compartido de archivos local.

### <a name="prerequisites"></a>Requisitos previos

Para acceder a un recurso compartido de archivos en el entorno local desde paquetes que se ejecutan en Azure, realice lo siguiente:

1. Permita el acceso a través de Firewall de Windows.

2. Una su instancia de Azure-SSIS IR a una red Microsoft Azure Virtual Network que esté conectada al recurso compartido de archivos del entorno local.  Para obtener más información, consulte [Unión de una instancia de Azure-SSIS IR a una red Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md).

3. Use el procedimiento almacenado `catalog.set_execution_credential` de SSISDB para proporcionar credenciales, como se describe en este artículo.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Conexión a un recurso compartido de archivos en una máquina virtual de Azure

Para acceder a un recurso compartido de archivos en una máquina virtual de Azure desde paquetes que se ejecutan en Azure, realice lo siguiente:

1. Con SSMS u otra herramienta, conéctese a SQL Database o a la instancia administrada de SQL que hospedan SSISDB. Para más información, consulte [Conectarse a SSISDB en Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Con SSISDB como base de datos actual, abra una ventana de consulta.

3. Ejecute el siguiente procedimiento almacenado y proporcione las credenciales de dominio adecuadas:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Conectarse a un recurso compartido de archivos en Azure Files

Para obtener más información acerca de Azure Files, consulte [Azure Files](https://azure.microsoft.com/services/storage/files/).

Para acceder a un recurso compartido de archivos en Azure Files desde paquetes que se ejecutan en Azure, realice lo siguiente:

1. Con SSMS u otra herramienta, conéctese a SQL Database o a la instancia administrada de SQL que hospedan SSISDB. Para más información, consulte [Conectarse a SSISDB en Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Con SSISDB como base de datos actual, abra una ventana de consulta.

3. Ejecute el siguiente procedimiento almacenado y proporcione las credenciales de dominio adecuadas:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Pasos siguientes

- Implemente los paquetes. Para más información, consulte [Implementar un proyecto de SSIS en Azure con SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Ejecute los paquetes. Para más información, consulte [Ejecutar paquetes SSIS en Azure con SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Programe los paquetes. Para más información, vea [Programar paquetes SSIS en Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).
