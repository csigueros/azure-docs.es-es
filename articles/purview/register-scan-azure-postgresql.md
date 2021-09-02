---
title: Registro y examen de una instancia de Azure Database for PostgreSQL
description: En este tutorial se describe cómo examinar una base de datos de Azure Database for PostgreSQL
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: 622e89392dc10f12fe81ed1c1085e5b9cc524999
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181303"
---
# <a name="register-and-scan-an-azure-database-for-postgresql"></a>Registro y examen de una instancia de Azure Database for PostgreSQL

En este artículo se describe cómo registrar y examinar una instancia de Azure Database for PostgreSQL.


## <a name="supported-capabilities"></a>Funcionalidades admitidas
- **Exámenes completos e incrementales** para capturar metadatos y clasificaciones de instancias de Azure Database for PostgreSQL.

- **Linaje** entre los recursos de datos de las actividades de copia y flujo de datos de ADF.

### <a name="known-limitations"></a>Restricciones conocidas

Purview solo admite la autenticación SQL para Azure Database for PostgreSQL.


## <a name="prerequisites"></a>Prerrequisitos

1. Cree una cuenta de Purview si aún no tiene una.

2. Acceso de red entre la cuenta de Purview y Azure Database for PostgreSQL.

#### <a name="sql-authentication-for-an-azure-database-for-postgresql"></a>Autenticación SQL para una instancia de Azure Database for PostgreSQL

La conexión a una base de datos Azure Database for PostgreSQL requiere el nombre completo del servidor y las credenciales de inicio de sesión. Puede seguir las instrucciones de [CONEXIÓN Y CONSULTAS](../postgresql/connect-python.md) para crear un inicio de sesión de su instancia de Azure Database for PostgreSQL si aún no tiene uno. En los pasos siguientes necesitará un **nombre de usuario** y una **contraseña**.

1. Vaya al almacén de claves en Azure Portal.
1. Seleccione **Configuración > Secretos**.
1. Seleccione **+ Generar/Importar** y escriba el **nombre** y el **valor** como la *contraseña* de su instancia de Azure PostgreSQL Database
1. Seleccione **Crear** para completar la acción.
1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una nueva credencial](manage-credentials.md#create-a-new-credential) de la autenticación SQL de tipo mediante el **nombre de usuario** y la **contraseña** para configurar el examen.

## <a name="register-an-azure-database-for-postgresql-data-source"></a>Registro de un origen de datos de Azure Database for PostgreSQL

Para registrar una nueva instancia de Azure Database for PostgreSQL en el catálogo de datos, haga lo siguiente:

1. Vaya a la cuenta de Purview.

1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.

1. Seleccione **Registrar**.

1. En **Registrar orígenes**, seleccione **Azure Database for PostgreSQL**. Seleccione **Continuar**.

:::image type="content" source="media/register-scan-azure-postgresql/01-register-azure-postgres.png" alt-text="Registro de un origen de datos nuevo" border="true":::

En la pantalla **Registrar orígenes [Azure Database for PostgreSQL]** , haga lo siguiente:

1. Escriba un **nombre** para el origen de datos. Este será el nombre para mostrar de este origen de datos en el catálogo.
1. Seleccione **From Azure subscription** (Desde la suscripción de Azure) y la suscripción adecuada en el cuadro desplegable **Suscripción de Azure** y el servidor correspondiente en el cuadro desplegable **Nombre del servidor**.
1. Seleccione **Registrar** para registrar el origen de datos. 
 

:::image type="content" source="media/register-scan-azure-postgresql/02-register-source-azure-postgres.png" alt-text="Opciones de registro de orígenes" border="true":::

## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de Purview Studio.

1. Seleccione el origen de Azure Database for PostgreSQL que registró.

1. Seleccione **New scan** (Nuevo examen).

1. Seleccione la credencial para conectarse al origen de datos.

   :::image type="content" source="media/register-scan-azure-postgresql/03-azure-postgres-scan.png" alt-text="Configurar examen":::

1. Elija los elementos adecuados de la lista para limitar el ámbito del examen a carpetas o subcarpetas específicas.

   :::image type="content" source="media/register-scan-azure-postgresql/04-scope-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

   :::image type="content" source="media/register-scan-azure-postgresql/05-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

   :::image type="content" source="media/register-scan-azure-postgresql/06-trigger-scan.png" alt-text="desencadenar examen":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

> [!NOTE]
> * Al eliminar el examen no se eliminan los recursos del catálogo creados a partir de examenes anteriores.
> * El recurso no se volverá a actualizar con los cambios de esquema si ha cambiado la tabla de origen y se vuelve a examinar la tabla de origen después de editar la descripción en la pestaña de esquema de Purview.

## <a name="next-steps"></a>Pasos siguientes

- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)