---
title: Migración mediante Azure Data Studio
description: Aprenda a usar la extensión Azure SQL Migration en Azure Data Studio para migrar bases de datos con Azure Database Migration Service.
services: database-migration
author: mokabiru
ms.author: mokabiru
manager: ''
ms.reviewer: cheryl.adams
ms.service: dms
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: e33c1fc4b5daa3ca2092e63413dcedcc42f82509
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123354414"
---
# <a name="migrate-databases-with-azure-sql-migration-extension-for-azure-data-studio-preview"></a>Migración de bases de datos con la extensión Azure SQL Migration para Azure Data Studio (versión preliminar)

La extensión Azure SQL Migration para [Azure Data Studio](/sql/azure-data-studio/what-is-azure-data-studio.md) permite usar la nueva funcionalidad de evaluación y migración de SQL Server en Azure Data Studio.

## <a name="architecture-of-azure-sql-migration-extension-for-azure-data-studio"></a>Arquitectura de la extensión Azure SQL Migration para Azure Data Studio

Azure Database Migration Service (DMS) es uno de los componentes principales de la arquitectura global. DMS proporciona un orquestador de migración confiable para permitir la migración de bases de datos a Azure SQL. Cree o reutilice una instancia de DMS con la extensión Azure SQL Migration en Azure Data Studio(ADS).
DMS utiliza el entorno de ejecución de integración autohospedado de Azure Data Factory para acceder a archivos de copia de seguridad de un recurso compartido de red de su entorno local o su cuenta de Azure Storage y cargarlos.

La siguiente imagen muestra el flujo de trabajo del proceso de migración.

:::image type="content" source="media/migration-using-azure-data-studio/architecture-ads-sql-migration.png" alt-text="Diagrama de la arquitectura para la migración de bases de datos usando Azure Data Studio con DMS":::

1. **Servidor SQL Server de origen:** instancia de SQL Server del entorno local, una nube privada o cualquier máquina virtual de una nube pública. Se admiten todas las ediciones de SQL Server 2008 y versiones posteriores.
1. **Base de datos de Azure SQL de destino:** los destinos admitidos de Azure SQL son Azure SQL Managed Instance o SQL Server en Azure Virtual Machines (registrado en la extensión Agente de IaaS de SQL en [modo de administración completa](../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md#management-modes)).
1. **Recurso compartido de archivos de red:** recurso compartido de archivos de red SMB (Bloque de mensajes del servidor) donde se almacenan los archivos de copia de seguridad de las bases de datos que se van a migrar. También se admiten contenedores de blobs y recursos compartidos de archivos de Azure Storage.
1. **Azure Data Studio:** descargue e instale la [extensión Azure SQL Migration en Azure Data Studio](/sql/azure-data-studio/extensions/azure-sql-migration-extension).
1. **Azure DMS:** servicio de Azure que organiza las canalizaciones de migración para mover los datos del entorno local a Azure. DMS está asociado al entorno de ejecución de integración (IR) autohospedado de Azure Data Factory (ADF) y proporciona la capacidad de registrar y supervisar este entorno.
1. **Entorno de ejecución de integración (IR) autohospedado:** el entorno de ejecución de integración autohospedado debe instalarse en un equipo que pueda conectarse al servidor SQL Server de origen y a la ubicación de los archivos de copia de seguridad. DMS proporciona las claves de autenticación y registra el entorno IR autohospedado.
1. **Carga de los archivos de copia de seguridad en Azure Storage:** DMS utiliza el entorno IR autohospedado para cargar los archivos de copia de seguridad de la ubicación de copia de seguridad del entorno local en la cuenta de Azure Storage aprovisionada. Las actividades y canalizaciones para el movimiento de los datos se crean automáticamente en el flujo de trabajo de migración para cargar los archivos de copia de seguridad.
1. **Restauración de las copias de seguridad en la base de datos de Azure SQL de destino:** DMS restaura los archivos de copia de seguridad de la cuenta de Azure Storage en la base de datos de Azure SQL de destino admitida. 
    > [!IMPORTANT]
    > Con el modo de migración en línea, DMS carga continuamente los archivos de copia de seguridad del origen en Azure Storage y los restaura en el destino hasta que se completa el paso final de la migración total al destino.
    >
    > En el modo de migración sin conexión, DMS carga los archivos de copia de seguridad del origen en Azure Storage y los restaura en el destino sin necesidad de realizar una migración total.

## <a name="prerequisites"></a>Requisitos previos

Los requisitos de Azure Database Migration Service que son comunes en todos los escenarios de migración compatibles incluyen la necesidad de:

* [Descarga e instalación de Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio.md)
* [Instalar la extensión Azure SQL Migration](/sql/azure-data-studio/extensions/azure-sql-migration-extension.md) desde el marketplace de Azure Data Studio
* Tener una cuenta de Azure que esté asignada a uno de los roles integrados que se enumeran a continuación:
    - Colaborador de la instancia de Azure SQL Managed Instance de destino (y una cuenta de almacenamiento para cargar los archivos de copia de seguridad de la base de datos desde el recurso compartido de red SMB).
    - Propietario o colaborador de los grupos de recursos de Azure que contienen la cuenta de Azure SQL Managed Instance o la cuenta de almacenamiento de Azure.
    - Propietario y colaborador de la suscripción de Azure.
* Crear una instancia de [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md) o de [SQL Server en Azure Virtual Machines](../azure-sql/virtual-machines/windows/create-sql-vm-portal.md) de destino.
* Asegurarse de que los inicios de sesión utilizados para conectar el servidor SQL Server de origen sean miembros del rol de servidor *sysadmin* o tengan el permiso `CONTROL SERVER`. 
* Usar una de las siguientes opciones de almacenamiento para los archivos de copia de seguridad de la base de datos completa y del registro de transacciones: 
    - Recurso compartido de red SMB. 
    - Recurso compartido de archivos o contenedor de blobs de la cuenta de almacenamiento de Azure. 

    > [!IMPORTANT]
    > - Si los archivos de copia de seguridad de la base de datos se proporcionan en un recurso compartido de red SMB, [cree una cuenta de almacenamiento de Azure](../storage/common/storage-account-create.md) que permita al servicio DMS cargar los archivos de copia de seguridad de la base de datos.  Asegúrese de crear la cuenta Azure Storage en la misma región en la que se creó la instancia de Azure Database Migration Service.
    > - Azure Database Migration Service no se inicia copias de seguridad y en su lugar usa las copias de seguridad existentes, que puede que ya tenga como parte de su plan de recuperación ante desastres, para la migración.
    > - Debe realizar las [copias de seguridad con la opción `WITH CHECKSUM`](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server). 
    > - Cada copia de seguridad se puede escribir en un archivo de copia de seguridad independiente o en varios archivos de copia de seguridad. Sin embargo, no se admite la anexación de varias copias de seguridad (es decir, el registro completo y de transacciones) en un único medio de copia de seguridad. 
    > - Use copias de seguridad comprimidas para reducir la probabilidad de problemas asociados a la migración de copias de seguridad de gran tamaño.
* Asegurarse de que la cuenta de servicio que ejecuta la instancia de SQL Server de origen tenga permisos de lectura y escritura en el recurso compartido de red SMB que contiene los archivos de copia de seguridad de la base de datos.
* El certificado de la instancia de SQL Server de origen de una base de datos protegida con Cifrado de datos transparente (TDE) debe migrarse a la instancia de destino de Azure SQL Managed Instance o SQL Server en Azure Virtual Machines antes de migrar los datos. Para obtener más información, consulte [Migración de certificados de una base de datos protegida por TDE a Azure SQL Managed Instance](../azure-sql/managed-instance/tde-certificate-migrate.md) y [Mover una base de datos protegida por TDE a otra instancia de SQL Server](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
    > [!TIP]
    > Si la base de datos contiene datos confidenciales protegidos por [Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio), el proceso de migración mediante Azure Data Studio con DMS migrará automáticamente las claves de Always Encrypted a la instancia de destino de Azure SQL Managed Instance o SQL Server en Azure Virtual Machines.

* Si las copias de seguridad de la base de datos están en un recurso compartido de archivos de red, proporcione una máquina para instalar el [entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md) con el fin de acceder a las copias de seguridad de la base de datos y migrarlas. El Asistente para migración proporciona el vínculo y las claves de autenticación para descargar e instalar el entorno de ejecución de integración autohospedado. Como preparación para la migración, asegúrese de que la máquina donde tiene previsto instalar el entorno de ejecución de integración autohospedado tenga habilitados los siguientes nombres de dominio y reglas de firewall de salida:

    | Nombres de dominio                                          | Puertos de salida | Descripción                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | Nube pública: `{datafactory}.{region}.datafactory.azure.net`<br> o bien `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> China: `{datafactory}.{region}.datafactory.azure.cn` | 443            | El entorno de ejecución de integración autohospedado lo necesita para conectarse al servicio Data Migration. <br>Para las nuevas factorías de datos creadas en la nube pública, busque el FQDN en la clave del entorno de ejecución de integración autohospedado, que tiene el formato `{datafactory}.{region}.datafactory.azure.net`. En el caso de factorías de datos antiguas, si no ve el FQDN en la clave del entorno de ejecución de integración autohospedado, use *.frontend.clouddatahub.net. |
    | `download.microsoft.com`    | 443            | Lo necesita el entorno de ejecución de integración autohospedado para descargar las actualizaciones. Si ha deshabilitado la actualización automática, puede omitir la configuración de este dominio. |
    | `*.core.windows.net`          | 443            | Lo utiliza el entorno de ejecución de integración autohospedado que se conecta a la cuenta de almacenamiento de Azure para cargar las copias de seguridad de la base de datos desde el recurso compartido de red. |

    > [!TIP]
    > Si los archivos de copia de seguridad de la base de datos ya se proporcionan en una cuenta de almacenamiento de Azure, no se necesita el entorno de ejecución de integración autohospedado durante el proceso de migración.

* Cuando use el entorno de ejecución de integración autohospedado, asegúrese de que la máquina donde está instalado el entorno de ejecución pueda conectarse a la instancia de SQL Server de origen y al recurso compartido de archivos de red donde se encuentran los archivos de copia de seguridad. El puerto 445 de salida debe estar habilitado para permitir el acceso al recurso compartido de archivos de red.
* Si es la primera vez que usa Azure Database Migration Service, asegúrese de que el proveedor de recursos Microsoft.DataMigration esté registrado en su suscripción. Puede seguir los pasos para [registrar el proveedor de recursos](/quickstart-create-data-migration-service-portal.md#register-the-resource-provider).

### <a name="recommendations-for-using-self-hosted-integration-runtime-for-database-migrations"></a>Recomendaciones para usar el entorno de ejecución de integración autohospedado para migrar bases de datos
- Use un único entorno de ejecución de integración autohospedado para varias bases de datos de SQL Server de origen.
- En cada máquina, instale solo una instancia del entorno de ejecución de integración autohospedado.
- Asocie solo un entorno de ejecución de integración autohospedado a una instancia de DMS.
- El entorno de ejecución de integración autohospedado utiliza recursos (memoria y CPU) de la máquina donde está instalado. Instale el entorno de ejecución de integración autohospedado en una máquina que no sea la de la instancia de SQL Server. Sin embargo, cuanto más cerca estén ambos, menos tiempo necesitará el primero para conectarse al segundo. 
- Use el entorno de ejecución de integración autohospedado solo cuando tenga las copias de seguridad de la base de datos en un recurso compartido de red SMB del entorno local. El entorno de ejecución de integración autohospedado no es necesario para migrar bases de datos si las copias de seguridad de la base de datos de origen ya están en un contenedor de blobs de Azure Storage.
- Se recomienda llevar a cabo un máximo de 10 migraciones de bases de datos simultáneas por entorno de ejecución de integración autohospedado en una sola máquina. Para aumentar el número de migraciones de bases de datos simultáneas, escale horizontalmente el entorno de ejecución autohospedado hasta cuatro nodos o cree otro entorno independiente en una máquina distinta.
- Configure la actualización automática del entorno de ejecución de integración autohospedado para que aplique automáticamente las nuevas características, correcciones de errores y mejoras que se lancen. Si desea obtener más información, consulte [Notificación de expiración y actualización automática del entorno de ejecución de integración autohospedado](../data-factory/self-hosted-integration-runtime-auto-update.md).

## <a name="known-issues-and-limitations"></a>Limitaciones y problemas conocidos
- No se admite la sobrescritura de bases de datos con DMS en la instancia de destino de Azure SQL Managed Instance o SQL Server en Azure Virtual Machines.
- DMS no admite la configuración de alta disponibilidad y recuperación ante desastres en el destino para que coincida con la topología de origen.
- No se admiten los siguientes objetos de servidor:
    - Inicios de sesión
    - trabajos del Agente SQL Server
    - Credenciales
    - Paquetes SSIS
    - Roles del servidor
    - Auditoría de servidor
- No se admite la automatización de las migraciones que se realizan con Azure Data Studio mediante PowerShell o la CLI.
- No se admite la migración a Azure SQL Database.
- Las cuentas de almacenamiento de Azure protegidas por reglas de firewall específicas o configuradas con un punto de conexión privado no se admiten para las migraciones.
- Para las migraciones de base de datos con DMS, no se puede usar un entorno de ejecución de integración autohospedado que se haya creado con Azure Data Factory. Inicialmente, el entorno de ejecución de integración autohospedado debe crearse con la extensión Azure SQL Migration en Azure Data Studio. Después, se puede reutilizar para otras migraciones de bases de datos.
> [!IMPORTANT]
> **Problema conocido al migrar varias bases de datos a SQL Server en Azure Virtual Machines:** la migración simultánea de varias bases de datos a la misma instancia de SQL Server en Azure Virtual Machines produce errores de migración para la mayoría de las bases de datos. Asegúrese de migrar solo una base de datos a una instancia de SQL Server en Azure Virtual Machines en cualquier momento dado.  

### <a name="regions"></a>Regions
Puede migrar bases de datos de SQL Server a una instancia de destino de Azure SQL Managed Instance o SQL Server en Azure Virtual Machines en cualquiera de las siguientes regiones durante la versión preliminar:
- Este de Australia
- Sudeste de Australia
- Centro de Canadá
- Este de Canadá
- Centro de la India
- Centro de EE. UU.
- Este de EE. UU.
- Este de EE. UU. 2
- Centro de Francia
- Japón Oriental
- Centro-Norte de EE. UU
- Centro-sur de EE. UU.
- Sudeste de Asia
- Sur de la India
- Sur de Reino Unido
- Oeste de Europa
- Oeste de EE. UU.
- Oeste de EE. UU. 2

## <a name="pricing"></a>Precios
- El uso de Azure Database Migration Service con la extensión Azure SQL Migration en Azure Data Studio es gratuito. Puede migrar varias bases de datos de SQL Server con Azure Database Migration Service sin cargo alguno por usar el servicio o la extensión Azure SQL Migration.
- No hay ningún costo de movimiento o entrada de datos por migrar las bases de datos del entorno local a Azure. Si la base de datos de origen se mueve desde otra región o una máquina virtual de Azure, puede incurrir en [cargos de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/) según el proveedor de ancho de banda y el escenario de enrutamiento.
- Proporcione su propia máquina o servidor del entorno local para instalar Azure Data Studio.
- Se necesita un entorno de ejecución de integración autohospedado para acceder a las copias de seguridad de la base de datos del recurso compartido de red del entorno local.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general e instalar la extensión Azure SQL Migration, consulte [Extensión Azure SQL Migration para Azure Data Studio](/sql/azure-data-studio/extensions/azure-sql-migration-extension.md).
