---
title: Registro y examen de una Instancia administrada de Azure SQL Database
description: En este tutorial, se describe cómo examinar la Instancia administrada de Azure SQL Database en Azure Purview.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 05/08/2021
ms.openlocfilehash: c633d95c615298876a8bedd76ecbd9c96a4f54da
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209704"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Registro y examen de una Instancia administrada de Azure SQL Database

En este artículo se describe cómo registrar un origen de datos de la Instancia administrada de Azure SQL Database en Purview y cómo configurar un examen en él.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

El origen de datos de la Instancia administrada de Azure SQL Database admite la funcionalidad siguiente:

- **Exámenes completos e incrementales** para capturar metadatos y su clasificación en la Instancia administrada de Azure SQL Database.

- **Linaje** entre los recursos de datos de las actividades de copia y flujo de datos de ADF.

## <a name="prerequisites"></a>Prerrequisitos

- Cree una cuenta de Purview si aún no tiene una.

- [Configuración de un punto de conexión público en Instancia administrada de Azure SQL](../azure-sql/managed-instance/public-endpoint-configure.md)

    > [!Note]
    > Ahora se admite el examen de instancias administradas de Azure SQL Database que estén configuradas con puntos de conexión privados mediante puntos de conexión privados de ingesta de Azure Purview y una máquina virtual del entorno de ejecución de integración autohospedado. Para más información relacionada con los requisitos previos, consulte [Conexión a Azure Purview y examen de los orígenes de datos de forma privada y segura](./catalog-private-link-end-to-end.md).

### <a name="setting-up-authentication-for-a-scan"></a>Configuración de la autenticación para un examen

Autenticación para examinar una Instancia administrada de Azure SQL Database. Si necesita crear una autenticación, debe [autorizar el acceso a la base de datos a la Instancia administrada de SQL Database](../azure-sql/database/logins-create-manage.md). A día de hoy, Purview admite tres métodos de autenticación:

- Autenticación SQL
- Entidad de servicio
- Identidad administrada

#### <a name="sql-authentication"></a>Autenticación SQL

> [!Note]
> Solo pueden crear inicios de sesión el inicio de sesión de entidad de seguridad a nivel de servidor (creado por el proceso de aprovisionamiento) o los miembros del rol de base de datos `loginmanager` en la base de datos maestra. Unos **15 minutos** después de conceder el permiso, la cuenta de Purview debería tener los permisos adecuados para poder examinar los recursos.

Puede seguir las instrucciones de [CREAR INICIO DE SESIÓN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) para crear un inicio de sesión de Instancia administrada de Azure SQL Database si aún no tiene uno. En los pasos siguientes necesitará un **nombre de usuario** y una **contraseña**.

1. Vaya al almacén de claves en Azure Portal.
1. Seleccione **Configuración > Secretos**.
1. Seleccione **+ Generar/Importar** y escriba el **nombre** y el **valor** como la *contraseña* del inicio de sesión de Instancia administrada de Azure SQL Database.
1. Seleccione **Crear** para completar la acción.
1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una nueva credencial](manage-credentials.md#create-a-new-credential) mediante el **nombre de usuario** y la **contraseña** para configurar el examen.

#### <a name="service-principal-and-managed-identity"></a>Entidad de servicio e identidad administrada

Para permitir que Purview use la entidad de servicio para examinar la Instancia administrada de Azure SQL Database se necesitan varios pasos.

##### <a name="create-or-use-an-existing-service-principal"></a>Creación de una entidad de servicio o uso de una existente

> [!Note]
> Omita este paso si usa la **identidad administrada**.

Puede usar una entidad de servicio existente o crear una. 

> [!Note]
> Si tiene que crear una entidad de servicio, siga estos pasos:
> 1. Acceda a [Azure Portal](https://portal.azure.com).
> 1. Seleccione **Azure Active Directory** en el menú de la izquierda.
> 1. Seleccione **App registrations** (Registros de aplicaciones).
> 1. Seleccione **+ Nuevo registro de aplicaciones**.
> 1. Escriba un nombre para la **aplicación** (el nombre de la entidad de servicio).
> 1. Seleccione **Solo las cuentas de este directorio organizativo**.
> 1. En el URI de redirección, seleccione **Web** y escriba cualquier dirección URL; no tiene que ser real ni del trabajo.
> 1. Después, seleccione **Register** (Registrar).

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Configuración de la autenticación de Azure AD en la cuenta de la base de datos

La entidad de servicio o la identidad administrada debe tener permiso para obtener los metadatos de la base de datos, los esquemas y las tablas. También debe ser capaz de consultar las tablas para realizar el muestreo para la clasificación.
- [Configuración y administración de la autenticación de Azure AD con Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Cree un usuario de Azure AD en Instancia administrada de Azure SQL Database según los requisitos previos y el tutorial sobre la [Creación de usuarios independientes asignados a identidades de Azure AD](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities).
- Asignación del permiso `db_datareader` a la identidad

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Incorporación de la entidad de servicio al almacén de claves y la credencial de Purview

> [!Note]
> Si tiene previsto usar la **identidad administrada**, puede omitir este paso, puesto que la identidad predeterminada de Purview ya está en **Purview-MSI**.

Es necesario obtener el id. de aplicación y el secreto de la entidad de servicio:

1. Vaya a la entidad de servicio en [Azure Portal](https://portal.azure.com).
1. Copie los valores de **Id. de aplicación (cliente)** de **Información general** y **Secreto de cliente** que están en **Certificados y secretos**.
1. Vaya a almacén de claves.
1. Seleccione **Configuración > Secretos**.
1. Seleccione **+ Generar/Importar** y escriba el **nombre** que quiera y el **valor** como **Secreto de cliente** de la entidad de servicio.
1. Seleccione **Crear** para completar la acción.
1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una credencial](manage-credentials.md#create-a-new-credential) mediante la entidad de servicio para configurar el examen.

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Registro de un origen de datos de Instancia administrada de Azure SQL Database

1. Vaya a la cuenta de Purview.

1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.

1. Seleccione **Registrar**.

1. Seleccione **Instancia administrada de Azure SQL Database** y luego **Continue** (Continuar).

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Configuración del origen de datos de SQL.":::

1. Seleccione **From Azure subscription** (Desde la suscripción de Azure) y la suscripción adecuada en el cuadro desplegable **Suscripción de Azure** y el servidor correspondiente en el cuadro desplegable **Nombre del servidor**.

1. Proporcione el **nombre de dominio completo del punto de conexión público** y el **número de puerto**. Después, seleccione **Registrar** para registrar el origen de datos.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Incorporación de Instancia administrada de Azure SQL Database.":::

    Por ejemplo, `foobar.public.123.database.windows.net,3342`

## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de Purview Studio.

1. Seleccione el origen de Instancia administrada de Azure SQL Database que ha registrado.

1. Seleccione **New scan** (Nuevo examen).

1. Seleccione la credencial para conectarse al origen de datos.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-scan-sql-mi.png" alt-text="Configurar examen":::

1. Elija los elementos adecuados de la lista para limitar el ámbito del examen a tablas específicas.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scope-your-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/trigger-scan.png" alt-text="trigger":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

> [!NOTE]
> Al eliminar el examen, no se eliminan los recursos de los exámenes de Instancia administrada de Azure SQL Database anteriores.

## <a name="next-steps"></a>Pasos siguientes

- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
