---
title: Registro y examen de Azure Data Explorer
description: En esta guía se describen los detalles de cómo analizar Azure Data Explorer en Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 28ed2e82108cce48ac7ce6988bd9e9833253855a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209950"
---
# <a name="register-and-scan-azure-data-explorer"></a>Registro y examen de Azure Data Explorer

En este artículo se describe cómo registrar una cuenta de Azure Data Explorer en Azure Purview y cómo configurar un examen.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Azure Data Explorer admite exámenes completos e incrementales para capturar los metadatos y el esquema. Los exámenes también clasifican los datos automáticamente en función de las reglas de clasificación personalizadas y del sistema.

## <a name="prerequisites"></a>Requisitos previos

- Antes de registrar los orígenes de datos, cree una cuenta de Azure Purview. Para más información sobre cómo crear una cuenta de Purview, consulte [Inicio rápido: creación de una cuenta de Azure Purview](create-catalog-portal.md).
- Tenga en cuenta que debe ser administrador de los orígenes de datos de Azure Purview.

## <a name="setting-up-authentication-for-a-scan"></a>Configuración de la autenticación para un examen

Solo hay una manera de configurar la autenticación de Azure Data Explorer:

- Entidad de servicio

### <a name="service-principal"></a>Entidad de servicio

Para usar la autenticación de entidad de servicio para realizar exámenes, puede usar una existente o crear una nuevo. 

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

Es necesario obtener el id. de aplicación y el secreto de la entidad de servicio:

1. Vaya a la entidad de servicio en [Azure Portal](https://portal.azure.com).
1. Copie los valores de **Id. de aplicación (cliente)** de **Información general** y **Secreto de cliente** que están en **Certificados y secretos**.
1. Vaya a almacén de claves.
1. Seleccione **Configuración > Secretos**.
1. Seleccione **+ Generar/Importar** y escriba el **nombre** que quiera y el **valor** como **Secreto de cliente** de la entidad de servicio.
1. Seleccione **Crear** para completar la acción.
1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una nueva credencial](manage-credentials.md#create-a-new-credential) mediante la entidad de servicio para configurar el examen.

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Acceso de la entidad de servicio a la instancia de Azure Data Explorer

1. Acceda a Azure Portal. A continuación, vaya a la instancia de Azure Data Explorer.

1. Agregue la entidad de servicio al rol **AllDatabasesViewer** en la pestaña **Permisos**.

## <a name="register-an-azure-data-explorer-account"></a>Registro de una cuenta de Azure Data Explorer

Para registrar una nueva cuenta de Azure Data Explorer (Kusto) en el catálogo de datos, haga lo siguiente:

1. Vaya a la cuenta de Purview.
1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1. Seleccione **Registrar**.
1. En **Registrar orígenes**, seleccione **Azure Data Explorer**.
1. Seleccione **Continuar**

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="registrar un nuevo origen de datos" border="true":::

En la pantalla **Registrar orígenes (Azure Data Explorer [Kusto])** , haga lo siguiente:

1. Escriba un **nombre** con el que se mostrará el origen de datos en el catálogo.
2. Elija la suscripción a Azure para filtrar Azure Data Explorer.
3. Seleccione un clúster adecuado.
4. Seleccione una colección o cree una nueva (opcional).
5. Seleccione **Registrar** para registrar el origen de datos.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="Opciones de registro de orígenes" border="true":::

## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de [Purview Studio](https://web.purview.azure.com/resource/).

1. Seleccione el origen de Azure Data Explorer que ha registrado.

1. Seleccione **New scan** (Nuevo examen).

1. Seleccione la credencial para conectarse al origen de datos. 

   :::image type="content" source="media/register-scan-azure-data-explorer/set-up-scan-data-explorer.png" alt-text="Configurar examen":::

1. Elija los elementos adecuados de la lista para limitar el ámbito del examen a bases de datos específicas.

   :::image type="content" source="media/register-scan-azure-data-explorer/scope-your-scan-data-explorer.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

   :::image type="content" source="media/register-scan-azure-data-explorer/scan-rule-set-data-explorer.png" alt-text="Conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

   :::image type="content" source="media/register-scan-azure-data-explorer/trigger-scan.png" alt-text="trigger":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
