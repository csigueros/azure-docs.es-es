---
title: Copia de seguridad de una aplicación
description: Obtenga información sobre cómo crear copias de seguridad de sus aplicaciones en Azure App Service. Ejecute copias de seguridad manuales o programadas. Personalice las copias de seguridad mediante la inclusión de la base de datos asociada.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 09/02/2021
ms.custom: seodec18
ms.openlocfilehash: f1bd37c1b3557a8106981377f9ed75c50a28c773
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433060"
---
# <a name="back-up-your-app-in-azure"></a>Realizar una copia de seguridad de la aplicación en Azure

La característica Copia de seguridad y restauración de [Azure App Service](overview.md) le permite crear fácilmente las copias de seguridad de la aplicación manualmente o con base en una programación. Puede configurar las copias de seguridad de modo que se conserven durante un período de tiempo indefinido. Puede restaurar la aplicación a una instantánea de un estado anterior sobrescribiendo la aplicación existente o restaurando en otra aplicación.

Para obtener información sobre cómo restaurar una aplicación desde la copia de seguridad, vea [Restauración de una aplicación en el Servicio de aplicaciones de Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>¿Qué se incluye en la copia de seguridad?

App Service puede hacer una copia de seguridad de la siguiente información en una cuenta de almacenamiento de Azure y el contenedor que ha configurado para que lo utilice la aplicación.

* Configuración de la aplicación
* Contenido del archivo
* Base de datos conectada a la aplicación

Las siguientes soluciones de base de datos son compatibles con la característica de copia de seguridad:

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database para PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL en aplicación](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)

> [!NOTE]
> Cada copia de seguridad es una copia completa sin conexión de su aplicación, no una actualización incremental.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Requisitos y restricciones

* La característica Copia de seguridad y restauración requiere que el plan de App Service tenga el nivel **Estándar**, **Prémium** o **Aislado**. Para obtener más información sobre cómo escalar el plan de App Service para usar un nivel superior, vea [Escalación de una aplicación web en Azure App Service](manage-scale-up.md). Los niveles **Premium** y **Aislado** permiten realizar un mayor número de copias de seguridad diarias que el nivel **Estándar**.
* Necesita una cuenta de almacenamiento de Azure y un contenedor en la misma suscripción que la aplicación de la que quiere realizar una copia de seguridad. Para más información sobre las cuentas de almacenamiento de Azure, consulte [Azure storage account overview](../storage/common/storage-account-overview.md) (Introducción a la cuenta de almacenamiento de Azure).
* Las copias de seguridad pueden tener hasta 10 GB de contenido de la aplicación y la base de datos, de los que hasta 4 GB pueden ser la copia de seguridad de la base de datos. Si el tamaño de la copia de seguridad supera este límite, obtendrá un error.
* No se admiten las copias de seguridad de [Azure Database for MySQL habilitado para TLS](../mysql/concepts-ssl-connection-security.md). Si se configura una copia de seguridad, se producirán errores de copia de seguridad.
* No se admiten las copias de seguridad de [Azure Database for PostgreSQL habilitado para TLS](../postgresql/concepts-ssl-connection-security.md). Si se configura una copia de seguridad, se producirán errores de copia de seguridad.
* Se hace una copia de datos automáticamente de las bases de datos MySQL en la aplicación sin ninguna configuración. Si realiza manualmente la configuración para las bases de datos MySQL en la aplicación, como agregar cadenas de conexión, es posible que las copias de seguridad no funcionen correctamente.
* No se admite el uso de una [cuenta de almacenamiento habilitada para firewall](../storage/common/storage-network-security.md) como destino para las copias de seguridad. Si se configura una copia de seguridad, se producirán errores de copia de seguridad.
* No se admite el uso de una [cuenta de almacenamiento habilitada para punto de conexión privado](../storage/common/storage-private-endpoints.md) para las tareas de copia de seguridad y restauración.

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Crear una copia de seguridad manual

1. En [Azure Portal](https://portal.azure.com), vaya a la página de la aplicación y seleccione **Copias de seguridad**. Se mostrará la página **Copias de seguridad**.

    ![Página Copias de seguridad](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Si ve el mensaje siguiente, haga clic en él para actualizar su plan de App Service antes de continuar con las copias de seguridad.
    > Vea [Escalado vertical de aplicaciones en Azure](manage-scale-up.md) para obtener más información.
    > :::image type="content" source="./media/manage-backup/upgrade-plan.png" alt-text="Captura de pantalla de un banner con un mensaje para actualizar el plan de App Service para tener acceso a la característica Copia de seguridad y restauración.":::
    >
    >

2. En la página **Copia de seguridad**, seleccione **Backup is not configured. Click here to configure backup for your app** (La copia de seguridad no está configurada. Haga clic aquí para configurarla para su aplicación).

    ![Clic en Configurar](./media/manage-backup/configure-start.png)

3. En la página **Configuración de copia de seguridad**, haga clic en **Almacenamiento no configurado** para configurar una cuenta de almacenamiento.

    :::image type="content" source="./media/manage-backup/configure-storage.png" alt-text="Captura de pantalla de la sección Almacenamiento de copia de seguridad con la opción Almacenamiento no configurado seleccionada.":::

4. Elija el destino de copia de seguridad; para ello, seleccione una **Cuenta de almacenamiento** y un **Contenedor**. La cuenta de almacenamiento debe pertenecer a la misma suscripción que la aplicación de la que quiere realizar una copia de seguridad. Si lo desea, puede crear una nueva cuenta de almacenamiento o un nuevo contenedor en las páginas correspondientes. Cuando haya terminado, haga clic en **Seleccionar**.

5. En la página **Configuración de copia de seguridad** que sigue abierta, puede configurar **Copia de seguridad de la base de datos**, seleccionar las bases de datos que desee incluir en las copias de seguridad (SQL Database o MySQL) y después haga clic en **Aceptar**.

    :::image type="content" source="./media/manage-backup/configure-database.png" alt-text="Captura de pantalla de la sección Copia de seguridad de la base de datos que muestra la opción Incluir en copia de seguridad seleccionada.":::

    > [!NOTE]
    > Para que una base de datos aparezca en esta lista, su cadena de conexión debe existir en la sección **Cadenas de conexión** de la página **Configuración de la aplicación** de la aplicación. 
    >
    > Se hace una copia de datos automáticamente de las bases de datos MySQL en la aplicación sin ninguna configuración. Si configura manualmente las bases de datos MySQL en la aplicación, como agregar cadenas de conexión, es posible que las copias de seguridad no funcionen correctamente.
    >
    >

6. En la página **Configuración de copia de seguridad**, haga clic en **Guardar**.
7. En la página **Copias de seguridad**, haga clic en **Copia de seguridad**.

    ![Botón Backup Now](./media/manage-backup/manual-backup.png)

    Verá un mensaje de progreso durante el proceso de realización de la copia de seguridad.

Una vez configurados tanto la cuenta de almacenamiento como el contenedor, puede iniciar una copia de seguridad manual en cualquier momento. Las copias de seguridad manuales se conservan indefinidamente.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configuración de copias de seguridad automatizadas

1. En la página **Configuración de copia de seguridad**, establezca **Copia de seguridad programada** en **Activada**.

    ![Activación de las copias de seguridad automatizadas](./media/manage-backup/scheduled-backup.png)

2. Configure la programación de la copia de seguridad como desee y seleccione **Aceptar**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Configuración de copias de seguridad parciales

En ocasiones, no querrá realizar una copia de seguridad de todo el contenido de la aplicación. Estos son algunos ejemplos:

* [Configure copias de seguridad semanales](#configure-automated-backups) de la aplicación que contiene contenido estático que nunca cambia, como entradas de blog antiguas o imágenes.
* Su aplicación tiene más de 10 GB de contenido (que es la cantidad máxima de la que puede realizar una copia de seguridad a la vez).
* No desea realizar copias de seguridad de los archivos de registro.

Las copias de seguridad parciales permiten elegir exactamente de qué archivos desea realizar la copia de seguridad.

> [!NOTE]
> Las bases de datos individuales de la copia de seguridad pueden tener un tamaño máximo de 4 GB, pero el tamaño máximo total de la copia de seguridad es de 10 GB.

### <a name="exclude-files-from-your-backup"></a>Exclusión de los archivos de la copia de seguridad

Suponga que tiene una aplicación que contiene archivos de registro e imágenes estáticas de los que se ha hecho una copia de seguridad una vez y nunca van a cambiar. En tales casos puede excluir las carpetas y los archivos para que no se almacenen en las futuras copias de seguridad. Para excluir archivos y carpetas de las copias de seguridad, cree un archivo `_backup.filter` en la carpeta `D:\home\site\wwwroot` de la aplicación. Especifique la lista de archivos y carpetas que desea excluir en este archivo.

Para tener acceso a los archivos, vaya a `https://<app-name>.scm.azurewebsites.net/DebugConsole`. Si se le pide, inicie sesión en su cuenta de Azure.

Identifique las carpetas que desee excluir de las copias de seguridad. Por ejemplo, desea filtrar los archivos y la carpeta resaltados.

![Carpeta de imágenes](./media/manage-backup/kudu-images.png)

Cree un archivo llamado `_backup.filter` y ponga la lista anterior en el archivo, pero quite `D:\home`. Enumere un directorio o archivo por línea. Por lo tanto, el contenido del archivo debe ser:

```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Cargue el archivo `_backup.filter` en el directorio `D:\home\site\wwwroot\` de su sitio mediante [ftp](deploy-ftp.md) o cualquier otro método. Si lo desea, puede crear el archivo directamente mediante `DebugConsole` de Kudu e insertar el contenido.

Ejecute copias de seguridad de la misma forma que lo haría normalmente, [manual](#create-a-manual-backup) o [automáticamente](#configure-automated-backups). Ahora, todos archivos y las carpetas que se especifican en `_backup.filter` se excluirán de las copias de seguridad futuras programadas o iniciadas manualmente.

> [!NOTE]
> La restauración de las copias de seguridad parciales del sitio se realizan de la misma manera que se hace para [restaurar una copia de seguridad regular](web-sites-restore.md). El proceso de restauración hace lo correcto.
>
> Cuando se restaura una copia de seguridad completa, se reemplaza todo el contenido en el sitio por lo que haya en la copia de seguridad. Si un archivo está en el sitio pero no en la copia de seguridad, se elimina. Sin embargo, cuando se restaura una copia de seguridad parcial, todo contenido que se encuentre en uno de los directorios restringidos, o todo archivo restringido, permanecerá tal y como está.
>

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Cómo se almacenan las copias de seguridad

Después de realizar una o varias copias de seguridad de la aplicación, dichas copias de seguridad estarán visibles en la página **Contenedores** de la cuenta de almacenamiento, así como en la aplicación. En la cuenta de almacenamiento, cada copia de seguridad consta de un archivo `.zip` que contiene los datos de copia de seguridad y un archivo `.xml` que contiene un manifiesto del contenido del archivo `.zip`. Puede descomprimir y examinar estos archivos si quiere disponer de acceso a las copias de seguridad sin tener que realizar una restauración de la aplicación.

La copia de seguridad de la base de datos para la aplicación se almacena en la raíz del archivo .zip. En SQL Database, este es un archivo BACPAC (sin extensión de archivo) y se puede importar. Para crear una base de datos en Azure SQL Database basada en la exportación del BACPAC, consulte [Importación de un archivo BACPAC para crear una base de datos de Azure SQL Database](../azure-sql/database/database-import.md).

> [!WARNING]
> La modificación de los archivos del contenedor **websitebackups** puede ocasionar que la base de datos deje de ser válida y, por lo tanto, no se pueda restaurar.

## <a name="troubleshooting"></a>Solución de problemas

La página **Backups** (Copias de seguridad) muestra el estado de cada copia de seguridad. Si hace clic en una copia de seguridad con errores, puede obtener los detalles del registro relacionados con el error. Use la siguiente tabla para obtener ayuda solucionar los problemas que pueden aparecer en la copia de seguridad. Si el error no está documentado en la tabla, abra una incidencia de soporte técnico.

| Error | Fix |
| - | - |
| Error al acceder a Storage. | Elimine la programación de las copias de seguridad y vuelva a configurarla. O bien, vuelva a configurar el almacenamiento de copia de seguridad. |
| El tamaño de la unión de sitio web y base de datos supera el límite de {0} GB para las copias de seguridad. El tamaño del contenido es {1} GB. | [Excluya algunos archivos](#configure-partial-backups) de la copia de seguridad o quite la parte de base de datos de la copia de seguridad y use copias de seguridad ofrecidas externamente en su lugar. |
| Error occurred while connecting to the database {0} on server {1}: Authentication to host '{1}' for user '\<username>' using method 'mysql_native_password' failed with message: Unknown database '\<db-name>' (Error al conectarse a la base de datos {0} en el servidor {1}: Error de autenticación para hospedar "{1}" para el usuario "\<username>" mediante el método "mysql_native_password" con el mensaje: Base de datos desconocida "\<db-name>") | Actualice la cadena de conexión de base de datos. |
| Cannot resolve {0}. {1} (CannotResolveStorageAccount) [No puede resolver. (CannotResolveStorageAccount)] | Elimine la programación de las copias de seguridad y vuelva a configurarla. |
| Error de inicio de sesión del usuario "{0}". | Actualice la cadena de conexión de base de datos. |
| Create Database copy of {0} ({1}) threw an exception. Could not create Database copy (La creación de la copia de la base de datos de generó una excepción. No se pudo crear la copia de la base de datos). | Utilice un usuario administrativo en la cadena de conexión. |
| La entidad de seguridad "\<name>" no puede acceder a la base de datos "maestra" en el contexto de seguridad actual. No se puede abrir la base de datos maestra solicitada por el inicio de sesión. Error de inicio de sesión. Login failed for user '\<name>'. (Error de inicio de sesión del usuario "\<name>") | Utilice un usuario administrativo en la cadena de conexión. |
| Error relacionado con la red o específico de la instancia mientras se establecía una conexión con el servidor SQL Server. No se encontró el servidor o no era accesible. Compruebe que el nombre de la instancia es correcto y que SQL Server está configurado para admitir conexiones remotas. (proveedor: Proveedor de canalizaciones con nombre; error: 40 - No se pudo abrir una conexión a SQL Server). | Compruebe que la cadena de conexión es válida. Permita las [direcciones IP de salida](overview-inbound-outbound-ips.md) de la aplicación en la configuración del servidor de bases de datos. |
| No se puede abrir el servidor "\<name>" solicitado por el inicio de sesión. Error de inicio de sesión. | Compruebe que la cadena de conexión es válida. |
| Missing mandatory parameters for valid Shared Access Signature (Faltan parámetros obligatorios de Firma de acceso compartido). | Elimine la programación de las copias de seguridad y vuelva a configurarla. |
| SSL connection is required. Please specify SSL options and retry. when trying to connect (Se requiere una conexión SSL al intentar conectar. Especifique las opciones de SSL y reinténtelo). | En su lugar, use la característica de copia de seguridad integrada en Azure MySQL o Azure Postgressql. |

## <a name="automate-with-scripts"></a>Automatizar con scripts

Puede automatizar la administración de copias de seguridad con scripts, mediante la [CLI de Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/).

Para obtener ejemplos, vea:

- [Ejemplos de la CLI de Azure](samples-cli.md)
- [Ejemplos de Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo restaurar una aplicación desde una copia de seguridad, vea [Restauración de una aplicación en el Servicio de aplicaciones de Azure](web-sites-restore.md).
