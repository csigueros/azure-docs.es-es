---
title: Información de actualización de la cuenta de Azure Purview
description: Las cuentas de Azure Purview creadas antes del 18 de agosto de 2021 se actualizarán automáticamente a la versión más reciente de Purview. En este artículo se describe lo que cambiará y los pasos siguientes necesarios.
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: template-concept
ms.openlocfilehash: b528e4a4b96e0dbe5f8b2eca748420a23f3c2950
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208912"
---
# <a name="azure-purview-account-upgrade-information"></a>Información de actualización de la cuenta de Azure Purview

Azure Purview ha publicado algunas características nuevas el 18 de agosto de 2021, como el mapa de datos elástico, las colecciones actualizadas y el control de acceso actualizado. Las cuentas de Azure Purview creadas **después** del 18 de agosto de 2021 se crean con todas estas características nuevas ya disponibles.

Las cuentas creadas **antes** del 18 de agosto de 2021 (cuentas heredadas) se actualizan automáticamente para incluir estas características.

Si tiene una cuenta de Purview creada **antes** del 18 de agosto de 2021, recibirá una notificación por correo electrónico cuando se haya actualizado.

En este documento se describen los cambios que verá cuando se actualice la cuenta y los pasos que deba seguir para usar las nuevas características.

## <a name="elastic-data-map"></a>Mapa de datos elástico

Las cuentas actualizadas de Azure Purview usan un nuevo sistema de mapa de datos que se escala dinámicamente en función del uso: el mapa de datos elástico. Los mapas de datos elásticos comienzan en una unidad de capacidad y agregan y reducen las unidades de capacidad según sea necesario.
Esta nueva característica no afecta a cómo interactúa directamente con Purview, pero sí a la facturación. Se escala al tamaño que necesita para administrar el panorama de datos y solo se le factura por lo que usa.

Para obtener más información sobre el nuevo mapa de datos elástico y cómo se factura, vea nuestra [página del mapa de datos elástico](concept-elastic-data-map.md).

Para obtener detalles de facturación de la totalidad de Purview, vea la [calculadora de precios](https://azure.microsoft.com/pricing/details/azure-purview/).

Cuando se actualice la cuenta, no tendrá que realizar ningún cambio para usar el mapa de datos elástico. Se habilita de forma automática.

## <a name="collections"></a>Colecciones

En las cuentas de Purview heredadas existen colecciones, pero tienen nuevas funcionalidades y se administran de una manera diferente en las cuentas de Purview actualizadas.

Las [colecciones heredadas](how-to-create-and-manage-collections.md#legacy-collection-guide) eran una manera de organizar orígenes de datos y artefactos en la cuenta de Purview. Las colecciones se siguen usando para personalizar el mapa de datos de Purview a fin de que coincida con el panorama empresarial, pero ahora también incluyen el control de acceso. En lugar de controlar el acceso en un nivel general fuera del mapa de datos, con las colecciones la experiencia de administración de acceso coincidirá con el mapa de datos.

Las [colecciones](how-to-create-and-manage-collections.md) le dan un control preciso sobre los orígenes de datos, pero también sobre la detectabilidad. Los usuarios solo verán los recursos de las colecciones a las que tienen acceso, por lo que solo verán la información que necesitan.

Cuando se actualice la cuenta de Purview, las colecciones también se actualizarán. Todos los recursos actuales se migrarán a estas nuevas colecciones. En las secciones siguientes, se analizará dónde puede encontrar las colecciones y los recursos existentes.

### <a name="locate-and-manage-collections"></a>Búsqueda y administración de colecciones

Para buscar las colecciones nuevas, comenzará en [Purview Studio](https://web.purview.azure.com/resource/). Para encontrar Studio, vaya al recurso de Purview en [Azure Portal](https://portal.azure.com) y seleccione el icono **Open Purview Studio** (Abrir Purview Studio) en la página de información general.

Seleccione Mapa de datos > Colecciones en el panel de la izquierda para abrir la página de administración de colecciones.

:::image type="content" source="./media/concept-account-upgrade/find-collections.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la pestaña Colecciones seleccionada." border="true":::

Allí verá la colección raíz, así como todas las colecciones existentes. La colección raíz es la principal de la lista de colecciones y tendrá el mismo nombre que el recurso de Purview. En el ejemplo siguiente, se denomina Contoso Purview.

:::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la colección raíz resaltada." border="true":::

Todas las colecciones existentes se han agregado a esta colección raíz y se pueden administrar desde esta página.
Para crear una colección, seleccione **+ Agregar una colección**.

:::image type="content" source="./media/concept-account-upgrade/select-add-a-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, en la que se muestra la ventana de la nueva colección, con los botones Agregar una colección resaltados." border="true":::

Para editar una colección, seleccione **Editar** en la página de detalles de la colección o en su menú desplegable.

:::image type="content" source="./media/concept-account-upgrade/edit-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en la ventana de colecciones, con el botón &quot;Editar&quot; resaltado tanto en la ventana de la colección seleccionada como bajo el botón de puntos suspensivos junto al nombre de la colección." border="true":::

Para editar las asignaciones de roles de una colección, seleccione la pestaña **Asignaciones de roles** para ver todos los roles de una colección. Solo un administrador de colecciones puede administrar las asignaciones de roles. Hay más información sobre los permisos en las cuentas actualizadas en la [sección siguiente](#permissions).

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="Captura de pantalla de la ventana de la colección en Purview Studio, con la pestaña Asignaciones de roles resaltada." border="true":::

Para obtener más información sobre las colecciones en las cuentas actualizadas, lea nuestra [guía sobre creación y administración de colecciones](how-to-create-and-manage-collections.md).

### <a name="what-happens-to-your-collections-during-upgrade"></a>¿Qué ocurre con las colecciones durante la actualización?

1. Se crea una colección raíz. La colección raíz es la principal de la lista de colecciones y tendrá el mismo nombre que el recurso de Purview. En el ejemplo siguiente, se denomina Contoso Purview.

    :::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la colección raíz resaltada." border="true":::

1. Las colecciones existentes anteriormente se conectarán a la colección raíz. Las verá debajo de la colección raíz y podrá interactuar con ellas allí.

### <a name="what-happens-to-your-sources-during-upgrade"></a>¿Qué ocurre con los orígenes durante la actualización?

1. Los orígenes que no estuvieran antes asociados a una colección se agregan automáticamente a la colección raíz.

1. Los orígenes o exámenes que estuvieran previamente asociados a una colección se agregan a las recopilaciones respectivas en la cuenta actualizada.

Los recursos también se asocian a colecciones de la cuenta actualizada, pero es posible que no se muestren inmediatamente en la colección actualizada. Esto se debe a uno de estos dos motivos:

* El recurso se examina mediante un examen programado y todavía no se ha producido la siguiente ejecución del examen.
* El recurso solo se ha examinado mediante un examen de una sola vez.

> [!NOTE]
> Los recursos se agregan a una colección durante el proceso de examen, por lo que habrá que ejecutar otro examen en los recursos para agregarlos a estas nuevas colecciones.

Para los exámenes programados, solo debe esperar a su siguiente ejecución y los recursos se agregarán a la colección.

Para los exámenes de una sola vez, tendrá que volver a ejecutarlos manualmente para rellenar los recursos de la colección.

1. En [Purview Studio](https://web.purview.azure.com/resource/), abra el Mapa de datos y seleccione **Orígenes**. Seleccione el origen que quiera examinar.

    :::image type="content" source="./media/concept-account-upgrade/select-sources.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con los orígenes resaltados." border="true":::

1. Seleccione la pestaña **Exámenes** y después el examen que quiera volver a ejecutar.

    :::image type="content" source="./media/concept-account-upgrade/select-scan.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en un origen, con los exámenes resaltados." border="true":::

1. Seleccione **Run scan now** (Ejecutar examen ahora) para volver a ejecutar el examen.

    :::image type="content" source="./media/concept-account-upgrade/run-scan-now.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en un examen, con la opción Run scan now (Ejecutar examen ahora) resaltada." border="true":::

### <a name="what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin"></a>¿Qué ocurre cuando la cuenta actualizada no tiene un administrador de colección?

La cuenta de Purview actualizada tendrá administradores de colección predeterminados si el proceso puede identificar al menos un usuario o grupo en el orden siguiente: 

1. Propietario (asignado explícitamente)

1. Administrador de acceso de usuario (asignado explícitamente)

1. Administrador de origen de datos y administrador provisional de datos

Si la cuenta no tiene ningún usuario o grupo coincidente con los criterios anteriores, la cuenta de Purview actualizada no tendrá ningún administrador de colección. 

Todavía puede agregar manualmente un administrador de colección mediante la API de administración. El usuario que llama a esta API debe tener permiso de propietario o de administrador de acceso de usuario en la cuenta de Purview para ejecutar una acción de escritura en la cuenta. Deberá conocer el `objectId` del nuevo administrador de colección para enviarlo a través de la API.

#### <a name="request"></a>Solicitud

   ```
    POST https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Purview/accounts/<accountName>/addRootCollectionAdmin?api-version=2021-07-01
   ```    
    
#### <a name="request-body"></a>Cuerpo de la solicitud

   ```json
    {
        "objectId": "<objectId>"
    }
   ```    

`objectId` es el objectId del nuevo administrador de la colección que se va a agregar.

#### <a name="response-body"></a>Response body

Si se completa correctamente, recibirá una respuesta de cuerpo vacío con el código `200`.

En caso de error, el formato del cuerpo de la respuesta es el siguiente.

   ```json
    {
        "error": {
            "code": "19000",
            "message": "The caller does not have Microsoft.Authorization/roleAssignments/write permission on resource: [/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>].",
            "target": null,
            "details": []
        }
    }
   ```

## <a name="permissions"></a>Permisos

En las cuentas de Purview actualizadas, los permisos se administran por medio de colecciones.

> [!NOTE]
> Los permisos de las cuentas actualizadas ya no se administran mediante Access Control (IAM). Cuando se actualiza una cuenta heredada, los permisos IAM preexistentes se seguirán mostrando en Access Control IAM, pero ya no afectarán al acceso en Purview.

Cuando se actualiza una cuenta heredada, todos los permisos asignados en Access Control IAM se asignan a la colección raíz a uno de los roles siguientes.

* **Administradores de colección**: pueden editar una colección y sus detalles, administrar el acceso a la colección y agregar subcolecciones.
* **Administradores de orígenes de datos**: pueden administrar orígenes de datos y exámenes de datos.
* **Conservadores de datos**: pueden crear, leer, modificar y eliminar acciones en objetos de datos del catálogo.
* **Lectores de datos**: pueden acceder a los objetos de datos del catálogo, pero no modificarlos.

Para conceder a los usuarios acceso a orígenes y artefactos en Purview, querrá concederles acceso a colecciones y subconsultas donde puedan necesitar acceso a los datos.

> [!NOTE]
> Solo los Administradores de colecciones tienen permiso para administrar el acceso en las colecciones.

Para ver o editar las asignaciones de roles de una colección, seleccione la pestaña **Asignaciones de roles** de la colección.

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="Captura de pantalla de la ventana de la colección en Purview Studio, con la pestaña Asignaciones de roles resaltada." border="true":::

Para obtener más información sobre cómo administrar permisos en una cuenta de Purview actualizada, siga nuestra [guía de permisos de Purview](catalog-permissions.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre los conceptos anteriores o sobre cómo proteger el recurso de Purview, siga los vínculos siguientes.

* [Puntos de conexión privados con Azure Purview](catalog-private-link.md)
* [Mapa de datos elástico](concept-elastic-data-map.md)
* [Guía de permisos de Purview](catalog-permissions.md)
