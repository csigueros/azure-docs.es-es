---
title: Solución de problemas de las conexiones en Azure Purview
description: En este artículo se explican los pasos para solucionar problemas con las conexiones en Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/04/2021
ms.openlocfilehash: 586c6988372d6ae6310367ba6a3c0c2f0fa4d9e0
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090554"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Solución de problemas de las conexiones en Azure Purview

En este artículo se describe cómo solucionar errores de conexión durante la configuración de exámenes en orígenes de datos en Azure Purview.

## <a name="permission-the-credential-on-the-data-source"></a>Permiso de la credencial en el origen de datos

Si usa una identidad administrada o una entidad de servicio como método de autenticación para los exámenes, deberá permitir que estas identidades tengan acceso a su origen de datos.

Existen instrucciones específicas para cada tipo de origen:

- [Varios orígenes de Azure](register-scan-azure-multiple-sources.md#set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group)
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Instancia administrada de Azure SQL Database](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-bucket-scan)

## <a name="verifying-azure-role-based-access-control-to-enumerate-azure-resources-in-azure-purview-studio"></a>Comprobación del control de acceso basado en roles de Azure para enumerar los recursos de Azure en Azure Purview Studio

### <a name="registering-single-azure-data-source"></a>Registro de un único origen de datos de Azure
Para registrar un único origen de datos en Azure Purview, como Azure Blog Storage o Azure SQL Database, se le debe conceder al menos el rol **Lector** en el recurso o heredarlo de un ámbito superior, como el grupo de recursos o la suscripción. Tenga en cuenta que algunos roles RBAC de Azure, como administrador de seguridad, no tienen acceso de lectura para ver los recursos de Azure en el plano de control.  

Compruebe si es así mediante los pasos siguientes:

1. En [Azure Portal](https://portal.azure.com), vaya al recurso que intenta registrar en Azure Purview. Si puede ver el recurso, es probable que ya tenga al menos un rol de lector en el recurso. 
2. Seleccione **Control de acceso (IAM)**  > **Asignaciones de roles**.
3. Busque por nombre o dirección de correo electrónico del usuario que intenta registrar orígenes de datos en Azure Purview.
4. Compruebe si en la lista existen asignaciones de roles, como Lector, o agregue una nueva asignación de roles si es necesario.

### <a name="scanning-multiple-azure-data-sources"></a>Examen de varios orígenes de datos de Azure
1. En [Azure Portal](https://portal.azure.com), vaya a la suscripción o al grupo de recursos.  
2. Seleccione  **Control de acceso (IAM)**   en el menú izquierdo. 
3. Seleccione **+Agregar**. 
4. En el cuadro **Seleccionar entrada**, seleccione el rol **Lector** y escriba el nombre de la cuenta de Azure Purview (que representa el nombre de MSI). 
5. Haga clic en **Guardar** para finalizar la asignación de roles.
6. Repita los pasos anteriores para agregar la identidad del usuario que intenta crear un nuevo examen para varios orígenes de datos en Azure Purview.

## <a name="scanning-data-sources-using-private-link"></a>Examen de orígenes de datos mediante Private Link 
Si el punto de conexión público está restringido en los orígenes de datos, para examinar los orígenes de datos de Azure mediante Private Link, tiene que configurar un entorno de ejecución de integración autohospedado y crear una credencial. 

> [!IMPORTANT]
> Se produciría un error si examina varios orígenes de datos que contienen bases de datos de Azure SQL con _Denegar acceso desde red pública_. Para examinar estos orígenes de datos mediante un punto de conexión privado, use en su lugar la opción para registrar un único origen de datos.

Para obtener más información sobre cómo configurar un entorno de ejecución de integración autohospedado, consulte el apartado sobre los [puntos de conexión privados de ingesta y el examen de orígenes](catalog-private-link.md#ingestion-private-endpoints-and-scanning-sources).

Para más información sobre cómo crear una credencial en Azure Purview, consulte [Credenciales para la autenticación de origen en Azure Purview](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Almacenamiento de la credencial en el almacén de claves y uso del nombre y la versión del secreto correctos

También debe almacenar la credencial en la instancia de Azure Key Vault y usar el nombre y la versión del secreto correctos.

Compruebe si es así mediante los pasos siguientes:

1. Vaya a su instancia de Key Vault.
1. Seleccione **Configuración** > **Secretos**.
1. Seleccione el secreto que va a usar para autenticarse en el origen de datos para realizar exámenes.
1. Seleccione la versión que desea usar y compruebe que la contraseña o la clave de la cuenta sean correctas. Para ello, haga clic en **Mostrar el valor secreto**. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Compruebe los permisos para la identidad administrada de Purview en la instancia de Azure Key Vault

Compruebe que se hayan configurado los permisos correctos para que la identidad administrada de Purview acceda a la instancia de Azure Key Vault.

Para ello, siga estos pasos:

1. En el almacén de claves, vaya a la sección **Directivas de acceso**.

1. Compruebe que la identidad administrada de Purview se muestra en la sección *Directivas de acceso actuales* con los permisos **Obtener** y **Enumerar** como mínimo en Secretos.

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="Imagen que muestra la selección desplegable de las opciones de permiso Obtener y Enumerar":::

Si no ve la identidad administrada de Purview, siga los pasos descritos en [Creación y administración de credenciales para exámenes](manage-credentials.md) para agregarla. 

## <a name="next-steps"></a>Pasos siguientes

- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
