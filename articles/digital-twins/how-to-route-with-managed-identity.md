---
title: Enrutamiento de eventos con una identidad administrada
titleSuffix: Azure Digital Twins
description: Vea cómo habilitar una identidad asignada por el sistema para Azure Digital Twins y úsela para reenviar eventos mediante Azure Portal o la CLI.
author: baanders
ms.author: baanders
ms.date: 6/15/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: subject-rbac-steps, contperf-fy21q4
ms.openlocfilehash: 4d50c40426d5fb687b28a965b9d921ef6fc4df38
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651928"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events"></a>Habilitación de una identidad administrada para el enrutamiento de eventos de Azure Digital Twins

En este artículo se describe cómo habilitar una [identidad asignada por el sistema para una instancia de Azure Digital Twins](concepts-security.md#managed-identity-for-accessing-other-resources) y usar la identidad al reenviar eventos a destinos de enrutamiento admitidos. No es necesario configurar una identidad administrada para el enrutamiento, pero puede ayudar a la instancia a acceder fácilmente a otros recursos protegidos por Azure AD, como [Event Hubs](../event-hubs/event-hubs-about.md), destinos de [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)  y [Azure Storage Container](../storage/blobs/storage-blobs-introduction.md).

Estos son los pasos que se describen en este artículo: 

1. Creación una instancia de Azure Digital Twins con una identidad asignada por el sistema o habilitación de la identidad asignada por el sistema en una instancia existente de Azure Digital Twins. 
1. Adición de un rol adecuado a la identidad. Por ejemplo, asigne el rol **Emisor de datos de Centro de eventos de Azure** a la identidad si el punto de conexión es Centro de eventos, o bien el **rol Emisor de datos de Azure Service Bus** si es Service Bus.
1. Cree un punto de conexión en Azure Digital Twins que pueda usar identidades asignadas por el sistema para la autenticación.

## <a name="enable-system-managed-identity-for-the-instance"></a>Habilitación de la identidad administrada por el sistema para la instancia 

Cuando se habilita una identidad asignada por el sistema en la instancia de Azure Digital Twins, Azure crea automáticamente una identidad para ella en [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Esa identidad se puede usar para autenticarse en los puntos de conexión de Azure Digital Twins para el reenvío de eventos.

Puede activar identidades administradas por el sistema para una instancia de Azure Digital Twins **como parte de la configuración inicial de la instancia**, o bien **habilitarlas más adelante en una instancia que ya exista**.

Cualquiera de estos métodos de creación proporcionará las mismas opciones de configuración y el mismo resultado final para la instancia. En esta sección se describe cómo realizar ambos.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Adición de una identidad administrada por el sistema durante la creación de la instancia

En esta sección, aprenderá a habilitar una identidad administrada por el sistema para una instancia de Azure Digital Twins mientras se crea la instancia. Puede habilitar la identidad si va a crear la instancia tanto con [Azure Portal](https://portal.azure.com) como con la [CLI de Azure](/cli/azure/what-is-azure-cli). Use las pestañas siguientes para seleccionar las instrucciones de la experiencia que prefiera.

# <a name="portal"></a>[Portal](#tab/portal) 

Para agregar una identidad administrada durante la creación de la instancia en el portal, comience por [crear una instancia como lo haría normalmente](how-to-set-up-instance-portal.md).

La opción de identidad administrada por el sistema se encuentra en la pestaña **Avanzado** de configuración de la instancia.

En esta pestaña, seleccione la opción **Activar** para **Identidad administrada por el sistema** a fin de activar esta característica.

:::image type="content" source="media/how-to-route-with-managed-identity/create-instance-advanced.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la pestaña Avanzado del cuadro de diálogo Crear recurso para Azure Digital Twins. La identidad administrada por el sistema está activada.":::

Después, puede usar los botones de navegación inferiores para continuar con el resto de la configuración de la instancia.
   
# <a name="cli"></a>[CLI](#tab/cli)

En la CLI, puede agregar un parámetro `--assign-identity` al comando `az dt create` que se usa para crear la instancia. (Para más información acerca de este comando, consulte la [documentación de referencia](/cli/azure/dt#az_dt_create) o las [instrucciones generales para configurar una instancia de Azure Digital Twins](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Para crear una instancia con una identidad administrada por el sistema, agregue el parámetro `--assign-identity` de la siguiente manera:

```azurecli-interactive
az dt create --dt-name <new-instance-name> --resource-group <resource-group> --assign-identity
```

---

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Adición de una identidad administrada por el sistema a una instancia existente

En esta sección, agregará una identidad administrada por el sistema a una instancia de Azure Digital Twins que ya existe. Use las pestañas siguientes para seleccionar las instrucciones de la experiencia que prefiera.

# <a name="portal"></a>[Portal](#tab/portal) 

Pare empezar, abra [Azure Portal](https://portal.azure.com) en el explorador.

1. Busque el nombre de la instancia en la barra de búsqueda del portal y selecciónela para ver sus detalles.

1. Seleccione **Identidad** en el menú de la izquierda.

1. En esta página, seleccione la opción **Activar** para activar esta característica.

1. Seleccione el botón **Guardar** y **Sí** para confirmar.

    :::image type="content" source="media/how-to-route-with-managed-identity/identity-digital-twins.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la página Identidad de una instancia de Azure Digital Twins.":::

Una vez que se haya guardado el cambio, aparecerán más campos en esta página para el **Id. de objeto** y los **Permisos** de la nueva identidad.

Puede copiar el **Id. de objeto** desde aquí si es necesario y usar el botón **Permisos** para ver los roles de Azure que están asignados a la identidad. Para configurar algunos roles, vaya a la sección siguiente.

# <a name="cli"></a>[CLI](#tab/cli)

Una vez más, puede agregar la identidad a la instancia mediante el comando `az dt create` y el parámetro `--assign-identity`. En lugar de proporcionar un nuevo nombre para la instancia que va a crear, puede proporcionar el nombre de una instancia de que ya existe para actualizar el valor de `--assign-identity` para esa instancia.

El comando para **habilitar** la identidad administrada es el mismo que el comando para crear una instancia con una identidad administrada por el sistema. Lo único que cambia es el valor del parámetro de nombre de la instancia:

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity
```

Para **deshabilitar** la identidad administrada en una instancia en la que está habilitada actualmente, use el siguiente comando parecido para establecer `--assign-identity` en `false`.

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity false
```

---

## <a name="assign-azure-roles-to-the-identity"></a>Asignación de roles de Azure a la identidad 

Una vez que se haya creado una identidad asignada por el sistema para la instancia de Azure Digital Twins, tendrá que asignarle los roles adecuados para que se autentique con distintos tipos de [puntos de conexión ](concepts-route-events.md) a fin de reenviar eventos a los destinos admitidos. En esta sección se describen las opciones de rol y cómo asignarlos a la identidad asignada por el sistema.

>[!NOTE]
> Este es un paso importante; sin él, la identidad no podrá acceder a los puntos de conexión y los eventos no se entregarán.

### <a name="supported-destinations-and-azure-roles"></a>Destinos admitidos y roles de Azure 

Estos son los roles mínimos que necesita una identidad para acceder a un punto de conexión, en función del tipo de destino. Los roles con permisos más altos (como los de propietario de datos) también funcionarán.

| Destination | Rol de Azure |
| --- | --- |
| Azure Event Hubs | Emisor de datos de Azure Event Hubs |
| Azure Service Bus | Emisor de datos de Azure Service Bus |
| Contenedor de almacenamiento de Windows Azure | Colaborador de datos de blobs de almacenamiento |

Para más información sobre los puntos de conexión, las rutas y los tipos de destinos que compatibles con el enrutamiento en Azure Digital Twins, consulte [Rutas de eventos](concepts-route-events.md).

### <a name="assign-the-role"></a>Asignación del rol

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Use las pestañas siguientes para seleccionar las instrucciones de la experiencia que prefiera.

# <a name="portal"></a>[Portal](#tab/portal) 

Para asignar un rol a la identidad, empiece por abrir [Azure Portal](https://portal.azure.com) en un explorador.

1. Navegue hasta el recurso de punto de conexión (el centro de eventos, el tema de Service Bus o el contenedor de almacenamiento); para ello, busque su nombre en la barra de búsqueda del portal. 

1. Seleccione **Access Control (IAM)** .

1. Seleccione **Agregar** > **Agregar asignación de roles** para abrir la página Agregar asignación de roles.

1. Asigne el rol deseado a la identidad administrada de la instancia de Azure Digital Twins, con la siguiente información. Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).
    
    | Configuración | Valor |
    | --- | --- |
    | Role | seleccione el rol deseado en el menú desplegable. |
    | Asignar acceso a | En **Identidad administrada asignada por el sistema**, seleccione **Digital Twins**. |
    | Miembros | Seleccione la identidad administrada de la instancia de Azure Digital Twins a la que se asigna el rol. El nombre de la identidad administrada coincide con el de la instancia, por lo que debe elegir el nombre de la instancia de Azure Digital Twins. |
    
    ![Página Agregar asignación de roles](../../includes/role-based-access-control/media/add-role-assignment-page.png)

# <a name="cli"></a>[CLI](#tab/cli)

Puede agregar el parámetro `--scopes` al comando `az dt create` para asignar la identidad a uno o varios ámbitos con un rol especificado. El comando con este parámetro se puede usar tanto cuando se crea la instancia como después pasando el nombre de una instancia que ya existe.

En este ejemplo se crea una instancia con una identidad administrada por el sistema y se asigna esa identidad a un rol personalizado denominado `MyCustomRole` de un centro de eventos.

```azurecli-interactive
az dt create --dt-name <instance-name> --resource-group <resource-group> --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource-group>/providers/Microsoft.EventHub/namespaces/<Event-Hubs-namespace>/eventhubs/<event-hub-name>" --role MyCustomRole
```

Para obtener más ejemplos de asignaciones de roles con este comando, consulte la documentación de referencia del comando [az dt create](/cli/azure/dt#az_dt_create).

También puede usar el grupo de comandos [az role assignment](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true) para crear y administrar roles. Este comando se puede usar para dar soporte técnico a otros escenarios en los que no desea agrupar la asignación de roles con el comando create.

---

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Creación de un punto de conexión con autenticación basada en identidades

Después de configurar una identidad administrada por el sistema para la instancia de Azure Digital Twins y asignarle los roles adecuados, puede crear [puntos de conexión](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins) de Azure Digital Twins que puedan usar la identidad para la autenticación. Esta opción solo está disponible para los puntos de conexión de centro de eventos y de tipo Service Bus (no se admite para Event Grid).

>[!NOTE]
> No se puede editar un punto de conexión que ya se haya creado con la identidad basada en claves para cambiar a la autenticación basada en identidades. Debe elegir el tipo de autenticación al crear el punto de conexión por primera vez.

Use las pestañas siguientes para seleccionar las instrucciones de la experiencia que prefiera.

# <a name="portal"></a>[Portal](#tab/portal) 

Para empezar, siga las [instrucciones para crear un punto de conexión de Azure Digital Twins](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins).

Cuando llegue al paso de completar los detalles necesarios para el tipo de punto de conexión, asegúrese de seleccionar **Basado en identidades** para el tipo de autenticación.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="Captura de pantalla de la creación de un punto de conexión de tipo de centro de eventos." lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Termine de configurar el punto de conexión y seleccione **Guardar**.

# <a name="cli"></a>[CLI](#tab/cli)

Para crear el punto de conexión con la CLI, se agrega un parámetro `--auth-type` al comando `az dt endpoint create` que se usa para crear el punto de conexión (para más información acerca de este comando, consulte la [documentación de referencia](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) o las [instrucciones generales para configurar un punto de conexión de Azure Digital Twins](how-to-manage-routes.md#create-the-endpoint)).

Para crear un punto de conexión que use la autenticación basada en identidades, especifique el tipo de autenticación `IdentityBased` con el parámetro `--auth-type`. En el ejemplo siguiente se muestra esta funcionalidad para un punto de conexión de Event Hubs.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <endpoint-name> --eventhub-resource-group <eventhub-resource-group> --eventhub-namespace <eventhub-namespace> --eventhub <eventhub-name> --auth-type IdentityBased --dt-name <instance-name>
```

---

## <a name="considerations-for-disabling-system-managed-identities"></a>Consideraciones para deshabilitar las identidades administradas por el sistema

Como una identidad se administra de forma independiente a los puntos de conexión que la usan, es importante tener en cuenta los efectos que cualquier cambio en la identidad o sus roles puede tener en los puntos de conexión de la instancia de Azure Digital Twins. Si la identidad está deshabilitada o se quita un rol necesario para un punto de conexión, este puede dejar de estar accesible y se interrumpirá el flujo de eventos.

Para seguir usando un punto de conexión que se ha configurado con una identidad administrada que ahora se ha deshabilitado, tendrá que eliminar el punto de conexión y [volver a crearlo](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins) con otro tipo de autenticación. Los eventos pueden tardar hasta una hora en reanudar la entrega al punto de conexión después de este cambio.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las identidades administradas en Azure AD: 
* [Identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md)