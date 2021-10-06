---
title: Uso de Python para administrar listas de control de acceso (ACL) en Azure Data Lake Storage Gen2
description: Use Python para administrar listas de control de acceso (ACL) en cuentas de almacenamiento que tengan habilitado un espacio de nombres jerárquico (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 25ee9ba5983bdcd59962d99384fbb7232860b825
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555496"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Uso de Python para administrar listas de control de acceso (ACL) en Azure Data Lake Storage Gen2

En este artículo se muestra cómo usar Python para obtener, establecer y actualizar las listas de control de acceso de directorios y archivos.

La herencia de ACL ya está disponible para los nuevos elementos secundarios que se crean en un directorio primario. Ahora bien, también se pueden agregar, actualizar y quitar listas de control de acceso de forma recursiva en los elementos secundarios existentes de un directorio primario sin tener que realizar estos cambios individualmente para cada elemento secundario.

[Paquete (índice de paquetes de Python)](https://pypi.org/project/azure-storage-file-datalake/) | [Muestras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [Ejemplos de ACL recursivas](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) | [Referencia de API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake) | [Asignación de Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Ofrecer comentarios](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. Para obtener más información, vea [Obtención de una evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

- Una cuenta de almacenamiento que tenga habilitado el espacio de nombres jerárquico (HNS). Siga [estas](create-data-lake-storage-account.md) instrucciones para crear uno.

- CLI de Azure versión `2.6.0` o posterior.

- Uno de los siguientes permisos de seguridad:

  - Una [entidad de seguridad](../../role-based-access-control/overview.md#security-principal) aprovisionada de Azure Active Directory a la que se ha asignado el rol [Propietario de datos de blobs de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) en el ámbito del contenedor de destino, el grupo de recursos primario o la suscripción.

  - El usuario propietario del contenedor o directorio de destino al que va a aplicar la configuración de ACL. Para establecer listas de control de acceso de forma recursiva, se incluyen todos los elementos secundarios en el contenedor o el directorio de destino.

  - Clave de la cuenta de almacenamiento.

## <a name="set-up-your-project"></a>Configurar su proyecto

Instale la biblioteca de cliente de Azure Data Lake Storage para Python con [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Agregue estas instrucciones import al inicio del archivo de código.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Conexión con la cuenta

Para usar los fragmentos de código de este artículo, tiene que crear una instancia de **DataLakeServiceClient** que represente la cuenta de almacenamiento.

### <a name="connect-by-using-azure-active-directory-ad"></a>Conexión con Azure Active Directory (AD)

> [!NOTE]
> Si usa Azure Active Directory (Azure AD) para autorizar el acceso, asegúrese de que la entidad de seguridad tenga asignado el [rol Propietario de datos de blobs de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para obtener más información sobre cómo se aplican los permisos de ACL y las consecuencias de cambiarlos, vea [Modelo de control de acceso de Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Puede usar la [biblioteca cliente de identidad de Azure para Python](https://pypi.org/project/azure-identity/) para autenticar la aplicación con Azure AD.

Obtenga un identificador de cliente, un secreto de cliente y un identificador de inquilino. Para ello, consulte [Adquisición de un token de Azure AD para la autorización de solicitudes desde una aplicación cliente](../common/storage-auth-aad-app.md). Como parte de ese proceso, tendrá que asignar uno de los siguientes roles de [control de acceso basado en roles de Azure (Azure RBAC)](../../role-based-access-control/overview.md) a la entidad de seguridad.

|Role|Capacidad de configuración de ACL|
|--|--|
|[Propietario de datos de blobs de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos los directorios y archivos de la cuenta.|
|[Colaborador de datos de blobs de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Solo los directorios y archivos que pertenecen a la entidad de seguridad.|

En este ejemplo se crea una instancia de **DataLakeServiceClient** con un identificador de cliente, un secreto de cliente y un identificador de inquilino.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para ver más ejemplos, consulte la documentación de la [biblioteca cliente de identidad de Azure para Python](https://pypi.org/project/azure-identity/).

### <a name="connect-by-using-an-account-key"></a>Conexión con una clave de cuenta

Es la manera más sencilla de conectarse a una cuenta.

En este ejemplo se crea una instancia de **DataLakeServiceClient** con una clave de cuenta.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- Reemplace el valor de marcador de posición `storage_account_name` por el nombre de la cuenta de almacenamiento.

- Reemplace el valor de marcador de posición `storage_account_key` por la clave de acceso de la cuenta de almacenamiento.

## <a name="set-acls"></a>Establecimiento de listas de control de acceso

Cuando *establece* una ACL, debe **reemplazar** toda la ACL, incluidas todas sus entradas. Si quiere cambiar el nivel de permiso de una entidad de seguridad o agregar una nueva entidad de seguridad a la ACL sin que esto afecte a otras entradas existentes, debe *actualizar* la ACL en su lugar. Para actualizar una ACL en lugar de reemplazarla, consulte la sección [Actualización de ACL](#update-acls-recursively) de este artículo.

Esta sección le muestra cómo:

- Establecer la ACL de un directorio
- Establecer la ACL de un archivo

### <a name="set-the-acl-of-a-directory"></a>Establecer la ACL de un directorio

Obtenga la lista de control de acceso (ACL) de un directorio llamando al método **DataLakeDirectoryClient.get_access_control** y establezca dicha lista con una llamada al método **DataLakeDirectoryClient.set_access_control**.

En este ejemplo se obtiene y después se establece la ACL de un directorio denominado `my-directory`. La cadena `rwxr-xrw-` concede al usuario propietario permisos de lectura, escritura y ejecución, permisos de solo lectura y ejecución al grupo propietario, y permisos de lectura y escritura al resto.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

También puede obtener y establecer la ACL del directorio raíz de un contenedor. Para obtener el directorio raíz, llame al método **FileSystemClient._get_root_directory_client**.

### <a name="set-the-acl-of-a-file"></a>Establecer la ACL de un archivo

Obtenga la lista de control de acceso (ACL) de un archivo llamando al método **DataLakeFileClient.get_access_control** y establezca dicha lista con una llamada al método **DataLakeFileClient.set_access_control**.

En este ejemplo se obtiene y después se establece la ACL de un archivo denominado `my-file.txt`. La cadena `rwxr-xrw-` concede al usuario propietario permisos de lectura, escritura y ejecución, permisos de solo lectura y ejecución al grupo propietario, y permisos de lectura y escritura al resto.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>Establecimiento de listas de control de acceso de forma recursiva

Cuando *establece* una ACL, debe **reemplazar** toda la ACL, incluidas todas sus entradas. Si quiere cambiar el nivel de permiso de una entidad de seguridad o agregar una nueva entidad de seguridad a la ACL sin que esto afecte a otras entradas existentes, debe *actualizar* la ACL en su lugar. Para actualizar una ACL en lugar de reemplazarla, consulte la sección [Actualizar una ACL de forma recursiva](#update-acls-recursively) de este artículo.

Establezca ACL de forma recursiva llamando al método **DataLakeDirectoryClient.set_access_control_recursive**.

Si desea establecer una entrada de la ACL **predeterminada**, agregue la cadena `default:` al principio de cada cadena de entrada de ACL.

En este ejemplo se establece la ACL de un directorio denominado `my-parent-directory`.

Este método acepta un parámetro booleano denominado `is_default_scope` que especifica si se debe establecer la ACL predeterminada. Si ese parámetro es `True`, la lista de entradas de la ACL va precedida de la cadena `default:`.

Las entradas de la ACL conceden al usuario propietario permisos de lectura, escritura y ejecución, permisos solo de lectura y ejecución al grupo propietario, y ningún permiso al resto. La última entrada de la ACL de este ejemplo proporciona a un usuario específico con el identificador de objeto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" permisos de lectura y ejecución. En estas entradas se concede al usuario propietario permisos de lectura, escritura y ejecución, permisos solo de lectura y ejecución al grupo propietario, y ningún permiso al resto. La última entrada de ACL de este ejemplo proporciona a un usuario específico con el identificador de objeto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" permisos de lectura y ejecución.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Para ver un ejemplo que procese ACL de forma recursiva en lotes especificando un tamaño de lote, consulte el [ejemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) de Python.

## <a name="update-acls-recursively"></a>Actualización de listas de control de acceso de forma recursiva

Cuando *actualiza* una ACL, modifica la ACL en lugar de reemplazarla. Por ejemplo, puede agregar una nueva entidad de seguridad a la ACL sin que esto afecte a otras entidades de seguridad que se enumeran en la ACL. Para reemplazar la ACL en lugar de actualizarla, consulte la sección [Establecimiento de listas de control de acceso](#set-acls) de este artículo.

Para actualizar una ACL de forma recursiva, cree un nuevo objeto ACL con la entrada de la ACL que quiera actualizar y, a continuación, use ese objeto en la operación para actualizar la ACL. No debe obtener la ACL existente, simplemente proporcione las entradas de la ACL que se vayan a actualizar. Actualice una ACL de forma recursiva llamando al método **DataLakeDirectoryClient.update_access_control_recursive**. Si desea actualizar una entrada de la ACL **predeterminada**, agregue la cadena `default:` al principio de cada cadena de entrada de la ACL.

En este ejemplo se actualiza una entrada de ACL con permiso de escritura.

En este ejemplo se establece la ACL de un directorio denominado `my-parent-directory`. Este método acepta un parámetro booleano denominado `is_default_scope` que especifica si se debe actualizar la ACL predeterminada. Si ese parámetro es `True`, la entrada de la ACL actualizada va precedida de la cadena `default:`.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Para ver un ejemplo que procese ACL de forma recursiva en lotes especificando un tamaño de lote, consulte el [ejemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) de Python.

## <a name="remove-acl-entries-recursively"></a>Eliminación de las entradas de ACL de forma recursiva

Puede quitar una o varias entradas de ACL. Para quitar entradas de ACL de forma recursiva, cree un nuevo objeto de ACL para la entrada de ACL que se va a quitar y, a continuación, use ese objeto en la operación para quitar la ACL. No debe obtener la ACL existente; simplemente proporcione las entradas de la ACL que se van a quitar.

Elimine entradas de ACL llamando al método **DataLakeDirectoryClient.remove_access_control_recursive**. Si desea quitar una entrada de la ACL **predeterminada**, agregue la cadena `default:` al principio de la cadena de entrada de la ACL.

En este ejemplo se quita una entrada de la ACL del directorio llamado `my-parent-directory`. Este método acepta un parámetro booleano denominado `is_default_scope` que especifica si se debe quitar la entrada de la ACL predeterminada. Si ese parámetro es `True`, la entrada de la ACL actualizada va precedida de la cadena `default:`.

```python
def remove_permission_recursively(is_default_scope):

    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Para ver un ejemplo que procese ACL de forma recursiva en lotes especificando un tamaño de lote, consulte el [ejemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) de Python.

## <a name="recover-from-failures"></a>Recuperación de errores

Podría encontrar errores de tiempo de ejecución o de permisos. En el caso de los errores de tiempo de ejecución, reinicie el proceso desde el principio. Los errores de permisos pueden producirse si la entidad de seguridad no tiene permisos suficientes para modificar la ACL de un directorio o archivo que se encuentra en la jerarquía de directorios que se está modificando. Solucione el problema de permisos y, a continuación, elija reanudar el proceso desde el punto de error mediante un token de continuación, o bien reinicie el proceso desde el principio. No tiene que usar el token de continuación si prefiere reiniciar desde el principio. Puede volver a aplicar las entradas de ACL sin ningún efecto negativo.

Este ejemplo devuelve un token de continuación en caso de que se produzca un error. La aplicación puede volver a llamar a este método de ejemplo después de que se haya solucionado el error y pasar el token de continuación. Si se llama a este método de ejemplo por primera vez, la aplicación puede pasar un valor de `None` para el parámetro del token de continuación.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Para ver un ejemplo que procese ACL de forma recursiva en lotes especificando un tamaño de lote, consulte el [ejemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) de Python.

Si desea que el proceso se complete sin que se interrumpa por errores de permisos, puede especificarlo.

Para asegurarse de que el proceso se completa sin interrupciones, no pase un token de continuación al **DataLakeDirectoryClient.set_access_control_recursive**.

En este ejemplo se establecen las entradas de ACL de forma recursiva. Si este código se encuentra un error de permiso, lo registra y continúa la ejecución. En este ejemplo se imprime el número de errores en la consola.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Para ver un ejemplo que procese ACL de forma recursiva en lotes especificando un tamaño de lote, consulte el [ejemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) de Python.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Consulte también

- [Documentación de referencia de API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Paquete (índice de paquetes de Python)](https://pypi.org/project/azure-storage-file-datalake/)
- [Muestras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Asignación de Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemas conocidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Envíenos sus comentarios](https://github.com/Azure/azure-sdk-for-python/issues)
- [Modelo de control de acceso de Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listas de control de acceso en Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
