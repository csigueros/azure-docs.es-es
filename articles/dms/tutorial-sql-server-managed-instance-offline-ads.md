---
title: 'Tutorial: Migración de SQL Server a Azure SQL Managed Instance sin conexión mediante Azure Data Studio'
titleSuffix: Azure Database Migration Service
description: Migración de SQL Server a Azure SQL Managed Instance sin conexión mediante Azure Data Studio con Azure Database Migration Service (versión preliminar)
services: dms
author: mokabiru
ms.author: mokabiru
manager: ''
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 10/05/2021
ms.openlocfilehash: a94587fc0ccaa2683ee00e2da01efad4cad77184
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546438"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-azure-data-studio-with-dms-preview"></a>Tutorial: Migración de SQL Server a Azure SQL Managed Instance sin conexión mediante Azure Data Studio con DMS (versión preliminar)

Puede usar la extensión de migración de Azure SQL en Azure Data Studio para migrar las bases de datos de una instancia de SQL Server a Azure SQL Managed Instance. Para ver otros métodos que pueden requerir algún trabajo manual, consulte el artículo [Guía de migración: de SQL Server a Azure SQL Managed Instance](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).

En este tutorial, migrará la base de datos **Adventureworks** desde una instancia local de SQL Server a Azure SQL Managed Instance mediante Azure Data Studio con Azure Database Migration Service (DMS). Este tutorial se centra en el modo de migración sin conexión, en el que se tiene en cuenta un tiempo de inactividad aceptable durante el proceso de migración.

En este tutorial, aprenderá a:
> [!div class="checklist"]
>
> * Inicio del asistente para migrar a Azure SQL en Azure Data Studio
> * Ejecución de una evaluación de las bases de datos de origen de SQL Server
> * Especificación de los detalles de la instancia de origen de SQL Server, la ubicación de copia de seguridad y la instancia de Azure SQL Managed Instance de destino
> * Creación de una nueva instancia de Azure Database Migration Service e instalación del entorno de ejecución de integración autohospedado para acceder al servidor de origen y a las copias de seguridad
> * Inicio y supervisión del progreso de la migración hasta su finalización

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

En este artículo se describe una migración en línea desde SQL Server hasta una Instancia administrada de SQL. Para migraciones en línea, consulte [Tutorial: Migración de SQL Server a Azure SQL Managed Instance con conexión mediante Azure Data Studio con DMS (versión preliminar)](tutorial-sql-server-managed-instance-online-ads.md).

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

* [Descarga e instalación de Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Instalar la extensión Azure SQL Migration](/sql/azure-data-studio/extensions/azure-sql-migration-extension) desde el marketplace de Azure Data Studio
* Tener una cuenta de Azure que esté asignada a uno de los roles integrados que se enumeran a continuación:
    - Colaborador de la instancia de Azure SQL Managed Instance de destino (y una cuenta de almacenamiento para cargar los archivos de copia de seguridad de la base de datos desde el recurso compartido de red SMB).
    - Propietario o colaborador de los grupos de recursos de Azure que contienen la cuenta de Azure SQL Managed Instance o la cuenta de almacenamiento de Azure.
    - Propietario y colaborador de la suscripción de Azure.
* Cree una Instancia administrada de SQL mediante los pasos que se describen en el artículo [Creación de una Instancia administrada de SQL en Azure Portal](../azure-sql/managed-instance/instance-create-quickstart.md).
* Asegurarse de que los inicios de sesión utilizados para conectar el servidor SQL Server de origen sean miembros del rol de servidor *sysadmin* o tengan el permiso `CONTROL SERVER`. 
* Especifique un recurso compartido de red SMB, un recurso compartido de archivos de una cuenta de Azure Storage o un contenedor de blobs de una cuenta de Azure Storage que contenga todos los archivos de copia de seguridad de la base de datos completa y los posteriores archivos de copia de seguridad del registro de transacciones, para que Azure Database Migration Service pueda usarlos para la migración de la base de datos.
    > [!IMPORTANT]
    > - Si los archivos de copia de seguridad de la base de datos se proporcionan en un recurso compartido de red SMB, [cree una cuenta de almacenamiento de Azure](../storage/common/storage-account-create.md) que permita al servicio DMS cargar los archivos de copia de seguridad de la base de datos y utilizarlos para la migración de las bases de datos.  Asegúrese de crear la cuenta Azure Storage en la misma región en la que se creó la instancia de Azure Database Migration Service.
    > - Azure Database Migration Service no se inicia copias de seguridad y en su lugar usa las copias de seguridad existentes, que puede que ya tenga como parte de su plan de recuperación ante desastres, para la migración.
    > - Debe realizar las [copias de seguridad con la opción `WITH CHECKSUM`](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017). 
    > - Cada copia de seguridad se puede escribir en un archivo de copia de seguridad independiente o en varios archivos de copia de seguridad. Sin embargo, no se admite la anexación de varias copias de seguridad (es decir, el registro completo y de transacciones) en un único medio de copia de seguridad. 
    > - Puede proporcionar copias de seguridad comprimidas para reducir la probabilidad de experimentar problemas asociados a la migración de copias de seguridad de gran tamaño.
* Asegurarse de que la cuenta de servicio que ejecuta la instancia de SQL Server de origen tenga permisos de lectura y escritura en el recurso compartido de red SMB que contiene los archivos de copia de seguridad de la base de datos.

* Si va a migrar una base de datos protegida mediante Cifrado de datos transparente (TDE), se debe migrar el certificado de la instancia de SQL Server de origen a la instancia de Azure SQL Managed Instance de destino antes de restaurar la base de datos. Para más información, consulte [Migración de certificados de una base de datos protegida por TDE a Azure SQL Managed Instance](../azure-sql/managed-instance/tde-certificate-migrate.md).
    > [!TIP]
    > Si la base de datos contiene datos confidenciales protegidos por [Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio), el proceso de migración mediante Azure Data Studio con DMS migrará automáticamente las claves de Always Encrypted a la instancia de destino de Azure SQL Managed Instance.

* **Si las copias de seguridad de la base de datos están en un recurso compartido de archivos de red**, proporcione una máquina para instalar el [entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md) con el fin de acceder a las copias de seguridad de la base de datos y migrarlas. El asistente para la migración le proporcionará el vínculo de descarga y las claves de autenticación para descargar e instalar el entorno de ejecución de integración autohospedado. Como preparación para la migración, asegúrese de que la máquina en la que va a instalar el entorno de ejecución de integración autohospedado tenga habilitados los siguientes nombres de dominio y reglas de firewall de salida:

    | Nombres de dominio                                          | Puertos de salida | Descripción                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | Nube pública: `{datafactory}.{region}.datafactory.azure.net`<br> o bien `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> China: `{datafactory}.{region}.datafactory.azure.cn` | 443            | El entorno de ejecución de integración autohospedado lo necesita para conectarse al servicio Data Migration. <br>En el caso de las nuevas instancias de Data Factory creadas en la nube pública, busque el nombre de dominio completo a partir de la clave del entorno de ejecución de integración autohospedado, que se encuentra en el formato {factoría_de_datos}.{región}.datafactory.azure.net. En el caso de factorías de datos antiguas, si no ve el FQDN en la clave del entorno de ejecución de integración autohospedado, use *.frontend.clouddatahub.net. |
    | `download.microsoft.com`    | 443            | Lo necesita el entorno de ejecución de integración autohospedado para descargar las actualizaciones. Si ha deshabilitado la actualización automática, puede omitir la configuración de este dominio. |
    | `*.core.windows.net`          | 443            | Lo utiliza el entorno de ejecución de integración autohospedado para conectarse a la cuenta de almacenamiento de Azure para cargar las copias de seguridad de la base de datos desde el recurso compartido de red. |

    > [!TIP]
    > Si los archivos de copia de seguridad de la base de datos ya se proporcionan en una cuenta de almacenamiento de Azure, no se necesita el entorno de ejecución de integración autohospedado durante el proceso de migración.

* Cuando use el entorno de ejecución de integración autohospedado, asegúrese de que la máquina donde está instalado el entorno de ejecución pueda conectarse a la instancia de SQL Server de origen y al recurso compartido de archivos de red donde se encuentran los archivos de copia de seguridad. El puerto 445 de salida debe estar habilitado para permitir el acceso al recurso compartido de archivos de red. Consulte también [Recomendaciones para usar el entorno de ejecución de integración autohospedado para migrar bases de datos](migration-using-azure-data-studio.md#recommendations-for-using-self-hosted-integration-runtime-for-database-migrations).
* Si es la primera vez que usa Azure Database Migration Service, asegúrese de que el proveedor de recursos Microsoft.DataMigration esté registrado en su suscripción. Puede seguir los pasos para [registrar el proveedor de recursos](quickstart-create-data-migration-service-portal.md#register-the-resource-provider).

## <a name="launch-the-migrate-to-azure-sql-wizard-in-azure-data-studio"></a>Inicio del asistente para migrar a Azure SQL en Azure Data Studio

1. Abra Azure Data Studio y seleccione el icono de servidor para conectarse a la instancia de SQL Server local (o SQL Server en una máquina virtual de Azure).
1. En la conexión del servidor, haga clic con el botón derecho y seleccione **Manage** (Administrar).
1. En la página principal del servidor, seleccione **Azure SQL Migration** (Migración de Azure SQL).
1. En el panel Azure SQL Migration (Migración de Azure SQL), seleccione **Migrate to Azure SQL** (Migración a Azure SQL) para iniciar el asistente para la migración.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/launch-migrate-to-azure-sql-wizard.png" alt-text="Inicio del Asistente para la migración a Azure SQL":::
1. En el primer paso del asistente para la migración, vincule la cuenta de Azure si ya ha iniciado sesión en Azure Data Studio o vincule una nueva cuenta de Azure.

## <a name="run-database-assessment-and-select-target"></a>Ejecución de la evaluación de la base de datos y selección del destino

1. Seleccione las bases de datos en las que se va a ejecutar la evaluación y seleccione **Next** (Siguiente).
1. Seleccione Azure SQL Managed Instance como destino.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/assessment-complete-target-selection.png" alt-text="Confirmación de la evaluación":::
1. Seleccione el botón **View/Select** (Ver/Seleccionar) para ver los detalles de los resultados de la evaluación de las bases de datos, seleccione las bases de datos que desea migrar y seleccione **OK** (Aceptar).
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/assessment-issues-details.png" alt-text="Detalles de la evaluación de las bases de datos":::
1. Especifique la **instancia de Azure SQL Managed Instance de destino**; para ello, seleccione la suscripción, la ubicación y el grupo de recursos en las listas desplegables correspondientes, y seleccione **Next** (Siguiente).

## <a name="configure-migration-settings"></a>Configuración de valores de migración

1. Seleccione **Online migration** (Migración en línea) como modo de migración.
    > [!NOTE]
    > En el modo de migración sin conexión, la base de datos de SQL Server de origen no está disponible para la actividad de lectura y escritura mientras se restauran las copias de seguridad de la base de datos en la instancia de Azure SQL Managed Instance de destino. Se debe tener en cuenta un tiempo de inactividad de la aplicación hasta que se complete la migración.
1. Seleccione la ubicación de las copias de seguridad de la base de datos. Las copias de seguridad de la base de datos pueden encontrarse en un recurso compartido de red local o en un contenedor de blobs de Azure Storage.

    > [!NOTE]
    > Si las copias de seguridad de la base de datos se proporcionan en un recurso compartido de red local, DMS requerirá que configure el entorno de ejecución de integración autohospedado en el paso siguiente del asistente. El entorno de ejecución de integración auto-hospedado es necesario para acceder a las copias de seguridad de la base de datos de origen, comprobar la validez del conjunto de copias de seguridad y cargarlas en la cuenta de almacenamiento de Azure.<br/> Si las copias de seguridad de la base de datos ya están en un contenedor de blobs de Azure Storage, no es necesario configurar el entorno de ejecución de integración autohospedado.

1. Después de seleccionar la ubicación de copia de seguridad, proporcione los detalles de la instancia de SQL Server de origen y la ubicación de la copia de seguridad de origen.

    |Campo    |Descripción  |
    |---------|-------------|
    |**Credenciales de origen: nombre de usuario**    |Credencial (Windows / Autenticación de SQL) para conectarse a la instancia de SQL Server de origen y validar los archivos de copia de seguridad.         |
    |**Credenciales de origen: contraseña**    |Credencial (Windows / Autenticación de SQL) para conectarse a la instancia de SQL Server de origen y validar los archivos de copia de seguridad.         |
    |**Ubicación del recurso compartido de red que contiene las copias de seguridad**     |Ubicación del recurso compartido de red que contiene los archivos de copia de seguridad completa y del registro de transacciones. Los archivos no válidos o los archivos de copia de seguridad del recurso compartido de red que no pertenezcan al conjunto de copia de seguridad válido se omitirán automáticamente durante el proceso de migración.        |
    |**Cuenta de usuario de Windows con acceso de lectura a la ubicación del recurso compartido de red**     |Credencial de Windows (nombre de usuario) que tiene acceso de lectura al recurso compartido de red para recuperar los archivos de copia de seguridad.       |
    |**Contraseña**     |Credencial de Windows (contraseña) que tiene acceso de lectura al recurso compartido de red para recuperar los archivos de copia de seguridad.         |
    |**Nombre de la base de datos de destino** |El nombre de la base de datos de destino se puede modificar si desea cambiar el nombre de la base de datos en el destino durante el proceso de migración.            |

1. Especifique la **cuenta de Almacenamiento de Azure**; para ello, seleccione la **suscripción**, la **ubicación** y el **grupo de recursos** en las listas desplegables correspondientes. DMS usará esta cuenta de almacenamiento de Azure para cargar las copias de seguridad de base de datos desde el recurso compartido de red. No es necesario crear un contenedor, ya que DMS creará automáticamente un contenedor de blobs en la cuenta de almacenamiento especificada durante el proceso de carga.
> [!IMPORTANT]
> Si la funcionalidad de comprobación de bucle invertido está habilitada y el recurso compartido de archivos y SQL Server de origen están en el mismo equipo, el origen no podrá tener acceso al recurso compartido de archivos mediante el FQDN. Para corregir este problema, deshabilite la funcionalidad de comprobación de bucle invertido con las instrucciones que se indican [aquí](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

## <a name="create-azure-database-migration-service"></a>Creación de Azure Database Migration Service

1. Cree un nuevo servicio Azure Database Migration Service o reutilice un servicio existente creado anteriormente.
    > [!NOTE]
    > Si anteriormente había creado un servicio DMS mediante Azure Portal, no puede reutilizarlo en el asistente para la migración en Azure Data Studio. Solo se puede reutilizar un servicio DMS creado anteriormente con Azure Data Studio.
1. Seleccione el **grupo de recursos** en el que tiene un servicio DMS existente o cree uno nuevo si es necesario. En la lista desplegable **Azure Database Migration Service**, se mostrarán todas las instancias de DMS del grupo de recursos seleccionado.
1. Para reutilizar una instancia de DMS existente, selecciónela en la lista desplegable y se mostrará el estado del entorno de ejecución de integración autohospedado en la parte inferior de la página.
1. Para crear un nuevo servicio DMS, seleccione **Create new** (Crear nuevo). En la pantalla **Create Azure Database Migration Service** (Crear instancia de Azure Database Migration Service), proporcione el nombre de la instancia de DMS y seleccione **Create** (Crear).
1. Después de crear correctamente la instancia de DMS, se le proporcionarán los detalles para configurar el **entorno de ejecución de integración**.
1. Seleccione **Download and install integration runtime** (Descargar e instalar Integration Runtime) para abrir el vínculo de descarga en un explorador web. Complete la descarga. Instale el entorno de ejecución de integración en una máquina que cumpla los requisitos previos para conectarse a la instancia de SQL Server de origen y a la ubicación que contiene la copia de seguridad de origen.
1. Una vez completada la instalación, se iniciará automáticamente el **administrador de configuración de Microsoft Integration Runtime** para iniciar el proceso de registro.
1. Copie y pegue una de las claves de autenticación proporcionadas en la pantalla del asistente en Azure Data Studio. Si la clave de autenticación es válida, se muestra un icono de comprobación verde en el administrador de configuración de Microsoft Integration Runtime que indica que puede continuar con el **registro**.
1. Después de completar correctamente el registro del entorno de ejecución de integración autohospedado, cierre el **administrador de configuración de Microsoft Integration Runtime** y vuelva al asistente para la migración de Azure Data Studio.
1. Seleccione **Test connection** (Probar conexión) en la pantalla **Create Azure Database Migration Service** (Crear instancia de Azure Database Migration Service) de Azure Data Studio para validar que la instancia de DMS recién creada esté conectada al entorno de ejecución de integración autohospedado recién registrado.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/test-connection-integration-runtime-complete.png" alt-text="Prueba de la conexión del entorno de ejecución de integración":::
1. Revise el resumen y seleccione **Done** (Listo) para iniciar la migración de la base de datos.

## <a name="monitor-your-migration"></a>Supervisión de la migración

1. En **Database Migration Status** (Estado de migración de las bases de datos), puede realizar un seguimiento de las migraciones en curso, las migraciones completadas y las migraciones con errores (si las hubiera).

    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/monitor-migration-dashboard.png" alt-text="Panel de supervisión de migraciones":::
1. Seleccione **Database migrations in progress** (Migraciones de base de datos en curso) para ver las migraciones en curso y obtener más detalles seleccionando el nombre de la base de datos.
1. La página de detalles de la migración muestra los archivos de copia de seguridad y su estado correspondiente:

    | Estado | Descripción |
    |--------|-------------|
    | Recibido | El archivo de copia de seguridad llegó a la ubicación de copia de seguridad de origen y se validó. |
    | Carga | El entorno de ejecución de integración está cargando actualmente el archivo de copia de seguridad en Azure Storage.|
    | Cargado | El archivo de copia de seguridad se ha cargado en Azure Storage. |
    | Restoring | Azure Database Migration Service está restaurando actualmente el archivo de copia de seguridad en Azure SQL Managed Instance.|
    | Restaurada | El archivo de copia de seguridad se ha restaurado correctamente en Azure SQL Managed Instance. |
    | Canceled | Se ha cancelado el proceso de migración. |
    | Omitido | El archivo de copia de seguridad se omitió porque no pertenece a una cadena de copia de seguridad de base de datos válida. |

    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/offline-to-mi-migration-details-inprogress-backups-restored.png" alt-text="Detalles de la restauración de la copia de seguridad sin conexión":::

Una vez restauradas todas las copias de seguridad de base de datos en Azure SQL Managed Instance, Azure DMS iniciará una migración total automática para asegurarse de que la base de datos migrada a Azure SQL Managed Instance esté lista para su uso y el estado de la migración cambia de *En curso* a *Correcto*.

> [!IMPORTANT]
> Después de la migración, la disponibilidad de SQL Managed Instance con el nivel de servicio Crítico para la empresa puede tardar mucho más que para el nivel De uso general, ya que se deben inicializar tres réplicas secundarias para el grupo de alta disponibilidad de AlwaysOn. La duración de esta operación depende del tamaño de los datos. Para más información, consulte [Duración de las operaciones de administración](../azure-sql/managed-instance/management-operations-overview.md#duration).

## <a name="next-steps"></a>Pasos siguientes

* Para consultar un tutorial que muestra cómo migrar una base de datos a SQL Managed Instance mediante el comando T-SQL RESTORE, consulte [Restauración de una copia de seguridad para SQL Managed Instance mediante el comando Restore](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
* Para más información sobre SQL Managed Instance, consulte [¿Qué es SQL Managed Instance?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
* Para más información sobre cómo conectar las aplicaciones a SQL Managed Instance, consulte [Conexión de aplicaciones](../azure-sql/managed-instance/connect-application-instance.md).
