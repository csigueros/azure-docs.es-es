---
title: Actualización de la clave de acceso de la cuenta de Azure Storage en Azure HDInsight
description: Obtenga información sobre cómo actualizar la clave de acceso de la cuenta de Azure Storage en el clúster de Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/29/2021
ms.openlocfilehash: 9adfb5e438aec8625cd7c4b164f5999181d9c31f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725353"
---
# <a name="update-azure-storage-account-access-keys-in-hdinsight-cluster"></a>Actualización de las claves de acceso de la cuenta de Azure Storage en el clúster de Azure HDInsight

En este artículo, aprenderá a rotar las claves de acceso de Azure Storage para las cuentas de almacenamiento principal o secundaria de Azure HDInsight.

>[!CAUTION]
> La rotación directa de la clave de acceso en el lado de almacenamiento hará que el clúster de HDInsight no sea accesible.

## <a name="prerequisites"></a>Requisitos previos

* Vamos a usar un enfoque para rotar las claves de acceso principal y secundaria de la cuenta de almacenamiento de manera escalonada y alternada para garantizar que el clúster de HDInsight sea accesible a lo largo del proceso.

    Este es un ejemplo sobre cómo usar las claves de acceso de almacenamiento principal y secundaria y configurar directivas de rotación en ellas:
    1. Use la clave de acceso 1 en la cuenta de almacenamiento al crear un clúster de HDInsight.
    1. Configure la directiva de rotación para la clave de acceso 2 cada N días. Como parte de esta rotación, actualice HDInsight para usar la clave de acceso 1 y, después, rote la clave de acceso 2 en la cuenta de almacenamiento.
    1. Configure la directiva de rotación para la clave de acceso 1 cada N/2 días. Como parte de esta rotación, actualice HDInsight para usar la clave de acceso 2 y, después, rote la clave de acceso 1 en la cuenta de almacenamiento.
    1. Con el enfoque anterior, la clave de acceso 1 se rotará cada N/2, 3N/2 días, etc., y la clave de acceso 2 se rotará cada N, 2N, 3N, etc., días.

* Para configurar la rotación periódica de claves de cuenta de almacenamiento, consulte [Automatización de la rotación de un secreto](../key-vault/secrets/tutorial-rotation-dual.md).

## <a name="update-storage-account-access-keys"></a>Actualización de las claves de acceso de la cuenta de almacenamiento

Use [Acción de script](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) para actualizar las claves con las siguientes consideraciones:

|Propiedad | Value |
|---|---|
|URI de script de Bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/update-storage-account-v01.sh`|
|Tipos de nodo|Head|
|Parámetros|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (opcional)|

* `ACCOUNTNAME` es el nombre de la cuenta de almacenamiento en el clúster de HDInsight.
* `ACCOUNTKEY` es la clave de acceso para `ACCOUNTNAME`.
* `-p` es opcional. si se especifica, la clave no se cifra y se almacena en el archivo core-site.xml como texto sin formato.

## <a name="known-issues"></a>Problemas conocidos

El script anterior actualiza directamente la clave de acceso solo en el lado del clúster y no renueva una copia en el lado del proveedor de recursos de HDInsight. Por lo tanto, se producirá un error en la acción de script hospedada en la cuenta de almacenamiento después de rotar la clave de acceso.

Solución alternativa: use los [URI de SAS](hdinsight-storage-sharedaccesssignature-permissions.md) para las acciones de script o haga que los scripts se puedan acceder públicamente.

## <a name="next-steps"></a>Pasos siguientes

* [Adición de cuentas de almacenamiento adicionales](hdinsight-hadoop-add-storage.md)