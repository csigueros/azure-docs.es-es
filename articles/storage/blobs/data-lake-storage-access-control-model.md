---
title: Modelo de control de acceso de Azure Data Lake Storage Gen2
description: Aprenda a configurar el acceso de nivel de contenedor, directorio y archivo en cuentas que tienen un espacio de nombres jerárquico.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.openlocfilehash: 5861d97f1adffc6d92bb702b4ce59a34c103d9f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600273"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Modelo de control de acceso de Azure Data Lake Storage Gen2

Data Lake Storage Gen2 admite los mecanismos de autorización siguientes:

- Autorización de clave compartida
- Autorización de firma de acceso compartido (SAS)
- Control de acceso basado en roles (RBAC de Azure)
- Listas de control de acceso (ACL)

La [autorización de clave compartida y de SAS](#shared-key-and-shared-access-signature-sas-authorization) concede acceso a un usuario (o una aplicación) sin necesidad de que tenga una identidad en Azure Active Directory (Azure AD). Con estas dos formas de autenticación, RBAC de Azure y las listas de control de acceso no tienen ningún efecto.

Tanto RBAC de Azure como las listas de control de acceso requieren que el usuario (o la aplicación) tenga una identidad en Azure AD. RBAC de Azure le permite conceder acceso más "general" a los datos de las cuentas de almacenamiento, como acceso de lectura o escritura a **todos** los datos de una cuenta de almacenamiento, mientras que las listas de control de acceso le permiten conceder acceso más "específico", como acceso de escritura en un directorio o archivo específico.

Este artículo se centra en RBAC de Azure y las listas de control de acceso y en cómo el sistema los evalúa en conjunto para tomar decisiones relacionadas con la autorización para los recursos de cuentas de almacenamiento.

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Control de acceso basado en roles (RBAC de Azure)

RBAC de Azure usa las asignaciones de roles para aplicar conjuntos de permisos a [entidades de seguridad](../../role-based-access-control/overview.md#security-principal). Una entidad de seguridad es un objeto que representa a un usuario, un grupo, una entidad de servicio o una identidad administrada que está definido en Azure Active Directory (AD). Un conjunto de permisos puede dar a una entidad de seguridad un nivel de acceso más "general", como acceso de lectura o escritura a **todos** los datos de una cuenta de almacenamiento o a **todos** los datos de un contenedor.

Los roles siguientes permiten que una entidad de seguridad acceda a los datos de una cuenta de almacenamiento.

|Rol|Descripción|
|--|--|
| [Propietario de datos de blobs de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Acceso total a datos y contenedores de Blob Storage. Este acceso permite que la entidad de seguridad establezca el propietario de un elemento y modifique las listas de control de acceso de todos los elementos. |
| [Colaborador de datos de blobs de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Acceso de lectura, escritura y eliminación a blobs y contenedores de Blob Storage. Este acceso no permite que la entidad de seguridad establezca la propiedad de un elemento, pero puede modificar la lista de control de acceso de los elementos que pertenecen a la entidad de seguridad. |
| [Lector de datos de blobs de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | Lee y enumera blobs y contenedores de Blob Storage. |

Los roles como [Propietario](../../role-based-access-control/built-in-roles.md#owner), [Colaborador](../../role-based-access-control/built-in-roles.md#contributor), [Lector](../../role-based-access-control/built-in-roles.md#reader) y [Colaborador de la cuenta de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-account-contributor) permiten que una entidad de seguridad administre una cuenta de almacenamiento, pero no proporcionan acceso a los datos dentro de esa cuenta. Sin embargo, estos roles (excepto el rol **Lector**) pueden obtener acceso a claves de almacenamiento que se pueden usar en diversas herramientas de cliente para acceder a los datos.

## <a name="access-control-lists-acls"></a>Listas de control de acceso (ACL)

Las ACL le ofrecen la posibilidad de aplicar un nivel de acceso más "específico" a los directorios y archivos. Una *ACL* es una construcción de permisos que contiene una serie de *entradas de ACL*. Cada entrada de ACL asocia una entidad de seguridad a un nivel de acceso. Para más información, consulte [Listas de control de acceso (ACL) en Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="how-permissions-are-evaluated"></a>Evaluación de los permisos

Durante la autorización basada en la entidad de seguridad, los permisos se evalúan en este orden.

:one:&nbsp;&nbsp; Las asignaciones de roles de Azure se evalúan primero y tienen prioridad sobre cualquier otra asignación de ACL.

:two:&nbsp;&nbsp; Si la operación está totalmente autorizada en función de la asignación de roles de Azure, las ACL no se evalúan en absoluto.

:three:&nbsp;&nbsp; Si la operación no está totalmente autorizada, se evalúan las ACL.

> [!div class="mx-imgBorder"]
> ![Flujo de permisos de Data Lake Storage](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

Debido a la manera en que el sistema evalúa los permisos de acceso, **no es posible** usar una ACL para **restringir** un acceso ya concedido por una asignación de roles. Esto se debe a que el sistema evalúa primero las asignaciones de roles de Azure y si la asignación concede permisos de acceso suficientes, las ACL se omiten.

En el diagrama siguiente se muestra el flujo de permisos para tres operaciones comunes: enumerar el contenido de un directorio, leer un archivo y escribir un archivo.

> [!div class="mx-imgBorder"]
> ![Ejemplo de flujos de permisos de Data Lake Storage](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>Tabla de permisos: combinación de RBAC de Azure y ACL

En la tabla siguiente se muestra cómo combinar roles de Azure y entradas de ACL para que una entidad de seguridad pueda realizar las operaciones que se muestran en la columna **Operación**. En esta tabla se muestra una columna que representa cada nivel de una jerarquía de directorios ficticia. Hay una columna para el directorio raíz del contenedor (`/`), un subdirectorio denominado **Oregón**, un subdirectorio del directorio Oregón denominado **Portland** y un archivo de texto en el directorio Portland denominado **Data.txt**. En esas columnas se muestran representaciones [abreviadas](data-lake-storage-access-control.md#short-forms-for-permissions) de la entrada de ACL que se necesita para conceder permisos. En la columna aparece **N/A** (_No aplicable_) si no se necesita ninguna entrada de ACL para realizar la operación.

|    Operación             | Rol de Azure asignado               |    /        | Oregón/     | Portland/ | Data.txt |
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Leer Data.txt            |   Propietario de datos de blobs de almacenamiento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de datos de blobs de almacenamiento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lector de datos de blobs de almacenamiento       | N/D      | N/D      | N/D       | N/D    |
|                          |   None                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Anexar a Data.txt       |   Propietario de datos de blobs de almacenamiento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de datos de blobs de almacenamiento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lector de datos de blobs de almacenamiento       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   Ninguno                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Eliminar Data.txt          |   Propietario de datos de blobs de almacenamiento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de datos de blobs de almacenamiento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lector de datos de blobs de almacenamiento       | `--X`    | `--X`    | `-WX`     | N/D    |
|                          |   None                           | `--X`    | `--X`    | `-WX`     | N/D    |
| Crear Data.txt          |   Propietario de datos de blobs de almacenamiento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de datos de blobs de almacenamiento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lector de datos de blobs de almacenamiento       | `--X`    | `--X`    | `-WX`     | N/D    |
|                          |   None                           | `--X`    | `--X`    | `-WX`     | N/D    |
| Enumerar /                   |   Propietario de datos de blobs de almacenamiento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de datos de blobs de almacenamiento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lector de datos de blobs de almacenamiento       | N/D      | N/D      | N/D       | N/D    |
|                          |   None                           | `R-X`    | N/D      | N/D       | N/D    |
| Enumerar /Oregón/            |   Propietario de datos de blobs de almacenamiento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de datos de blobs de almacenamiento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lector de datos de blobs de almacenamiento       | N/D      | N/D      | N/D       | N/D    |
|                          |   None                           | `--X`    | `R-X`    | N/D       | N/D    |
| Enumerar /Oregón/Portland/   |   Propietario de datos de blobs de almacenamiento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de datos de blobs de almacenamiento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lector de datos de blobs de almacenamiento       | N/D      | N/D      | N/D       | N/D    |
|                          |   None                           | `--X`    | `--X`    | `R-X`     | N/D    |

> [!NOTE]
> Para ver el contenido de un contenedor en el Explorador de Azure Storage, las entidades de seguridad deben [iniciar sesión en el Explorador de Storage mediante Azure AD](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource) y (como mínimo) tener acceso de lectura (R--) a la carpeta raíz (`\`) de un contenedor. Este nivel de permiso les permite mostrar el contenido de la carpeta raíz. Si no quiere que el contenido de la carpeta raíz esté visible, puede asignarles el rol [Lector](../../role-based-access-control/built-in-roles.md#reader). Con ese rol, podrán enumerar los contenedores de la cuenta, pero no el contenido de cada contenedor. Después, puede conceder acceso a directorios y archivos específicos mediante las listas de control de acceso.

## <a name="security-groups"></a>Grupos de seguridad

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Límites en las asignación de roles de Azure y entradas de ACL

Mediante el uso de los grupos, es menos probable que supere el número máximo de asignaciones de roles por suscripción y el número máximo de entradas de ACL por archivo o directorio. En la siguiente tabla se describen estos límites.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)]

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>Autorización con clave compartida y firma de acceso compartido (SAS)

Azure Data Lake Storage Gen2 también admite los métodos de autenticación con [clave compartida](/rest/api/storageservices/authorize-with-shared-key) y [SAS](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Una característica de estos métodos de autenticación es que no se asocia ninguna identidad con el autor de la llamada y, en consecuencia, no se puede realizar la autorización basada en permisos de la entidad de seguridad.

En el caso de la clave compartida, el autor de la llamada obtiene acceso de "superusuario" de forma eficaz, lo que significa que puede acceder plenamente a todas las operaciones en todos los recursos, lo que incluye los datos, la configuración del propietario y el cambio de las ACL.

Los tokens de SAS incluyen permisos permitidos como parte del token. Los permisos incluidos en el token de SAS se aplican con eficacia a todas las decisiones de autorización, pero no se realizan comprobaciones de ACL adicionales.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las listas de control de acceso, consulte [Listas de control de acceso (ACL) en Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).
