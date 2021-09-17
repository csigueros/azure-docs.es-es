---
title: Permisos del catálogo (versión preliminar)
description: En este artículo se proporciona información general sobre cómo configurar el control de acceso basado en rol (RBAC) en Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: 2d9b9aaa6cfd0236a6bc990da5b3ec0870adcd87
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123478201"
---
# <a name="access-control-in-azure-purview"></a>Control de acceso en Azure Purview

Azure Purview usa **colecciones** para organizar y administrar el acceso a través de sus orígenes, recursos y otros artefactos. En este artículo se describen las colecciones y la administración de acceso en una cuenta de Azure Purview.

> [!NOTE]
> En este momento, esta información solo se aplica a las cuentas de Purview creadas **a partir del 18 de agosto de 2021, inclusive**. Las instancias creadas antes del 18 de agosto pueden crear colecciones, pero no administran permisos a través de esas colecciones. Para obtener información sobre el control de acceso de una instancia de Purview creada antes del 18 de agosto, consulte nuestra [**guía de permisos heredados**](#legacy-permission-guide) en la parte inferior de la página.
>
> Todas las cuentas heredadas se actualizarán automáticamente en las próximas semanas. Recibirá una notificación por correo electrónico cuando se actualice la cuenta de Purview. Para obtener más información sobre los cambios que se producirán al actualizar la cuenta, consulte nuestra [guía de cuentas actualizadas](concept-account-upgrade.md).

## <a name="collections"></a>Colecciones

Una colección es una herramienta que Azure Purview usa para agrupar recursos, orígenes y otros artefactos en una jerarquía para facilitar la detección y para administrar el control de acceso. Todo el acceso a los recursos de Purview se administra desde las colecciones de la propia instancia de Purview.

## <a name="roles"></a>Roles

Azure Purview usa un conjunto de roles predefinidos para controlar quién puede acceder a lo que hay dentro de la cuenta. Estos roles son actualmente:

- **Administradores de colecciones**: pueden editar las colecciones de Purview, sus detalles, y agregar subcolecciones. También pueden agregar a los usuarios otros roles de Purview en colecciones en las que sean administradores.
- **Administradores de orígenes de datos**: pueden administrar orígenes de datos y exámenes de datos.
- **Administradores provisionales de datos**: pueden crear, leer, modificar y eliminar recursos de datos del catálogo y establecer relaciones entre recursos.
- **Lectores de datos**: pueden acceder a los recursos de datos del catálogo, pero no modificarlos.

## <a name="who-should-be-assigned-to-what-role"></a>¿A quién se le debe asignar cada rol?

|Escenario de usuario|Roles adecuados|
|-------------|-----------------|
|Solo necesito buscar recursos, no deseo editar nada.|Lector de datos|
|Necesito editar información sobre los recursos, asignarles clasificaciones, asociarlos a entradas del glosario, etc.|Administrador provisional de datos|
|Necesito editar el glosario o configurar nuevas definiciones de clasificación.|Administrador provisional de datos|
|La entidad de servicio de la aplicación debe insertar datos en Azure Purview.|Administrador provisional de datos|
|Necesito configurar exámenes mediante Purview Studio.|Administrador de orígenes de datos, más, como mínimo, lector de datos **o** administrador provisional de datos sobre la colección en la que está registrado el origen.|
|Necesito habilitar una entidad de servicio o un grupo para configurar y supervisar exámenes en Azure Purview sin permitirles que accedan a la información del catálogo. |Administrador de orígenes de datos|
|Necesito asignar usuarios a roles en Azure Purview. | Administrador de colecciones |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles.png" alt-text="Gráfico que muestra los roles de Purview" lightbox="./media/catalog-permissions/collection-permissions-roles.png":::

## <a name="understand-how-to-use-azure-purviews-roles-and-collections"></a>Uso de los roles y las colecciones de Azure Purview

Todo el control de acceso se administra en las colecciones de Purview. Las colecciones de Purview se pueden encontrar en [Purview Studio](use-purview-studio.md). Abra la cuenta de Purview en [Azure Portal](https://portal.azure.com) y seleccione el icono de Purview Studio en la página de información general. Desde allí, vaya al mapa de datos en el menú izquierdo y seleccione la pestaña "Colecciones".

Cuando se crea una cuenta de Azure Purview, comienza con una colección raíz que tiene el mismo nombre que la propia cuenta de Purview. El creador de la cuenta de Purview se agrega automáticamente como administrador de colecciones, administrador de orígenes de datos, administrador provisional de datos y lector de datos en esta colección raíz, y puede editarla y administrarla.

A esta colección raíz se le pueden agregar directamente orígenes, recursos y objetos, pero también otras colecciones. Al agregar colecciones, tendrá un mayor control sobre quién tiene acceso a los datos en la cuenta de Purview.

Los restantes usuarios solo pueden acceder a la información que contiene la cuenta de Azure Purview si a ellos, o a un grupo en el que estén, se les da uno de los roles anteriores. Esto significa que, al crear una cuenta de Azure Purview, nadie más que el creador puede acceder o usar sus API hasta que se le [agrega uno o varios de los roles anteriores en una colección](how-to-create-and-manage-collections.md#add-role-assignments).

Los usuarios solo pueden ser agregados a una colección por un administrador de colecciones, o mediante la herencia de permisos. Los permisos de una colección principal son heredados automáticamente por sus subcolecciones. Sin embargo, puede optar por [restringir la herencia de permisos](how-to-create-and-manage-collections.md#restrict-inheritance) en cualquier colección. Si lo hace, sus subcolecciones ya no heredarán los permisos de la colección primaria y será preciso agregarlos directamente, aunque los administradores de la colección que se heredan automáticamente de una colección principal no se pueden eliminar.

## <a name="assign-permissions-to-your-users"></a>Asignación de permisos a los usuarios

Después de crear una cuenta de Azure Purview, lo primero que debe hacer es crear colecciones y asignar usuarios a roles dentro de estas.

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

La asignación de roles se administra mediante las colecciones. Solo un usuario con el [rol de administrador de colecciones](#roles) puede conceder permisos a otros usuarios para esa colección. Cuando sea necesario agregar nuevos permisos, un administrador de colecciones accederá a Purview Studio, irá al mapa de datos, luego a la pestaña Colecciones y seleccionará la colección en la que es necesario agregar un usuario. En la pestaña Asignaciones de roles, podrán agregar y administrar los usuarios que necesitan permisos.

Puede encontrar instrucciones completas en nuestra [guía paso a paso para agregar asignaciones de roles](how-to-create-and-manage-collections.md#add-role-assignments).

## <a name="legacy-permission-guide"></a>Guía de permisos heredados

> [!NOTE]
> Esta guía de la colección heredada es solo para las cuentas de Purview creadas antes del 18 de agosto de 2021. Las instancias creadas después de esa fecha deben seguir la guía anterior.

En este artículo se describe cómo se implementa el control de acceso basado en rol (RBAC) en el [plano de datos](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane) de Azure Purview para los recursos creados antes del 18 de agosto.

> [!IMPORTANT]
> A la entidad de seguridad que ha creado una cuenta de Purview se le conceden automáticamente todos los permisos del plano de datos, independientemente de los roles del plano de datos en los que pueda estar. Para que cualquier otro usuario realice cualquier acción en Azure Purview, debe encontrarse en al menos uno de los roles predefinidos del plano de datos.

### <a name="azure-purviews-pre-defined-legacy-data-plane-roles"></a>Roles heredados predefinidos del plano de datos de Azure Purview

Azure Purview define un conjunto de roles predefinidos del plano de datos que se pueden usar para controlar quién puede acceder a qué en Azure Purview. Estos roles son:

* **Rol Lector de datos de Purview**: tiene acceso al portal de Purview y puede leer todo el contenido de Azure Purview, excepto los enlaces de examen.
* **Rol Conservador de datos de Purview**: tiene acceso al portal de Purview y puede leer todo el contenido de Azure Purview, excepto los enlaces de examen, puede editar información sobre los recursos, editar definiciones de clasificación y términos del glosario, y puede aplicar clasificaciones y términos del glosario a los recursos.
* **Rol Administrador de orígenes de datos de Purview**: no tiene acceso al portal de Purview (el usuario también debe estar en los roles Lector de datos o Conservador de datos) y puede administrar todos los aspectos del examen de datos en Azure Purview, pero no tiene acceso de lectura o escritura al contenido de Azure Purview más allá de lo relacionado con el examen.

### <a name="understand-how-to-use-azure-purviews-legacy-data-plane-roles"></a>Uso de los roles heredados del plano de datos de Azure Purview

Cuando se crea una cuenta de Azure Purview, el creador se trata como si estuviera en los roles Conservador de datos de Purview y Administrador de orígenes de datos de Purview. Pero el creador de la cuenta no se asigna a estos roles en el almacén de roles. Azure Purview reconoce que la entidad de seguridad es el creador de la cuenta y extiende estas funcionalidades en función de su identidad.

Todos los demás usuarios solo pueden usar la cuenta de Azure Purview si se encuentran en al menos uno de estos roles. Esto significa que, cuando se crea una cuenta de Azure Purview, nadie salvo el creador puede acceder a la cuenta o usar sus API hasta que se asignan a uno o varios de los roles definidos anteriormente.

Tenga en cuenta que el rol Administrador de orígenes de datos de Purview tiene dos escenarios admitidos. El primer escenario es para los usuarios que ya están en el rol Lector de datos de Purview o Conservador de datos de Purview, y que también necesitan poder crear exámenes. Esos usuarios deben tener dos roles, al menos uno entre Lector de datos de Purview o Conservador de datos de Purview, así como estar asignados al rol Administrador de orígenes de datos de Purview.

El otro escenario para el Administrador de orígenes de datos de Purview es para los procesos de programación, como las entidades de servicio, que necesitan poder configurar y supervisar exámenes, pero no deben tener acceso a los datos del catálogo.

Para implementar este escenario, asigne a la entidad de servicio el rol Administrador de orígenes de datos de Purview sin asignar ninguno de los otros dos roles. La entidad de seguridad no tendrá acceso al portal de Purview, pero eso es correcto porque es una entidad de seguridad de programación y solo se comunica mediante las API.

### <a name="putting-users-into-legacy-roles"></a>Asignación a los usuarios de roles heredados

Por lo tanto, la primera tarea en la empresa después de crear una cuenta de Azure Purview es asignar personas a estos roles.

La asignación de roles se administra mediante [RBAC de Azure](../role-based-access-control/overview.md).

Solo dos roles integrados del plano de control en Azure pueden asignar roles de usuario, son los Propietarios o Administradores de acceso de usuarios. Por lo tanto, para asignar roles a los usuarios en Azure Purview, debe encontrar a alguien que sea Propietario o Administrador de acceso de usuarios o que se convierta en uno de ellos.

#### <a name="an-example-of-assigning-someone-to-a-legacy-role"></a>Ejemplo de asignación de un rol heredado a un usuario

1. Vaya a https://portal.azure.com y vaya a la cuenta de Azure Purview.
1. Haga clic en "Control de acceso (IAM)" en el lado izquierdo.
1. A continuación, siga las instrucciones generales que se indican [aquí](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group).

### <a name="legacy-role-definitions-and-actions"></a>Definiciones y acciones de roles heredados

Un rol se define como una colección de acciones. Consulte [aquí](../role-based-access-control/role-definitions.md) para más información sobre cómo se definen los roles. Y consulte [aquí](../role-based-access-control/built-in-roles.md) para conocer las definiciones de roles de los roles de Azure Purview.

### <a name="getting-added-to-a-legacy-data-plane-role-in-an-azure-purview-account"></a>Incorporación a un rol heredado del plano de datos en una cuenta de Azure Purview

Si desea tener acceso a una cuenta de Azure Purview para poder usar Studio o llamar a sus API, necesita que le agreguen a un rol del plano de datos de Azure Purview. Las únicas personas que pueden hacer esto son aquellas que son Propietarios o Administradores de acceso de usuarios en la cuenta de Azure Purview. Para la mayoría de los usuarios, el siguiente paso es encontrar un administrador local que pueda ayudar a encontrar a las personas adecuadas que les puedan dar acceso.

En el caso de los usuarios que tienen acceso a [Azure Portal](https://portal.azure.com) de la empresa, pueden buscar la cuenta de Azure Purview en particular a la que desean unirse, hacer clic en la pestaña "Control de acceso (IAM)" y ver quiénes son los propietarios o los administradores de acceso de usuarios (UAA). Pero tenga en cuenta que, en algunos casos, los grupos de Azure Active Directory o las entidades de servicio se pueden usar como propietarios o UAA, en cuyo caso puede que no sea posible ponerse en contacto con ellos directamente. En su lugar, debe buscar un administrador para obtener ayuda.

### <a name="legacy---who-should-be-assigned-to-what-role"></a>Heredado: ¿quién debe asignarse a qué rol?

|Escenario de usuario|Roles adecuados|
|-------------|-----------------|
|Solo necesito buscar recursos, no deseo editar nada.|Rol Lector de datos de Purview|
|Necesito editar información sobre los recursos, poner clasificaciones en ellos, asociarlos a entradas del glosario, etc.|Rol Conservador de datos de Purview|
|Necesito editar el glosario o configurar nuevas definiciones de clasificación.|Rol Conservador de datos de Purview|
|La entidad de servicio de la aplicación debe insertar datos en Azure Purview.|Rol Conservador de datos de Purview|
|Necesito configurar exámenes mediante Purview Studio.|Rol Administrador de orígenes de datos de Purview más al menos uno de los roles Lector de datos de Purview o Conservador de datos de Purview|
|Necesito habilitar una entidad de servicio u otra identidad de programación para configurar y supervisar exámenes en Azure Purview sin permitir que la identidad de programación tenga acceso a la información del catálogo. |Rol Administrador de orígenes de datos de Purview|
|Necesito asignar usuarios a roles en Azure Purview. | Propietario o Administrador de acceso de usuarios |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles-legacy.png" alt-text="Gráfico que muestra los roles heredados de Purview" lightbox="./media/catalog-permissions/collection-permissions-roles-legacy.png":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una comprensión básica de las colecciones y el control de acceso, siga las guías siguientes para crear y administrar esas colecciones, o bien empiece a registrar orígenes en el recurso de Purview.

- [Creación y administración de colecciones](how-to-create-and-manage-collections.md)
- [Orígenes de datos admitidos de Purview](purview-connector-overview.md)
