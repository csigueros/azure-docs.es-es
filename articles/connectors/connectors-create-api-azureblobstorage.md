---
title: Conexión con Azure Blob Storage
description: Cree y administre blobs en cuentas de Azure Storage con Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/11/2021
tags: connectors
ms.openlocfilehash: cc56c079173fad1509d9da9cf1d435675b1918f7
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808992"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Creación y administración de blobs en Azure Blob Storage con Azure Logic Apps

Desde el flujo de trabajo de Azure Logic Apps, puede usar el [conector de Azure Blob Storage](/connectors/azureblobconnector/) para acceder a los archivos almacenados como blobs en una cuenta de Azure Storage y administrarlos. Este conector proporciona desencadenadores y acciones que el flujo de trabajo puede usar para las operaciones de blobs. A continuación, puede automatizar las tareas para administrar archivos en la cuenta de almacenamiento. Por ejemplo, las [acciones del conector](/connectors/azureblobconnector/#actions) incluyen la comprobación, la eliminación, la lectura y la carga de blobs. El [desencadenador disponible](/connectors/azureblobconnector/#triggers) se activa cuando se agrega o modifica un blob.

Puede conectarse a Blob Storage desde los tipos de recursos **Logic App (Consumption)** (Aplicación lógica [Consumo]) y **Logic App (Standard)** .(Aplicación lógica [Estándar]). Puede usar el conector con flujos de trabajo de aplicación lógica en entornos multiinquilino y de inquilino único de Azure Logic Apps y el entorno del servicio de integración (ISE). Con el recurso **Logic App (Standard)** (Aplicación lógica [Estándar]), Blob Storage ofrece operaciones integradas *y* operaciones de conector administradas.

> [!IMPORTANT]
> Un flujo de trabajo de aplicación lógica no puede acceder directamente a una cuenta de almacenamiento detrás de un firewall si ambos están en la misma región. Como alternativa, puede tener la aplicación lógica y la cuenta de almacenamiento en diferentes regiones. Para más información sobre cómo habilitar el acceso desde Azure Logic Apps a las cuentas de almacenamiento que se encuentran detrás de los firewalls, consulte la sección [Acceso a cuentas de almacenamiento detrás de firewalls](#access-storage-accounts-behind-firewalls) más adelante en este tema.

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta y una suscripción de Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Una [cuenta de Azure Storage y un contenedor de almacenamiento](../storage/blobs/storage-quickstart-blobs-portal.md)

- Un flujo de trabajo desde el que quiera acceder a la cuenta de Blob Storage. Si quiere iniciar el flujo de trabajo con un desencadenador de Blob Storage, necesita un [flujo de trabajo de aplicación lógica en blanco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limits"></a>Límites

- En el caso de los flujos de trabajo de aplicación lógica que se ejecutan en un [entorno de servicio de integración (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la versión con la etiqueta ISE de este conector usa en su lugar los [límites de mensajes de ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits).

- De forma predeterminada, las acciones de Blob Storage pueden leer o escribir archivos de *50 MB o más pequeños*. Para controlar los archivos mayores de 50 MB, pero hasta 1024 MB, las acciones de Blob Storage admiten la [fragmentación de mensajes](../logic-apps/logic-apps-handle-large-messages.md). En la [acción **Obtener contenido de blog**](/connectors/azureblobconnector/#get-blob-content) se usa la fragmentación de forma implícita.

- Los desencadenadores de Blob Storage no admiten la fragmentación. Cuando se solicita el contenido del archivo, los desencadenadores seleccionan solo los archivos que tienen un tamaño de 50 MB o menos. Para obtener archivos de más de 50 MB, siga este patrón:

  - Use un desencadenador de Blob Storage que devuelva propiedades de archivo, como [**Cuando se agrega o modifica un blob (solo propiedades)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)).

  - Siga el desencadenador con la [acción **Obtener contenido de blob**](/connectors/azureblobconnector/#get-blob-content) de Blob Storage, que lee el archivo completo y usa la fragmentación de forma implícita.

## <a name="connector-reference"></a>Referencia de conectores

Para más información técnica sobre este conector, como los desencadenadores, las acciones y los límites, consulte la [página de referencia del conector](/connectors/azureblobconnector/). Si no quiere usar el conector de Blob Storage, en su lugar, puede [utilizar el desencadenador o la acción HTTP junto con una identidad administrada para las operaciones de blob](#access-blob-storage-with-managed-identities).

## <a name="add-blob-storage-trigger"></a>Adición de un desencadenador de Blob Storage

En Azure Logic Apps, cada flujo de trabajo debe comenzar con un [desencadenador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que se activa cuando se produce un evento específico o cuando se cumple una condición determinada.

Este conector tiene un desencadenador disponible, denominado [**Cuando se agrega o modifica un blob en Azure Storage** o **Cuando se agrega o modifica un blob (solo propiedades)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)). El desencadenador se activa cuando las propiedades de un blob se agregan o actualizan en el contenedor de almacenamiento. Cada vez, el motor de Azure Logic Apps crea una instancia de aplicación lógica y empieza a ejecutar el flujo de trabajo.

### <a name="consumption"></a>[Consumo](#tab/consumption)

Para agregar un desencadenador de Azure Blob Storage a un flujo de trabajo de aplicación lógica en un entorno multiinquilino de Azure Logic Apps, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. En el cuadro de búsqueda del diseñador, escriba `Azure blob` como filtro. En la lista de desencadenadores, seleccione llamado **When a blob is added or modified (properties only)** (Cuando se agrega o modifica un blob [solo propiedades]).

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-add.png" alt-text="Captura de pantalla que muestra Azure Portal y el diseñador de flujos de trabajo con una aplicación lógica de Consumo y el desencadenador llamado &quot;When a blob is added or modified (properties only)&quot; (Cuando se agrega o modifica un blob [solo propiedades]) seleccionado.":::

1. Si se le piden los detalles de la conexión, [cree ahora su conexión de Azure Blob Storage](#connect-blob-storage-account).

1. Proporcione la información necesaria para el desencadenador.

   1. En el valor de propiedad **Contenedor**, seleccione el icono de la carpeta para buscar el contenedor de Blob Storage. O bien, escriba la ruta manualmente.

   1. Configure otros valores del desencadenador según corresponda.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-configure.png" alt-text="Captura de pantalla que muestra el desencadenador de blobs de Azure con la configuración de parámetros.":::

1. Agregue una o varias acciones al flujo de trabajo.

1. En la barra de herramientas del diseñador, seleccione **Guardar** para guardar los cambios.

### <a name="standard"></a>[Estándar](#tab/standard)

Para agregar un desencadenador de blobs de Azure a un flujo de trabajo de aplicación lógica en un entono de inquilino único de Azure Logic Apps, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com) abra el flujo de trabajo de la aplicación lógica en el diseñador.

1. En el diseñador, seleccione **Elegir una operación**. En el panel **Agregar un desencadenador** que se abre, en el cuadro de búsqueda **Elegir una operación**, seleccione **Integrado** para encontrar el desencadenador **integrado** *Blob de Azure*, o seleccione **Azure** para encontrar el desencadenador de **conector administrado** de *Azure Blob Storage*.

1. En el cuadro de búsqueda, escriba `Azure blob`. En la lista de desencadenadores, seleccione el desencadenador denominado **Cuando se agrega o modifica un blob en Azure Storage**.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-add.png" alt-text="Captura de pantalla que muestra Azure Portal, el diseñador de flujos de trabajo, el flujo de trabajo de aplicación lógica Estándar y el desencadenador Blob de Azure seleccionado.":::

1. Si se le piden los detalles de la conexión, [cree ahora su conexión de Azure Blob Storage](#connect-blob-storage-account).

1. Proporcione la información necesaria para el desencadenador. En la pestaña **Parámetros** agregue la **ruta de acceso del blob** al blob que quiera supervisar.

   1. Para buscar la ruta del blob, abra la cuenta de almacenamiento en Azure Portal.

   1. En el menú de navegación, en **Almacenamiento de datos**, seleccione **Contenedores**.

   1. Seleccione el contenedor de blobs. En el menú de navegación del contenedor, en **Configuración**, seleccione **Propiedades**.

   1. Copie el valor **URL**, que es la ruta al blob. La ruta es similar a `https://<storage-container-name>/<folder-name>/{name}`. En su lugar, proporcione el nombre del contenedor y de la carpeta, pero mantenga la cadena literal `{name}`.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-configure.png" alt-text="Captura de pantalla que muestra el diseñador de flujos de trabajo de un flujo de trabajo de aplicación lógica Estándar con un desencadenador Blob Storage y la configuración de parámetros.":::

1. Siga creando el flujo de trabajo y agregue una o varias acciones.

1. En la barra de herramientas del diseñador, seleccione **Guardar** para guardar los cambios.

---

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Adición de un acción de Blob Storage

En Azure Logic Apps, una [acción](../logic-apps/logic-apps-overview.md#logic-app-concepts) es un paso del flujo de trabajo que sigue a un desencadenador u otra acción.

### <a name="consumption"></a>[Consumo](#tab/consumption)

Para agregar una acción de Azure Blob Storage a un flujo de trabajo de aplicación lógica en un entorno multiinquilino de Azure Logic Apps, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), abra el flujo de trabajo en el diseñador.

1. Si el flujo de trabajo está en blanco, agregue el desencadenador que prefiera.

   Este ejemplo comienza con el [desencadenador **Periodicidad**](connectors-native-recurrence.md).

1. En el desencadenador o la acción donde quiera agregar la acción de Blob Storage, seleccione **Nuevo paso** o **Agregar una acción**, en el caso de que quiera hacerlo entre pasos.

1. En el cuadro de búsqueda del diseñador, escriba `Azure blob`. Seleccione la acción de Blob Storage que quiera usar.

   En este ejemplo de usa la acción llamada **Obtener contenido de blob**.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-add.png" alt-text="Captura de pantalla de la aplicación lógica Consumo en el diseñador, en la que se muestra la lista de acciones de Blob Storage disponibles.":::

1. Si se le piden los detalles de la conexión, [cree una conexión a la cuenta de Blob Storage](#connect-blob-storage-account).

1. Proporcione la información necesaria para la acción.

    1. Como valor de propiedad de **Blob**, seleccione el icono de la carpeta para buscar el contenedor de Blob Storage. O bien, escriba la ruta manualmente.

       :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-configure.png" alt-text="Captura de pantalla de la aplicación lógica de consumo en el diseñador, en la que se muestra la configuración de la acción de Blob Storage.":::

    1. Configure otros valores de acción según corresponda.

### <a name="standard"></a>[Estándar](#tab/standard)

Para agregar una acción de Blob de Azure a un flujo de trabajo de aplicación lógica en un entorno de inquilino único de Azure Logic Apps, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), abra el flujo de trabajo en el diseñador.

1. Si el flujo de trabajo está en blanco, agregue el desencadenador que prefiera.

   Este ejemplo comienza con el [desencadenador **Periodicidad**](connectors-native-recurrence.md).

1. En el desencadenador o la acción donde quiere agregar la acción de Blob Storage, seleccione **Insertar un nuevo paso** ( **+** ) > **Agregar una acción**.

1. En el diseñador, asegúrese de que está seleccionada la opción **Agregar una operación**. En el panel **Agregar una acción** que se abre, en el cuadro de búsqueda **Elegir una operación**, seleccione **Intregrado** para encontrar el desencadenador **integrado** *Blob de Azure*, o seleccione **Azure** para encontrar el desencadenador de **conector administrado** de *Azure Blob Storage*.

1. En el cuadro de búsqueda, escriba `Azure blob`. Seleccione la acción de Blob de Azure que quiera usar.

   En este ejemplo se usa la acción llamada **Reads Blob Content from Azure Storage** (Lee el contenido del blob de Azure Storage), que solo lee el contenido del blob. Para ver más adelante el contenido, agregue una acción diferente que cree un archivo con el contenido del blob mediante otro conector. Por ejemplo, puede agregar una acción de OneDrive que cree un archivo basado en el contenido del blob.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-add.png" alt-text="Captura de pantalla que muestra Azure Portal y el diseñador de flujos de trabajo con un flujo de trabajo de aplicación lógica Estándar y las acciones de Azure Blob Storage disponibles.":::

1. Si se le piden los detalles de la conexión, [cree una conexión a la cuenta de Blob Storage](#connect-blob-storage-account).

1. Proporcione la información necesaria para la acción.

    1. En **Nombre del contenedor**, escriba la ruta del contenedor de almacenamiento de blobs que quiera usar.

    1. En la propiedad **Nombre del blob**, escriba la ruta de acceso del blob que quiere usar.

       :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-configure.png" alt-text="Captura de pantalla de la aplicación lógica Estándar en el diseñador, en la que se muestra la selección del desencadenador de Blob Storage.":::

    1. Configure otros valores de acción según corresponda.

1. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

1. Pruebe la aplicación lógica para asegurarse de que el contenedor seleccionado contiene un blob.

---

<a name="connect-blob-storage-account"></a>

## <a name="connect-to-blob-storage-account"></a>Conexión a la cuenta de Blob Storage

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

### <a name="consumption"></a>[Consumo](#tab/consumption)

Para poder configurar el [desencadenador de Azure Blob Storage](#add-blob-storage-trigger) o la [acción de Azure Blob Storage](#add-blob-storage-action), debe conectarse a una cuenta de almacenamiento. Una cadena de conexión necesita las propiedades siguientes:

| Propiedad | Obligatorio | Value | Descripción |
|----------|----------|-------|-------------|
| **Nombre de la conexión** | Sí | <*connection-name*> | El nombre que se usa para su conexión. |
| **Storage Account** | Sí | <*storage-account*> | Seleccione la cuenta de almacenamiento de la lista, o bien proporcione una cadena. <p>**Nota**: Para encontrar la cadena de conexión, vaya a la página de la cuenta de almacenamiento. En el menú de navegación, en **Seguridad y redes**, seleccione **Claves de acceso** > **Mostrar claves**. Copie uno de los dos valores de cadena de conexión disponibles. |
|||||

Para crear una conexión de Azure Blob Storage desde un flujo de trabajo de aplicación lógica en el entorno multiinquilino de Azure Logic Apps, siga estos pasos:

1. En **Nombre de la conexión**, especifique un nombre para la conexión.

1. En **Cuenta de almacenamiento**, seleccione la cuenta de almacenamiento en la que se encuentra el contenedor de blobs. O bien, seleccione **Especificar la información de conexión manualmente** para proporcionar la ruta.

1. Seleccione **Crear** para establecer la conexión.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-connection-create.png" alt-text="Captura de pantalla que muestra el diseñador de flujos de trabajo con un flujo de trabajo de aplicación lógica de Consumo y un aviso para agregar una nueva conexión al paso de Azure Blob Storage.":::

> [!NOTE]
> Después de crear la conexión, si tiene una conexión de Azure Blob Storage existente diferente que quiera usar en su lugar, seleccione **Cambiar conexión** en el editor de detalles del desencadenador o la acción.

Si tiene problemas para conectarse a la cuenta de almacenamiento, vea [cómo acceder a cuentas de almacenamiento detrás de firewalls](#access-storage-accounts-behind-firewalls).

### <a name="standard"></a>[Estándar](#tab/standard)

Para poder configurar el [desencadenador de Blob de Azure](#add-blob-storage-trigger) o la [acción de Blob de Azure](#add-blob-storage-action), debe conectarse a una cuenta de almacenamiento. Una cadena de conexión necesita las propiedades siguientes:

| Propiedad | Obligatorio | Value | Descripción |
|----------|----------|-------|-------------|
| **Nombre de la conexión** | Sí | <*connection-name*> | El nombre que se usa para su conexión. |
| **Cadena de conexión de Azure Blob Storage** | Sí | <*storage-account*> | Seleccione la cuenta de almacenamiento de la lista, o bien proporcione una cadena. <p>**Nota**: Para encontrar la cadena de conexión, vaya a la página de la cuenta de almacenamiento. En el menú de navegación, en **Seguridad y redes**, seleccione **Claves de acceso** > **Mostrar claves**. Copie uno de los dos valores de cadena de conexión disponibles. |
|||||

Para crear una conexión de Azure Blob Storage desde un flujo de trabajo de aplicación lógica en un entorno de inquilino único de Azure Logic Apps, siga estos pasos:

1. En **Nombre de la conexión**, escriba un nombre para la conexión.

1. En **Azure Blob Storage Connection String** (Cadena de conexión de Azure Blob Storage), escriba la cadena de conexión de la cuenta de almacenamiento que quiera usar.

1. Seleccione **Crear** para establecer la conexión.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-connection-create.png" alt-text="Captura de pantalla que muestra el diseñador de flujos de trabajo con un flujo de trabajo de aplicación lógica Estándar y un aviso para agregar una nueva conexión al paso de Azure Blob Storage.":::

> [!NOTE]
> Después de crear la conexión, si tiene una conexión de Azure Blob Storage existente diferente que quiera usar en su lugar, seleccione **Cambiar conexión** en el editor de detalles del desencadenador o la acción.

Si tiene problemas para conectarse a la cuenta de almacenamiento, vea [cómo acceder a cuentas de almacenamiento detrás de firewalls](#access-storage-accounts-behind-firewalls).

---

## <a name="access-storage-accounts-behind-firewalls"></a>Acceso a cuentas de almacenamiento detrás de firewalls

Puede agregar la seguridad de red a una cuenta de Azure Storage si [ restringe el acceso con un firewall y reglas de firewall](../storage/common/storage-network-security.md). Sin embargo, este programa de instalación crea un desafío para Azure y otros servicios de Microsoft que necesitan acceder a la cuenta de almacenamiento. La comunicación local en el centro de datos abstrae las direcciones IP internas, por lo que no se pueden configurar reglas de firewall con restricciones de IP.

Para acceder a las cuentas de almacenamiento detrás de firewalls mediante el conector de Blob Storage:

- [Acceso a las cuentas de almacenamiento de otras regiones](#access-storage-accounts-in-other-regions)
- [Acceso a las cuentas de almacenamiento a través de una red virtual de confianza](#access-storage-accounts-through-trusted-virtual-network)

Otras soluciones para acceder a cuentas de almacenamiento detrás de firewalls:

- [Acceso a las cuentas de almacenamiento como un servicio de confianza con identidades administradas](#access-blob-storage-with-managed-identities)
- [Acceso a las cuentas de almacenamiento a través de Azure API Management](#access-storage-accounts-through-azure-api-management)

### <a name="access-storage-accounts-in-other-regions"></a>Acceso a las cuentas de almacenamiento de otras regiones

Los flujos de trabajo de aplicación lógica no pueden acceder directamente a cuentas de almacenamiento que se encuentren detrás de un firewall cuando están en la misma región. Como solución alternativa, coloque los recursos de aplicación lógica en una región diferente a la de la cuenta de almacenamiento. Después, conceda acceso a las [direcciones IP de salida para los conectores administrados en la región](/connectors/common/outbound-ip-addresses#azure-logic-apps).

> [!NOTE]
> Esta solución no se aplica al conector de Azure Table Storage y al conector de Azure Queue Storage. En su lugar, para acceder a Table Storage o Queue Storage, [use la acción y el desencadenador HTTP integrados](../logic-apps/logic-apps-http-endpoint.md).

Para agregar las direcciones IP salientes al firewall de la cuenta de almacenamiento, siga estos pasos:

1. Anote las [direcciones IP de salida del conector administrado](/connectors/common/outbound-ip-addresses#azure-logic-apps) de la región del recurso de aplicación lógica.

1. En [Azure Portal](https://portal.azure.com), busque y abra su recurso de cuenta de almacenamiento.

1. En el menú de navegación de la cuenta de almacenamiento, en **Seguridad y redes**, seleccione **Redes**.

   1. En **Permitir acceso desde**, seleccione **Redes seleccionadas**, que muestra la configuración pertinente.

   1. En **Firewall**, agregue las direcciones IP o los intervalos que necesitan acceso. Si tiene que acceder a la cuenta de almacenamiento desde el equipo, seleccione **Agregar la dirección IP del cliente**.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-ip-configure.png" alt-text="Captura de pantalla de la página de redes de la cuenta de almacenamiento de blobs en Azure Portal, en la que se muestra la configuración del firewall para agregar direcciones IP e intervalos a la lista de permitidos.":::

   1. Cuando finalice, seleccione **Guardar**.

### <a name="access-storage-accounts-through-trusted-virtual-network"></a>Acceso a las cuentas de almacenamiento desde una red virtual de confianza

Puede colocar la cuenta de almacenamiento en una red virtual de Azure que administra y, a continuación, agregar esa red virtual a la lista de redes virtuales de confianza. Para conceder a la aplicación lógica acceso a la cuenta de almacenamiento por medio de una [red virtual de confianza](../virtual-network/virtual-networks-overview.md), debe implementar esa aplicación lógica en un [entorno de servicio de integración (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que se puede conectar a los recursos de una red virtual. Después puede agregar las subredes de ese ISE a la lista de confianza. Los conectores de Azure Storage, como el conector de Blob Storage, pueden acceder directamente al contenedor de almacenamiento. Este programa de instalación es la misma experiencia que al usar los puntos de conexión de servicio de un ISE.

### <a name="access-storage-accounts-through-azure-api-management"></a>Acceso a las cuentas de almacenamiento a través de Azure API Management

Si usa un nivel dedicado para [API Management](../api-management/api-management-key-concepts.md), puede presentar la API de Azure Storage utilizando API Management y permitiendo las direcciones IP de esta última a través del firewall. Básicamente, agregue la red virtual de Azure que API Management usa a la configuración del firewall de la cuenta de almacenamiento. Después, puede usar la acción API Management o la acción HTTP para llamar a las API de Azure Storage. Aunque si elige esta opción, tendrá que controlar el proceso de autenticación personalmente. Para obtener más información, vea [Arquitectura de integración empresarial sencilla](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="access-blob-storage-with-managed-identities"></a>Acceso a Blob Storage con identidades administradas

Si quiere acceder a Blob Storage sin usar este conector, en su lugar, puede usar [identidades administradas para la autenticación](../active-directory/managed-identities-azure-resources/overview.md). Puede crear una excepción que proporciona a los servicios de confianza de Microsoft, como una identidad administrada, acceso a la cuenta de almacenamiento por medio de un firewall.

Para usar identidades administradas en la aplicación lógica para acceder a Blob Storage, siga estos pasos:

1. [Configure el acceso a la cuenta de almacenamiento](#configure-storage-account-access).

1. [Cree una asignación de roles para la aplicación lógica](#create-role-assignment-logic-app).

1. [Habilite la compatibilidad con la identidad administrada en la aplicación lógica](#enable-managed-identity-support).

> [!NOTE]
> Limitaciones de esta solución:
>
> - *Solo* puede usar el desencadenador o la acción HTTP en el flujo de trabajo.
> - Tendrá que configurar una identidad administrada para autenticar la conexión de la cuenta de almacenamiento.
> - No puede usar operaciones integradas de Blob Storage si se autentica con una identidad administrada.
> - Para las aplicaciones lógicas en un entorno de un solo inquilino, solo está disponible y admitida la identidad administrada asignada por el sistema, no la identidad administrada asignada por el usuario.

### <a name="configure-storage-account-access"></a>Configuración del acceso de la cuenta de almacenamiento

Para configurar la compatibilidad con excepciones e identidades administradas, configure primero el acceso adecuado a la cuenta de almacenamiento:

1. En [Azure Portal](https://portal.azure.com), busque y abra su recurso de cuenta de almacenamiento.

1. En el menú de navegación de la cuenta de almacenamiento, en **Seguridad y redes**, seleccione **Redes**.

   1. En **Permitir acceso desde**, seleccione **Redes seleccionadas**, que muestra la configuración pertinente.

   1. Si tiene que acceder a la cuenta de almacenamiento desde el equipo, en **Firewall**, seleccione **Agregar la dirección IP del cliente**.

   1. En **Excepciones**, habilite **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento**.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-networking-configure.png" alt-text="Captura de pantalla que muestra Azure Portal y el panel de redes de cuentas de Blob Storage con la configuración de permitir.":::

   1. Cuando finalice, seleccione **Guardar**.

> [!NOTE]
> Si recibe un error **403 Prohibido** al intentar conectarse a la cuenta de almacenamiento desde el flujo de trabajo, hay varias causas posibles. Pruebe la resolución siguiente antes de pasar a pasos adicionales. En primer lugar, deshabilite el valor **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** y guarde los cambios. Después, vuelva a habilitar el valor y guarde los cambios de nuevo.

<a name="create-role-assignment-logic-app"></a>

### <a name="create-role-assignment-for-logic-app"></a>Creación de una asignación de roles para la aplicación lógica

A continuación, [habilite la compatibilidad con identidades administradas](../logic-apps/create-managed-service-identity.md) en el recurso de aplicación lógica.

Los pasos siguientes son los mismos para las aplicaciones lógicas de Consumo en entornos multiinquilino y las aplicaciones lógicas Estándar en entornos de inquilino único.

1. En [Azure Portal](https://portal.azure.com), abra el recurso de aplicación lógica.

1. En el menú de navegación del recurso de aplicación lógica, en **Configuración**, seleccione **Identidad**.

1. En el panel **Asignado por el sistema**, establezca **Estado** en **Activado** (puede que ya esté habilitado). En **Permisos**, seleccione **Asignaciones de roles de Azure**.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-1.png" alt-text="Captura de pantalla que muestra Azure Portal y el menú del recurso de aplicación lógica con el panel de configuración &quot;Identidad&quot; y el botón &quot;Azure role assignment permissions&quot; (Permisos de asignación de roles de Azure).":::

1. En el panel **Asignaciones de roles de Azure**, seleccione **Agregar asignación de roles**.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-2.png" alt-text="Captura de pantalla que muestra el panel Asignaciones de roles de la aplicación lógica, con la suscripción seleccionada y el botón para agregar una nueva asignación de roles.":::

1. En el panel **Add role assignments** (Agregar asignaciones de roles), configure la nueva asignación de roles mediante los pasos siguientes:

   1. En **Ámbito**, seleccione **Almacenamiento**.

   1. En **Suscripción**, seleccione la suscripción de la cuenta de almacenamiento.

   1. En **Recurso**, elija la cuenta de almacenamiento a la que quiere acceder desde el flujo de trabajo de aplicación lógica.

   1. En **Rol**, seleccione los permisos adecuados para el escenario.

      En este ejemplo se usa **Colaborador de datos de Storage Blob**, que permite el acceso de lectura, escritura y eliminación a los contenedores de blobs y la fecha. Para ver la información de permisos, mueva el puntero sobre el icono de información situado junto a un rol en el menú desplegable.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-configure.png" alt-text="Captura de pantalla del panel de configuración de asignación de roles, en el que se muestra la configuración del ámbito, la suscripción, el recurso y el rol.":::

   1. Seleccione **Guardar** para finalizar la creación de la asignación de roles.

<a name="enable-managed-identity-support"></a>

### <a name="enable-managed-identity-support-on-logic-app"></a>Habilitación de la compatibilidad con identidades administradas en la aplicación lógica

A continuación, agregue [un desencadenador o una acción HTTP](connectors-native-http.md) al flujo de trabajo. Asegúrese de [establecer el tipo de autenticación para usar la identidad administrada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity).

Los pasos siguientes son los mismos para las aplicaciones lógicas de Consumo en entornos multiinquilino y las aplicaciones lógicas Estándar en entornos de inquilino único.

1. Abra el flujo de trabajo de aplicación lógica en el diseñador.

1. Según su escenario, agregue un desencadenador o una acción **HTTP** al flujo de trabajo. Configure los valores de parámetro necesarios.

   1. Elija un **método** para la solicitud. En este ejemplo se usa el método HTTP **PUT**.

   1. Escriba el **URI** del blob. La ruta es similar a `https://<storage-container-name>/<folder-name>/{name}`. En su lugar, proporcione el nombre del contenedor y de la carpeta, pero mantenga la cadena literal `{name}`.

   1. En **Encabezados**, agregue los siguientes elementos:

      - El encabezado de tipo de blob `x-ms-blob-type` con el valor `BlockBlob`.

      - El encabezado de versión de API `x-ms-version` con el valor adecuado.

      Para más información, consulte [Autenticación del acceso con la identidad administrada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) y [Creación de versiones para los servicios de Azure Storage](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-connect.png" alt-text="Captura de pantalla que muestra el diseñador de flujos de trabajo y el desencadenador o la acción HTTP con los parámetros &quot;PUT&quot; necesarios.":::

1. En **Agregar un nuevo parámetro**, elija **Autenticación** para [configurar la identidad administrada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity).

    1. En **Autenticación**, en la propiedad **Tipo de autenticación**, elija **Identidad administrada**.

    1. En la propiedad **Identidad administrada**, elija **Identidad administrada asignada por el sistema**.

    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-authenticate.png" alt-text="Captura de pantalla que muestra el diseñador de flujos de trabajo y la acción HTTP con la configuración de parámetros de autenticación de identidad administrada.":::

1. Cuando esté listo, seleccione **Guardar** en la barra de herramientas del diseñador.

Ahora, puede llamar a la [API REST Blob service](/rest/api/storageservices/blob-service-rest-api) para ejecutar las operaciones de almacenamiento necesarias.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a los conectores para Azure Logic Apps](apis-list.md)