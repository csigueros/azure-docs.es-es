---
title: Registro y examen de Azure Cosmos Database (SQL API)
description: En este artículo se describe el proceso para registrar un origen de datos de Azure Cosmos (API de SQL) en Azure Purview, incluidas instrucciones para autenticarse e interactuar con la base de datos de Azure Cosmos.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 7d5556fcd040eaefe1078cddaf084089e471d496
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076960"
---
# <a name="connect-to-azure-cosmos-database-sql-api-in-azure-purview"></a>Conectar a la base de datos de Azure Cosmos (API de SQL) en Azure Purview

En este artículo se describe el proceso para registrar una base de datos de Azure Cosmos (API de SQL) en Azure Purview, incluidas instrucciones para autenticarse e interactuar con el origen de la base de datos de Azure Cosmos.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan)|[Sí](#scan) | [Sí](#scan)|[Sí](#scan)|No|No|

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

## <a name="register"></a>Register

Esta sección le permitirá registrar la base de datos de Azure Cosmos (API de SQL) y configurar un mecanismo de autenticación adecuado para garantizar un examen correcto del origen de datos.

### <a name="steps-to-register"></a>Pasos para registrarse

Es importante registrar el origen de datos en Azure Purview antes de configurar un examen para el origen de datos.

1. Vaya a [Azure Portal](https://portal.azure.com), vaya a la página **Cuentas de Purview** y seleccione su _cuenta de Purview_.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-purview-acct.png" alt-text="Captura de pantalla que muestra la cuenta de Purview usada para registrar el origen de datos":::

1. Abra **Purview Studio** y vaya hasta **Mapa de datos --> Colecciones**.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-open-purview-studio.png" alt-text="Captura de pantalla que navega al vínculo Orígenes en el Mapa de datos":::

1. Cree la [Jerarquía de colecciones](./quickstart-create-collection.md) mediante el menú **Colecciones** y asigne permisos a las subcolecciones individuales, según sea necesario.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-collections.png" alt-text="Captura de pantalla que muestra el menú de colecciones para crear la jerarquía de colecciones":::

1. Vaya a la colección adecuada en el menú **Orígenes** y seleccione el icono **Registrar** para registrar una nueva base de datos de Azure Cosmos.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-register-data-source.png" alt-text="Captura de pantalla que muestra la colección usada para registrar el origen de datos":::

1. Seleccione el origen de datos de **Azure Cosmos DB (API de SQL)**  y, a continuación, **Continuar**.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-select-data-source.png" alt-text="Captura de pantalla que permite seleccionar el origen de datos":::

1. Asigne un **Nombre** adecuado al origen de datos, seleccione la **suscripción de Azure** correspondiente, el **nombre de la cuenta de Cosmos DB** existente y la **colección**, y seleccione **Aplicar**.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-data-source-details.png" alt-text="Captura de pantalla que muestra los detalles que debe escribir para registrar el origen de datos":::

1. La cuenta de la _base de datos de Azure Cosmos_ aparecerá en la colección seleccionada.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-collection-mapping.png" alt-text="Captura de pantalla que muestra el origen de datos asignado a la colección para iniciar el examen":::

## <a name="scan"></a>Examinar

### <a name="authentication-for-a-scan"></a>Autenticación para un examen

Para tener acceso para examinar el origen de datos, es necesario configurar un método de autenticación en la cuenta de almacenamiento de la base de datos de Azure Cosmos.

Solo hay una manera de configurar la autenticación de Azure Cosmos Database:

**Clave de cuenta**: los secretos se pueden crear dentro de una instancia de Azure Key Vault para almacenar las credenciales con el fin de permitir el acceso de Azure Purview para examinar los orígenes de datos de forma segura mediante los secretos. Un secreto puede ser una clave de cuenta de almacenamiento, una contraseña de inicio de sesión SQL o una contraseña.

> [!Note]
> Debe implementar un recurso de _Azure Key Vault_ en la suscripción y asignar el MSI de la _cuenta de Azure Purview_ con el permiso de acceso necesario a los secretos dentro de _Azure Key Vault_.

#### <a name="using-account-key-for-scanning"></a>Uso de la clave de cuenta para el examen

Debe obtener la clave de acceso y almacenarla en el almacén de claves:

1. Vaya a la cuenta de almacenamiento de la base de datos de Azure Cosmos
1. Seleccione **Configuración > Claves**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-access-keys.png" alt-text="Captura de pantalla que muestra las claves de acceso de la cuenta de almacenamiento":::

1. Copie la *clave* y guárdela por separado para usarla en los pasos siguientes.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key.png" alt-text="Captura de pantalla que muestra las claves de acceso que se deben copiar":::

1. Vaya a almacén de claves.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key-vault.png" alt-text="Captura de pantalla que muestra el almacén de claves":::

1. Seleccione **Configuración > Secretos** y, después, **+ Generar/Importar**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-generate-secret.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para generar un secreto":::

1. Escriba el **nombre** y el **valor** como la *clave* de la cuenta de almacenamiento y seleccione **Crear** para completar el proceso

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key-vault-options.png" alt-text="Captura de pantalla que muestra la opción del almacén de claves para especificar los valores del secreto":::

1. Si el almacén de claves no está conectado todavía a Purview, necesitará [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una credencial](manage-credentials.md#create-a-new-credential) mediante la clave para configurar el examen.

### <a name="creating-the-scan"></a>Creación del examen

1. Abra la **cuenta de Purview** y seleccione **Apertura de Purview Studio**.
1. Vaya a **Mapa de datos** --> **Orígenes** para ver la jerarquía de colecciones.
1. Seleccione el icono **Nuevo examen** en la **base de datos de Azure Cosmos** registrada anteriormente.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-create-scan.png" alt-text="Captura de pantalla que muestra la pantalla para crear un nuevo examen":::

1. Proporcione un **Nombre** para el examen, elija la colección adecuada para el examen y seleccione **+ Nuevo** en **Credencial**.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-acct-key-option.png" alt-text="Captura de pantalla que muestra la opción Clave de cuenta para el examen":::

1. Seleccione la **conexión del almacén de claves** adecuada y el **Nombre de secreto** que se usó al crear la _Clave de cuenta_. Elija el **Método de autenticación** como _Clave de cuenta_.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-acct-key-details.png" alt-text="Captura de pantalla que muestra las opciones de clave de cuenta":::

1. Seleccione **Test connection** (Probar conexión). Tras una conexión correcta, haga clic en **Continuar**.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-test-connection.png" alt-text="Captura de pantalla que muestra la conexión de prueba correcta":::

### <a name="scoping-and-running-the-scan"></a>Ámbito y ejecución del examen

1. Elija los elementos adecuados de la lista para limitar el ámbito del examen a carpetas y subcarpetas específicas.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scope-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-new-scan-rule-set.png" alt-text="Nueva regla de examen":::

1. Puede seleccionar las **reglas de clasificación** que se incluirán en la regla de examen.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-classification.png" alt-text="Reglas de clasificación del conjunto de reglas de examen":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-select-scan-rule-set.png" alt-text="Selección del conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-trigger.png" alt-text="desencadenador del examen":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-review-scan.png" alt-text="revisión del examen":::

### <a name="viewing-scan"></a>Visualización del examen

1. Vaya al _origen de datos_ en la _Colección_ y seleccione **Ver detalles** para comprobar el estado del examen.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-view-scan.png" alt-text="visualización del examen":::

1. Los detalles del examen indican el progreso del examen en el **Estado de la última ejecución** y el número de recursos _examinados_ y _clasificados_.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-last-run-status.png" alt-text="ver detalles del examen":::

1. El **Estado de la última ejecución** se actualizará a **En curso** y, después, a **Completado** una vez que todo el examen se haya ejecutado correctamente.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-in-progress.png" alt-text="visualización del examen en curso":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-completed.png" alt-text="visualización del examen completado":::

### <a name="managing-scan"></a>Administración del examen

Los exámenes se pueden administrar o ejecutar de nuevo al finalizar.

1. Seleccione el **Nombre del examen** para administrar el examen.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-manage-scan.png" alt-text="administración del examen":::

1. Puede volver a _ejecutar el examen_, _editarlo_ o _eliminarlo_.  

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-manage-scan-options.png" alt-text="administración de las opciones del examen":::

1. Puede volver a ejecutar _un examen completo_.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-full-scan.png" alt-text="examen completo":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
