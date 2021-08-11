---
title: Copia de seguridad completa/restauración y restauración selectiva para Azure HSM administrado
description: En este documento se explica cómo realizar operaciones de copia de seguridad completa/restauración y restauración selectiva.
services: key-vault
author: mbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: d9307f1310a2690ef68909a143195c76f8c2b9a4
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471323"
---
# <a name="full-backup-and-restore"></a>Copia de seguridad completa y restauración

> [!NOTE]
> Esta característica solo está disponible para el tipo de recurso HSM administrado.

HSM administrado admite la creación de una copia de seguridad completa de todo el contenido de HSM, incluidas todas las claves, versiones, atributos, etiquetas y asignaciones de roles. La copia de seguridad se cifra con claves criptográficas asociadas al dominio de seguridad de HSM.

La copia de seguridad es una operación de plano de datos. El autor de la llamada que inicia la operación de copia de seguridad debe tener permiso para realizar una operación dataAction **Microsoft.KeyVault/managedHsm/backup/start/action**.

Solo los siguientes roles integrados tienen permiso para realizar una copia de seguridad completa:
- Managed HSM Administrator
- Managed HSM Backup

Debe proporcionar la siguiente información para ejecutar una copia de seguridad completa:
- URL o nombre de HSM
- Nombre de la cuenta de almacenamiento
- Contenedor de almacenamiento de blobs de una cuenta de almacenamiento
- Token de SAS del contenedor de almacenamiento con permisos `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Copia de seguridad completa

La copia de seguridad es una operación de ejecución de larga duración, pero devolverá inmediatamente un identificador de trabajo. Puede comprobar el estado del proceso de copia de seguridad con este identificador de trabajo. El proceso de copia de seguridad crea una carpeta dentro del contenedor designado con el patrón de nomenclatura **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** , donde HSM_NAME es el nombre del HSM administrado del que se realiza la copia de seguridad e YYYY, MM, DD, HH, MM, mm y SS son el año, el mes, la fecha, la hora, los minutos y los segundos de la fecha y hora en formato UTC de cuando se recibió el comando de copia de seguridad.

Mientras la copia de seguridad está en curso, es posible que el HSM no funcione a pleno rendimiento, ya que algunas particiones de HSM estarán ocupadas llevando a cabo la operación de copia de seguridad.

```azurecli-interactive
# time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Restauración completa

La restauración completa le permite restaurar por completo el contenido del HSM con una copia de seguridad anterior, incluidas todas las claves, las versiones, los atributos, las etiquetas y las asignaciones de roles. Todo lo que se almacena actualmente en el HSM se borrará y volverá al mismo estado en el que se encontraba cuando se creó la copia de seguridad de origen.

> [!IMPORTANT]
> La restauración completa es una operación muy destructiva y disruptiva. Por lo tanto, es obligatorio haber realizado una copia de seguridad completa en los últimos 30 minutos para poder realizar una operación `restore`.

La restauración es una operación de plano de datos. El autor de la llamada que inicia la operación de restauración debe tener permiso para realizar una operación dataAction **Microsoft.KeyVault/managedHsm/restore/start/action**. El HSM de origen en el que se creó la copia de seguridad y el HSM de destino donde se realizará la restauración **deben** tener el mismo dominio de seguridad. Obtenga más información [sobre el dominio de seguridad del HSM administrado](security-domain.md).

Debe proporcionar la siguiente información para ejecutar una restauración completa:
- URL o nombre de HSM
- Nombre de la cuenta de almacenamiento
- Contenedor de blobs en una cuenta de almacenamiento
- Token de SAS del contenedor de almacenamiento con permisos `rl`
- Nombre de la carpeta del contenedor de almacenamiento donde se almacena la copia de seguridad de origen

La restauración es una operación de ejecución de larga duración, pero devolverá inmediatamente un identificador de trabajo. Puede comprobar el estado del proceso de restauración con este identificador de trabajo. Cuando el proceso de restauración está en curso, el HSM entra en el modo de restauración y todos los comandos del plano de datos (excepto el de comprobación del estado de restauración) se deshabilitan.

```azurecli-interactive
#### time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="restore-hsm"></a>Restauración de HSM

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Pasos siguientes
- Consulte [Administración de un HSM administrado mediante la CLI de Azure](key-management.md).
- Obtenga más información sobre el [dominio de seguridad del HSM administrado](security-domain.md).
