---
title: 'Inicio rápido: Creación de una colección'
description: En este artículo se describe cómo crear una colección y agregar permisos y orígenes en Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: quickstart
ms.date: 08/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: ba3265ff435bc0e8a34048b07aeee3ef82b24b6c
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609144"
---
# <a name="quickstart-create-a-collection-and-assign-permissions-in-purview"></a>Inicio rápido: Creación de una colección y asignación de permisos en Purview

> [!NOTE]
> En este momento, este inicio rápido solo se aplica a las instancias de Purview creadas el 18 de agosto de 2021 o después. Las instancias creadas antes del 18 de agosto pueden crear colecciones, pero no administran permisos a través de esas colecciones. Para obtener información sobre cómo crear una colección para una instancia de Purview creada antes del 18 de agosto, consulte nuestra [**guía de colecciones heredada**](#legacy-collection-guide) en la parte inferior de la página.
> 
> Todas las cuentas heredadas se actualizarán automáticamente en las próximas semanas. Recibirá una notificación por correo electrónico cuando se actualice la cuenta de Purview. Cuando se actualice la cuenta, todos los permisos asignados se volverán a implementar automáticamente en la colección raíz.

Las colecciones son la herramienta de Purview para administrar la propiedad y el control de acceso entre recursos, orígenes e información. También organizan los orígenes y recursos en categorías que se personalizan para que coincidan con la experiencia de administración con los datos. Esta guía le guiará por el proceso de configuración de su primera colección y el administrador de recopilación a fin de preparar el entorno de Purview para su organización.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Su propio [inquilino de Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Un [recurso de Purview](create-catalog-portal.md) activo.

## <a name="check-permissions"></a>Compruebe los permisos.

Para crear y administrar colecciones en Purview, deberá ser un **administrador de colecciones** en Purview. Podemos comprobar estos permisos en [Purview Studio](use-purview-studio.md). Para encontrar Studio, vaya al recurso de Purview en [Azure Portal](https://portal.azure.com) y seleccione el icono **Abrir Purview Studio** en la página de información general.

1. Seleccione Mapa de datos > Colecciones en el panel izquierdo para abrir la página de administración de colecciones.

    :::image type="content" source="./media/quickstart-create-collection/find-collections.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la pestaña Colecciones seleccionada." border="true":::

1. Seleccione la colección raíz. Es la colección principal de la lista de colecciones y tendrá el mismo nombre que el recurso de Purview. En el ejemplo siguiente, se denomina Contoso Purview.

    :::image type="content" source="./media/quickstart-create-collection/select-root-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la colección raíz resaltada." border="true":::

1. Seleccione asignaciones de roles en la ventana de colecciones.

    :::image type="content" source="./media/quickstart-create-collection/role-assignments.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la pestaña Asignaciones de roles resaltada." border="true":::

1. Para crear una colección, debe estar en la lista de administradores de colecciones en las asignaciones de roles. Si creó el recurso de Purview, debería aparecer como administrador de colecciones en la colección raíz. Si no es así, deberá ponerse en contacto con el administrador de colecciones para que le conceda permiso.

    :::image type="content" source="./media/quickstart-create-collection/collection-admins.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la sección de administradores de colecciones resaltada." border="true":::

## <a name="create-a-collection-in-the-portal"></a>Creación de una colección en el portal

Para crear la colección, comenzaremos en [Purview Studio](use-purview-studio.md). Para encontrar Studio, vaya al recurso de Purview en Azure Portal y seleccione el icono **Abrir Purview Studio** en la página de información general.

1. Seleccione Mapa de datos > Colecciones en el panel izquierdo para abrir la página de administración de colecciones.

    :::image type="content" source="./media/quickstart-create-collection/find-collections.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la pestaña Colecciones seleccionada." border="true":::

1. Seleccione **+ Agregar una colección**.
1. En el panel derecho, escriba el nombre de la colección, la descripción y busque usuarios para agregarlos como administradores de la colección.

    :::image type="content" source="./media/quickstart-create-collection/create-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, en la que se muestra la ventana de la nueva colección, con un nombre para mostrar y administradores de colecciones seleccionados, así como el botón Crear resaltado." border="true":::

1. Seleccione **Crear**. La información de la colección se reflejará en la página.

    :::image type="content" source="./media/quickstart-create-collection/created-collection.png" alt-text="Captura de pantalla de la ventana de Purview Studio, en la que se muestra la ventana de la colección recién creada." border="true":::

## <a name="assign-permissions-to-collection"></a>Asignación de permisos a la colección

Ahora que tiene una colección, puede asignarle permisos para administrar el acceso de los usuarios a Purview.

### <a name="roles"></a>Roles

Todos los roles asignados se aplican a orígenes, recursos y otros objetos dentro de la colección donde se aplica el rol.

* **Administradores de la colección**: pueden editar una colección y sus detalles, administrar el acceso a la colección y agregar subcolecciones.
* **Administradores de orígenes de datos**: pueden administrar orígenes de datos y exámenes de datos.
* **Administradores de datos**: pueden crear, leer, modificar y eliminar acciones en objetos de datos del catálogo.
* **Lectores de datos**: pueden acceder a los objetos de datos del catálogo, pero no modificarlos.

### <a name="assign-permissions"></a>Asignación de permisos

1. Seleccione la pestaña **Asignaciones de roles** para ver todos los roles de una colección.

    :::image type="content" source="./media/quickstart-create-collection/select-role-assignments.png" alt-text="Captura de pantalla de la ventana de la colección de Purview Studio, con la pestaña Asignaciones de roles resaltada." border="true":::

1. Seleccione **Editar asignaciones de roles** o el icono de persona para editar cada miembro del rol.

    :::image type="content" source="./media/quickstart-create-collection/edit-role-assignments.png" alt-text="Captura de pantalla de la ventana de la colección de Purview Studio, con la lista desplegable Asignaciones de roles seleccionada." border="true":::

1. Escriba en el cuadro de texto los usuarios que quiere agregar al miembro del rol fin de buscarlos. Haga clic en **Aceptar** para guardar el cambio.

## <a name="legacy-collection-guide"></a>Guía de la colección heredada

> [!NOTE]
> Esta guía de la colección heredada es solo para las instancias de Purview creadas antes del 18 de agosto de 2021. Las instancias creadas después de ese tiempo deben seguir la guía anterior.

### <a name="create-a-legacy-collection"></a>Creación de una colección heredada

1. Seleccione Mapa de datos en el panel izquierdo para abrir el mapa de datos. Con la vista de mapa, puede ver las colecciones y los orígenes enumerados en ellas.

    :::image type="content" source="./media/quickstart-create-collection/legacy-collection-view.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos." border="true":::

1. Seleccione **+ Nueva colección**.

    :::image type="content" source="./media/quickstart-create-collection/legacy-collection-create.png" alt-text="Captura de pantalla de la ventana de Purview Studio, abierta en el Mapa de datos, con la opción + Nueva colección resaltada." border="true":::

1. Asigne un nombre a la colección y seleccione un elemento primario o "Ninguno". Seleccione **Crear**. La información de la colección se reflejará en el mapa de datos.

    :::image type="content" source="./media/quickstart-create-collection/legacy-collection-name.png" alt-text="Captura de pantalla del menú emergente de la nueva colección de Purview Studio." border="true":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una colección, puede seguir las guías que hay a continuación para agregar recursos y examinar y administrar las colecciones.

* [Registro del origen en la colección](how-to-create-and-manage-collections.md#register-source-to-a-collection)
* [Administración del acceso mediante las colecciones](how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)
