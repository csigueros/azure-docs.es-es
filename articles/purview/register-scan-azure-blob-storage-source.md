---
title: Registro y examen de Azure Blob Storage
description: En este artículo se describe el proceso para registrar un origen de datos de Azure Blob Storage en Azure Purview, incluidas instrucciones para autenticarse e interactuar con el origen de Azure Blob Storage Gen2.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 9f6f13805a1b93f415bd7398299ed5e4b2f27947
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131062830"
---
# <a name="connect-to-azure-blob-storage-in-azure-purview"></a>Conexión a Azure Blob Storage en Azure Purview

En este artículo se describe el proceso para registrar una cuenta de Azure Blob Storage en Azure Purview, incluidas instrucciones para autenticarse e interactuar con el origen de Azure Blob Storage.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan)|[Sí](#scan) | [Sí](#scan)|[Sí](#scan)| Sí | No|

En el caso de los tipos de archivo como csv, tsv, psv y ssv, el esquema se extrae cuando se implementan las siguientes lógicas:

* Los valores de la primera fila no están vacíos.
* Los valores de la primera fila son únicos.
* Los valores de la primera fila no son una fecha ni un número

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

## <a name="register"></a>Register

Esta sección le permitirá registrar la cuenta de Azure Blob Storage y configurar un mecanismo de autenticación adecuado para garantizar un examen correcto del origen de datos.

### <a name="steps-to-register"></a>Pasos para registrarse

Es importante registrar el origen de datos en Azure Purview antes de configurar un examen para el origen de datos.

1. Vaya a [Azure Portal](https://portal.azure.com), vaya a la página **Cuentas de Purview** y seleccione su _cuenta de Purview_.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-purview-acct.png" alt-text="Captura de pantalla que muestra la cuenta de Purview usada para registrar el origen de datos":::

1. Abra **Purview Studio** y vaya hasta **Mapa de datos --> Orígenes**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-open-purview-studio.png" alt-text="Captura de pantalla que muestra el vínculo para abrir Purview Studio":::

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-sources.png" alt-text="Captura de pantalla que navega al vínculo Orígenes en el Mapa de datos":::

1. Cree la [Jerarquía de colecciones](./quickstart-create-collection.md) mediante el menú **Colecciones** y asigne permisos a las subcolecciones individuales, según sea necesario.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-collections.png" alt-text="Captura de pantalla que muestra el menú de colecciones para crear la jerarquía de colecciones":::

1. Vaya a la colección adecuada en el menú **Orígenes** y seleccione el icono **Registrar** para registrar un nuevo origen de datos de Azure Blob.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-register-source.png" alt-text="Captura de pantalla que muestra la colección usada para registrar el origen de datos":::

1. Seleccione el origen de datos de **Azure Blob Storage** en la lista y, a continuación, **Continuar**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-select-data-source.png" alt-text="Captura de pantalla que permite seleccionar el origen de datos":::

1. Asigne un **Nombre** adecuado al origen de datos, seleccione la **suscripción de Azure** correspondiente, el **nombre de la cuenta de Azure Blob Storage** existente y la **colección**, y seleccione **Aplicar**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-data-source-details.png" alt-text="Captura de pantalla que muestra los detalles que debe escribir para registrar el origen de datos":::

1. La cuenta de Azure Blob Storage aparecerá en la colección seleccionada.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-data-source-collection.png" alt-text="Captura de pantalla que muestra el origen de datos asignado a la colección para iniciar el examen":::

## <a name="scan"></a>Examinar

### <a name="authentication-for-a-scan"></a>Autenticación para un examen

Para tener acceso para examinar el origen de datos, es necesario configurar un método de autenticación en la cuenta de Azure Blob Storage.

Se admiten las siguientes opciones:

> [!Note]
> Si tiene el firewall habilitado para la cuenta de almacenamiento, debe usar el método de autenticación de Identidad administrada al configurar un examen.

- **Identidad administrada (recomendado)** : En cuanto se crea la cuenta de Azure Purview, se crea automáticamente una **identidad administrada** por el sistema en el inquilino de Azure AD. Según el tipo de recurso, se requieren asignaciones de roles RBAC específicas para que la MSI de Azure Purview realice los exámenes.

- **Clave de cuenta**: los secretos se pueden crear dentro de una instancia de Azure Key Vault para almacenar las credenciales con el fin de permitir el acceso de Azure Purview para examinar los orígenes de datos de forma segura mediante los secretos. Un secreto puede ser una clave de cuenta de almacenamiento, una contraseña de inicio de sesión SQL o una contraseña.

   > [!Note]
   > Si usa esta opción, debe implementar un recurso de _Azure Key Vault_ en la suscripción y asignar el MSI de la _cuenta de Azure Purview_ con el permiso de acceso necesario a los secretos dentro de _Azure Key Vault_.

- **Entidad de servicio**: En este método, puede crear una nueva o usar una entidad de servicio existente en el inquilino de Azure Active Directory.

#### <a name="using-managed-identity-for-scanning"></a>Uso de la identidad administrada para examinar

Es importante conceder a la cuenta de Purview el permiso para examinar el origen de datos de Azure Blob. Puede Agregar la MSI del catálogo en el nivel de suscripción, grupo de recursos o recurso, en función de para qué desea que tenga permisos de examen.

> [!NOTE]
> Si tiene el firewall habilitado para la cuenta de almacenamiento, debe usar el método de autenticación de **Identidad administrada** al configurar un examen.

> [!Note]
> Debe ser propietario de la suscripción para poder agregar una identidad administrada a un recurso de Azure.

1. En [Azure Portal](https://portal.azure.com), busque la suscripción, el grupo de recursos o el recurso (por ejemplo, una cuenta de almacenamiento de Azure Blob) que desea permitir que el catálogo examine.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-storage-acct.png" alt-text="Captura de pantalla que muestra la cuenta de almacenamiento":::

1. Seleccione **Control de acceso (IAM)** en el panel de navegación izquierdo y, después, seleccione **+ Agregar** --> **Adición de la asignación de roles**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-access-control.png" alt-text="Captura de pantalla que muestra el control de acceso para la cuenta de almacenamiento":::

1. Establezca el **Rol** en el **Lector de datos de blobs de almacenamiento** y escriba el _nombre de la cuenta de Azure Purview_ en el cuadro de entrada **Seleccionar**. A continuación, seleccione **Save** (Guardar) para dar esta asignación de rol a su cuenta de Purview.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-assign-permissions.png" alt-text="Captura de pantalla que muestra los detalles para asignar permisos para la cuenta de Purview":::

1. Vaya a la cuenta de almacenamiento de Azure Blob en [Azure Portal](https://portal.azure.com).
1. Vaya a **Seguridad y redes > Redes**

1. Seleccione **Redes seleccionadas** en **Permitir acceso desde**.

1. En la sección **Excepciones**, seleccione **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** y haga clic en **Guardar**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-permission.png" alt-text="Captura de pantalla que muestra las excepciones para permitir que los servicios de Microsoft de confianza accedan a la cuenta de almacenamiento":::.

> [!Note]
> Para obtener más información, consulte los pasos que se detallan en [Autorización del acceso a blobs y colas con Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md).

#### <a name="using-account-key-for-scanning"></a>Uso de la clave de cuenta para el examen

Cuando el método de autenticación seleccionado es **Clave de cuenta**, debe obtener la clave de acceso y almacenarla en el almacén de claves:

1. Vaya a la cuenta de Azure Blob Storage.
1. Seleccione **Seguridad y redes > Claves de acceso**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-access-keys.png" alt-text="Captura de pantalla que muestra las claves de acceso de la cuenta de almacenamiento":::

1. Copie la *clave* y guárdela por separado para usarla en los pasos siguientes.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-key.png" alt-text="Captura de pantalla que muestra las claves de acceso que se deben copiar":::

1. Vaya a almacén de claves.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-key-vault.png" alt-text="Captura de pantalla que muestra el almacén de claves":::

1. Seleccione **Configuración > Secretos** y, después, **+ Generar/Importar**

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-generate-secret.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para generar un secreto":::

1. Escriba el **Nombre** y **Valor** como *clave* de la cuenta de almacenamiento.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-secret-values.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para especificar los valores del secreto":::

1. Seleccione **Crear** para completar la acción.

1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una nueva credencial](manage-credentials.md#create-a-new-credential) mediante la clave para configurar el examen.

#### <a name="using-service-principal-for-scanning"></a>Uso de la entidad de servicio para examinar

##### <a name="creating-a-new-service-principal"></a>Creación de una nueva entidad de servicio

Si tiene que [crear una nueva entidad de servicio](./create-service-principal-azure.md), es necesario registrar una aplicación en el inquilino de Azure AD y proporcionar acceso a la entidad de servicio en los orígenes de datos. El administrador global de Azure AD u otros roles, como el administrador de aplicaciones, pueden realizar esta operación.

##### <a name="getting-the-service-principals-application-id"></a>Obtención del identificador de aplicación de la entidad de servicio

1. Copie el **Id. de aplicación (cliente)** presente en la **Información general** de la [_Entidad de servicio_](./create-service-principal-azure.md) ya creada.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-sp-appln-id.png" alt-text="Captura de pantalla que muestra el identificador de aplicación (cliente) de la entidad de servicio":::

##### <a name="granting-the-service-principal-access-to-your-azure-blob-account"></a>Concesión de acceso a la entidad de servicio a la cuenta de Azure Blob

Es importante conceder a la entidad de servicio el permiso para examinar el origen de datos de Azure Blob. Puede Agregar la MSI del catálogo en el nivel de suscripción, grupo de recursos o recurso, en función de para qué desea que tenga permisos de examen.

> [!Note]
> Debe ser propietario de la suscripción para poder agregar una entidad de servicio a un recurso de Azure.

1. En [Azure Portal](https://portal.azure.com), busque la suscripción, el grupo de recursos o el recurso (por ejemplo, una cuenta de almacenamiento de Azure Blob Storage) que desea permitir que el catálogo examine.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-storage-acct.png" alt-text="Captura de pantalla que muestra la cuenta de almacenamiento":::

1. Seleccione **Control de acceso (IAM)** en el panel de navegación izquierdo y, después, seleccione **+ Agregar** --> **Adición de la asignación de roles**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-access-control.png" alt-text="Captura de pantalla que muestra el control de acceso para la cuenta de almacenamiento":::

1. Establezca el **Rol** en **Lector de datos de Storage Blob** y especifique la _entidad de servicio_ en el cuadro de entrada **Seleccionar**. A continuación, seleccione **Save** (Guardar) para dar esta asignación de rol a su cuenta de Purview.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-sp-permission.png" alt-text="Captura de pantalla que muestra los detalles para proporcionar permisos de cuenta de almacenamiento en la entidad de servicio":::

### <a name="creating-the-scan"></a>Creación del examen

1. Abra la **cuenta de Purview** y seleccione **Apertura de Purview Studio**.
1. Vaya a **Mapa de datos** --> **Orígenes** para ver la jerarquía de colecciones.
1. Seleccione el icono **Nuevo examen** en el **origen de datos de Azure Blob** registrado anteriormente.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-new-scan.png" alt-text="Captura de pantalla que muestra la pantalla para crear un nuevo examen":::

#### <a name="if-using-managed-identity"></a>Si se utiliza una identidad administrada

Proporcione un **Nombre** para el examen, seleccione el **MSI de Purview** en **Credencial**, elija la colección adecuada para el examen y seleccione **Probar conexión**. Tras una conexión correcta, haga clic en **Continuar**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-managed-identity.png" alt-text="Captura de pantalla que muestra la opción de identidad administrada para ejecutar el examen":::

#### <a name="if-using-account-key"></a>Si usa una clave de cuenta

Proporcione un **Nombre** para el examen, elija la recopilación adecuada para el examen, seleccione **Método de autenticación** como _Clave de cuenta_ y elija **Crear**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-acct-key.png" alt-text="Captura de pantalla que muestra la opción Clave de cuenta para el examen":::

#### <a name="if-using-service-principal"></a>Si se utiliza una entidad de servicio

1. Proporcione un **Nombre** para el examen, elija la colección adecuada para el examen y seleccione **+ Nuevo** en **Credencial**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-sp-option.png" alt-text="Captura de pantalla que muestra la opción para que la entidad de servicio habilite el examen":::

1. Seleccione la **conexión del almacén de claves** adecuada y el **Nombre de secreto** que se usó al crear la _Entidad de servicio_. El **identificador de la entidad de servicio** es el **Id. de aplicación (cliente)** que se copió anteriormente.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-service-principal-option.png" alt-text="Captura de pantalla que muestra la opción de entidad de servicio":::

1. Seleccione **Test connection** (Probar conexión). Tras una conexión correcta, haga clic en **Continuar**.

### <a name="scoping-and-running-the-scan"></a>Ámbito y ejecución del examen

1. Para limitar el ámbito del examen a carpetas y subcarpetas específicas, puede elegir los elementos adecuados en la lista.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scope-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Si crea un nuevo _conjunto de reglas de examen_, seleccione los **tipos de archivo** que se incluirán en la regla de examen.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-file-types.png" alt-text="Tipos de archivo del conjunto de reglas de examen":::

1. Puede seleccionar las **reglas de clasificación** que se incluirán en la regla de examen.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-classification rules.png" alt-text="Reglas de clasificación del conjunto de reglas de examen":::

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-select-scan-rule-set.png" alt-text="Selección del conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scan-trigger.png" alt-text="desencadenador del examen":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-review-scan.png" alt-text="revisión del examen":::

### <a name="viewing-scan"></a>Visualización del examen

1. Vaya al _origen de datos_ en la _Colección_ y seleccione **Ver detalles** para comprobar el estado del examen.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-view-scan.png" alt-text="visualización del examen":::

1. Los detalles del examen indican el progreso del examen en el **Estado de la última ejecución** y el número de recursos _examinados_ y _clasificados_.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scan-details.png" alt-text="Ver detalles del examen":::

1. El **Estado de la última ejecución** se actualizará a **En curso** y, posteriormente, a **Completado** una vez que todo el examen se haya ejecutado correctamente.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scan-in-progress.png" alt-text="visualización del examen en curso":::

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scan-completed.png" alt-text="visualización del examen completado":::

### <a name="managing-scan"></a>Administración del examen

Los exámenes se pueden administrar o ejecutar de nuevo al finalizar.

1. Seleccione el **Nombre del examen** para administrar el examen.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-manage-scan.png" alt-text="administración del examen":::

1. Puede volver a _ejecutar el examen_, _editarlo_ o _eliminarlo_.  

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-manage-scan-options.png" alt-text="administración de las opciones del examen":::

1. Puede volver a _ejecutar un examen incremental_ o un _examen completo_. 

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-full-inc-scan.png" alt-text="examen completo o incremental":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
