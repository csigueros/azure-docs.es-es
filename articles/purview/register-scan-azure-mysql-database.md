---
title: Conexión y administración de Azure MySQL Database
description: En esta guía se describe cómo conectarse a Azure MySQL Database en Azure Purview y usar las características de Purview para examinar y administrar el origen de Azure MySQL Database.
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: bdb96c3e1de3062426b87fe702d7694890fca44f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131015055"
---
# <a name="connect-to-and-manage-azure-mysql-databases-in-azure-purview"></a>Conexión y administración de instancias de Azure MySQL Database en Azure Purview

En este artículo se describe cómo registrar una instancia de Azure MySQL Database y cómo autenticarse e interactuar con las instancias de Azure MySQL Database en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan)| [Sí*](#scan) | [Sí](#scan) | [Sí](#scan) | No | [Linaje de Data Factory](how-to-link-azure-data-factory.md) |

\* Purview se basa en metadatos UPDATE_TIME de Azure Database for MySQL para los exámenes incrementales. En algunos casos, es posible que este campo no se conserve en la base de datos y se realice un examen completo. Para más información, vea [la tabla INFORMATION_SCHEMA TABLES](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) para MySQL.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

## <a name="register"></a>Register

En esta sección se describe cómo registrar una instancia de Azure MySQL Database en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Autenticación para registro

En los pasos siguientes necesitará un **nombre de usuario** y una **contraseña**.

Siga las instrucciones del artículo [Creación de bases de datos y usuarios en Azure Database for MySQL](../mysql/howto-create-users.md) para crear un inicio de sesión para la base de datos de Azure Database for MySQL.

1. Vaya al almacén de claves en Azure Portal.
1. Seleccione **Configuración > Secretos**.
1. Seleccione **+ Generar/Importar** y escriba el **nombre** y el **valor** como la *contraseña* del inicio de sesión de Azure SQL Database.
1. Seleccione **Crear** para completar la acción.
1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una nueva credencial](manage-credentials.md#create-a-new-credential) del tipo Autenticación SQL y utilice el **nombre de usuario** y la **contraseña** para configurar el examen.

### <a name="steps-to-register"></a>Pasos para registrarse

Para registrar una nueva instancia de Azure MySQL Database en el catálogo de datos, siga estos pasos:

1. Vaya a la cuenta de Purview.

1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.

1. Seleccione **Registrar**.

1. En **Register sources** (Registrar orígenes), seleccione **Azure Database for MySQL**. Seleccione **Continuar**.

:::image type="content" source="media/register-scan-azure-mysql/01-register-azure-mysql-data-source.png" alt-text="Registro de un origen de datos nuevo" border="true":::

En la pantalla **Register sources (Azure MySQL Database)** (Registrar orígenes [Azure Database for MySQL]), haga lo siguiente:

1. Escriba el **nombre** del origen de datos. Este será el nombre para mostrar de este origen de datos en el catálogo.
1. Seleccione **From Azure subscription** (Desde la suscripción de Azure) y la suscripción adecuada en el cuadro desplegable **Suscripción de Azure** y el servidor correspondiente en el cuadro desplegable **Nombre del servidor**.
1. Seleccione **Registrar** para registrar el origen de datos.

:::image type="content" source="media/register-scan-azure-mysql/02-register-azure-mysql-name-connection.png" alt-text="Opciones de registro de orígenes" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos que se indican a continuación para examinar la instancia de Azure MySQL Database para identificar los recursos y clasificar los datos de manera automática. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de [Purview Studio](https://web.purview.azure.com/resource/).

1. Seleccione el origen de Azure Database for MySQL que registró.

1. Seleccione **New scan** (Nuevo examen).

1. Seleccione la credencial para conectarse al origen de datos y compruebe la conexión para asegurarse de que la credencial está configurada correctamente.

   :::image type="content" source="media/register-scan-azure-mysql/03-new-scan-azure-mysql-connection-credential.png" alt-text="Configurar examen":::

1. Elija los elementos adecuados de la lista para limitar el ámbito del examen a carpetas o subcarpetas específicas.

   :::image type="content" source="media/register-scan-azure-mysql/04-scope-azure-mysql-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

   :::image type="content" source="media/register-scan-azure-mysql/05-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

   :::image type="content" source="media/register-scan-azure-mysql/06-trigger-scan.png" alt-text="trigger":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).-->

:::image type="content" source="media/register-scan-azure-mysql/07-review-your-scan.png" alt-text="Revise el examen" border="true":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
