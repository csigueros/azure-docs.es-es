---
title: Solución de problemas de copia de seguridad de Azure Database for PostgreSQL
description: Información de solución de problemas para la copia de seguridad de Azure Database for PostgreSQL.
ms.topic: troubleshooting
ms.date: 09/22/2021
ms.openlocfilehash: 0dcb5b3a85fd1364d90327648274194d3bc30364
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699491"
---
# <a name="troubleshoot-postgresql-database-backup-by-using-azure-backup-preview"></a>Solución de problemas de copia de seguridad de bases de datos de PostgreSQL con Azure Backup (versión preliminar)

En este artículo, se proporciona información para la solución de problemas de copia de seguridad de bases de datos de Azure Database for PostgreSQL con Azure Backup.

## <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Proporcione al valor MSI del almacén de Backup acceso de tipo **Lectura** en el servidor de PostgreSQL en el que quiera realizar copias de seguridad o restaurar.

Para establecer una conexión segura a la base de datos de PostgreSQL, Azure Backup usa el modelo de autenticación de [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md). Esto significa que el almacén de copia de seguridad solo tendrá acceso a los recursos a los que el usuario haya concedido explícitamente el permiso.

En el momento de la creación del almacén, se le asigna automáticamente un valor MSI del sistema. Debe proporcionar a este valor MSI del almacén acceso a los servidores de PostgreSQL de cuyas bases de datos quiere realizar copias de seguridad.

Pasos:

1. En el servidor de Postgres, vaya al panel **Control de acceso (IAM)** .

    ![Panel Control de acceso](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Seleccione **Agregar asignación de roles**.

    ![Captura de pantalla que muestra cómo agregar una asignación de roles.](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. En el panel de contexto derecho que se abre, escriba lo siguiente:<br>

   - **Rol:** elija el rol **Lector** en la lista desplegable.<br>
   - **Asignar acceso a:** elija la opción **Usuario, grupo o entidad de servicio** en la lista desplegable.<br>
   - **Seleccionar**: escriba el nombre del almacén de Backup en el que desea realizar la copia de seguridad de este servidor y sus bases de datos.<br>

    ![Captura de pantalla que muestra cómo seleccionar un rol.](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

## <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Cree un usuario de copia de seguridad de base de datos que pueda autenticarse con Azure Active Directory:

Este error puede deberse a la ausencia de un administrador de Azure Active Directory para el servidor de PostgreSQL o a la ausencia de un usuario de copia de seguridad que pueda autenticarse mediante Azure Active Directory.

Pasos:

Agregue un administrador de Active Directory al servidor OSS:

Este paso es necesario para conectarse a la base de datos con un usuario que puede autenticarse mediante Azure Active Directory en lugar de usar una contraseña. El usuario administrador de Azure AD en Azure Database for PostgreSQL tendrá el rol **azure_ad_admin**. Solo un rol **azure_ad_admin** puede crear nuevos usuarios de base de datos que se puedan autenticar con Azure AD.

1. Vaya a la pestaña Administrador de Active Directory en el panel de navegación izquierdo de la vista de servidor y agréguese (o agregue a otra persona) como administrador de Active Directory.

    ![Captura de pantalla que muestra cómo establecer el administrador de Active Directory.](./media/backup-azure-database-postgresql/set-admin.png)

1. Asegúrese de **guardar** la configuración de usuario administrador de AD.

    ![Captura de pantalla que muestra cómo guardar la configuración del usuario administrador de Active Directory.](./media/backup-azure-database-postgresql/save-admin-setting.png)

Consulte [este documento](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) para ver la lista de pasos que debe realizar para completar la concesión de permisos.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Habilite la marca **Permitir el acceso a servicios de Azure** para establecer la línea de visión de la red. En la vista de servidor, en el panel **Seguridad de la conexión**, establezca la marca **Permitir el acceso a servicios de Azure** en **Sí**.

>[!Note]
>Antes de habilitar esta marca, asegúrese de establecer la marca **Denegar acceso a la red pública** en **No**.

![Captura de pantalla que muestra cómo permitir el acceso a los servicios de Azure.](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

## <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Permiso para restaurar en un contenedor de la cuenta de almacenamiento al restaurar como archivos

1. Conceda al valor MSI del almacén de Backup permiso para acceder a los contenedores de la cuenta de almacenamiento mediante Azure Portal.
    1. Vaya a **Cuenta de almacenamiento** -> **Control de acceso** -> **Agregar asignación de rol**.
    1. Asigne el rol **Colaborador de datos de Storage Blob** al valor MSI del almacén de Backup.

    ![Captura de pantalla que muestra el proceso para asignar el rol Colaborador de datos de Storage Blob.](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. También puede conceder permisos pormenorizados al contenedor específico en el que va a realizar la restauración mediante el comando [az role assignment create](/cli/azure/role/assignment) de la CLI de Azure.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Reemplace el parámetro assignee por el valor de **Id. de aplicación** de la identidad MSI del almacén y modifique el parámetro scope para que haga referencia al contenedor específico.
    1. Para obtener el valor de **Id. de aplicación** de la identidad MSI del almacén, seleccione **Todas las aplicaciones** en **Tipo de aplicación**:

        ![Captura de pantalla que muestra cómo seleccionar Todas las aplicaciones.](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Busque el nombre del almacén y copie el identificador de la aplicación:

        ![Captura de pantalla que muestra cómo buscar el nombre del almacén.](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Pasos siguientes

[Acerca de la copia de seguridad de Azure Database for PostgreSQL (versión preliminar)](backup-azure-database-postgresql-overview.md)
