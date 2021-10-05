---
title: Conexión con Azure Blob Storage
description: Cree y administre blobs en cuentas de Azure Storage con Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 06/23/2021
tags: connectors
ms.openlocfilehash: 7fc6b33248af8b638218858c95d1c0de8b056e76
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824868"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Creación y administración de blobs en Azure Blob Storage con Azure Logic Apps

Puede acceder y administrar los archivos almacenados como blobs en una cuenta de Azure Storage desde dentro de Azure Logic Apps con el [conector de Azure Blob Storage](/connectors/azureblobconnector/). Este conector proporciona desencadenadores y acciones para las operaciones de blob dentro de los flujos de trabajo de la aplicación lógica. Puede usar estas operaciones a fin de automatizar tareas y flujos de trabajo para administrar los archivos de la cuenta de almacenamiento. Las [acciones del conector disponibles](/connectors/azureblobconnector/#actions) incluyen la comprobación, eliminación, lectura y carga de blobs. El [desencadenador disponible](/connectors/azureblobconnector/#triggers) se activa cuando se agrega o modifica un blob.

Puede conectarse a Blob Storage desde los tipos de recurso de aplicación lógica Estándar y Consumo. Puede usar el conector con aplicaciones lógicas en un entorno de un solo inquilino, de varios inquilinos o de servicio de integración (ISE). Para las aplicaciones lógicas en un entorno de un solo inquilino, Blob Storage proporciona operaciones integradas y también operaciones de conector administradas.

> [!NOTE]
> En el caso de las aplicaciones lógicas de un [entorno de servicio de integración (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la versión con la etiqueta ISE de este conector usa en su lugar los [límites de mensajes de ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits).

Para obtener más información técnica sobre este conector, como los desencadenadores, las acciones y los límites, vea la [página de referencia del conector](/connectors/azureblobconnector/).

También puede [usar una identidad administrada con un desencadenador o una acción HTTP para realizar operaciones de blob](#access-blob-storage-with-managed-identities) en lugar del conector de Blob Storage.

> [!IMPORTANT]
> Las aplicaciones lógicas no pueden acceder directamente a cuentas de almacenamiento que se encuentren detrás de un firewall si se encuentran en la misma región. Como alternativa, puede tener las aplicaciones lógicas y la cuenta de almacenamiento en diferentes regiones. Para más información sobre cómo habilitar el acceso desde Azure Logic Apps a cuentas de almacenamiento que se encuentran detrás de los firewalls, consulte la sección [Acceso a cuentas de almacenamiento detrás de firewalls](#access-storage-accounts-behind-firewalls) más adelante en este tema.

## <a name="prerequisites"></a>Requisitos previos

- Suscripción a Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/).
- Una [cuenta de Azure Storage y un contenedor de almacenamiento](../storage/blobs/storage-quickstart-blobs-portal.md)
- Un flujo de trabajo desde el que quiera acceder a la cuenta de Blob Storage. Si quiere iniciar la aplicación lógica con un desencadenador de Blob Storage, necesita una [aplicación lógica en blanco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limits"></a>Límites

- De forma predeterminada, las acciones de Blob Storage pueden leer o escribir archivos de *50 MB o más pequeños*. Para controlar los archivos mayores de 50 MB, pero hasta 1024 MB, las acciones de Blob Storage admiten la [fragmentación de mensajes](../logic-apps/logic-apps-handle-large-messages.md). En la [acción **Obtener contenido de blog**](/connectors/azureblobconnector/#get-blob-content) se usa la fragmentación de forma implícita.
- Los desencadenadores de Blob Storage no admiten la fragmentación. Cuando se solicita el contenido del archivo, los desencadenadores seleccionan solo los archivos que tienen un tamaño de 50 MB o menos. Para obtener archivos de más de 50 MB, siga este patrón:
  - Use un desencadenador de Blob Storage que devuelva propiedades de archivo, como [**Cuando se agrega o modifica un blob (solo propiedades)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)).
  - Siga el desencadenador con la [acción **Obtener contenido de blob**](/connectors/azureblobconnector/#get-blob-content) de Blob Storage, que lee el archivo completo y usa la fragmentación de forma implícita.

## <a name="add-blob-storage-trigger"></a>Adición de un desencadenador de Blob Storage

En Logic Apps, cada aplicación lógica debe comenzar con un [desencadenador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que se activa cuando tiene lugar un evento específico o cuando se cumple una condición determinada. 

Este conector tiene un desencadenador disponible, denominado [**Cuando se agrega o modifica un blob en Azure Storage** o **Cuando se agrega o modifica un blob (solo propiedades)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)). El desencadenador se activa cuando las propiedades de un blob se agregan o actualizan en el contenedor de almacenamiento. Cada vez, el motor de Logic Apps crea una instancia de aplicación lógica y empieza a ejecutar el flujo de trabajo.

### <a name="single-tenant"></a>[Inquilino único](#tab/single-tenant)

Para agregar una acción de Blob Storage en una aplicación lógica de un solo inquilino que usa un plan Estándar:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Abra el flujo de trabajo en el diseñador.
1. En el cuadro de búsqueda, escriba `Azure blob` como filtro. En la lista de desencadenadores, seleccione el desencadenador denominado **Cuando se agrega o modifica un blob en Azure Storage**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-add.png" alt-text="Captura de pantalla de la aplicación lógica Estándar en el diseñador, en la que se muestra la selección del desencadenador denominado Cuando se agrega o modifica un blob en Azure Storage.":::
1. Si se le piden los detalles de la conexión, [cree ahora su conexión de Blob Storage](#connect-to-storage-account).
1. Proporcione la información necesaria para el desencadenador.
    1. En la pestaña **Parámetros** agregue la **Ruta de acceso del blob** al blob que quiera supervisar.
        Para buscar la ruta del blob, abra la cuenta de almacenamiento en Azure Portal. En el menú de navegación, en **Almacenamiento de datos**, seleccione **Contenedores**. Seleccione el contenedor de blobs. En el menú de navegación del contenedor, en **Configuración**, seleccione **Propiedades**. Copie el valor **URL**, que es la ruta al blob. La ruta es similar a `https://{your-storage-account}.blob.core.windows.net/{your-blob}`.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-configure.png" alt-text="Captura de pantalla de la aplicación lógica Estándar en el diseñador, en la que se muestra la configuración de parámetros para el desencadenador de Blob Storage.":::
    1. Configure otros valores del desencadenador según corresponda.
    1. Seleccione **Listo**.
1. Agregue una o varias acciones al flujo de trabajo.
1. En la barra de herramientas del diseñador, seleccione **Guardar** para guardar los cambios.

### <a name="multi-tenant"></a>[Multiinquilino](#tab/multi-tenant)

Para agregar una acción de Blob Storage en una aplicación lógica multiinquilino que usa un plan de consumo:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Abra el flujo de trabajo en el Diseñador de Logic Apps.
1. En el cuadro de búsqueda, escriba "Azure blob" como filtro. En la lista de desencadenadores, seleccione el desencadenador denominado **Cuando se agrega o modifica un blob (solo propiedades)** .
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-add.png" alt-text="Captura de pantalla de la aplicación lógica de consumo en el diseñador, en la que se muestra la selección del desencadenador de Blob Storage.":::
1. Si se le piden los detalles de la conexión, [cree ahora su conexión de Blob Storage](#connect-to-storage-account).
1. Proporcione la información necesaria para el desencadenador.
    1. En **Contenedor**, seleccione el icono de carpeta para elegir el contenedor de Blob Storage. O bien, escriba la ruta manualmente.
    1. Configure otros valores del desencadenador según corresponda.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-configure.png" alt-text="Captura de pantalla de la aplicación lógica Consumo en el diseñador, en la que se muestra la configuración de parámetros para el desencadenador de Blob Storage.":::
1. Agregue una o varias acciones al flujo de trabajo.
1. En la barra de herramientas del diseñador, seleccione **Guardar** para guardar los cambios.

---

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Adición de un acción de Blob Storage

En Logic Apps, una [acción](../logic-apps/logic-apps-overview.md#logic-app-concepts) es un paso del flujo de trabajo que sigue a un desencadenador u otra acción.

### <a name="single-tenant"></a>[Inquilino único](#tab/single-tenant)

Para aplicaciones lógicas en un entorno de un solo inquilino:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Abra el flujo de trabajo en el Diseñador de Logic Apps.
1. Agregue un desencadenador. Este ejemplo comienza con el [desencadenador **Periodicidad**](../connectors/connectors-native-recurrence.md).
1. Agregue un nuevo paso al flujo de trabajo. En el cuadro de búsqueda, escriba "Azure blob" como filtro. Después, seleccione la acción de Blob Storage que quiera usar. En este ejemplo se usa **Lee el contenido del blob de Azure Storage**. 
   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-add.png" alt-text="Captura de pantalla de la aplicación lógica Estándar en el diseñador, en la que se muestra la lista de acciones de Blob Storage disponibles.":::
1. Si se le piden los detalles de la conexión, [cree una conexión a la cuenta de Blob Storage](#connect-to-storage-account).
1. Proporcione la información necesaria para la acción.
    1. En **Nombre del contenedor**, escriba la ruta del contenedor de blobs que quiera usar.
    1. En **Nombre del blob**, escriba la ruta de blob que quiera usar.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-configure.png" alt-text="Captura de pantalla de la aplicación lógica Estándar en el diseñador, en la que se muestra la selección del desencadenador de Blob Storage.":::
    1. Configure otros valores de acción según corresponda.
1. En la barra de herramientas del diseñador, seleccione **Save** (Guardar). 
1. Pruebe la aplicación lógica para asegurarse de que el contenedor seleccionado contiene un blob. 

> [!TIP]
> En este ejemplo solo se lee el contenido de un blob. Para ver dicho contenido, agregue otra acción que cree un archivo con el blob mediante otro conector. Por ejemplo, agregue una acción de OneDrive que cree un archivo basándose en el contenido del blob.

### <a name="multi-tenant"></a>[Multiinquilino](#tab/multi-tenant)

Para aplicaciones lógicas en un entorno multiinquilino:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Abra el flujo de trabajo en el Diseñador de Logic Apps.
1. Agregue un desencadenador. Este ejemplo comienza con el [desencadenador **Periodicidad**](../connectors/connectors-native-recurrence.md).
1. Agregue un nuevo paso al flujo de trabajo. En el cuadro de búsqueda, escriba "Azure blob" como filtro. Después, seleccione la acción de Blob Storage que quiera usar. En este ejemplo se usa **Obtener contenido de blob**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-add.png" alt-text="Captura de pantalla de la aplicación lógica Consumo en el diseñador, en la que se muestra la lista de acciones de Blob Storage disponibles.":::
1. Si se le piden los detalles de la conexión, [cree una conexión a la cuenta de Blob Storage](#connect-to-storage-account).
1. Proporcione la información necesaria para la acción.
    1. En **Blob**, seleccione el icono de carpeta para elegir el contenedor de Blob Storage. O bien, escriba la ruta manualmente.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-configure.png" alt-text="Captura de pantalla de la aplicación lógica de consumo en el diseñador, en la que se muestra la configuración de la acción de Blob Storage.":::
    1. Configure otros valores de acción según corresponda.

---

## <a name="connect-to-storage-account"></a>Conectar con la cuenta de almacenamiento

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Para poder configurar el [desencadenador de Blob Storage](#add-blob-storage-trigger) o la [acción de Blob Storage](#add-blob-storage-action), debe conectarse a una cuenta de almacenamiento. Una cadena de conexión necesita las propiedades siguientes.

| Propiedad | Obligatorio | Value | Descripción |
|----------|----------|-------|-------------|
| **Nombre de la conexión** | Sí | <*connection-name*> | El nombre que se va a crear para su conexión |
| **Cadena de conexión de Azure Blob Storage** | Sí | <*storage-account*> | Seleccione la cuenta de almacenamiento de la lista, o bien proporcione una cadena. |

> [!TIP]
> Para buscar una cadena de conexión, vaya a la página de la cuenta de almacenamiento. En el menú de navegación, en **Seguridad y redes**, seleccione **Claves de acceso**. Seleccione **Mostrar claves**. Copie uno de los dos valores de cadena de conexión disponibles.

### <a name="single-tenant"></a>[Inquilino único](#tab/single-tenant)

Para aplicaciones lógicas en un entorno de un solo inquilino:

1. En **Nombre de la conexión**, escriba un nombre para la conexión.
1. En **Azure Blob Storage Connection String** (Cadena de conexión de Azure Blob Storage), escriba la cadena de conexión de la cuenta de almacenamiento que quiera usar.
1. Seleccione **Crear** para establecer la conexión.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-connection-create.png" alt-text="Captura de pantalla de la aplicación lógica Estándar en el diseñador, en la que se muestra el mensaje para agregar una nueva conexión a un paso de almacenamiento de blobs.":::

Si ya tiene una conexión existente, pero quiere elegir otra, seleccione **Cambiar conexión** en el editor del paso.

Si tiene problemas para conectarse a la cuenta de almacenamiento, vea [cómo acceder a cuentas de almacenamiento detrás de firewalls](#access-storage-accounts-behind-firewalls).

### <a name="multi-tenant"></a>[Multiinquilino](#tab/multi-tenant)

Para aplicaciones lógicas en un entorno multiinquilino:

1. En **Nombre de la conexión**, escriba un nombre para la conexión.
1. En **Cuenta de almacenamiento**, seleccione la cuenta de almacenamiento en la que se encuentra el contenedor de blobs. O bien, seleccione **Especificar la información de conexión manualmente** para proporcionar la ruta.
1. Seleccione **Crear** para establecer la conexión.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-connection-create.png" alt-text="Captura de pantalla de la aplicación lógica Consumo en el diseñador, en la que se muestra el mensaje para agregar una nueva conexión a un paso de almacenamiento de blobs.":::

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

Las aplicaciones lógicas no pueden acceder directamente a cuentas de almacenamiento que se encuentren detrás de un firewall cuando se encuentran en la misma región. Como solución alternativa, coloque las aplicaciones lógicas en una región diferente a la de la cuenta de almacenamiento. Después, conceda acceso a las [direcciones IP de salida para los conectores administrados en la región](/connectors/common/outbound-ip-addresses#azure-logic-apps).

> [!NOTE]
> Esta solución no se aplica al conector de Azure Table Storage y al conector de Azure Queue Storage. En su lugar, para acceder a Table Storage o Queue Storage, [use la acción y el desencadenador HTTP integrados](../logic-apps/logic-apps-http-endpoint.md).

Para agregar las direcciones IP salientes al firewall de la cuenta de almacenamiento:

1. Anote las [direcciones IP de salida del conector administrado](/connectors/common/outbound-ip-addresses#azure-logic-apps) de la región de la aplicación lógica.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y busque el recurso de cuenta de almacenamiento.

1. En el menú del recurso de cuenta de almacenamiento, en **Seguridad y redes**, seleccione **Redes**.

1. Haga clic en **Redes seleccionadas** en **Permitir el acceso desde**. Ahora la configuración relacionada aparece en la página.

1. En **Firewall**, agregue las direcciones IP o los intervalos que necesitan acceso.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-ip-configure.png" alt-text="Captura de pantalla de la página de redes de la cuenta de almacenamiento de blobs en Azure Portal, en la que se muestra la configuración del firewall para agregar direcciones IP e intervalos a la lista de permitidos.":::

### <a name="access-storage-accounts-through-trusted-virtual-network"></a>Acceso a las cuentas de almacenamiento desde una red virtual de confianza

Puede colocar la cuenta de almacenamiento en una red virtual de Azure que administra y, a continuación, agregar esa red virtual a la lista de redes virtuales de confianza. Para conceder a la aplicación lógica acceso a la cuenta de almacenamiento por medio de una [red virtual de confianza](../virtual-network/virtual-networks-overview.md), debe implementar esa aplicación lógica en un [entorno de servicio de integración (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que se puede conectar a los recursos de una red virtual. Después puede agregar las subredes de ese ISE a la lista de confianza. Los conectores de Azure Storage, como el conector de Blob Storage, pueden acceder directamente al contenedor de almacenamiento. Este programa de instalación es la misma experiencia que al usar los puntos de conexión de servicio de un ISE.

### <a name="access-storage-accounts-through-azure-api-management"></a>Acceso a las cuentas de almacenamiento a través de Azure API Management

Si usa un nivel dedicado para [API Management](../api-management/api-management-key-concepts.md), puede presentar la API de Azure Storage utilizando API Management y permitiendo las direcciones IP de esta última a través del firewall. Básicamente, agregue la red virtual de Azure que API Management usa a la configuración del firewall de la cuenta de almacenamiento. Después, puede usar la acción API Management o la acción HTTP para llamar a las API de Azure Storage. Aunque si elige esta opción, tendrá que controlar el proceso de autenticación personalmente. Para obtener más información, vea [Arquitectura de integración empresarial sencilla](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="access-blob-storage-with-managed-identities"></a>Acceso a Blob Storage con identidades administradas

Si quiere acceder a Blob Storage sin usar este conector de Logic Apps, en su lugar puede usar [identidades administradas para la autenticación](../active-directory/managed-identities-azure-resources/overview.md). Puede crear una excepción que proporciona a los servicios de confianza de Microsoft, como una identidad administrada, acceso a la cuenta de almacenamiento por medio de un firewall.

Para usar identidades administradas en la aplicación lógica a fin de acceder a Blob Storage:

1. [Configure el acceso a la cuenta de almacenamiento](#configure-storage-account-access)
1. [Cree una asignación de roles para la aplicación lógica](#create-role-assignment-for-logic-app)
1. [Habilite la compatibilidad con la identidad administrada en la aplicación lógica](#enable-support-for-managed-identity-in-logic-app)

> [!NOTE]
> Limitaciones de esta solución:
> - *Solo* puede usar el desencadenador o la acción HTTP en el flujo de trabajo.
> - Tendrá que configurar una identidad administrada para autenticar la conexión de la cuenta de almacenamiento.
> - No puede usar operaciones integradas de Blob Storage si se autentica con una identidad administrada.
> - Para las aplicaciones lógicas en un entorno de un solo inquilino, solo está disponible y admitida la identidad administrada asignada por el sistema, no la identidad administrada asignada por el usuario.

### <a name="configure-storage-account-access"></a>Configuración del acceso de la cuenta de almacenamiento

Para configurar la compatibilidad con excepciones e identidades administradas, configure primero el acceso adecuado a la cuenta de almacenamiento:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Abra la página de la cuenta de almacenamiento. En el menú de navegación, en **Seguridad y redes**, seleccione **Redes**. 
1. En **Permitir acceso desde**, seleccione la opción **Redes seleccionadas**. Ahora la configuración relacionada aparece en la página.
1. Si tiene que acceder a la cuenta de almacenamiento desde el equipo, en **Firewall**, habilite **Agregar la dirección IP del cliente**.
1. En **Excepciones**, habilite **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento**. 
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-networking-configure.png" alt-text="Captura de pantalla de la página de redes de la cuenta de almacenamiento de blobs en Azure Portal, en la que se muestran los valores para permitir redes seleccionadas, la dirección IP del cliente y los servicios de Microsoft de confianza.":::
1. Seleccione **Guardar**.

> [!TIP]
> Si recibe un error **403 Prohibido** al intentar conectarse a la cuenta de almacenamiento desde el flujo de trabajo, hay varias causas posibles. Pruebe la resolución siguiente antes de pasar a pasos adicionales. En primer lugar, deshabilite el valor **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** y guarde los cambios. Después, vuelva a habilitar el valor y guarde los cambios de nuevo. 

### <a name="create-role-assignment-for-logic-app"></a>Creación de una asignación de roles para la aplicación lógica

A continuación, [habilite la compatibilidad con identidades administradas](../logic-apps/create-managed-service-identity.md) en la aplicación lógica.

1. Abra la aplicación lógica en Azure Portal.
1. En el menú de navegación, en **Configuración**, seleccione **Identidad**.
1. En **Asignado por el sistema**, establezca **Estado** en **Activado**. Es posible que este valor ya esté habilitado.
1. En **Permisos**, seleccione **Asignaciones de roles de Azure**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-1.png" alt-text="Captura de pantalla del menú de la aplicación lógica en Azure Portal, en la que se muestra el panel de configuración de identidad con el botón para agregar permisos de asignación de roles de Azure.":::
1. En el panel **Asignaciones de roles de Azure**, seleccione **Agregar asignación de roles**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-2.png" alt-text="Captura de pantalla del panel Asignaciones de roles de la aplicación lógica, en el que se muestra la suscripción seleccionada y el botón para agregar una nueva asignación de roles.":::
1. Configure la nueva asignación de roles como se muestra a continuación.
    1. En **Ámbito**, seleccione **Almacenamiento**.
    1. En **Suscripción**, elija la suscripción en la que se encuentra la cuenta de almacenamiento.
    1. En **Recurso**, elija la cuenta de almacenamiento a la que quiere acceder desde la aplicación lógica.
    1. En **Rol**, seleccione los permisos adecuados para el escenario. En este ejemplo se usa **Colaborador de datos de Storage Blob**, que permite el acceso de lectura, escritura y eliminación a los contenedores de blobs y la fecha. Mantenga el puntero sobre el icono de información situado junto a un rol en el menú desplegable para obtener detalles sobre los permisos.
    1. Seleccione **Guardar** para finalizar la creación de la asignación de roles.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-configure.png" alt-text="Captura de pantalla del panel de configuración de asignación de roles, en el que se muestra la configuración del ámbito, la suscripción, el recurso y el rol.":::

### <a name="enable-support-for-managed-identity-in-logic-app"></a>Habilitación de la compatibilidad con la identidad administrada en la aplicación lógica

A continuación, agregue [un desencadenador o una acción HTTP](connectors-native-http.md) al flujo de trabajo. Asegúrese de [establecer el tipo de autenticación para usar la identidad administrada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). 

Los pasos son los mismos para las aplicaciones lógicas en entornos de un solo inquilino y de varios inquilinos.

1. Abra el flujo de trabajo en el Diseñador de Logic Apps.
1. Agregue un nuevo paso al flujo de trabajo con un desencadenador o una acción **HTTP**, en función del escenario.
1. Configure todos los parámetros necesarios para el desencadenador o la acción **HTTP**.
    1. Elija un **Método** para la solicitud. En este ejemplo se usa el método HTTP PUT.
    1. Escriba el **URI** del blob. La ruta es similar a `https://{your-storage-account}.blob.core.windows.net/{your-blob}`.
    1. En **Encabezados**, agregue el encabezado de tipo de blob `x-ms-blob-type` con el valor `BlockBlob`.
    1. En **Encabezados**, agregue también el encabezado de versión de API `x-ms-version` con el valor adecuado. Para más información, consulte [Autenticación del acceso con la identidad administrada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) y [Creación de versiones para los servicios de Azure Storage](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-connect.png" alt-text="Captura de pantalla del diseñador de Logic Apps, en la que se muestran los parámetros de acción HTTP PUT necesarios.":::
1. Seleccione **Agregar un nuevo parámetro** y elija **Autenticación** para [configurar la identidad administrada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity).
    1. En **Autenticación**, en **Tipo de autenticación**, elija **Identidad administrada**.
    1. En **Identidad administrada**, elija **Identidad administrada asignada por el sistema**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-authenticate.png" alt-text="Captura de pantalla del diseñador de aplicaciones lógicas, en la que se muestra la configuración de parámetros de autenticación de acción HTTP para la identidad administrada.":::
1. Seleccione **Guardar** en la barra de herramientas del diseñador de aplicaciones lógicas.

Ahora, puede llamar a la [API REST Blob service](/rest/api/storageservices/blob-service-rest-api) para ejecutar las operaciones de almacenamiento necesarias.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre los conectores de Logic Apps](../connectors/apis-list.md)
