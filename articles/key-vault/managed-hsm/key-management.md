---
title: 'Administración de claves en un HSM administrado: Azure Key Vault | Microsoft Docs'
description: Use este artículo para administrar las claves en un HSM administrado.
services: key-vault
author: mbaldwin
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 418bc82a503822a79f138fc71213f9ec5c9b5266
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471316"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Administración de un HSM administrado mediante la CLI de Azure

> [!NOTE]
> Key Vault admite dos tipos de recursos: almacenes y HSM administrados. Este artículo trata sobre los **HSM administrados**. Si desea obtener información sobre cómo administrar un almacén, consulte [Administración de Key Vault mediante la CLI de Azure](../general/manage-with-cli2.md).

Para obtener información general sobre los HSM administrados, consulte [¿Qué es HSM administrado?](overview.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, debe tener los siguientes elementos:

* Una suscripción a Microsoft Azure. Si no tiene una, puede registrarse para una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).
* La CLI de Azure, versión 2.25.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).
* Un HSM administrado en la suscripción. Consulte [Quickstart: Aprovisionamiento y activación de un HSM administrado mediante la CLI de Azure](quick-create-cli.md) para aprovisionar y activar un HSM administrado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Para iniciar sesión en Azure mediante la CLI puede escribir:

```azurecli
az login
```

Para más información sobre las opciones de inicio de sesión mediante la CLI, consulte [Inicio de sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli).

> [!NOTE]
> Todos los comandos siguientes muestran dos métodos de uso. Uno que usa los parámetros **--hsm-name** y **--name** (para el nombre de clave) y otro que usa el parámetro **--id**, donde puede especificar la dirección URL completa, incluido el nombre de clave si corresponde. El último método es útil cuando el autor de la llamada (un usuario o una aplicación) no tiene acceso de lectura en el plano de control y solo tiene acceso restringido en el plano de datos.

## <a name="create-an-hsm-key"></a>Creación de una clave de HSM

> [!NOTE]
> La clave generada o importada en Managed HSM no se puede exportar. Consulte los procedimientos recomendados para la portabilidad y durabilidad de claves.

Utilice el comando `az keyvault key create` para crear una clave.

### <a name="create-an-rsa-key"></a>Creación de una clave RSA

En el ejemplo siguiente se muestra cómo crear una clave **RSA** de 3072 bits que solo se utilizará para las operaciones **wrapKey y unwrapKey** (--ops). 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

Tenga en cuenta que la operación `get` solo devuelve la clave pública y los atributos de clave. No devuelve la clave privada (en el caso de una clave asimétrica) ni el material de clave (en el caso de una clave simétrica).

### <a name="create-an-ec-key"></a>Creación de una clave EC

En el ejemplo siguiente se muestra cómo crear una clave **EC** con una curva P-256 que solo se utilizará para las operaciones **sign y verify** (--ops) con dos etiquetas, **usage** y **appname**. Las etiquetas le ayudan a agregar metadatos adicionales a la clave para facilitar el seguimiento y la administración.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>Creación de una clave simétrica de 256 bits

En el ejemplo siguiente se muestra cómo crear una clave **simétrica** de 256 bits que solo se utilizará para las operaciones **encrypt y decrypt** (--ops).

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>Visualización de atributos y etiquetas de claves

Use el comando `az keyvault key show` para ver los atributos, las versiones y las etiquetas de una clave.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>Enumeración de claves

Use el comando `az keyvault key list` para enumerar todas las claves dentro de un HSM administrado.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>Eliminación de una clave

Use el comando `az keyvault key delete` para eliminar una clave de un HSM administrado. Tenga en cuenta que la eliminación temporal está siempre habilitada. Por lo tanto, una clave eliminada permanecerá en estado eliminado y se podrá recuperar hasta que haya transcurrido el número de días de retención, momento en el que la clave se purgará (se eliminará de forma permanente) sin que sea posible la recuperación.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>Enumeración de claves eliminadas

Use el comando `az keyvault key list-deleted` para enumerar todas las claves en estado eliminado en el HSM administrado.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>Recuperación de una clave eliminada

Use el comando `az keyvault key list-deleted` para enumerar todas las claves en estado eliminado en el HSM administrado. Si necesita recuperar una clave, al usar el parámetro --id para recuperar una clave eliminada debe anotar el valor `recoveryId` de la clave eliminada obtenida del comando `az keyvault key list-deleted`.

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>Purga (eliminación permanente) de una clave

Use el comando `az keyvault key purge` para purgar (eliminar de forma permanente) una clave.

> [!NOTE]
> Si el HSM administrado tiene habilitada la protección de purga, la operación de purga no se permitirá. La clave se purgará automáticamente cuando haya transcurrido el período de retención.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>Creación de una copia de seguridad de una sola clave

Use `az keyvault key backup` para crear una copia de seguridad de una clave. El archivo de copia de seguridad es un blob cifrado vinculado de forma criptográfica al dominio de seguridad del HSM de origen. Solo se puede restaurar en HSM que compartan el mismo dominio de seguridad. Obtenga más información sobre el [dominio de seguridad](security-domain.md).

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>Restauración de una única clave a partir de una copia de seguridad

Use `az keyvault key restore` para restaurar una sola clave. El HSM de origen en el que se creó la copia de seguridad debe compartir el mismo dominio de seguridad que el HSM de destino donde se va a restaurar la clave.

> [!NOTE]
> La restauración no se realizará correctamente si existe una clave con el mismo nombre en estado activo o eliminado.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>Importación de una clave desde un archivo

Use el comando `az keyvault key import` para importar una clave (solo RSA y EC) desde un archivo. El archivo de certificado debe tener una clave privada y debe usar la codificación PEM (tal y como se define en los RFC [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423) y [1424](https://tools.ietf.org/html/rfc1424)).

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

Para importar una clave desde el HSM local a un HSM administrado, consulte [Importación de claves protegidas con HSM a un HSM administrado (BYOK).](hsm-protected-keys-byok.md)

## <a name="next-steps"></a>Pasos siguientes

- Para ver una referencia completa de la CLI de Azure para los comandos de Key Vault, consulte la [guía de referencia de la CLI de Key Vault](/cli/azure/keyvault).
- Para conocer las referencias de programación, consulte la [Guía del desarrollador de Azure Key Vault](../general/developers-guide.md).
- Obtenga más información sobre la [administración de roles del HSM administrado](role-management.md).
- Obtenga más información sobre los [procedimientos recomendados del HSM administrado](best-practices.md).
