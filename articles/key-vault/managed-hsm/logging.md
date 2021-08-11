---
title: Registro de HSM administrado de Azure
description: Use este tutorial para empezar a trabajar con el registro de HSM administrado.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: mbaldwin
ms.openlocfilehash: 029913125104f1c4eaa6ed627feae6a4ec207bd9
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112379229"
---
# <a name="managed-hsm-logging"></a>Registro de Managed HSM 

Después de crear uno o varios HSM administrados, es probable que desee supervisar cómo y cuándo se accede a ellos y quién lo hace. Puede hacerlo al habilitar el registro, que guarda la información en una cuenta de almacenamiento de Azure proporcionada por el usuario. Un nuevo contenedor denominado **insights-logs-auditevent** se crea automáticamente para su cuenta de almacenamiento especificada. Puede usar esta misma cuenta de almacenamiento para recopilar registros de varios HSM administrados.

Puede acceder a la información de registro 10 minutos (como máximo) después de la operación del HSM administrado. En la mayoría de los casos, será más rápido que esto.  Es decisión suya administrar los registros en la cuenta de almacenamiento:

* Use los métodos de control de acceso estándar de Azure para proteger los registros mediante la restricción de los usuarios que pueden entrar a ellos.
* Elimine los registros que ya no desee mantener en la cuenta de almacenamiento.

Use este tutorial para empezar a trabajar con el registro de HSM administrado. Deberá crear una cuenta de almacenamiento, habilitar el registro e interpretar la información de los registros recopilados.  

> [!NOTE]
> Este tutorial no incluye instrucciones sobre cómo crear HSM administrados o claves. En este artículo se ofrecen instrucciones de la CLI de Azure para actualizar el registro de diagnóstico.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, debe tener los siguientes elementos:

* Una suscripción a Microsoft Azure. Si no tiene una, puede registrarse para una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).
* La CLI de Azure, versión 2.25.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).
* Un HSM administrado en la suscripción. Consulte [Quickstart: Aprovisionamiento y activación de un HSM administrado mediante la CLI de Azure](quick-create-cli.md) para aprovisionar y activar un HSM administrado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Conexión a su suscripción de Azure

El primer paso para configurar el registro de claves es hacer que la CLI de Azure apunte al HSM administrado que desea registrar.

```azurecli-interactive
az login
```

Para más información sobre las opciones de inicio de sesión mediante la CLI, consulte [Inicio de sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli)

Es posible que deba especificar la suscripción que usó para crear el HSM administrado. Para ver las suscripciones de su cuenta, escriba el siguiente comando:

## <a name="identify-the-managed-hsm-and-storage-account"></a>Identificación del HSM administrado y la cuenta de almacenamiento

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>Habilitar registro

Para habilitar el registro del HSM administrado, use el comando **az monitor diagnostic-settings create**, junto con las variables que hemos creado para la nueva cuenta de almacenamiento y el HSM administrado. También estableceremos la marca **-Enabled** en **$true** y estableceremos la categoría en **AuditEvent** (la única categoría del registro de HSM administrado):

Este resultado confirma que el registro está habilitado ahora para el HSM administrado, y guardará la información en su cuenta de almacenamiento.

Opcionalmente, puede establecer una directiva de retención para los registros, de forma que los registros más antiguos se eliminen automáticamente. Por ejemplo, establezca la directiva de retención mediante la configuración de la marca **-RetentionEnabled** en **$true**, y establezca el parámetro **-RetentionInDays** en **90**, con el fin de que los registros que tengan más de 90 días se eliminen automáticamente.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

Qué se registra:

* Todas las solicitudes de la API REST autenticadas, incluidas las solicitudes con error debido a permisos de acceso, errores del sistema o solicitudes incorrectas.
* Operaciones del plano de administración en el propio recurso de HSM administrado, incluidas la creación, eliminación y actualización de atributos, como las etiquetas.
* Operaciones relacionadas con el dominio de seguridad, como la inicialización y descarga, la inicialización de la recuperación y la carga.
* Operaciones de copia de seguridad completa de HSM, restauración y restauración selectiva.
* Operaciones de administración de roles, como crear, ver o eliminar asignaciones de roles, y crear, ver o eliminar definiciones de roles personalizados.
* Operaciones con claves, entre las que se incluyen las siguientes:
  * Creación, modificación o eliminación de las claves.
  * Firma, comprobación, cifrado, descifrado, encapsulado, desencapsulado y enumeración de claves.
  * Copia de seguridad, restauración y purga de claves.
* Solicitudes no autenticadas que dan como resultado una respuesta 401. Por ejemplo, las solicitudes que no tienen un token de portador, cuyo formato es incorrecto o está caducado o que tienen un token no válido.  

## <a name="access-your-logs"></a>Acceso a los registros

Los registros de Managed HSM se almacenan en el contenedor **insights-logs-auditevent** de la cuenta de almacenamiento proporcionada. Para ver los registros, tendrá que descargar blobs. Para obtener información sobre Azure Storage, consulte [Creación, descarga y enumeración de blobs mediante la CLI de Azure](../../storage/blobs/storage-quickstart-blobs-cli.md).

Los blobs individuales se almacenan como texto, con formato de JSON. Veamos una entrada de registro como ejemplo. En el ejemplo siguiente se muestra la entrada de registro cuando se envía una solicitud para crear una copia de seguridad completa al HSM administrado.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```



## <a name="use-azure-monitor-logs"></a>Uso de registros de Azure Monitor

Puede utilizar la solución Key Vault en registros de Azure Monitor para revisar los registros **AuditEvent** de HSM administrado. En los registros de Azure Monitor, las consultas de los registros se usan para analizar los datos y obtener la información que necesita.

Para más información, incluido cómo configurar esta opción, consulte [Azure Key Vault en Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre [procedimientos recomendados](best-practices.md) para aprovisionar y usar un HSM administrado.
- Obtenga más información sobre [cómo realizar una copia de seguridad y restauración](backup-restore.md) de un HSM administrado.
