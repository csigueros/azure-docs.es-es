---
title: Conexión a Instancia administrada de Azure SQL Database y administración de la misma
description: En esta guía se describe cómo conectarse a Instancia administrada de Azure SQL Database en Azure Purview y usar las características de Purview para examinar y administrar el origen de su Instancia administrada de Azure SQL Database.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 9c86bb1d8eea79c7094aacebb891305a1af66115
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048223"
---
# <a name="connect-to-and-manage-an-azure-sql-database-managed-instance-in-azure-purview"></a>Conexión a una Instancia administrada de Azure SQL Database y administración de la misma en Azure Purview

En este artículo se describe cómo registrar una Instancia administrada de Azure SQL Database, además de cómo autenticarla e interactuar con ella en Azure Purview. Para obtener más información sobre Azure Purview, lea el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan)| [Sí](#scan) | [Sí](#scan) | [Sí](#scan) | No | [Linaje de Data Factory](how-to-link-azure-data-factory.md) |

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener detalles, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* [Configuración de un punto de conexión público en Instancia administrada de Azure SQL](../azure-sql/managed-instance/public-endpoint-configure.md)

    > [!Note]
    > Ahora se admite el examen de instancias administradas de Azure SQL Database que estén configuradas con puntos de conexión privados mediante puntos de conexión privados de ingesta de Azure Purview y una máquina virtual del entorno de ejecución de integración autohospedado.
    > Para más información relacionada con los requisitos previos, consulte [Conexión a Azure Purview y examen de los orígenes de datos de forma privada y segura](./catalog-private-link-end-to-end.md).

## <a name="register"></a>Register

En esta sección se describe cómo registrar una Instancia administrada de Azure SQL Database en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Autenticación de registro

Si necesita crear una autenticación, debe [autorizar el acceso a la base de datos a la Instancia administrada de SQL Database](../azure-sql/database/logins-create-manage.md). A día de hoy, Purview admite tres métodos de autenticación:

- [Identidad administrada](#managed-identity-to-register)
- [Entidad de seguridad de servicio](#service-principal-to-register)
- [Autenticación de SQL](#sql-authentication-to-register)

#### <a name="managed-identity-to-register"></a>Identidad administrada para registrarse

La identidad administrada de Purview es la identidad de la cuenta de Purview que podemos usar para autenticarnos como cualquier otro usuario, grupo o entidad de servicio.

Puede encontrar el identificador de objeto de la identidad administrada en Azure Portal siguiendo estos pasos:

1. Abra Azure Portal y vaya a su cuenta de Purview.
1. Seleccione la pestaña **Propiedades** en el menú de la izquierda.
1. Seleccione el valor del **identificador de objeto de la identidad administrada** y cópielo.

La identidad administrada necesitará permiso para obtener metadatos para la base de datos, los esquemas y las tablas, y para consultar las tablas para su clasificación.
- Cree un usuario de Azure AD en Instancia administrada de Azure SQL Database según los requisitos previos y el tutorial sobre la [Creación de usuarios independientes asignados a identidades de Azure AD](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities).
- Asigne el permiso `db_datareader` a la identidad.

#### <a name="service-principal-to-register"></a>Entidad de servicio para el registro

Para permitir que Purview use la entidad de servicio para examinar la Instancia administrada de Azure SQL Database se necesitan varios pasos.

#### <a name="create-or-use-an-existing-service-principal"></a>Creación de una entidad de servicio o uso de una existente

Puede usar una entidad de servicio existente o crear una. Si va a usar una entidad de servicio existente, vaya al paso siguiente.
Si tiene que crear una nueva entidad de servicio, siga estos pasos:

 1. Acceda a [Azure Portal](https://portal.azure.com).
 1. Seleccione **Azure Active Directory** en el menú de la izquierda.
 1. Seleccione **App registrations** (Registros de aplicaciones).
 1. Seleccione **+ Nuevo registro de aplicaciones**.
 1. Escriba un nombre para la **aplicación** (el nombre de la entidad de servicio).
 1. Seleccione **Solo las cuentas de este directorio organizativo**.
 1. En el URI de redirección, seleccione **Web** y escriba cualquier dirección URL; no tiene que ser real ni del trabajo.
 1. Después, seleccione **Register** (Registrar).

#### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Configuración de la autenticación de Azure AD en la cuenta de la base de datos

La entidad de servicio debe tener permiso para obtener metadatos para la base de datos, los esquemas y las tablas. También debe ser capaz de consultar las tablas para realizar el muestreo para la clasificación.
- [Configuración y administración de la autenticación de Azure AD con Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Cree un usuario de Azure AD en Instancia administrada de Azure SQL Database según los requisitos previos y el tutorial sobre la [Creación de usuarios independientes asignados a identidades de Azure AD](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities).
- Asigne el permiso `db_datareader` a la identidad.

#### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Incorporación de la entidad de servicio al almacén de claves y la credencial de Purview

Es necesario obtener el id. de aplicación y el secreto de la entidad de servicio:

1. Vaya a la entidad de servicio en [Azure Portal](https://portal.azure.com).
1. Copie los valores de **Id. de aplicación (cliente)** de **Información general** y **Secreto de cliente** que están en **Certificados y secretos**.
1. Vaya a almacén de claves.
1. Seleccione **Configuración > Secretos**.
1. Seleccione **+ Generar/Importar** y escriba el **nombre** que quiera y el **valor** como **Secreto de cliente** de la entidad de servicio.
1. Seleccione **Crear** para completar la acción.
1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una credencial](manage-credentials.md#create-a-new-credential) mediante la entidad de servicio para configurar el examen.

#### <a name="sql-authentication-to-register"></a>Autenticación de SQL para registrarse

> [!Note]
> Solo pueden crear inicios de sesión el inicio de sesión de entidad de seguridad a nivel de servidor (creado por el proceso de aprovisionamiento) o los miembros del rol de base de datos `loginmanager` en la base de datos maestra. Unos **15 minutos** después de conceder el permiso, la cuenta de Purview debería tener los permisos adecuados para poder examinar los recursos.

Puede seguir las instrucciones de [CREAR INICIO DE SESIÓN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) para crear un inicio de sesión de Instancia administrada de Azure SQL Database si aún no tiene uno. En los pasos siguientes necesitará un **nombre de usuario** y una **contraseña**.

1. Vaya al almacén de claves en Azure Portal.
1. Seleccione **Configuración > Secretos**.
1. Seleccione **+ Generar/Importar** y escriba el **nombre** y el **valor** como la *contraseña* del inicio de sesión de Instancia administrada de Azure SQL Database.
1. Seleccione **Crear** para completar la acción.
1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una nueva credencial](manage-credentials.md#create-a-new-credential) mediante el **nombre de usuario** y la **contraseña** para configurar el examen.

### <a name="steps-to-register"></a>Pasos para registrarse

1. Vaya a [Purview Studio](https://web.purview.azure.com/resource/).

1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.

1. Seleccione **Registrar**.

1. Seleccione **Instancia administrada de Azure SQL Database** y luego **Continue** (Continuar).

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Configuración del origen de datos de SQL.":::

1. Seleccione **From Azure subscription** (Desde la suscripción de Azure) y la suscripción adecuada en el cuadro desplegable **Suscripción de Azure** y el servidor correspondiente en el cuadro desplegable **Nombre del servidor**.

1. Proporcione el **nombre de dominio completo del punto de conexión público** y el **número de puerto**. Después, seleccione **Registrar** para registrar el origen de datos.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Incorporación de Instancia administrada de Azure SQL Database.":::

    Por ejemplo: `foobar.public.123.database.windows.net,3342`

## <a name="scan"></a>Examinar

Siga estos pasos para examinar una Instancia administrada de Azure SQL Database para identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

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

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
