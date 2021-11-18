---
title: Habilitación de la identidad administrada en temas y dominios personalizados de Azure Event Grid
description: En este artículo se describe cómo habilitar una identidad de servicio administrada de un tema o un dominio personalizados de Azure Event Grid.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 1f9cae4c25c37d0fcb670804ae3450871dbd5259
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156586"
---
# <a name="assign-a-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Asignación de una identidad administrada a un tema o un dominio personalizado de Event Grid 
En este artículo se muestra cómo usar Azure Portal y la CLI para asignar una [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md) asignada por el sistema o por el usuario a un tema o un dominio personalizado de Event Grid. 

## <a name="enable-identity-when-creating-a-topic-or-domain"></a>Habilitación de la identidad al crear un tema o un dominio

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)
En **Azure Portal**, al crear un tema o un dominio, puede asignar una identidad asignada por el sistema o dos identidades asignadas por el usuario, pero no ambos tipos de identidades. Una vez creado el tema o dominio, puede asignar ambos tipos de identidades siguiendo los pasos descritos en la sección [Habilitación de la identidad para un tema o dominio existente](#enable-identity-for-an-existing-custom-topic-or-domain).

### <a name="enable-system-assigned-identity"></a>Activar una identidad asignada por el sistema
En la pestaña **Opciones avanzadas** del Asistente para la creación de temas o dominios, seleccione **Habilitar la identidad asignada por el sistema**. 

:::image type="content" source="./media/managed-service-identity/create-topic-identity.png" alt-text="Imagen que muestra la opción Habilitar identidad asignada por el sistema seleccionada.":::

### <a name="enable-user-assigned-identity"></a>Habilitar la identidad asignada por el usuario
1. En la página **Opciones avanzadas** del Asistente para la creación de temas o dominios, seleccione **Habilitar la identidad asignada por el usuario** y, a continuación, seleccione **Agregar identidad asignada por el usuario**. 

    :::image type="content" source="./media/managed-service-identity/create-page-add-user-assigned-identity-link.png" alt-text="Imagen que muestra la opción Habilitar la identidad asignada por el usuario seleccionada.":::
1. En la ventana **Seleccionar una identidad asignada por el usuario**, seleccione la suscripción que tiene la identidad asignada por l usuario, seleccione la **identidad asignada por usuario** y haga clic en **Seleccionar**. 

# <a name="azure-cli"></a>[CLI de Azure](#tab/cli)
También puede usar la CLI de Azure para crear un tema o un dominio personalizados con una identidad asignada por el sistema. Actualmente, la CLI de Azure no admite la asignación de una identidad administrada asignada por el usuario a un tema o dominio.  

Use el comando `az eventgrid topic create` con el parámetro `--identity` establecido en `systemassigned`. Si no especifica un valor para este parámetro, se utiliza el valor predeterminado `noidentity`. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Del mismo modo, puede usar el comando `az eventgrid domain create` para crear un dominio con una identidad asignada por el sistema.

---

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Habilitación de una identidad para un tema o dominio personalizados existentes
En esta sección, aprenderá a habilitar una identidad asignada por el sistema o una identidad asignada por el usuario para un tema o un dominio personalizado existente. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)
Si usa Azure Portal, puede asignar una identidad administrada asignada por el sistema y hasta dos identidades administradas asignadas por el usuario a un tema existente o un dominio.

Los procedimientos siguientes muestran cómo habilitar una identidad para un tema personalizado. Los pasos para habilitar una identidad para un dominio son similares. 

1. Vaya a [Azure Portal](https://portal.azure.com).
2. Busque **temas de Event Grid** en la barra de búsqueda de la parte superior.
3. Seleccione el **tema personalizado** para el que quiere habilitar la identidad administrada. 
4. Seleccione **Identidad** en el menú de la izquierda.

### <a name="to-assign-a-system-assigned-identity-to-a-topic"></a>Para asignar una identidad asignada por el sistema a un tema
1. En la pestaña **Asignada por el sistema**, **active** el conmutador para habilitar la identidad. 
1. Seleccione **Guardar** en la barra de herramientas para guardar la configuración. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Página de identidad para un tema personalizado"::: 

### <a name="to-assign-a-user-assigned-identity-to-a-topic"></a>Para asignar una identidad asignada por el usuario a un tema
1. Cee una identidad asignada por el usuario siguiendo las instrucciones del artículo [Administración de identidades administradas asignadas por el usuario](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md). 
1. En la página **Identidad**, cambie a la pestaña **Asignada por el usuario** del panel derecho y seleccione **+ Agregar** en la barra de herramientas.

    :::image type="content" source="./media/managed-service-identity/user-assigned-identity-add-button.png" alt-text="Imagen que muestra la pestaña Identidad asignada por el usuario.":::     
1. En la ventana para **agregar una identidad administrada por el usuario**, siga estos pasos:
    1. Seleccione la **Suscripción de Azure** que tiene la identidad por el usuario. 
    1. Seleccione la **identidad asignada por el usuario**. 
    1. Seleccione **Agregar**. 
1. Actualice la lista en la pestaña **Asignada por el usuario** para ver la identidad asignada por el usuario que ha agregado.

Puede usar pasos similares para habilitar una identidad de un dominio de Event Grid.

# <a name="azure-cli"></a>[CLI de Azure](#tab/cli)
También puede usar la CLI de Azure para asignar una identidad administrada asignada por el sistema a un dominio o tema personalizado existente. Actualmente, la CLI de Azure no admite la asignación de una identidad administrada asignada por el usuario a un tema o dominio.

Use el comando `az eventgrid topic update` con `--identity` establecido en `systemassigned` para habilitar la identidad asignada por el sistema para un tema personalizado existente. Si desea deshabilitar la identidad, especifique `noidentity` como valor. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

El comando para actualizar un dominio existente es similar (`az eventgrid domain update`).

---

## <a name="next-steps"></a>Pasos siguientes
Agregue la identidad a un rol adecuado (por ejemplo, Remitente de los datos de Service Bus) en el destino (por ejemplo, una cola de Service Bus). Para los pasos detallados, consulte [Concesión a la identidad administrada el acceso al destino de Event Grid](add-identity-roles.md). 
