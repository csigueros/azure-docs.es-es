---
title: Descripción del acceso y los permisos
description: En este artículo se proporciona información general sobre los permisos, el control de acceso y las colecciones en Azure Purview. El control de acceso basado en roles (RBAC) se administra en el propio Azure Purview, por lo que en esta guía se tratarán los conceptos básicos para proteger la información.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 276069d17dba6b02cbc46026f4660525fe93d136
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158346"
---
# <a name="access-control-in-azure-purview"></a>Control de acceso en Azure Purview

Azure Purview usa **colecciones** para organizar y administrar el acceso a través de sus orígenes, recursos y otros artefactos. En este artículo se describen las colecciones y la administración de acceso en una cuenta de Azure Purview.

## <a name="collections"></a>Colecciones

Una colección es una herramienta que Azure Purview usa para agrupar recursos, orígenes y otros artefactos en una jerarquía para facilitar la detección y para administrar el control de acceso. Todo el acceso a los recursos de Purview se administra desde las colecciones de la propia instancia de Purview.

> [!NOTE]
> A partir del 8 de noviembre de 2021, los conservadores de datos y otros roles superiores pueden acceder a ***Insights***. Los lectores de datos no tienen acceso a Insights.
>
>
## <a name="roles"></a>Roles

Azure Purview usa un conjunto de roles predefinidos para controlar quién puede acceder a lo que hay dentro de la cuenta. Estos roles son actualmente:

- **Administradores de colecciones**: un rol para los usuarios que tienen que asignar roles a otros en Azure Purview o bien administrar colecciones. Los administradores de colecciones pueden agregar usuarios a roles en las colecciones en las que son administradores. También pueden editar las colecciones y sus detalles y agregar subcolecciones.
- **Administradores provisionales de datos**: un rol que proporciona acceso al catálogo de datos para administrar recursos, configurar clasificaciones personalizadas, configurar términos de glosario y revisar la información. Los administradores provisionales de datos pueden crear, leer, modificar, mover y eliminar recursos. También pueden aplicarles anotaciones.
- **Lectores de datos**: rol que proporciona acceso de solo lectura a recursos de datos, clasificaciones, reglas de clasificación, colecciones y términos del glosario.
- **Administradores de orígenes de datos**: un rol que permite a un usuario administrar orígenes y análisis de datos. Un usuario que tenga el rol de administrador de orígenes de datos no tiene acceso a Azure Purview Studio. La combinación de este rol con los roles Lector de datos o Administrador provisional de datos en cualquier ámbito de colección proporciona acceso a Azure Purview Studio.

## <a name="who-should-be-assigned-to-what-role"></a>¿A quién se le debe asignar cada rol?

|Escenario de usuario|Roles adecuados|
|-------------|-----------------|
|Solo necesito buscar recursos, no deseo editar nada.|Lector de datos|
|Necesito editar información sobre los recursos, asignarles clasificaciones, asociarlos a entradas del glosario, etc.|Administrador provisional de datos|
|Necesito editar el glosario o configurar nuevas definiciones de clasificación.|Administrador provisional de datos|
|Tengo que ver Insights para comprender la posición de gobernanza de mi patrimonio de datos.|Administrador provisional de datos|
|La entidad de servicio de la aplicación debe insertar datos en Azure Purview.|Administrador provisional de datos|
|Necesito configurar exámenes mediante Purview Studio.|Administrador de orígenes de datos, más, como mínimo, lector de datos **o** administrador provisional de datos sobre la colección en la que está registrado el origen.|
|Necesito habilitar una entidad de servicio o un grupo para configurar y supervisar exámenes en Azure Purview sin permitirles que accedan a la información del catálogo. |Administrador de orígenes de datos|
|Necesito asignar usuarios a roles en Azure Purview. | Administrador de colecciones |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles.png" alt-text="Gráfico que muestra los roles de Purview" lightbox="./media/catalog-permissions/collection-permissions-roles.png":::

## <a name="understand-how-to-use-azure-purviews-roles-and-collections"></a>Uso de los roles y las colecciones de Azure Purview

Todo el control de acceso se administra en las colecciones de Purview. Las colecciones de Purview se pueden encontrar en [Purview Studio](https://web.purview.azure.com/resource/). Abra la cuenta de Purview en [Azure Portal](https://portal.azure.com) y seleccione el icono de Purview Studio en la página de información general. Desde allí, vaya al mapa de datos en el menú izquierdo y seleccione la pestaña "Colecciones".

Cuando se crea una cuenta de Azure Purview, comienza con una colección raíz que tiene el mismo nombre que la propia cuenta de Purview. El creador de la cuenta de Purview se agrega automáticamente como administrador de colecciones, administrador de orígenes de datos, administrador provisional de datos y lector de datos en esta colección raíz, y puede editarla y administrarla.

A esta colección raíz se le pueden agregar directamente orígenes, recursos y objetos, pero también otras colecciones. Al agregar colecciones, tendrá un mayor control sobre quién tiene acceso a los datos en la cuenta de Purview.

Los restantes usuarios solo pueden acceder a la información que contiene la cuenta de Azure Purview si a ellos, o a un grupo en el que estén, se les da uno de los roles anteriores. Esto significa que, al crear una cuenta de Azure Purview, nadie más que el creador puede acceder o usar sus API hasta que se le [agrega uno o varios de los roles anteriores en una colección](how-to-create-and-manage-collections.md#add-role-assignments).

Los usuarios solo pueden ser agregados a una colección por un administrador de colecciones, o mediante la herencia de permisos. Los permisos de una colección principal son heredados automáticamente por sus subcolecciones. Sin embargo, puede optar por [restringir la herencia de permisos](how-to-create-and-manage-collections.md#restrict-inheritance) en cualquier colección. Si lo hace, sus subcolecciones ya no heredarán los permisos de la colección primaria y será preciso agregarlos directamente, aunque los administradores de la colección que se heredan automáticamente de una colección principal no se pueden eliminar.

## <a name="assign-permissions-to-your-users"></a>Asignación de permisos a los usuarios

Después de crear una cuenta de Azure Purview, lo primero que debe hacer es crear colecciones y asignar usuarios a roles dentro de estas.

> [!NOTE]
> Si creó la cuenta de Azure Purview mediante una entidad de servicio, para poder acceder a Purview Studio y asignar permisos a los usuarios, deberá conceder a un usuario permisos de administrador de colecciones en la colección raíz.
> Puede usar [este comando de la CLI de Azure](/cli/azure/purview/account#az_purview_account_add_root_collection_admin):
>
>   ```azurecli
>   az purview account add-root-collection-admin --account-name --resource-group [--object-id]
>   ```
>

### <a name="create-collections"></a>Creación de recopilaciones

Las colecciones se pueden personalizar de acuerdo a la estructura de los orígenes de la cuenta de Purview y pueden actuar como ubicaciones de almacenamiento organizadas para estos recursos. Cuando piense en las colecciones que pueda necesitar, tenga en cuenta cómo los usuarios accederán a la información o la detectarán. ¿Los orígenes están divididos por departamentos? ¿Hay grupos especializados dentro de esos departamentos que solo necesitarán detectar algunos recursos? ¿Hay algunos orígenes que todos los usuarios deben poder detectar?

Hacerse estas preguntas le permitirá saber que colecciones y subcolecciones puede necesitar para organizar de forma más eficaz el mapa de datos.

Se pueden agregar nuevas colecciones directamente al mapa de datos, donde puede elegir su colección primaria en una lista desplegable, o bien se pueden agregar desde la colección primaria como una subcolección. En la vista de mapa de datos, puede ver todos los orígenes y recursos ordenados por las colecciones y, en la lista, se muestra la colección del origen.

Para más instrucciones e información, puede seguir nuestra [guía para crear y administrar colecciones](how-to-create-and-manage-collections.md).

#### <a name="a-collections-example"></a>Ejemplo de colección

Ahora que tenemos un conocimiento básico de las colecciones, los permisos y cómo funcionan, veamos un ejemplo.

:::image type="content" source="./media/catalog-permissions/collection-example.png" alt-text="Gráfico que muestra una jerarquía de colecciones de ejemplo dividida por región y departamento." border="true":::

Esta es una de las maneras en que una organización podría estructurar sus datos: comenzando por la colección raíz (Contoso, en este ejemplo), las colecciones se organizan en regiones y, luego, en departamentos y subdepartamentos. Se pueden agregar orígenes de datos y recursos a cualquiera de estas colecciones para organizar los recursos de datos por estas regiones y departamento, y administrar el control de acceso en esas líneas. Hay un subdepartamento, Ingresos, que tiene directrices de acceso estrictas, por lo que los permisos tendrán que administrarse firmemente.

El [rol de lector de datos](#roles) puede acceder a la información del catálogo, pero no administrarla ni editarla. Por lo tanto, siguiendo el ejemplo anterior, agregar el permiso Lector de datos a un grupo en la colección raíz y permitir la herencia dará a todos los usuarios de ese grupo permisos de lector en los recursos y orígenes de Purview. Esto hace que todos los usuarios de ese grupo puedan detectar, pero no editar, estos recursos. La [restricción de la herencia](how-to-create-and-manage-collections.md#restrict-inheritance) en el grupo Ingresos controlará el acceso a esos recursos. Los usuarios que necesitan acceso a la información de ingresos se pueden agregar por separado a la colección Ingresos.
Del mismo modo que con los roles Administrador provisional de datos y Administrador de orígenes de datos, los permisos para esos grupos se iniciarán en la colección en la que se asignan y se limitarán a las subcolecciones en las que no se ha restringido la herencia. A continuación, se han asignado permisos para varios grupos en los niveles de colecciones de la subcolección Américas.

:::image type="content" source="./media/catalog-permissions/collection-permissions-example.png" alt-text="Gráfico que muestra una jerarquía de colecciones de ejemplo dividida por región y departamento que muestra la distribución de permisos." border="true":::

### <a name="add-users-to-roles"></a>Asignación de roles a los usuarios

La asignación de roles se administra mediante las colecciones. Solo un usuario con el [rol de administrador de colecciones](#roles) puede conceder permisos a otros usuarios para esa colección. Cuando sea necesario agregar nuevos permisos, un administrador de colecciones accederá a [Purview Studio](https://web.purview.azure.com/resource/), irá al mapa de datos, luego a la pestaña Colecciones y seleccionará la colección en la que es necesario agregar un usuario. En la pestaña Asignaciones de roles, podrán agregar y administrar los usuarios que necesitan permisos.

Puede encontrar instrucciones completas en nuestra [guía paso a paso para agregar asignaciones de roles](how-to-create-and-manage-collections.md#add-role-assignments).

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una comprensión básica de las colecciones y el control de acceso, siga las guías siguientes para crear y administrar esas colecciones, o bien empiece a registrar orígenes en el recurso de Purview.

- [Creación y administración de colecciones](how-to-create-and-manage-collections.md)
- [Orígenes de datos admitidos de Purview](purview-connector-overview.md)
