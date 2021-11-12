---
title: Registro y examen de Azure Data Lake Storage (ADLS) Gen1
description: En este artículo se describe el proceso para registrar un origen de datos de Azure Data Lake Storage Gen1 en Azure Purview, incluidas instrucciones para autenticarse e interactuar con el origen de Azure Data Lake Storage Gen1.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 743db90c66f4499b356ca0413ab1827aa73d5f2e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852757"
---
# <a name="connect-to-azure-data-lake-gen1-in-azure-purview"></a>Conexión a Azure Data Lake Gen1 en Azure Purview

En este artículo se describe el proceso para registrar un origen de datos de Azure Data Lake Storage Gen1 en Azure Purview, incluidas instrucciones para autenticarse e interactuar con el origen de Azure Data Lake Storage Gen1.

> [!Note]
> Azure Data Lake Storage Gen2 ya está disponible con carácter general. Se recomienda que empiece a usarlo hoy mismo. Para más información, consulte la [página del producto](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan)|[Sí](#scan) | [Sí](#scan)|[Sí](#scan)| No |Limitado** |

\** Se admite el linaje si el conjunto de datos se usa como origen o receptor en la [actividad de copia de Data Factory](how-to-link-azure-data-factory.md) 

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

## <a name="register"></a>Register

Esta sección le permitirá registrar el origen de datos de ADLS Gen1 y configurar un mecanismo de autenticación adecuado para garantizar un examen correcto del origen de datos.

### <a name="steps-to-register"></a>Pasos para registrarse

Es importante registrar el origen de datos en Azure Purview antes de configurar un examen para el origen de datos.

1. Vaya a [Azure Portal](https://portal.azure.com), vaya a la página **Cuentas de Purview** y haga clic en su _cuenta de Purview_.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="Captura de pantalla que muestra la cuenta de Purview usada para registrar el origen de datos":::

1. Abra **Purview Studio** y vaya hasta **Mapa de datos --> Orígenes**.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="Captura de pantalla que muestra el vínculo para abrir Purview Studio":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sources.png" alt-text="Captura de pantalla que navega al vínculo Orígenes en el Mapa de datos":::

1. Cree la [Jerarquía de colecciones](./quickstart-create-collection.md) mediante el menú **Colecciones** y asigne permisos a las subcolecciones individuales, según sea necesario.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-collection.png" alt-text="Captura de pantalla que muestra el menú de colecciones para crear la jerarquía de colecciones":::

1. Vaya a la colección adecuada en el menú **Orígenes** y haga clic en el icono **Registrar** para registrar un nuevo origen de datos de ADLS Gen1.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-register.png" alt-text="Captura de pantalla que muestra la colección usada para registrar el origen de datos":::

1. Seleccione el origen de datos **Azure Data Lake Storage Gen1** y haga clic en **Continue**.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-source.png" alt-text="Captura de pantalla que permite seleccionar el origen de datos":::

1. Asigne un **Nombre** adecuado al origen de datos, seleccione la **suscripción de Azure** correspondiente, el **nombre de la cuenta de Data Lake Store** existente y la **colección**, y haga clic en **Aplicar**.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-details.png" alt-text="Captura de pantalla que muestra los detalles que debe escribir para registrar el origen de datos":::

1. La cuenta de almacenamiento de ADLS Gen1 aparecerá en la colección seleccionada.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-hierarchy.png" alt-text="Captura de pantalla que muestra el origen de datos asignado a la colección para iniciar el examen":::

## <a name="scan"></a>Examinar

### <a name="prerequisites-for-scan"></a>Requisitos previos para el examen

Para tener acceso para examinar el origen de datos, es necesario configurar un método de autenticación en la cuenta de almacenamiento de ADLS Gen1.
Se admiten las siguientes opciones:

> [!Note]
> Si tiene el firewall habilitado para la cuenta de almacenamiento, debe usar el método de autenticación de Identidad administrada al configurar un examen.

* **Identidad administrada (recomendado)** : En cuanto se crea la cuenta de Azure Purview, se crea automáticamente una **identidad administrada** por el sistema en el inquilino de Azure AD. Según el tipo de recurso, se requieren asignaciones de roles RBAC específicas para que la MSI de Azure Purview realice los exámenes.

* **Entidad de servicio**: En este método, puede crear una nueva o usar una entidad de servicio existente en el inquilino de Azure Active Directory.

### <a name="authentication-for-a-scan"></a>Autenticación para un examen

#### <a name="using-managed-identity-for-scanning"></a>Uso de la identidad administrada para examinar

Es importante conceder a la cuenta de Purview el permiso para examinar el origen de datos de ADLS Gen1. Puede Agregar la MSI del catálogo en el nivel de suscripción, grupo de recursos o recurso, en función de para qué desea que tenga permisos de examen.

> [!Note]
> Debe ser propietario de la suscripción para poder agregar una identidad administrada a un recurso de Azure.

1. En [Azure Portal](https://portal.azure.com), busque la suscripción, el grupo de recursos o el recurso (por ejemplo, una cuenta de almacenamiento de Azure Data Lake Storage Gen1) que desea permitir que el catálogo examine.
1. Haga clic en **Información general** y, a continuación, seleccione **Explorador de datos**.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="Captura de pantalla que muestra la cuenta de almacenamiento":::

1. Haga clic en **Acceso** en la barra de navegación superior.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="Captura de pantalla que muestra el Explorador de datos para la cuenta de almacenamiento":::

1. Haga clic en **Seleccionar** y agregue el _Catálogo de Purview_ en la selección **Seleccionar usuario o grupo**.
1. Seleccione los permisos **Lectura** y **Ejecución**. Asegúrese de elegir **Esta carpeta y todos los elementos secundarios** y **Una entrada de permiso de acceso y una entrada de permiso predeterminada** en las opciones Agregar, como se muestra en la siguiente captura de pantalla. Haga clic en **Aceptar**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-assign-permissions.png" alt-text="Captura de pantalla que muestra los detalles para asignar permisos para la cuenta de Purview":::

> [!Tip]
> Una **entrada de permiso de acceso** es un entrada de permiso sobre los archivos y carpetas _actuales_. Una **entrada de permiso predeterminada** es una entrada de permiso que _heredarán_ los nuevos archivos y carpetas.
Para conceder permiso solo a los archivos existentes actualmente, **elija una entrada de permiso de acceso**.
Para conceder permiso para examinar archivos y carpetas que se agregarán en el futuro, **incluya una entrada de permiso predeterminada**.

#### <a name="using-service-principal-for-scanning"></a>Uso de la entidad de servicio para examinar

##### <a name="creating-a-new-service-principal"></a>Creación de una nueva entidad de servicio

Si tiene que [crear una nueva entidad de servicio](./create-service-principal-azure.md), es necesario registrar una aplicación en el inquilino de Azure AD y proporcionar acceso a la entidad de servicio en los orígenes de datos. El administrador global de Azure AD u otros roles, como el administrador de aplicaciones, pueden realizar esta operación.

##### <a name="getting-the-service-principals-application-id"></a>Obtención del identificador de aplicación de la entidad de servicio

1. Copie el **Id. de aplicación (cliente)** presente en la **Información general** de la [_Entidad de servicio_](./create-service-principal-azure.md) ya creada.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-appl-id.png" alt-text="Captura de pantalla que muestra el identificador de aplicación (cliente) de la entidad de servicio":::

##### <a name="granting-the-service-principal-access-to-your-adls-gen1-account"></a>Concesión de acceso a la entidad de servicio a la cuenta de ADLS Gen1

Es importante conceder a la entidad de servicio el permiso para examinar el origen de datos de ADLS Gen1. Puede Agregar la MSI del catálogo en el nivel de suscripción, grupo de recursos o recurso, en función de para qué desea que tenga permisos de examen.

> [!Note]
> Debe ser propietario de la suscripción para poder agregar una entidad de servicio a un recurso de Azure.

1. Para proporcionar a la entidad de servicio acceso a la cuenta de almacenamiento, abra la cuenta de almacenamiento y haga clic en **Información general** --> **Explorador de datos**.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="Captura de pantalla que muestra la cuenta de almacenamiento":::

1. Haga clic en **Acceso** en la barra de navegación superior.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="Captura de pantalla que muestra el Explorador de datos para la cuenta de almacenamiento":::

1. Haga clic en **Seleccionar** y agregue la _Entidad de servicio_ en la selección **Seleccionar usuario o grupo**.
1. Seleccione los permisos **Lectura** y **Ejecución**. Asegúrese de elegir **Esta carpeta y todos los elementos secundarios** y **Una entrada de permiso de acceso y una entrada de permiso predeterminada** en las opciones Agregar. Haga clic en **Aceptar**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-permissions.png" alt-text="Captura de pantalla que muestra los detalles para asignar permisos para la entidad de servicio":::

### <a name="creating-the-scan"></a>Creación del examen

1. Abra la **cuenta de Purview**, haga clic en **Apertura de Purview Studio**.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="Captura de pantalla que muestra Apertura de Purview Studio":::

1. Vaya a **Mapa de datos** --> **Orígenes** para ver la jerarquía de colecciones.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="Captura de pantalla que muestra la jerarquía de colecciones":::

1. Haga clic en el icono **Nuevo examen** en el **origen de datos de ADLS Gen1** registrado anteriormente.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-new-scan.png" alt-text="Captura de pantalla que muestra el origen de datos con el nuevo icono de examen":::

#### <a name="if-using-managed-identity"></a>Si se utiliza una identidad administrada

Proporcione un **Nombre** para el examen, seleccione **Purview MSI** en **Credencial**, elija la colección adecuada para el examen y haga clic en **Probar conexión**. Tras una conexión correcta, haga clic en **Continuar**.

:::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-managed-identity.png" alt-text="Captura de pantalla que muestra la opción de identidad administrada para ejecutar el examen":::

#### <a name="if-using-service-principal"></a>Si se utiliza una entidad de servicio

1. Proporcione un **Nombre** para el examen, elija la colección adecuada para el examen y haga clic en **+ Nuevo** en **Credencial**.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp.png" alt-text="Captura de pantalla que muestra la opción de entidad de servicio":::

1. Seleccione la **conexión del almacén de claves** adecuada y el **Nombre de secreto** que se usó al crear la _Entidad de servicio_. El **identificador de la entidad de servicio** es el **Id. de aplicación (cliente)** que se copió según se indicó anteriormente.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-key-vault.png" alt-text="Captura de pantalla que muestra la opción de almacén de claves de la entidad de servicio":::

1. Haga clic en **Probar la conexión**. Tras una conexión correcta, haga clic en **Continuar**.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-test-connection.png" alt-text="Captura de pantalla que muestra la prueba de conexión para la entidad de servicio":::

### <a name="scoping-and-running-the-scan"></a>Ámbito y ejecución del examen

1. Para limitar el ámbito del examen a carpetas y subcarpetas específicas, puede elegir los elementos adecuados en la lista.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scope-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Si crea un nuevo _conjunto de reglas de examen_, seleccione los **tipos de archivo** que se incluirán en la regla de examen. 

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-file-types.png" alt-text="Tipos de archivo del conjunto de reglas de examen":::

1. Puede seleccionar las **reglas de clasificación** que se incluirán en la regla de examen.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-classification-rules.png" alt-text="Reglas de clasificación del conjunto de reglas de examen":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-scan-rule-set.png" alt-text="Selección del conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-trigger.png" alt-text="desencadenador del examen":::

    :::image type="content" source="media/register-scan-adls-gen1/register-register-adls-gen1-scan-trigger-selection.png" alt-text="selección del desencadenador del examen":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-review-scan.png" alt-text="revisión del examen":::

### <a name="viewing-scan"></a>Visualización del examen

1. Vaya al _origen de datos_ en la _Colección_ y haga clic en **Ver detalles** para comprobar el estado del examen.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-view-scan.png" alt-text="visualización del examen":::

1. Los detalles del examen indican el progreso del examen en el **Estado de la última ejecución** y el número de recursos _examinados_ y _clasificados_.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-details.png" alt-text="visualización de los detalles del examen":::

1. El **Estado de la última ejecución** se actualizará a **En curso** y, posteriormente, a **Completado** una vez que todo el examen se haya ejecutado correctamente.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-in-progress.png" alt-text="visualización del examen en curso":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-completed.png" alt-text="visualización del examen completado":::

### <a name="managing-scan"></a>Administración del examen

Los exámenes se pueden administrar o ejecutar de nuevo al finalizar.

1. Haga clic en el **Nombre del examen** para administrar el examen.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan.png" alt-text="administración del examen":::

1. Puede volver a _ejecutar el examen_, _editarlo_ o _eliminarlo_.  

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan-options.png" alt-text="administración de las opciones del examen":::

    > [!NOTE]
    > * Al eliminar el examen no se eliminan los recursos del catálogo creados a partir de examenes anteriores.
    > * El recurso no se volverá a actualizar con los cambios de esquema si ha cambiado la tabla de origen y se vuelve a examinar la tabla de origen después de editar la descripción en la pestaña de esquema de Purview.

1. Puede volver a _ejecutar un examen incremental_ o un _examen completo_.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-full-inc-scan.png" alt-text="administración de un examen completo o incremental":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan-results.png" alt-text="administración de los resultados del examen":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
