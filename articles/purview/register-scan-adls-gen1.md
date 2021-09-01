---
title: Registro y examen de Azure Data Lake Storage (ADLS) Gen1
description: En este tutorial se describe cómo examinar los datos de Azure Data Lake Storage Gen1 en Azure Purview.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 1c4801814e77efdb681f32ea35d4dfb68618900b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736369"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Registro y examen de Azure Data Lake Storage Gen1

En este artículo se describe cómo registrar Azure Data Lake Storage Gen1 como un origen de datos en Azure Purview y cómo configurar un examen.

> [!Note]
> Azure Data Lake Storage Gen2 ya está disponible con carácter general. Se recomienda que empiece a usarlo hoy mismo. Para más información, consulte la [página del producto](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

El origen de datos de Azure Data Lake Storage Gen1 admite la funcionalidad siguiente:

- **Exámenes completos e incrementales** para capturar metadatos y clasificaciones de Azure Data Lake Storage Gen1.

- **Linaje** entre los recursos de datos de las actividades de copia y flujo de datos de ADF.

En el caso de los tipos de archivo como csv, tsv, psv y ssv, el esquema se extrae cuando se implementan las siguientes lógicas:

1. Los valores de la primera fila no están vacíos.
2. Los valores de la primera fila son únicos.
3. Los valores de la primera fila no son una fecha ni un número.

## <a name="prerequisites"></a>Requisitos previos

- Antes de registrar los orígenes de datos, cree una cuenta de Azure Purview. Para más información sobre cómo crear una cuenta de Purview, consulte [Inicio rápido: creación de una cuenta de Azure Purview](create-catalog-portal.md).
- Tenga en cuenta que debe ser administrador de los orígenes de datos de Azure Purview.

## <a name="setting-up-authentication-for-a-scan"></a>Configuración de la autenticación para un examen

Se admiten los siguientes métodos de autenticación para Azure Data Lake Storage Gen1:

- Identidad administrada
- Entidad de servicio

### <a name="managed-identity-recommended"></a>Identidad administrada (recomendado)

Para mayor comodidad y seguridad, puede que desee usar la MSI de Purview para autenticarse en el almacén de datos.

Para configurar un examen con la MSI del catálogo de datos, primero debe conceder a la cuenta de Purview el permiso para examinar el origen de datos. Este paso se debe realizar *antes de* configurar el examen o se producirá un error en el examen.

#### <a name="adding-the-purview-msi-to-an-azure-data-lake-storage-gen1-account"></a>Adición de la MSI de Purview a una cuenta de Azure Data Lake Storage Gen1

Puede Agregar la MSI del catálogo en el nivel de suscripción, grupo de recursos o recurso, en función de para qué desea que tenga permisos de examen.

> [!Note]
> Debe ser propietario de la suscripción para poder agregar una identidad administrada a un recurso de Azure.

1. En [Azure Portal](https://portal.azure.com), busque la suscripción, el grupo de recursos o el recurso (por ejemplo, una cuenta de almacenamiento de Azure Data Lake Storage Gen1) que desea permitir que el catálogo examine.

2. Haga clic en **Información general** y, a continuación, seleccione **Explorador de datos**.

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Elegir control de acceso":::

3. Haga clic en **Acceso** en la barra de navegación superior.

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Clic en Acceso":::

4. Haga clic en **Agregar**. Agregue el **Catálogo e Purview** en la selección Seleccionar usuario o grupo. Seleccione los permisos **Lectura** y **Ejecución**. Asegúrese de elegir **Esta carpeta y todos los elementos secundarios** y **Una entrada de permiso de acceso y una entrada de permiso predeterminada** en la opción Agregar a, como se muestra en la siguiente captura de pantalla. Haga clic en **Aceptar**
   :::image type="content" source="./media/register-scan-adls-gen1/gen1-managed-service-identity-authentication.png" alt-text="Detalles de la autenticación de MSI":::
   
> [!Tip]
> Una **entrada de permiso de acceso** es un entrada de permiso sobre los archivos y carpetas *actuales*.
> Una **entrada de permiso predeterminada** es una entrada de permiso que *heredarán* los nuevos archivos y carpetas.
> 
> Para conceder permiso solo a los archivos existentes actualmente, **elija una entrada de permiso de acceso**.
> 
> Para conceder permiso para examinar archivos y carpetas que se agregarán en el futuro, **incluya una entrada de permiso predeterminada**.
> 
> Para más información, consulte la [documentación sobre permisos](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders).

5. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una nueva conexión al almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Por último, [cree una credencial](manage-credentials.md#create-a-new-credential) mediante la entidad de servicio para configurar el examen.

> [!Note]
> Una vez que haya agregado la MSI del catálogo al origen de datos, espere hasta 15 minutos para que los permisos se propaguen antes de configurar un examen.

Después de unos 15 minutos, el catálogo debe tener los permisos adecuados para poder examinar los recursos.

### <a name="service-principal"></a>Entidad de servicio

Para usar una entidad de servicio, primero debe crear una según se indica en estos pasos:

1. Acceda a [Azure Portal](https://portal.azure.com).

2. Seleccione **Azure Active Directory** en el menú de la izquierda.

3. Seleccione **App registrations** (Registros de aplicaciones).

4. Seleccione **+ Nuevo registro de aplicaciones**.

5. Escriba un nombre para la **aplicación** (el nombre de la entidad de servicio).

6. Seleccione **Solo las cuentas de este directorio organizativo**.

7. En **URI de redirección**, seleccione **Web** y escriba cualquier dirección URL; no tiene que ser real ni estar en funcionamiento.

8. Después, seleccione **Register** (Registrar).

9. Copie los valores del nombre para mostrar y el identificador de la aplicación.

#### <a name="adding-the-purview-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Adición de la entidad de servicio de Purview a una cuenta de Azure Data Lake Storage Gen1

1. En [Azure Portal](https://portal.azure.com), busque la suscripción, el grupo de recursos o el recurso (por ejemplo, una cuenta de almacenamiento de Azure Data Lake Storage Gen1) que desea permitir que el catálogo examine.

2. Haga clic en **Información general** y, a continuación, seleccione **Explorador de datos**.

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Elegir control de acceso":::

3. Haga clic en **Acceso** en la barra de navegación superior.

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Clic en Acceso":::

4. Haga clic en **Agregar**. Agregue la **aplicación de la entidad de servicio** en la selección Seleccionar usuario o grupo. Seleccione los permisos **Lectura** y **Ejecución**. Asegúrese de elegir **Esta carpeta y todos los elementos secundarios** y **Una entrada de permiso de acceso y una entrada de permiso predeterminada** en la opción Agregar a, como se muestra en la siguiente captura de pantalla. Haga clic en **Aceptar**
   :::image type="content" source="./media/register-scan-adls-gen1/gen1-service-principal-permissions.png" alt-text="Detalles de autenticación de la entidad de servicio":::

> [!Tip]
> Una **entrada de permiso de acceso** es un entrada de permiso sobre los archivos y carpetas *actuales*.
> Una **entrada de permiso predeterminada** es una entrada de permiso que *heredarán* los nuevos archivos y carpetas.
>
> Para conceder permiso solo a los archivos existentes actualmente, **elija una entrada de permiso de acceso**.
>
> Para conceder permiso para examinar archivos y carpetas que se agregarán en el futuro, **incluya una entrada de permiso predeterminada**.
>
> Para más información, consulte la [documentación sobre permisos](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders).

5. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una nueva conexión al almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Por último, [cree una credencial](manage-credentials.md#create-a-new-credential) mediante la entidad de servicio para configurar el examen.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Registro del origen de datos de Azure Data Lake Storage Gen1

Para registrar una nueva cuenta de ADLS Gen1 en el catálogo de datos, haga lo siguiente:

1. Vaya al catálogo de datos de Purview.
2. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
3. Seleccione **Registrar**.
4. En **Register sources** (Registrar orígenes), seleccione **Azure Data Lake Storage Gen1**. 
5. Seleccione **Continuar**

En la pantalla Register sources (Registrar orígenes) (Azure Data Lake Storage Gen1), haga lo siguiente:

1. Escriba un **nombre** con el que se mostrará el origen de datos en el catálogo.
2. Elija la suscripción para filtrar las cuentas de almacenamiento.
3. Seleccione una cuenta de almacenamiento.
4. Seleccione una colección o cree una nueva (opcional).
5. Seleccione **Registrar** para registrar el origen de datos.

:::image type="content" source="media/register-scan-adls-gen1/register-sources.png" alt-text="Opciones de registro de orígenes" border="true":::

## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de Purview Studio.

1. Seleccione el origen de Azure Data Lake Storage Gen1 que registró.

1. Seleccione **New scan** (Nuevo examen).

1. Seleccione la credencial para conectarse al origen de datos.

   :::image type="content" source="media/register-scan-adls-gen1/set-up-scan-adls-gen1.png" alt-text="Configurar examen":::

1. Elija los elementos adecuados de la lista para limitar el ámbito del examen a carpetas y subcarpetas específicas.

   :::image type="content" source="media/register-scan-adls-gen1/gen1-scope-your-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

   :::image type="content" source="media/register-scan-adls-gen1/select-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

   :::image type="content" source="media/register-scan-adls-gen1/trigger-scan.png" alt-text="trigger":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
