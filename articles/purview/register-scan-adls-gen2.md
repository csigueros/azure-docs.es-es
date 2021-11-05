---
title: Registro y examen de Azure Data Lake Storage (ADLS) Gen2
description: En este artículo se describe el proceso para registrar un origen de datos de Azure Data Lake Storage Gen2 en Azure Purview, incluidas instrucciones para autenticarse e interactuar con el origen de Azure Data Lake Storage Gen2.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 6459dc9cfccac5f17e0fac155121a817a0fad060
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038483"
---
# <a name="connect-to-azure-data-lake-gen2-in-azure-purview"></a>Conexión a Azure Data Lake Gen2 en Azure Purview

En este artículo se describe el proceso para registrar un origen de datos de Azure Data Lake Storage Gen2 en Azure Purview, incluidas instrucciones para autenticarse e interactuar con el origen de Azure Data Lake Storage Gen2.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan)|[Sí](#scan) | [Sí](#scan)|[Sí](#scan)| Sí | [Linaje de Data Factory](how-to-link-azure-data-factory.md) |

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

## <a name="register"></a>Register

Esta sección le permitirá registrar el origen de datos de ADLS Gen2 y configurar un mecanismo de autenticación adecuado para garantizar un examen correcto del origen de datos.

### <a name="steps-to-register"></a>Pasos para registrarse

Es importante registrar el origen de datos en Azure Purview antes de configurar un examen para el origen de datos.

1. Vaya a [Azure Portal](https://portal.azure.com), vaya a la página **Cuentas de Purview** y haga clic en su _cuenta de Purview_.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-purview-acct.png" alt-text="Captura de pantalla que muestra la cuenta de Purview usada para registrar el origen de datos":::

1. Abra **Purview Studio** y vaya hasta **Mapa de datos --> Orígenes**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-open-purview-studio.png" alt-text="Captura de pantalla que muestra el vínculo para abrir Purview Studio":::

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sources.png" alt-text="Captura de pantalla que navega al vínculo Orígenes en el Mapa de datos":::

1. Cree la [Jerarquía de colecciones](./quickstart-create-collection.md) mediante el menú **Colecciones** y asigne permisos a las subcolecciones individuales, según sea necesario.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-collections.png" alt-text="Captura de pantalla que muestra el menú de colecciones para crear la jerarquía de colecciones":::

1. Vaya a la colección adecuada en el menú **Orígenes** y haga clic en el icono **Registrar** para registrar un nuevo origen de datos de ADLS Gen2.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-register-source.png" alt-text="Captura de pantalla que muestra la colección usada para registrar el origen de datos":::

1. Seleccione el origen de datos **Azure Data Lake Storage Gen2** y haga clic en **Continuar**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-select-data-source.png" alt-text="Captura de pantalla que permite seleccionar el origen de datos":::

1. Asigne un **Nombre** adecuado al origen de datos, seleccione la **suscripción de Azure** correspondiente, el **nombre de la cuenta de Data Lake Store** existente y la **colección**, y haga clic en **Aplicar**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-data-source-details.png" alt-text="Captura de pantalla que muestra los detalles que debe escribir para registrar el origen de datos":::

1. La cuenta de almacenamiento de ADLS Gen2 aparecerá en la colección seleccionada.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-data-source-collection.png" alt-text="Captura de pantalla que muestra el origen de datos asignado a la colección para iniciar el examen":::

## <a name="scan"></a>Examinar

### <a name="prerequisites-for-scan"></a>Requisitos previos para el examen

Para tener acceso para examinar el origen de datos, es necesario configurar un método de autenticación en la cuenta de almacenamiento de ADLS Gen2.
Se admiten las siguientes opciones:

> [!Note]
> Si tiene el firewall habilitado para la cuenta de almacenamiento, debe usar el método de autenticación de Identidad administrada al configurar un examen.

* **Identidad administrada (recomendado)** : En cuanto se crea la cuenta de Azure Purview, se crea automáticamente una **identidad administrada** por el sistema en el inquilino de Azure AD. Según el tipo de recurso, se requieren asignaciones de roles RBAC específicas para que la MSI de Azure Purview realice los exámenes.

* **Clave de cuenta**: los secretos se pueden crear dentro de una instancia de Azure Key Vault para almacenar las credenciales con el fin de permitir el acceso de Azure Purview para examinar los orígenes de datos de forma segura mediante los secretos. Un secreto puede ser una clave de cuenta de almacenamiento, una contraseña de inicio de sesión de SQL o una contraseña.

   > [!Note]
   > Si usa esta opción, debe implementar un recurso de _Azure Key Vault_ en la suscripción y asignar el MSI de la _cuenta de Azure Purview_ con el permiso de acceso necesario a los secretos dentro de _Azure Key Vault_.

* **Entidad de servicio**: En este método, puede crear una nueva o usar una entidad de servicio existente en el inquilino de Azure Active Directory.

### <a name="authentication-for-a-scan"></a>Autenticación para un examen

#### <a name="using-managed-identity-for-scanning"></a>Uso de la identidad administrada para examinar

Es importante conceder a la cuenta de Purview el permiso para examinar el origen de datos de ADLS Gen2. Puede Agregar la MSI del catálogo en el nivel de suscripción, grupo de recursos o recurso, en función de para qué desea que tenga permisos de examen.

> [!Note]
> Debe ser propietario de la suscripción para poder agregar una identidad administrada a un recurso de Azure.

1. En [Azure Portal](https://portal.azure.com), busque la suscripción, el grupo de recursos o el recurso (por ejemplo, una cuenta de almacenamiento de Azure Data Lake Storage Gen2) que quiere permitir que examine el catálogo.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-storage-acct.png" alt-text="Captura de pantalla que muestra la cuenta de almacenamiento":::

1. Haga clic en **Control de acceso (IAM)** en el panel de navegación izquierdo y, después, haga clic en **+ Agregar** --> **Agregar asignación de roles**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-control.png" alt-text="Captura de pantalla que muestra el control de acceso para la cuenta de almacenamiento":::

1. Establezca el **Rol** en **Lector de datos de Storage Blob** y escriba el _nombre de la cuenta de Azure Purview_ en el cuadro de entrada **Seleccionar**. A continuación, seleccione **Save** (Guardar) para dar esta asignación de rol a su cuenta de Purview.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-assign-permissions.png" alt-text="Captura de pantalla que muestra los detalles para asignar permisos para la cuenta de Purview":::

> [!Note]
> Para obtener más información, consulte los pasos que se detallan en [Autorización del acceso a blobs y colas con Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md).

> [!NOTE]
> Si tiene el firewall habilitado para la cuenta de almacenamiento, debe usar el método de autenticación de **Identidad administrada** al configurar un examen.

1. Vaya a la cuenta de almacenamiento de ADLS Gen2 en [Azure Portal](https://portal.azure.com).
1. Vaya a **Seguridad y redes > Redes**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-networking.png" alt-text="Captura de pantalla que muestra los detalles para proporcionar acceso al firewall":::

1. Seleccione **Redes seleccionadas** en **Permitir acceso desde**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-network-access.png" alt-text="Captura de pantalla que muestra los detalles para permitir el acceso a redes seleccionadas":::

1. En la sección **Excepciones**, seleccione **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** y haga clic en **Guardar**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-permission-microsoft-services.png" alt-text="Captura de pantalla que muestra las excepciones para permitir que los servicios de Microsoft de confianza accedan a la cuenta de almacenamiento":::.

#### <a name="using-account-key-for-scanning"></a>Uso de la clave de cuenta para el examen

Cuando el método de autenticación seleccionado es **Clave de cuenta**, debe obtener la clave de acceso y almacenarla en el almacén de claves:

1. Vaya a la cuenta de almacenamiento de ADLS Gen2.
1. Seleccione **Seguridad y redes > Claves de acceso**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-keys.png" alt-text="Captura de pantalla que muestra las claves de acceso de la cuenta de almacenamiento":::

1. Copie la *clave* y guárdela por separado para usarla en los pasos siguientes.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-key.png" alt-text="Captura de pantalla que muestra las claves de acceso que se deben copiar":::

1. Vaya a almacén de claves.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-key-vault.png" alt-text="Captura de pantalla que muestra el almacén de claves":::

1. Seleccione **Configuración > Secretos** y haga clic en **+ Generar o importar**
:::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-generate-secret.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para generar un secreto":::.

1. Escriba el **Nombre** y el **Valor** como la *clave* de la cuenta de almacenamiento.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-secret-values.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para especificar los valores del secreto":::

1. Seleccione **Crear** para completar la acción.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-secret.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para crear un secreto":::

1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una nueva credencial](manage-credentials.md#create-a-new-credential) mediante la clave para configurar el examen.

#### <a name="using-service-principal-for-scanning"></a>Uso de la entidad de servicio para examinar

##### <a name="creating-a-new-service-principal"></a>Creación de una nueva entidad de servicio

Si tiene que [crear una nueva entidad de servicio](./create-service-principal-azure.md), es necesario registrar una aplicación en el inquilino de Azure AD y proporcionar acceso a la entidad de servicio en los orígenes de datos. El administrador global de Azure AD u otros roles, como el administrador de aplicaciones, pueden realizar esta operación.

##### <a name="getting-the-service-principals-application-id"></a>Obtención del identificador de aplicación de la entidad de servicio

1. Copie el **Id. de aplicación (cliente)** presente en la **Información general** de la [_Entidad de servicio_](./create-service-principal-azure.md) ya creada.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-appln-id.png" alt-text="Captura de pantalla que muestra el identificador de aplicación (cliente) de la entidad de servicio":::

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>Concesión de acceso a la entidad de servicio a la cuenta de ADLS Gen2

Es importante conceder a la entidad de servicio el permiso para examinar el origen de datos de ADLS Gen1. Puede Agregar la MSI del catálogo en el nivel de suscripción, grupo de recursos o recurso, en función de para qué desea que tenga permisos de examen.

> [!Note]
> Debe ser propietario de la suscripción para poder agregar una entidad de servicio a un recurso de Azure.

1. En [Azure Portal](https://portal.azure.com), busque la suscripción, el grupo de recursos o el recurso (por ejemplo, una cuenta de almacenamiento de Azure Data Lake Storage Gen2) que quiere permitir que examine el catálogo.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-storage-acct.png" alt-text="Captura de pantalla que muestra la cuenta de almacenamiento":::

1. Haga clic en **Control de acceso (IAM)** en el panel de navegación izquierdo y, después, haga clic en **+ Agregar** --> **Agregar asignación de roles**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-control.png" alt-text="Captura de pantalla que muestra el control de acceso para la cuenta de almacenamiento":::

1. Establezca el **Rol** en **Lector de datos de Storage Blob** y especifique la _entidad de servicio_ en el cuadro de entrada **Seleccionar**. A continuación, seleccione **Save** (Guardar) para dar esta asignación de rol a su cuenta de Purview.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-permission.png" alt-text="Captura de pantalla que muestra los detalles para proporcionar permisos de cuenta de almacenamiento a la entidad de servicio":::

### <a name="create-the-scan"></a>Creación del examen

1. Abra la **cuenta de Purview**, haga clic en **Apertura de Purview Studio**.
1. Vaya a **Mapa de datos** --> **Orígenes** para ver la jerarquía de colecciones.
1. Haga clic en el icono **Nuevo examen** en el **origen de datos de ADLS Gen2** registrado anteriormente.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-new-scan.png" alt-text="Captura de pantalla que muestra la pantalla para crear un nuevo examen":::

#### <a name="if-using-managed-identity"></a>Si se utiliza una identidad administrada

1. Proporcione un **Nombre** para el examen, seleccione **Purview MSI** en **Credencial**, elija la colección adecuada para el examen y haga clic en **Probar conexión**. Tras una conexión correcta, haga clic en **Continuar**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-managed-identity.png" alt-text="Captura de pantalla que muestra la opción Identidad administrada para ejecutar el examen":::

#### <a name="if-using-account-key"></a>Si se usa una clave de cuenta

1. Proporcione un **Nombre** para el examen, elija la colección adecuada para el examen y seleccione **Método de autenticación** como _Clave de cuenta_.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-acct-key.png" alt-text="Captura de pantalla que muestra la opción Clave de cuenta para el examen":::

#### <a name="if-using-service-principal"></a>Si se utiliza una entidad de servicio

1. Proporcione un **Nombre** para el examen, elija la colección adecuada para el examen y haga clic en **+ Nuevo** en **Credencial**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-option.png" alt-text="Captura de pantalla que muestra la opción para que la entidad de servicio habilite el examen":::

1. Seleccione la **conexión del almacén de claves** adecuada y el **Nombre de secreto** que se usó al crear la _Entidad de servicio_. El **Id. de la entidad de servicio** es el **Id. de aplicación (cliente)** que se copió anteriormente.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-service-principal-option.png" alt-text="Captura de pantalla que muestra la opción de entidad de servicio":::

1. Haga clic en **Probar la conexión**. Tras una conexión correcta, haga clic en **Continuar**.

### <a name="scope-and-run-the-scan"></a>Ámbito y ejecución del examen

1. Elija los elementos adecuados de la lista para limitar el ámbito del examen a carpetas y subcarpetas específicas.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scope-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Si crea un nuevo _conjunto de reglas de examen_, seleccione los **tipos de archivo** que se incluirán en la regla de examen.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-file-types.png" alt-text="Tipos de archivo del conjunto de reglas de examen":::

1. Puede seleccionar las **reglas de clasificación** que se incluirán en la regla de examen.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-classification rules.png" alt-text="Reglas de clasificación del conjunto de reglas de examen":::

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-select-scan-rule-set.png" alt-text="Selección del conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scan-trigger.png" alt-text="desencadenador del examen":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-review-scan.png" alt-text="revisión del examen":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
