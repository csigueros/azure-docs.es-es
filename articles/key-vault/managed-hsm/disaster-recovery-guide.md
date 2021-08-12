---
title: 'Qué hacer si se produce una interrupción del servicio de Azure que afecte al HSM administrado: Azure Key Vault | Microsoft Docs'
description: Descubra qué hacer en caso de que se produzca una interrupción del servicio de Azure que afecte al HSM administrado.
services: key-vault
author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 5f2ea76f9c1a27485e31a3e90f27b1d5d4e424cb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443546"
---
# <a name="managed-hsm-disaster-recovery"></a>Recuperación ante desastres de Managed HSM

Puede que desee crear una réplica exacta del HSM si el original se pierde o no está disponible debido a cualquiera de los siguientes motivos:

- Se ha eliminado y posteriormente se ha purgado.
- Un error catastrófico en la región ha dado lugar a que se destruyan todas las particiones de miembros.

Puede volver a crear la instancia de HSM en la misma región o en otra diferente si tiene lo siguiente:
- El [dominio de seguridad](security-domain.md) del HSM de origen.
- Las claves privadas (al menos el número de cuórum) que cifran el dominio de seguridad.
- La [copia de seguridad](backup-restore.md) completa más reciente del HSM de origen.

Estos son los pasos del procedimiento de recuperación ante desastres:

1. Cree una nueva instancia de HSM.
1. Active el modo de recuperación del dominio de seguridad. Se generará un nuevo par de claves RSA (clave de intercambio del dominio de seguridad) para la transferencia del dominio de seguridad y se enviará como respuesta, que se descargará como una clave pública SecurityDomainExchangeKey.
1. Cree y cargue el "archivo de transferencia del dominio de seguridad". Necesitará las claves privadas que cifran el dominio de seguridad. Las claves privadas se usan localmente y nunca se transfieren en ninguna parte de este proceso.
1. Realice una copia de seguridad del nuevo HSM. Se requiere una copia de seguridad antes de cualquier restauración, incluso cuando el HSM está vacío. Las copias de seguridad permiten una reversión sencilla.
1. Restaure la copia de seguridad de HSM reciente del HSM de origen.

Estos pasos le permitirán replicar manualmente el contenido del HSM en otra región. El nombre del HSM (y el identificador URI del punto de conexión de servicio) serán diferentes, por lo que es posible que tenga que cambiar la configuración de la aplicación para usar estas claves desde otra ubicación.

## <a name="create-a-new-managed-hsm"></a>Creación de un nuevo HSM administrado

Use el comando `az keyvault create` para crear un HSM administrado. Este script tiene tres parámetros obligatorios: un nombre de grupo de recursos, un nombre de HSM y la ubicación geográfica.

Para crear un recurso de HSM administrado, es preciso que proporcione lo siguiente:

- El nombre del HSM.
- El grupo de recursos donde se colocará en su suscripción.
- La ubicación de Azure.
- Una lista de administradores iniciales.

En el ejemplo siguiente se crea un HSM denominado **ContosoMHSM**, en el grupo de recursos **ContosoResourceGroup**, que reside en la ubicación **Este de EE. UU. 2**, con **el usuario actual con sesión iniciada** como único administrador.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> El comando de creación puede tardar unos minutos. Una vez que se devuelva correctamente, estará listo para activar el HSM.

La salida de este comando muestra las propiedades del HSM administrado que ha creado. Las dos propiedades más importantes son:

* **name**: en el ejemplo, el nombre es ContosoMHSM. Usará este nombre para otros comandos de Key Vault.
* **hsmUri**: en el ejemplo, el URI es "https://contosohsm.managedhsm.azure.net". Las aplicaciones que utilizan el HSM a través de su API REST deben usar este identificador URI.

Su cuenta de Azure ahora está autorizada para realizar operaciones en este HSM administrado. Hasta ahora, nadie más está autorizado.

## <a name="activate-the-security-domain-recovery-mode"></a>Activación del modo de recuperación del dominio de seguridad

En el proceso de creación normal, se inicializa y descarga un nuevo dominio de seguridad en este momento. Sin embargo, al estar ejecutando un procedimiento de recuperación ante desastres, solicitamos que el HSM acceda al modo de recuperación del dominio de seguridad y descargue una clave de intercambio del dominio de seguridad. La clave de intercambio del dominio de seguridad es una clave pública de RSA que se usará para cifrar el dominio de seguridad antes de cargarlo en el HSM. La clave privada correspondiente está protegida dentro del HSM para garantizar la seguridad del contenido del dominio de seguridad durante la transferencia.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Carga del dominio de seguridad en el HSM de destino

Para este paso, necesitará lo siguiente:
- La clave de intercambio del dominio de seguridad que descargó en el paso anterior.
- El dominio de seguridad del HSM de origen.
- Las claves privadas (al menos el número de cuórum) usadas para cifrar el dominio de seguridad.

El comando `az keyvault security-domain upload` realiza las siguientes operaciones:

- Descifra el dominio de seguridad del HSM de origen con las claves privadas que proporcione. 
- Crea un blob de carga del dominio de seguridad cifrado con la clave de intercambio del dominio de seguridad que descargó en el paso anterior.
- Carga el blob de carga del dominio de seguridad en el HSM para completar la recuperación del dominio de seguridad.

En el ejemplo siguiente, se usa el dominio de seguridad de **ContosoMHSM**, la segunda de las claves privadas correspondientes, y lo cargamos en **ContosoMHSM2**, que está esperando a recibir un dominio de seguridad. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

Ahora tanto el HSM de origen (ContosoMHSM) como el HSM de destino (ContosoMHSM2) tienen el mismo dominio de seguridad. Ahora podemos restaurar una copia de seguridad completa del HSM de origen en el HSM de destino.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Creación de una copia de seguridad (como punto de restauración) del nuevo HSM

Siempre es una buena idea realizar una copia de seguridad completa antes de ejecutar una restauración completa del HSM, para disponer así de un punto de restauración en caso de que se produzca algún problema con la restauración.

Para crear una copia de seguridad del HSM, necesitará lo siguiente:
- Una cuenta de almacenamiento en la que se almacenará la copia de seguridad.
- Un contenedor de almacenamiento de blobs en esta cuenta de almacenamiento donde el proceso de copia de seguridad creará una nueva carpeta para almacenar la copia de seguridad cifrada.

En el ejemplo siguiente, se usa el comando `az keyvault backup` para la copia de seguridad de HSM en el contenedor de almacenamiento **mhsmbackupcontainer** de la cuenta de almacenamiento **ContosoBackup**. Creamos un token de SAS que expira en 30 minutos y lo proporcionamos al HSM administrado para escribir la copia de seguridad.

```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Restauración de una copia de seguridad del HSM de origen

Para este paso, necesitará lo siguiente:

- La cuenta de almacenamiento y el contenedor de blobs donde se almacenan las copias de seguridad del HSM de origen.
- El nombre de la carpeta desde la que desea restaurar la copia de seguridad. Si crea copias de seguridad periódicas, habrá muchas carpetas dentro de este contenedor.


```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

Ha realizado el proceso completo de recuperación ante desastres. El contenido del HSM de origen cuando se realizó la copia de seguridad se habrá copiado en el HSM de destino, incluidas todas las claves, las versiones, los atributos, las etiquetas y las asignaciones de roles.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre el dominio de seguridad, consulte [Acerca del dominio de seguridad del HSM administrado](security-domain.md).
- Siga los [Procedimientos recomendados de HSM administrado](best-practices.md).
