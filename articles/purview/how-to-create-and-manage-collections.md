---
title: Creación y administración de colecciones de trabajos
description: En este artículo se explica cómo crear y administrar colecciones en Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: template-how-to
ms.openlocfilehash: a5d0935955e10a1d520c88863af6a16b7160bc40
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398156"
---
# <a name="create-and-manage-collections-in-azure-purview"></a>Creación y administración de colecciones en Azure Purview

Las colecciones de Purview se pueden usar para organizar recursos y orígenes según el flujo del negocio, pero también son la herramienta que se usa para administrar el acceso a través de Purview. Esta guía le orientará sobre la creación y administración de estas colecciones, así como sobre los pasos para registrar orígenes y agregar recursos a las colecciones.

> [!NOTE]
> En este momento, este inicio rápido solo se aplica a las instancias de Purview creadas **el 18 de agosto o después**. Las instancias creadas antes del 18 de agosto pueden crear colecciones, pero no administran permisos a través de esas colecciones. Para obtener información sobre cómo crear una colección para instancias de Purview creadas antes del 18 de agosto, consulte nuestra [**guía de colecciones heredada**](#legacy-collection-guide) en la parte inferior de la página.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Su propio [inquilino de Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Un [recurso de Purview](create-catalog-portal.md) activo.

### <a name="check-permissions"></a>Compruebe los permisos.

Para crear y administrar colecciones en Purview, deberá ser un **administrador de colecciones** dentro de Purview. Podemos comprobar estos permisos en [Purview Studio](use-purview-studio.md). Para encontrar Studio, vaya al recurso de Purview en Azure Portal y seleccione el icono "Abrir Purview Studio" en la página de información general.

1. Seleccione Mapa de datos > Colecciones en el panel izquierdo para abrir la página de administración de colecciones.
:::image type="content" source="./media/quickstart-create-collection/find-collections.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en Mapa de datos, con la pestaña Colecciones seleccionada." border="true":::
1. Seleccione la colección raíz. Esta es la colección superior de la lista de colecciones y tendrá el mismo nombre que el recurso de Purview. En el ejemplo siguiente, se denomina Contoso Purview. Como alternativa, si ya existen colecciones, puede seleccionar cualquier colección en la que desee crear una subcolección.
:::image type="content" source="./media/quickstart-create-collection/select-root-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en Mapa de datos, con la colección raíz resaltada." border="true":::
1. Seleccione asignaciones de roles en la ventana de colecciones.
:::image type="content" source="./media/quickstart-create-collection/role-assignments.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en Mapa de datos, con la pestaña Asignaciones de roles resaltada." border="true":::
1. Para crear una colección, debe estar en la lista de administradores de colección en asignaciones de roles. Si creó el recurso de Purview, debería aparecer como administrador de colecciones en la colección raíz. Si no es así, deberá ponerse en contacto con el administrador de colecciones para que le conceda permiso.
:::image type="content" source="./media/quickstart-create-collection/collection-admins.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en Mapa de datos, con la sección de administradores de colecciones resaltada." border="true":::

## <a name="collection-management"></a>Administración de colecciones

### <a name="create-a-collection"></a>Creación de una colección

Debe ser un administrador colecciones para crear una colección. Si no está seguro, siga la [guía anterior](#check-permissions) para comprobar los permisos.
 
1. Seleccione Mapa de datos > Colecciones en el panel izquierdo para abrir la página de administración de colecciones.
:::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en Mapa de datos, con la pestaña Colecciones seleccionada y abierta." border="true":::
1. Seleccione “+ Agregar una colección”. Una vez más, tenga en cuenta que solo los [administradores de colecciones](#check-permissions) pueden administrar colecciones.
1. En el panel derecho, escriba el nombre y la descripción de la colección. Si es necesario, también puede agregar usuarios como administradores de colecciones a la nueva colección.
:::image type="content" source="./media/how-to-create-and-manage-collections/create-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, en la que se muestra la nueva ventana de colección, con un nombre para mostrar y administradores de colecciones seleccionados, así como el botón Crear resaltado." border="true":::
1. Seleccione “Crear”. La información de la nueva colección se reflejará en la página.
:::image type="content" source="./media/how-to-create-and-manage-collections/created-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, en la que se muestra la ventana de colección recién creada." border="true":::

### <a name="edit-a-collection"></a>Editar una colección

1. Seleccione "Editar" en la página de detalles de la colección o en el menú desplegable de esta.
:::image type="content" source="./media/how-to-create-and-manage-collections/edit-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en la ventana de colecciones, con el botón &quot;Editar&quot; resaltado tanto en la ventana de colección seleccionada como bajo el botón de puntos suspensivos junto al nombre de la colección." border="true":::
1. Actualmente se pueden editar la descripción de la colección y los administradores de recopilación. Realice los cambios y, a continuación, seleccione “Guardar” para guardar el cambio.
:::image type="content" source="./media/how-to-create-and-manage-collections/edit-description.png" alt-text="Captura de pantalla de la ventana de Purview Studio con la ventana Editar colección abierta, una descripción agregada a la colección y el botón Guardar resaltado." border="true":::

### <a name="view-collections"></a>Visualización de colecciones

1. Seleccione el icono de triángulo situado junto al nombre de la colección para expandir o contraer su jerarquía. Haga clic en los nombres de colección para navegar.
1. Escriba en el cuadro de filtro de la parte superior de la lista para filtrar colecciones.
1. Seleccione “Actualizar” en el menú contextual de la colección raíz para volver a cargar la lista de colecciones.
:::image type="content" source="./media/how-to-create-and-manage-collections/refresh-collections.png" alt-text="Captura de pantalla de la ventana de la colección de Purview Studio, con el botón junto al nombre del recurso seleccionado y el botón Actualizar resaltado." border="true":::
1. Seleccione “Actualizar” en la página de detalles de la colección para volver a cargar la colección única.
:::image type="content" source="./media/how-to-create-and-manage-collections/refresh-single-collection.png" alt-text="Captura de pantalla de la ventana de colecciones de Purview Studio, con el botón Actualizar debajo de la ventana de la colección resaltado." border="true":::

## <a name="add-roles-and-restrict-access-through-collections"></a>Incorporación de roles y restricción del acceso a través de colecciones

Puesto que los permisos se administran a través de colecciones en Purview, es importante comprender los roles y qué permisos concederán a los usuarios. Un usuario al que se le conceden permisos sobre una colección tendrá acceso a las fuentes y recursos asociados a esa colección, y heredará los permisos para las subcolecciones. La herencia [se puede restringir](#restrict-inheritance), pero se permite de forma predeterminada.

En la guía siguiente se discutirán los roles, cómo administrarlos y la herencia de permisos.

### <a name="roles"></a>Roles

Todos los roles asignados se aplican a orígenes, recursos y otros objetos dentro de la colección donde se aplica el rol.

- **Administradores de colección**: pueden editar la colección, sus detalles y agregar subcolecciones. También pueden agregar responsables de datos, lectores de datos y otros roles de Purview a un ámbito de colección. Los administradores de colecciones que se heredan automáticamente de una colección primaria no se pueden quitar.
- **Administradores de orígenes de datos**: pueden administrar orígenes de datos y exámenes de datos.
- **Responsables de datos**: pueden realizar acciones de creación, lectura, modificación y eliminación de objetos de datos de catálogo y pueden establecer relaciones entre los objetos.
- **Lectores de datos**: pueden acceder a los objetos de datos del catálogo, pero no modificarlos.

### <a name="add-role-assignments"></a>Agregar asignación de roles

1. Seleccione la pestaña “Asignaciones de roles” para ver todos los roles de una colección. Solo un administrador de colecciones puede administrar las asignaciones de roles.
:::image type="content" source="./media/how-to-create-and-manage-collections/select-role-assignments.png" alt-text="Captura de pantalla de la ventana de la colección de Purview Studio, con la pestaña Asignaciones de roles resaltada." border="true":::
1. Seleccione “Editar asignaciones de roles” o el icono de persona para editar cada miembro del rol.
:::image type="content" source="./media/how-to-create-and-manage-collections/edit-role-assignments.png" alt-text="Captura de pantalla de la ventana de la colección de Purview Studio, con la lista desplegable Editar asignaciones de roles seleccionada." border="true":::
1. Escriba en el cuadro de texto los usuarios que quiere agregar al miembro del rol para buscarlos. Seleccione “X” para quitar los miembros que no desea agregar.
1. Haga clic en “Aceptar” para guardar los cambios y verá los nuevos usuarios reflejados en la lista de asignaciones de roles.

### <a name="remove-role-assignments"></a>Eliminación de asignaciones de rol

1. Haga clic en el botón “X” situado junto al nombre de un usuario para quitar una asignación de rol.
:::image type="content" source="./media/how-to-create-and-manage-collections/remove-role-assignment.png" alt-text="Captura de pantalla de la ventana de la colección de Purview Studio, con la pestaña de asignaciones de roles seleccionada y el botón x junto a uno de los nombres resaltados." border="true":::
1. Seleccione “Confirmar” si está seguro de quitar el usuario.
:::image type="content" source="./media/how-to-create-and-manage-collections/confirm-remove.png" alt-text="Captura de pantalla de un menú emergente de confirmación, con el botón Confirmar resaltado." border="true":::

### <a name="restrict-inheritance"></a>Restricción de la herencia

Los permisos de colección se heredan automáticamente de la colección primaria. Por ejemplo, todos los permisos de la colección raíz (la colección de la parte superior de la lista que tiene el mismo nombre que el recurso de Purview) se heredarán en todas las colecciones debajo de ella. Puede restringir la herencia de una colección primaria en cualquier momento mediante la opción de restricción de permisos heredados.

Una vez que restrinja la herencia, deberá agregar usuarios directamente a la colección restringida para concederles acceso.

1. Vaya a la colección en la que desea restringir la herencia y seleccione la pestaña "Asignaciones de roles".
1. Seleccione “Restrict inherited permissions” (Restringir permisos heredados) y haga clic en “Restringir acceso” en el cuadro de diálogo emergente para quitar los permisos heredados de esta colección y cualquier subcolección. Tenga en cuenta que los permisos de administrador de colecciones no se verán afectados.
:::image type="content" source="./media/how-to-create-and-manage-collections/restrict-access-inheritance.png" alt-text="Captura de pantalla de la ventana de la colección de Purview Studio, con la pestaña Asignaciones de roles seleccionada y el botón deslizante Restrict inherited permissions (Restringir permisos heredados)." border="true":::
1. Después de la restricción, los miembros heredados se eliminan de los roles, excepto el administrador de la colección.
1. Haga clic de nuevo en el botón de alternancia “Restrict inherited permissions” (Restringir permisos heredados) para revertir.
:::image type="content" source="./media/how-to-create-and-manage-collections/remove-restriction.png" alt-text="Captura de pantalla de la ventana de colecciones de Purview Studio, con la pestaña Asignaciones de roles seleccionada y el botón deslizante Restrict inherited permissions (Restringir permisos heredados) resaltado." border="true":::

## <a name="add-assets-to-collections"></a>Incorporación de recursos a colecciones

Los recursos y orígenes también están asociados a colecciones. Durante un examen, si este se asoció a una colección, los recursos se agregarán automáticamente a esa colección, pero también se pueden agregar manualmente.

1. Compruebe la información de la colección en los detalles del recurso. Puede encontrar información de la colección en la sección “Collection path” (Ruta de acceso de la colección) en la esquina superior derecha de la página de detalles del recurso.
:::image type="content" source="./media/how-to-create-and-manage-collections/collection-path.png" alt-text="Captura de pantalla de la ventana de recursos de Purview Studio, con la ruta de acceso de la colección resaltada." border="true":::
1. Permisos en la página de detalles del recurso:
    1. Compruebe el modelo de permisos basado en recopilación siguiendo la [guía anterior para agregar roles y restringir el acceso a las colecciones.](#add-roles-and-restrict-access-through-collections)
    1. Si no tiene permiso de lectura en una colección, los recursos de esa recopilación no se mostrarán en los resultados de la búsqueda. Si obtiene la dirección URL directa de un recurso y la abre, verá la página sin acceso. En este caso, póngase en contacto con el administrador de Purview para que le conceda el acceso. Puede hacer clic en el botón “Actualizar” para volver a comprobar el permiso.
    :::image type="content" source="./media/how-to-create-and-manage-collections/no-access.png" alt-text="Captura de pantalla de la ventana de recursos de Purview Studio donde el usuario no tiene permisos y no tiene acceso a información u opciones." border="true":::
    1. Si tiene permiso de lectura para una colección pero no tiene permiso de escritura, puede examinar la página de detalles del recurso, pero se deshabilitan las siguientes operaciones:
        - Edite el recurso. El botón “Editar” se deshabilitará.
        - Elimine el recurso. El botón “Eliminar” se deshabilitará.
        - Mueva el recurso a otra colección. El botón “...” situado en la esquina superior derecha de la sección Collection path (Ruta de acceso de la colección) se ocultará.
    :::image type="content" source="./media/how-to-create-and-manage-collections/read-access-only.png" alt-text="Captura de pantalla de la ventana de recursos de Purview Studio donde el usuario solo tiene permisos de lectura y solo tiene acceso parcial a las opciones." border="true":::
    1. Los recursos de la sección “Jerarquía” también se ven afectados por los permisos. Los recursos sin permiso de lectura estarán atenuados.
    :::image type="content" source="./media/how-to-create-and-manage-collections/hierarchy-permissions.png" alt-text="Captura de pantalla de la ventana Jerarquía de Purview Studio donde el usuario solo tiene permisos de lectura y no tiene acceso a las opciones." border="true":::

### <a name="move-asset-to-another-collection"></a>Traslado del recurso a otra colección

1. Haga clic en el botón “...” situado en la esquina superior derecha de la sección Collection path (Ruta de acceso de la colección).
:::image type="content" source="./media/how-to-create-and-manage-collections/move-asset.png" alt-text="Captura de pantalla de la ventana de recursos de Purview Studio con la ruta de acceso de la colección resaltada y el botón de puntos suspensivos junto a la dicha ruta seleccionado." border="true":::
1. Haga clic en el botón “Move to another collection” (Mover a otra colección).
1. En el panel derecho, elija la colección de destino a la que desea mover el recurso. Tenga en cuenta que solo puede ver las colecciones en las que tiene permisos de escritura.
:::image type="content" source="./media/how-to-create-and-manage-collections/move-select-collection.png" alt-text="Captura de pantalla de la ventana emergente de Purview Studio con el menú desplegable Seleccionar una colección resaltado." border="true":::
1. Haga clic en el botón “Mover” en la parte inferior de la ventana para mover el recurso.

## <a name="search-and-browse-by-collections"></a>Búsqueda y exploración por colecciones

### <a name="search-by-collection"></a>Búsqueda por colección

En Azure Purview, la barra de búsqueda se encuentra en la parte superior de la experiencia de usuario de Purview Studio.

:::image type="content" source="./media/how-to-create-and-manage-collections/purview-search-bar.png" alt-text="Captura de pantalla que muestra la ubicación de la barra de búsqueda de Azure Purview" border="true":::

Al hacer clic en la barra de búsqueda, puede ver el historial de búsquedas recientes y los recursos a los que se ha accedido recientemente. Seleccione "Ver todo" para ver todos los recursos que se han visto recientemente.

:::image type="content" source="./media/how-to-create-and-manage-collections/search-no-keywords.png" alt-text="Captura de pantalla que muestra la barra de búsqueda antes de que se hayan escrito las palabras clave" border="true":::

Escriba las palabras clave que ayuden a identificar el recurso, como su nombre, tipo de datos, clasificaciones y términos del glosario. A medida que escribe palabras clave relacionadas con el recurso deseado, Azure Purview muestra sugerencias sobre qué buscar y posibles coincidencias de recursos. Para completar la búsqueda, haga clic en "Ver resultados de la búsqueda" o presione "Entrar".

:::image type="content" source="./media/how-to-create-and-manage-collections/search-keywords.png" alt-text="Captura de pantalla que muestra la barra de búsqueda cuando un usuario escribe palabras clave" border="true":::

En la página resultados de la búsqueda se muestra una lista de los recursos que coinciden con las palabras clave que especificó por orden de relevancia. Hay varios factores que pueden afectar a la puntuación de relevancia de un recurso. Puede filtrar más la lista si selecciona colecciones, almacenes de datos, clasificaciones, contactos, etiquetas y términos del glosario específicos que se apliquen al recurso que está buscando.

:::image type="content" source="./media/how-to-create-and-manage-collections/search-results.png" alt-text="Captura de pantalla que muestra los resultados de una búsqueda" border="true":::

 Haga clic en el recurso deseado para ver la página Detalles del recurso, donde podrá ver sus propiedades, incluidos el esquema, el linaje y los propietarios del recurso.

:::image type="content" source="./media/how-to-create-and-manage-collections/search-by-collection.png" alt-text="Captura de pantalla que muestra los resultados de la búsqueda con colecciones." border="true":::

### <a name="browse-by-collection"></a>Examen por colección

1. Puede examinar recursos de datos seleccionando `Browse assets` en la página principal.
:::image type="content" source="./media/how-to-create-and-manage-collections/browse-by-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio del catálogo con el botón Examinar los activos resaltado." border="true":::
1. En la página Examinar los activos, seleccione el pivote `By collection`. Las colecciones se enumeran con vista de tabla jerárquica. Para explorar aún más los recursos de cada colección, haga clic en el nombre de la colección correspondiente.
:::image type="content" source="./media/how-to-create-and-manage-collections/by-collection-view.png" alt-text="Captura de pantalla de la ventana de Purview Studio del recurso con la pestaña By collection (Por colección) seleccionada."border="true":::
1. En la página siguiente, se mostrarán los resultados de la búsqueda de los recursos de la colección seleccionada. Puede restringir los resultados seleccionando los filtros de faceta. O bien, puede ver los recursos de otras colecciones haciendo clic en los nombres de colecciones relacionados o de subcolecciones:::image type="content" source="./media/how-to-create-and-manage-collections/search-results-by-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio del catálogo con la pestaña By collection (Por colección) seleccionada."border="true":::
1. Para ver los detalles de un recurso, haga clic en el nombre del recurso en el resultado de la búsqueda. O bien, puede comprobar los recursos y editarlos de forma masiva.
:::image type="content" source="./media/how-to-create-and-manage-collections/view-asset-details.png" alt-text="Captura de pantalla de la ventana de Purview Studio del catálogo con la pestaña By collection (Por colección) seleccionada y las casillas de recursos resaltadas."border="true":::

## <a name="register-source-to-a-collection"></a>Registro del origen en una colección

1. Seleccione “Registrar” o el icono de registro del nodo de colección para registrar un origen de datos. Tenga en cuenta que solo el administrador del origen de datos puede registrar orígenes.
:::image type="content" source="./media/how-to-create-and-manage-collections/register-by-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio del mapa de datos con el botón Registrar resaltado en la parte superior de la página y debajo de una colección."border="true":::
1. Rellene el nombre del origen de datos y otra información de origen.  Enumera todas las colecciones en las que tiene permiso de examen en la parte inferior del formulario. Puede seleccionar una colección. Todos los recursos que se encuentran en este origen pertenecerán a la colección que seleccione.
:::image type="content" source="./media/how-to-create-and-manage-collections/register-source.png" alt-text="Captura de pantalla de la ventana de registro de origen."border="true":::
1. El origen de datos creado se colocará en la colección seleccionada. Haga clic en “Ver detalles” para ver el origen de datos.
:::image type="content" source="./media/how-to-create-and-manage-collections/see-registered-source.png" alt-text="Captura de pantalla de la ventana de Purview Studio del mapa de datos con la tarjeta de origen recién agregada resaltada."border="true":::
1. Seleccione “New scan” (Nuevo examen) para crear el examen en el origen de datos.
:::image type="content" source="./media/how-to-create-and-manage-collections/new-scan.png" alt-text="Captura de pantalla de una ventana de Purview Studio de origen con el botón New scan (Nuevo examen) resaltado."border="true":::
1. De forma similar, en la parte inferior del formulario, puede seleccionar una colección y todos los recursos analizados se incluirán en la colección. Tenga en cuenta que las colecciones enumeradas aquí están restringidas a las subcolecciones de la colección de orígenes de datos. 
:::image type="content" source="./media/how-to-create-and-manage-collections/scan-under-collection.png" alt-text="Captura de pantalla de una nueva ventana de examen con la lista desplegable de recopilación resaltada."border="true":::
1. De vuelta a la colección, verá que un origen de datos está vinculado a la colección.
:::image type="content" source="./media/how-to-create-and-manage-collections/source-under-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio del mapa de datos con la tarjeta de origen recién agregada resaltada en el mapa."border="true":::

## <a name="legacy-collection-guide"></a>Guía de la colección heredada

> [!NOTE]
> Esta guía de la colección heredada es solo para las instancias de Purview creadas antes del 18 de agosto. Las instancias creadas después de esa fecha deben seguir la guía anterior.

Las colecciones heredadas solo organizan los orígenes en el mapa de datos y no administran los permisos para estos orígenes.

### <a name="create-a-legacy-collection"></a>Creación de una colección heredada

1. Seleccione Mapa de datos en el panel izquierdo para abrir el mapa de datos. Con la vista de mapa, puede ver las colecciones y los orígenes enumerados en ellas.
:::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-view.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos." border="true":::
1. Seleccione “+ Nueva colección”.
:::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-create.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la opción + Nueva colección resaltada." border="true":::
1. Asigne un nombre a la colección y seleccione un elemento primario o "Ninguno". Seleccione “Crear”. La información de la colección se reflejará en el mapa de datos.
:::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-name.png" alt-text="Captura de pantalla de la ventana de Purview Studio, en la que se muestra la ventana de la nueva colección." border="true":::

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene una colección, puede seguir las guías que hay a continuación para agregar recursos y examinar y administrar las colecciones.

- [Administración de orígenes de datos](manage-data-sources.md)
- [Orígenes de datos admitidos](purview-connector-overview.md)
