---
title: Registro y análisis de una base de datos de Azure Database for MySQL
description: En este tutorial se describe cómo examinar una base de datos de Azure Database for MySQL
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: ed3a0abc6bdd99e5c86e67a410d7c9ecadf7a718
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737642"
---
# <a name="register-and-scan-an-azure-mysql-database"></a>Registro y análisis de una base de datos de Azure Database for MySQL

En este artículo se describe cómo registrar y examinar una base de datos de Azure Database for MySQL.


## <a name="supported-capabilities"></a>Funcionalidades admitidas
- **Exámenes completos e incrementales** para capturar metadatos y su clasificación de bases de datos de Azure Database for MySQL.

- **Linaje** entre los recursos de datos de las actividades de copia y flujo de datos de ADF.

### <a name="known-limitations"></a>Restricciones conocidas
Purview solo admite la autenticación SQL para bases de datos de Azure Database for MySQL.


## <a name="prerequisites"></a>Prerrequisitos

1. Cree una cuenta de Purview si aún no tiene una.

2. Acceso de red entre la cuenta de Purview y la base de datos de Azure Database for MySQL.

### <a name="set-up-authentication-for-a-scan"></a>Configuración de la autenticación para un examen

En los pasos siguientes necesitará un **nombre de usuario** y una **contraseña**.

Siga las instrucciones del artículo [Creación de bases de datos y usuarios en Azure Database for MySQL](../mysql/howto-create-users.md) para crear un inicio de sesión para la base de datos de Azure Database for MySQL.

1. Vaya al almacén de claves en Azure Portal.
1. Seleccione **Configuración > Secretos**.
1. Seleccione **+ Generar/Importar** y escriba el **nombre** y el **valor** como la *contraseña* del inicio de sesión de Azure SQL Database.
1. Seleccione **Crear** para completar la acción.
1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una credencial](manage-credentials.md#create-a-new-credential) de la autenticación de tipo SQL y utilice el **nombre de usuario** y la **contraseña** para configurar el examen.

## <a name="register-an-azure-mysql-database-data-source"></a>Registro de un origen de datos de una base de datos de Azure Database for MySQL

Para registrar una nueva base de datos de Azure Database for MySQL en el catálogo de datos, haga lo siguiente:

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

## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de Purview Studio.

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

> [!NOTE]
> * Al eliminar el examen, no se eliminan los recursos creados a partir de los anteriores exámenes de Azure Database for MySQL.
> * El recurso no se volverá a actualizar con los cambios de esquema si ha cambiado la tabla de origen y se vuelve a examinar la tabla de origen después de editar la descripción en la pestaña de esquema de Purview.

## <a name="next-steps"></a>Pasos siguientes

- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
