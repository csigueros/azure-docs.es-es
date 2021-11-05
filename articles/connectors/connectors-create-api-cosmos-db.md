---
title: Conexión a Azure Cosmos DB
description: Procese y cree documentos de Azure Cosmos DB mediante Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: jcocchi
ms.author: jucocchi
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
tags: connectors
ms.openlocfilehash: 7a5025e284933284d910412fb2f9771dee43e31d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093612"
---
# <a name="process-and-create-azure-cosmos-db-documents-using-azure-logic-apps"></a>Procesamiento y creación de documentos de Azure Cosmos DB mediante Azure Logic Apps

Desde el flujo de trabajo Azure Logic Apps, puede conectarse a Azure Cosmos DB y trabajar con documentos usando el [conector de Azure Cosmos DB](/connectors/documentdb/). Este conector proporciona desencadenadores y acciones que el flujo de trabajo puede usar en las operaciones de Azure Cosmos DB. Puede crear flujos de trabajo automatizados para administrar documentos. Por ejemplo, entre las acciones se incluye la creación, actualización, lectura, consulta y eliminación de documentos.

Puede conectarse a Azure Cosmos DB desde los tipos de recursos tanto de **aplicación lógica (consumo)** como de **aplicación lógica (estándar)** mediante las operaciones de [*conector administrado*](managed.md). En el caso de los recursos de **aplicación lógica (estándar)** , Azure Cosmos DB también proporciona operaciones [*integradas*](built-in.md), que actualmente se encuentran en versión preliminar y que ofrecen otras funcionalidades, un rendimiento mejor y más elevado. Por ejemplo, si está trabajando con el tipo de recurso de **aplicación lógica (estándar)** , puede usar el desencadenador integrado para responder a los cambios en un contenedor de Azure Cosmos DB. Puede combinar operaciones de Azure Cosmos DB con otras acciones y desencadenadores en sus flujos de trabajo de la aplicación lógica para habilitar escenarios como el aprovisionamiento de eventos y el procesamiento de datos general.

> [!NOTE]
> Actualmente, solo los flujos de trabajo con estado de un recurso de **aplicación lógica (estándar)** pueden usar tanto las operaciones del conector administrado como las operaciones integradas. Los flujos de trabajo sin estado solo pueden usar operaciones integradas.

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta y una suscripción de Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Una [cuenta de Azure Cosmos DB](../cosmos-db/sql/create-cosmosdb-resources-portal.md).

- Un flujo de trabajo desde el que quiera acceder a la cuenta de Azure Cosmos DB. Para usar el desencadenador de Azure Cosmos DB, debe [crear la aplicación lógica **usando el tipo de recurso de** aplicación lógica (estándar)](../logic-apps/create-single-tenant-workflows-azure-portal.md) y agregar un flujo de trabajo en blanco.

## <a name="add-azure-cosmos-db-trigger"></a>Adición de un desencadenador de Azure Cosmos DB

En Azure Logic Apps, cada flujo de trabajo debe comenzar con un [desencadenador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que se activa cuando se produce un evento específico o cuando se cumple una condición determinada.

Si está trabajando con el tipo de recurso de **aplicación lógica (estándar)** , el desencadenador integrado denominado **When an item is created or modified (preview)** (Cuando se crea o se modifica un elemento [versión preliminar]) está disponible y se basa en el [patrón de fuente de cambios de Azure Cosmos DB](../cosmos-db/sql/change-feed-design-patterns.md). Este desencadenador no está disponible para el tipo de recurso de **aplicación lógica (consumo)** .

### <a name="consumption"></a>[Consumo](#tab/consumption)

No hay ningún desencadenador de Azure Cosmos DB disponible para el tipo de recurso de **aplicación lógica (consumo)** .

### <a name="standard"></a>[Estándar](#tab/standard)

Haga lo siguiente para agregar un desencadenador integrado de Azure Cosmos DB a un flujo de trabajo de aplicación lógica en una instancia de Azure Logic Apps de inquilino único:

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. Haga lo siguiente para encontrar el desencadenador:

   1. En el diseñador, seleccione **Elegir una operación**.

   1. Cuando se abra el panel **Agregar un desencadenador**, en el cuadro de búsqueda **Elegir una operación**, seleccione **Integrado**.

   1. En el cuadro de búsqueda, escriba `Azure Cosmos DB`. En la lista de desencadenadores, seleccione el desencadenador llamado **When an item is created or modified (preview)** (Cuando se crea o se modifica un elemento [versión preliminar]).

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-trigger-add.png" alt-text="Captura de pantalla de Azure Portal y el diseñador de un flujo de trabajo de aplicación lógica estándar con el desencadenador When an item is created or modified (preview) seleccionado":::

1. Si se le piden los detalles de la conexión, [cree una conexión a Azure Cosmos DB ahora](#connect-to-azure-cosmos-db).

1. En la pestaña **Parámetros**, proporcione la información necesaria del desencadenador.

   | Propiedad | Obligatorio | Value | Descripción |
   |----------|----------|-------|-------------|
   | **Id. de base de datos** | Yes | <*nombre de la base de datos*> | Nombre de la base de datos con el contenedor que desea supervisar. Esta base de datos también debe tener el contenedor de concesión. Si aún no tiene un contenedor de concesión, el conector creará uno automáticamente en un paso posterior. |
   | **Monitored Container Id** (Id. de contenedor supervisado) | Yes | <*nombre del contenedor*> | Nombre del contenedor que desea supervisar. Este contenedor ya debe existir en la base de datos especificada. |
   | **Lease Container Id** (Id. de contenedor de concesión) |  Yes | <*nombre del contenedor de concesión*> | Nombre de un contenedor de concesión existente, o un contenedor nuevo que desee crear automáticamente. El nombre del desencadenador se rellena de forma predeterminada como `leases`. |
   | **Create Lease Container** (Crear contenedor de concesión) | No | **Sí** o **No** | Si el contenedor de concesión ya existe en la base de datos especificada, seleccione **No**. Si desea que el desencadenador cree este contenedor, seleccione **Sí**. Si selecciona **Sí,** , asegúrese de abrir la lista **Agregar nuevo parámetro** y seleccionar la propiedad **Lease Container Throughput** (Rendimiento del contenedor de concesión). Escriba el número de [unidades de solicitud (RU)](../cosmos-db/request-units.md) que desea aprovisionar para este contenedor. |
   |||||

   En la imagen siguiente se muestra un desencadenador de ejemplo:

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-trigger-parameters.png" alt-text="Captura de pantalla que muestra el diseñador de un flujo de trabajo de aplicación lógica estándar con un desencadenador de Azure Cosmos DB y la configuración de parámetros":::

   > [!Note]
   > El desencadenador creará varias ejecuciones de flujo de trabajo para cada elemento creado o modificado en Azure Cosmos DB, por lo que la salida de contenido dinámico de este desencadenador siempre es un solo elemento.

1. Agregue cualquier otra acción que desee incluir en el flujo de trabajo.

1. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

---

## <a name="add-azure-cosmos-db-action"></a>Adición de una acción de Azure Cosmos DB

En Azure Logic Apps, una [acción](../logic-apps/logic-apps-overview.md#logic-app-concepts) es un paso del flujo de trabajo que sigue a un desencadenador u otra acción. El conector de Azure Cosmos DB ofrece acciones para los tipos de recursos de **aplicación lógica (consumo)** y de **aplicación lógica (estándar)** . Los ejemplos siguientes para cada tipo de recurso muestran cómo usar una acción que crea o actualiza un documento.

### <a name="consumption"></a>[Consumo](#tab/consumption)

Haga lo siguiente para agregar una acción de Azure Cosmos DB a un flujo de trabajo de aplicación lógica en una instancia de Azure Logic Apps con varios inquilinos:

1. En [Azure Portal](https://portal.azure.com), abra el flujo de trabajo en el diseñador.

1. Si el flujo de trabajo está en blanco, agregue el desencadenador que prefiera.

   Este ejemplo comienza con el desencadenador [**Cuando se recibe una solicitud HTTP**](connectors-native-reqres.md#add-request-trigger).

1. En el desencadenador o la acción donde quiera agregar la acción de Azure Cosmos DB, seleccione **Nuevo paso** o **Agregar una acción**, en el caso de que quiera hacerlo entre pasos.

1. En el cuadro de búsqueda del diseñador, escriba `Azure Cosmos DB`. Seleccione la acción de Azure Cosmos DB que quiera usar.

   En este ejemplo se usa la acción denominada **Crear o actualizar documento (V3)** .

   :::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-action-add.png" alt-text="Captura de pantalla que muestra el diseñador de un flujo de trabajo de una aplicación lógica de consumo con las acciones de Azure Cosmos DB disponibles":::

1. Si se le piden los detalles de la conexión, [cree una conexión a su cuenta de Azure Cosmos DB ahora](#connect-to-azure-cosmos-db).

1. Proporcione la información necesaria para la acción.

   | Propiedad | Obligatorio | Value | Descripción |
   |----------|----------|-------|-------------|
   | **Azure Cosmos DB account name** (Nombre de la cuenta de Azure Cosmos DB) | Yes | Seleccione **Use connection settings (Usar configuración de conexión [<*nombre de la cuenta de Azure Cosmos DB*>])** o escriba el nombre manualmente. | Nombre de cuenta de la cuenta de Azure Cosmos DB. |
   | **Identificador de base de datos** | Yes | <*identificador de la base de datos*> | Base de datos a la que desea conectarse. |
   | **ID de contenedor** | Yes | <*identificador del contenedor*> | Contenedor que desea consultar. |
   | **Documento** | Yes | <*documento JSON*> | Documento JSON que desea crear. En este ejemplo se usa el cuerpo de la solicitud de la salida del desencadenador. <p><p>**Sugerencia**: Si el token **Cuerpo** del desencadenador HTTP no aparece en la lista de contenido dinámico para agregarlo, seleccione **Ver más** junto al nombre del desencadenador. <p><p>**Nota**: Asegúrese de que el cuerpo tiene un formato JSON correcto y que contiene como mínimo la propiedad `id` y la propiedad de clave de partición del documento. Si ya existe un documento con la propiedad `id` y la clave de partición especificadas, el documento se actualiza. En caso contrario, se crea un nuevo documento. |
   |||||

   En la imagen siguiente se muestra una acción de ejemplo:

   :::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-create-action.png" alt-text="Captura de pantalla que muestra el diseñador de un flujo de trabajo de aplicación lógica de consumo con la acción de Azure Cosmos DB &quot;Crear o actualizar documento (V3)&quot; y la configuración de parámetros":::

1. Configure cualquier otro ajuste de acción según corresponda.

1. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

1. Pruebe la aplicación lógica para asegurarse de que el flujo de trabajo crea un documento en el contenedor especificado.

### <a name="standard"></a>[Estándar](#tab/standard)

Haga lo siguiente para agregar una acción de Azure Cosmos DB a un flujo de trabajo de aplicación lógica en una instancia de Azure Logic Apps de inquilino único:

1. En [Azure Portal](https://portal.azure.com), abra el flujo de trabajo en el diseñador.

1. Si el flujo de trabajo está en blanco, agregue el desencadenador que prefiera.

   Este ejemplo comienza con el desencadenador [**Cuando se recibe una solicitud HTTP**](connectors-native-reqres.md#add-request-trigger), que usa una definición de esquema básica para representar el elemento que desea crear.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-http-trigger.png" alt-text="Captura de pantalla que muestra Azure Portal y el diseñador de un flujo de trabajo de aplicación lógica estándar con el desencadenador &quot;Cuando se recibe una solicitud HTTP&quot; y la configuración de parámetros":::

1. En el desencadenador o la acción donde quiere agregar la acción de Azure Cosmos DB, seleccione **Insertar un nuevo paso** ( **+** ) > **Agregar una acción**.

1. En el diseñador, asegúrese de que está seleccionada la opción **Agregar una operación**. En el panel **Agregar una acción** que se abre, en el cuadro de búsqueda **Elegir una operación**, seleccione **Integrado** para encontrar las acciones de **Azure Cosmos DB**.

   > [!NOTE]
   > Si tiene un flujo de trabajo con estado, las acciones del *conector administrado* también están disponibles en la pestaña **Azure**, pero úselas solo cuando las acciones *integradas* que desee no estén disponibles.

1. En el cuadro de búsqueda, escriba `Azure Cosmos DB`. Seleccione la acción de Azure Cosmos DB que quiera usar.

   En este ejemplo se usa la acción denominada **Create or update item (preview)** (Crear o actualizar elemento [versión preliminar]), que crea un elemento o actualiza uno ya existente.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-action-add.png" alt-text="Captura de pantalla que muestra el diseñador de un flujo de trabajo de una aplicación lógica de consumo con las acciones de Azure Cosmos DB disponibles":::

1. Si se le piden los detalles de la conexión, [cree una conexión a su cuenta de Azure Cosmos DB ahora](#connect-to-azure-cosmos-db).

1. Proporcione la información necesaria para la acción.

   | Propiedad | Obligatorio | Value | Descripción |
   |----------|----------|-------|-------------|
   | **Id. de base de datos** | Yes | <*identificador de la base de datos*> | Base de datos a la que desea conectarse. |
   | **Id. de contenedor** | Yes | <*identificador del contenedor*> | Contenedor que desea consultar. |
   | **Elemento** | Yes | <*documento JSON*> | Documento JSON que desea crear. En este ejemplo se usa el cuerpo de la solicitud de la salida del desencadenador. <p><p>**Sugerencia**: Si el token **Cuerpo** del desencadenador HTTP no aparece en la lista de contenido dinámico para agregarlo, seleccione **Ver más** junto al nombre del desencadenador. <p><p>**Nota**: Asegúrese de que el cuerpo tiene un formato JSON correcto y que contiene como mínimo la propiedad `id` y la propiedad de clave de partición del documento. Si ya existe un documento con la propiedad `id` y la clave de partición especificadas, el documento se actualiza. En caso contrario, se crea un nuevo documento. | 
   | **Clave de partición** | Yes | <*clave de partición*> | Valor de clave de partición del documento que desea crear. Si esta propiedad no aparece, abra la lista **Agregar nuevo parámetro** y seleccione **Clave de partición**. <p><p>**Importante**: Aunque esta propiedad no está marcada como obligatoria con un asterisco (*), realmente es necesaria para que el flujo de trabajo se realice correctamente. |
   |||||

   En la imagen siguiente se muestra una acción de ejemplo:

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-create-action.png" alt-text="Captura de pantalla que muestra el diseñador de un flujo de trabajo de aplicación lógica estándar con la acción de Azure Cosmos DB &quot;Create or update item&quot; (Crear o actualizar elemento) y la configuración de parámetros":::

1. Configure cualquier otro ajuste de acción según corresponda.

1. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

1. Pruebe la aplicación lógica para asegurarse de que el flujo de trabajo crea un documento en el contenedor especificado.

---

## <a name="connect-to-azure-cosmos-db"></a>Conexión a Azure Cosmos DB

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Para poder configurar el [desencadenador de Azure Cosmos DB](#add-azure-cosmos-db-trigger) o la [acción de Azure Cosmos DB](#add-azure-cosmos-db-action), debe conectarse a una cuenta de base de datos.

### <a name="consumption"></a>[Consumo](#tab/consumption)

En un flujo de trabajo de **aplicación lógica (consumo)** , una conexión de Azure Cosmos DB requiere los siguientes valores de propiedad:

| Propiedad | Obligatorio | Value | Descripción |
|----------|----------|-------|-------------|
| **Nombre de la conexión** | Sí | <*connection-name*> | El nombre que se usa para su conexión. |
| **Tipo de autenticación** | Yes | <*tipo de conexión*> | El tipo de autenticación que desea utilizar. En este ejemplo se usa **Clave de acceso**. <p><p>- Si selecciona **Clave de acceso**, proporcione los valores de propiedad necesarios restantes para crear la conexión. <p><p>- Si selecciona **Azure AD integrado**, no se requieren otros valores de propiedad, pero debe configurar la conexión siguiendo los pasos de [autenticación de Azure AD y el conector de Cosmos DB](/connectors/documentdb/#azure-ad-authentication-and-cosmos-db-connector).  |
| **Clave de acceso a la cuenta de Azure Cosmos DB** | Yes | <*clave de acceso*> | Clave de acceso de la cuenta de Azure Cosmos DB que se usará para esta conexión. Este valor es una clave de lectura y escritura o una clave de solo lectura. <p><p>**Nota**: Para encontrar la clave, vaya a la página de la cuenta de Azure Cosmos DB. En el menú de navegación, en **Configuración**, seleccione **Claves**. Copie uno de los valores de clave disponibles. |
| **Id. de cuenta** | Yes | <*identificador de la cuenta*> | Nombre de la cuenta de Azure Cosmos DB que se usará para esta conexión. |
|||||

La siguiente imagen muestra una conexión de ejemplo:

:::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-connection-configure.png" alt-text="Captura de pantalla con un ejemplo de configuración de conexión de Azure Cosmos DB de un flujo de trabajo de aplicación lógica de consumo":::

> [!NOTE]
> Después de crear la conexión, si tiene una conexión de Azure Cosmos DB existente distinta que quiera usar en su lugar, o si prefiere crear otra conexión desde cero, seleccione **Cambiar conexión** en el editor de detalles del desencadenador o la acción.

### <a name="standard"></a>[Estándar](#tab/standard)

En un flujo de trabajo de **aplicación lógica (estándar)** , una conexión de Azure Cosmos DB requiere los siguientes valores de propiedad:

| Propiedad | Obligatorio | Value | Descripción |
|----------|----------|-------|-------------|
| **Nombre de la conexión** | Sí | <*connection-name*> | El nombre que se usa para su conexión. |
| **Cadena de conexión** | Yes | <*cadena de conexión*> | Cadena de conexión de Azure Cosmos DB que se usará para la conexión. <p><p>**Nota**: Para encontrar la cadena de conexión, vaya a la página de la cuenta de Azure Cosmos DB. En el menú de navegación, en **Configuración**, seleccione **Claves**. Copie uno de los dos valores de cadena de conexión disponibles. |
|||||

La siguiente imagen muestra una conexión de ejemplo:

:::image type="content" source="./media/connectors-create-api-cosmos-db/standard-connection-configure.png" alt-text="Captura de pantalla con un ejemplo de configuración de conexión de Azure Cosmos DB de un flujo de trabajo de aplicación lógica estándar":::

> [!NOTE]
> Después de crear la conexión, si tiene una conexión de Azure Cosmos DB existente distinta que quiera usar en su lugar, o si prefiere crear otra conexión desde cero, seleccione **Cambiar conexión** en el editor de detalles del desencadenador o la acción.

---

## <a name="connector-reference"></a>Referencia de conectores

Para obtener información de referencia sobre las distintas operaciones de *conector administrado* de Azure Cosmos DB (como los desencadenadores, las acciones y los límites), revise la [página de referencia del conector](/connectors/documentdb/).

No existe ninguna página de referencia relativa a las operaciones *integradas* de Azure Cosmos DB. En su lugar, revise la tabla siguiente para obtener más información:

| Tipo | Nombre | Parámetros |
|------|------|------------|
| Desencadenador | When an item is created or modified (Cuando se crea o se modifica un elemento) | - **Id. de base de datos**: obligatorio. Nombre de la base de datos con los contenedores supervisado y de concesión. <br>- **Monitored Container Id** (Id. de contenedor supervisado): obligatorio. Nombre del contenedor que se está supervisando. <br>- **Lease Container Id** (Id. de contenedor de concesión): obligatorio. Nombre del contenedor utilizado para almacenar concesiones. <br>- **Create Lease Container** (Crear contenedor de concesión): obligatorio. Si es true, cree el contenedor de concesión si aún no existe. <br>- **Lease Container Throughput** (Rendimiento del contenedor de concesión): opcional. Número de unidades de solicitud que se asignarán al crear el contenedor de concesión. |
| Acción | Create or update item (Crear o actualizar elemento) | - **Id. de base de datos**: obligatorio. El nombre de la base de datos. <br>- **Id. de contenedor**: obligatorio. nombre del contenedor. <br>- **Elemento**: obligatorio. Elemento que se creará o actualizará. <br>- **Clave de partición**: obligatorio. Valor de clave de partición del elemento solicitado. <br>- **Is Upsert** (Es upsert): opcional. Si es true, reemplace el elemento (si existe). De lo contrario, cree el elemento. |
| Acción | Create or update many items in bulk (Crear o actualizar muchos elementos de forma masiva) | Esta acción está optimizada para escenarios de alto rendimiento y tiene procesamiento adicional antes de que la acción envíe los elementos que se crearán en el contenedor de Azure Cosmos DB. Si el número de elementos es elevado, este procesamiento adicional acelera el tiempo total de solicitud. Si el número es reducido, esta sobrecarga adicional puede tener un rendimiento más lento que si se usan varias acciones de creación de elementos individuales. <p><p>- **Id. de base de datos**: obligatorio. El nombre de la base de datos. <br>- **Id. de contenedor**: obligatorio. nombre del contenedor. <br>- **Elemento**: obligatorio. Matriz de los elementos que se crearán o actualizarán. <br>- **Is Upsert** (Es upsert): opcional. Si es true, reemplace un elemento (si existe). De lo contrario, cree el elemento. |
| Acción | Lectura de un elemento | - **Id. de base de datos**: obligatorio. El nombre de la base de datos. <br>- **Id. de contenedor**: obligatorio. nombre del contenedor. <br>- **Id. de elemento**: obligatorio. Valor de `id` del elemento solicitado. <br>- **Clave de partición**: obligatorio. Valor de clave de partición del elemento solicitado. |
| Acción | Eliminación de un elemento | - **Id. de base de datos**: obligatorio. El nombre de la base de datos. <br>- **Id. de contenedor**: obligatorio. nombre del contenedor. <br>- **Id. de elemento**: obligatorio. Valor de `id` del elemento solicitado. <br>- **Clave de partición**: obligatorio. Valor de clave de partición del elemento solicitado. |
| Acción | Elementos de consulta | - **Id. de base de datos**: obligatorio. El nombre de la base de datos. <br>- **Id. de contenedor**: obligatorio. nombre del contenedor. <br>- **Consulta SQL**: obligatorio. Texto de la consulta SQL de Azure Cosmos DB.  <br>- **Clave de partición**: opcional. Valor de clave de partición de la solicitud, si lo hay. <br>- **Token de continuación**: opcional. Token de continuación de esta consulta que proporciona el servicio Azure Cosmos DB, si lo hay. <br>- **Recuento máximo de elementos**: opcional. Número máximo de elementos que se devolverán en la consulta. |
||||

## <a name="best-practices-for-azure-cosmos-db-built-in-operations"></a>Procedimientos recomendados de operaciones integradas de Azure Cosmos DB

### <a name="get-iterable-results-from-the-query-items-action-by-using-expressions"></a>Obtener resultados iterables de la acción de consulta de elementos de consulta mediante expresiones

El tipo de salida de la acción integrada **Query items** (Consultar elementos) en un flujo de trabajo de **aplicación lógica (estándar)** es un objeto no iterable. Haga lo siguiente para obtener los resultados de la consulta como un objeto iterable para su procesamiento:

1. En [Azure Portal](https://portal.azure.com), abra el flujo de trabajo en el diseñador.

1. Si el flujo de trabajo está en blanco, agregue el desencadenador que prefiera.

   Este ejemplo comienza con el [desencadenador **Periodicidad**](connectors-native-recurrence.md).

1. En el desencadenador o la acción donde quiere agregar la acción de Azure Cosmos DB, seleccione **Insertar un nuevo paso** ( **+** ) > **Agregar una acción**.

1. En el diseñador, asegúrese de que está seleccionada la opción **Agregar una operación**. En el panel **Agregar una acción** que se abre, en el cuadro de búsqueda **Elegir una operación**, seleccione **Integrado** para encontrar las acciones de **Azure Cosmos DB**.

1. En el cuadro de búsqueda, escriba `Azure Cosmos DB`. Seleccione la acción **Query items (preview)** (Consultar elementos [versión preliminar]).

1. Si se le piden los detalles de la conexión, [cree una conexión a su cuenta de Azure Cosmos DB](#connect-to-azure-cosmos-db).

1. Proporcione la información necesaria para la acción.

   | Propiedades | Obligatorio | Value | Descripción |
   |------------|----------|-------|-------------|
   | **Id. de base de datos** | Yes | <*identificador de la base de datos*> | Base de datos a la que desea conectarse. |
   | **Id. de contenedor** | Yes | <*identificador del contenedor*> | Contenedor que desea consultar. |
   | **Consulta SQL** | Yes | <*consulta sql*> | Consulta SQL de la solicitud. |
   |||||

   En la imagen siguiente se muestra una acción de ejemplo:

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-query-action.png" alt-text="Captura de pantalla que muestra el diseñador de un flujo de trabajo de aplicación lógica estándar con la acción de Azure Cosmos DB &quot;Query items&quot; (Consultar elementos) y la configuración de parámetros":::

1. Configure cualquier otro ajuste de acción según corresponda.

1. En la acción, seleccione **Permite agregar un nuevo paso** ( **+** ) > **Agregar una acción**. En el panel **Agregar una acción** que se abre, en el cuadro de búsqueda **Elegir una operación**, seleccione **Integrado** para encontrar y agregar la acción **Para cada uno**.

1. Haga lo siguiente para convertir las salidas de la acción **Query items** (Consultar elementos):

   1. En el panel de la acción **Para cada uno**, haga clic dentro del cuadro **Seleccionar una salida de los pasos anteriores** para que se abra la lista de contenido dinámico. Seleccione la pestaña **Expresión** para abrir el editor de expresiones.

   1. En la lista de funciones, busque la sección **Funciones de referencia** y seleccione la función **outputs(actionName),** que aparece en el cuadro del editor de expresiones.

   1. En el cuadro del editor, escriba `'Query_items'` como parámetro de la función **outputs(actionName)** , seguido de `?['body']?['item']?['results']` para acceder a la matriz con los resultados de la consulta, y seleccione **Aceptar**. La expresión completa tiene este formato:

      `outputs('Query_items')?['body']?['item']?['results']`

      :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-query-configure.png" alt-text="Captura de pantalla de Azure Portal y el diseñador de un flujo de trabajo de aplicación lógica estándar con la acción &quot;Para cada uno&quot; para procesar los resultados de la consulta de Azure Cosmos DB":::

1. En el bucle **Para cada uno**, agregue cualquier otra acción que desee.

1. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

1. Pruebe la aplicación lógica para asegurarse de que el flujo de trabajo devuelve la salida que espera.

### <a name="confirm-action-success-by-checking-the-operation-status-code"></a>Comprobar el código de estado de la operación para confirmar que la acción se ha realizado correctamente

Al usar las acciones integradas de Azure Cosmos DB en un flujo de trabajo de **aplicación lógica (estándar)** , debe tener un paso después de cada acción que compruebe el código de estado devuelto por la llamada de servicio al servicio Azure Cosmos DB. De este modo, las acciones posteriores en el flujo de trabajo pueden reaccionar a los distintos códigos de estado que devuelve el servicio Azure Cosmos DB en función del escenario.

> [!NOTE]
> Actualmente, y a diferencia de otras acciones de conector administrado y acciones integradas, el código de estado devuelto por una acción de Azure Cosmos DB es distinto del código de estado devuelto por la llamada de servicio que se envía al servicio Azure Cosmos DB. El código de estado de la acción está disponible en la lista de contenido dinámico, mientras que el código de estado de la llamada de servicio se devuelve y se anida en el elemento `body` del objeto de respuesta.
>
> En los pasos siguientes se muestra cómo acceder al código de estado de la llamada de servicio mediante una expresión que analiza el elemento `body` del objeto de respuesta.

Por ejemplo, con los pasos siguientes se crea un flujo de trabajo que usa la acción **Read an item** (Leer un elemento) y que muestra cómo comprobar el código de estado devuelto por la llamada de servicio al servicio Azure Cosmos DB:

1. En [Azure Portal](https://portal.azure.com), abra el flujo de trabajo en el diseñador.

1. Si el flujo de trabajo está en blanco, agregue el desencadenador que prefiera.

   Este ejemplo comienza con el desencadenador [**Cuando se recibe una solicitud HTTP**](connectors-native-reqres.md#), que usa una definición de esquema básica para representar el documento que desea leer.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-http-trigger.png" alt-text="Captura de pantalla que muestra el diseñador de un flujo de trabajo de aplicación lógica estándar con el desencadenador &quot;Cuando se recibe una solicitud HTTP&quot; y la configuración de parámetros":::

1. En el desencadenador o la acción donde quiere agregar la acción de Azure Cosmos DB, seleccione **Insertar un nuevo paso** ( **+** ) > **Agregar una acción**.

1. En el diseñador, asegúrese de que está seleccionada la opción **Agregar una operación**. En el panel **Agregar una acción** que se abre, en el cuadro de búsqueda **Elegir una operación**, seleccione **Integrado** para encontrar las acciones de **Azure Cosmos DB**.

1. En el cuadro de búsqueda, escriba `Azure Cosmos DB`. Seleccione la acción **Query items (preview)** (Consultar elementos [versión preliminar]).

1. Si se le piden los detalles de la conexión, [cree una conexión a su cuenta de Azure Cosmos DB](#connect-to-azure-cosmos-db).

1. Proporcione la información necesaria para la acción.

   | Propiedades | Obligatorio | Value | Descripción |
   |------------|----------|-------|-------------|
   | **Id. de base de datos** | Yes | <*identificador de la base de datos*> | Base de datos a la que desea conectarse. |
   | **Id. de contenedor** | Yes | <*identificador del contenedor*> | Contenedor que desea consultar. |
   | **Id. de elemento** | Yes | <*identificador del elemento*> | Valor de `id` del documento que desea leer. |
   | **Clave de partición** | Yes | <*clave de partición*> | Valor de clave de partición del documento que desea leer. |
   |||||

   En la imagen siguiente se muestra una acción de ejemplo:

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-read-configure.png" alt-text="Captura de pantalla que muestra el diseñador de un flujo de trabajo de aplicación lógica estándar con la acción de Azure Cosmos DB &quot;Read an item&quot; (Leer un elemento) y la configuración de parámetros":::

1. Configure cualquier otro ajuste de acción según corresponda.

1. En la acción, seleccione **Permite agregar un nuevo paso** ( **+** ) > **Agregar una acción**. En el panel **Agregar una acción** que se abre, en el cuadro de búsqueda **Elegir una operación**, seleccione **Integrado** para encontrar y agregar la acción **Condición**.

1. Haga clic dentro del cuadro **Elegir un valor** de la condición situado más a la izquierda para que aparezca la lista de contenido dinámico. Seleccione la pestaña **Expresión** para abrir el editor de expresiones.

1. En la lista de funciones, busque la sección **Funciones de referencia** y seleccione la función **outputs(actionName),** que aparece en el cuadro del editor de expresiones.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-condition-configure.png" alt-text="Captura de pantalla que muestra el diseñador de un flujo de trabajo de aplicación lógica estándar con una configuración de condición":::

1. En el cuadro del editor de expresiones, escriba `'Read_an_item'` como parámetro de la función **outputs(actionName)** , seguido de `?['body']?['statusCode']`, y seleccione **Aceptar**.

   Esta expresión analiza el contenido del elemento `body` del objeto de respuesta para acceder al código de estado devuelto por la llamada de servicio que ejecuta la operación de lectura en Azure Cosmos DB. La expresión completa tiene este formato:

   `outputs('Read_an_item')?['body']?['statusCode']`

   > [!NOTE]
   > Recuerde que el código de estado devuelto por una acción de Azure Cosmos DB es distinto del código de estado devuelto de la llamada que realmente se envía a Azure Cosmos DB. El código de estado de la acción está disponible en la lista de contenido dinámico, mientras que el código de estado de la llamada de servicio se devuelve y se anida en el elemento `body` del objeto de respuesta.

1. En el cuadro central de la condición y el cuadro **Elegir un valor** situado más a la derecha, especifique los valores correspondientes al flujo de trabajo. En este ejemplo, la condición comprueba si el código de estado es igual a `OK`.

   > [!NOTE]
   > Los códigos de estado se representan como texto, no como números.

1. En las rutas **True** y **False** de la condición, agregue cualquier otra acción que encaje en su escenario.

1. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

1. Pruebe la aplicación lógica para asegurarse de que el flujo de trabajo devuelve la salida que espera.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a los conectores para Azure Logic Apps](apis-list.md)
