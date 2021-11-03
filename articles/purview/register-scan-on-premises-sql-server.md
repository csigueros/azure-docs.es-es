---
title: Conexión y administración de instancias de servidores SQL locales
description: En esta guía se describe cómo conectar instancias de servidores SQL locales en Azure Purview, y cómo usar las características de Purview para examinar y administrar el origen de servidores SQL locales.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: d517f3a54963f08a4607e7f95cb5cffbea2486d9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010999"
---
# <a name="connect-to-and-manage-an-on-premises-sql-server-instance-in-azure-purview"></a>Conexión y administración de una instancia de servidor SQL local en Azure Purview

En este artículo se describe cómo registrar instancias de servidores SQL locales, y cómo autenticarse e interactuar con una instancia de servidor SQL local en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan) | [Sí](#scan) | [Sí](#scan) | [Sí](#scan) | No| [Linaje de Data Factory](how-to-link-azure-data-factory.md) |

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="register"></a>Register

En esta sección se describe cómo registrar una instancia de servidor SQL local en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Autenticación para registro

Solo hay una manera de configurar la autenticación para la instancia local de SQL Server:

- Autenticación de SQL

#### <a name="sql-authentication-to-register"></a>Autenticación de SQL para registrarse

La cuenta de SQL debe tener acceso a la base de datos **maestra**. El motivo es que `sys.databases` está en la base de datos maestra. El examen de Purview debe enumerar `sys.databases` para buscar todas las instancias de SQL en el servidor.

##### <a name="creating-a-new-login-and-user"></a>Creación de un inicio de sesión y un usuario nuevos

Si quiere crear un nuevo inicio de sesión y un usuario para poder examinar el servidor de SQL Server, siga estos pasos:

> [!Note]
> Todos los pasos siguientes se pueden ejecutar con el código proporcionado [aquí](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql).

1. Vaya a SQL Server Management Studio (SSMS), conéctese al servidor, vaya a Seguridad, haga clic con el botón derecho en Inicio de sesión y cree un nuevo inicio de sesión. Asegúrese de seleccionar la autenticación de SQL.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Creación de un nuevo inicio de sesión y un usuario.":::

1. Seleccione Roles de servidor en el panel de navegación izquierdo y asegúrese de que está asignado el rol público.

1. Seleccione sucesivamente Asignación de usuarios en el panel de navegación izquierdo, luego, todas las bases de datos del mapa y, seguidamente, el rol de base de datos **db_datareader**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="asignación de usuarios.":::

1. Seleccione Aceptar para guardar.

1. Vuelva al usuario que creó; para ello, selecciónelo y manténgalo presionado (o haga clic con el botón derecho) y seleccione **Propiedades**. Escriba una contraseña nueva y confírmela. Seleccione "Especificar la contraseña anterior" y escríbala. **Es necesario cambiar la contraseña en cuanto cree un nuevo inicio de sesión.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="cambiar contraseña.":::

##### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Almacenaje de la contraseña de inicio de sesión de SQL en un almacén de claves y creación una credencial en Purview

1. Vaya al almacén de claves en Azure Portal1. Seleccione **Configuración > Secretos**.
1. Seleccione **+ Generate/Import** (+ Generar/Importar) y escriba el **nombre** y el **valor** como la *contraseña* del inicio de sesión de SQL Server.
1. Seleccione **Crear** para completar la acción.
1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una nueva credencial](manage-credentials.md#create-a-new-credential) mediante el **nombre de usuario** y la **contraseña** para configurar el examen.

### <a name="steps-to-register"></a>Pasos para registrarse

1. Vaya a la cuenta de Purview.

1. En el panel de navegación izquierdo de Orígenes y análisis, seleccione **Entornos de ejecución de integración**. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no está configurado, siga los pasos que se mencionan [aquí](manage-integration-runtimes.md) para crear un entorno de ejecución de integración autohospedado para realizar exámenes en una VM o una instancia local de Azure que tenga acceso a la red local.

1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.

1. Seleccione **Registrar**.

1. Seleccione **SQL Server** y, a continuación, **Continuar**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Configure el origen de datos de SQL.":::

1. Proporcione un nombre descriptivo, que será un nombre corto que puede usar para identificar el servidor y el punto de conexión del servidor.

1. A continuación, seleccione **Finalizar** para registrar el origen de datos.

## <a name="scan"></a>Examinar

Siga los pasos que se indican a continuación para examinar las instancias de servidores SQL locales para identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de [Purview Studio](https://web.purview.azure.com/resource/).

1. Seleccione el origen de SQL Server que ha registrado.

1. Seleccione **Nuevo análisis**

1. Seleccione la credencial para conectarse al origen de datos.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-set-up-scan.png" alt-text="Configurar examen":::

1. Elija los elementos adecuados de la lista para limitar el ámbito del examen a tablas específicas.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

   :::image type="content" source="media/register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
