---
title: Cuentas de Batch y de Azure Storage
description: Obtenga información sobre las cuentas de Azure Batch y cómo se usan desde el punto de vista del desarrollo.
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: 3dd071a55e8605b8b5b1cf72c517c9304878e101
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/25/2021
ms.locfileid: "110452055"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Cuentas de Batch y de Azure Storage

Una cuenta de Azure Batch es una entidad identificada de forma exclusiva en el servicio Batch. La mayoría de las soluciones de Batch usan [Azure Storage](../storage/index.yml) para almacenar archivos de recursos y archivos de salida, por lo que cada cuenta de Batch suele estar asociada a una cuenta de almacenamiento correspondiente.

## <a name="batch-accounts"></a>Cuentas de Batch

Todo el procesamiento y los recursos se asocian con una cuenta de Batch. Cuando la aplicación realiza una solicitud en el servicio Batch, autentica la solicitud mediante el nombre de la cuenta de Azure Batch, la dirección URL de la cuenta y una clave de acceso<o un token de Azure Active Directory.

Puede ejecutar varias cargas de trabajo de Batch en una sola cuenta de Batch. También puede distribuir las cargas de trabajo entre cuentas de Batch que se encuentren en la misma suscripción, pero ubicadas en diferentes regiones de Azure.

Puede crear una cuenta de Batch mediante [Azure Portal](batch-account-create-portal.md) o mediante programación, como con la [biblioteca Batch Management .NET](batch-management-dotnet.md). Al crear la cuenta, puede asociar una cuenta de Azure Storage para almacenar los datos o aplicaciones de entrada y salida relacionados con los trabajos.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]


## <a name="azure-storage-accounts"></a>Cuentas de Azure Storage

La mayoría de las soluciones de Batch usan Azure Storage para almacenar los [archivos de recursos](resource-files.md) y los archivos de salida. Por ejemplo, las tareas de Batch (incluidas las tareas estándar, las de inicio, las de preparación de trabajos y las de liberación de trabajos) especifican normalmente archivos de recursos que residen en cuentas de almacenamiento. Las cuentas de almacenamiento también almacenan los datos que se procesan y los datos de salida que se generan.

Batch admite los siguientes tipos de cuentas de almacenamiento de Azure:

- Cuentas de uso general v2 (GPv2)
- Cuentas de uso general v1 (GPv1)
- Cuentas de Blob Storage (actualmente admitidas para grupos en la configuración de máquina virtual)

Para más información sobre las cuentas de almacenamiento, vea [Introducción a las cuentas de Azure Storage](../storage/common/storage-account-overview.md).

Puede asociar una cuenta de almacenamiento con su cuenta de Batch cuando crea la cuenta de Batch o en otro momento. Al elegir una cuenta de almacenamiento, tenga en cuenta los requisitos de costo y rendimiento. Por ejemplo, las opciones de cuenta GPv2 y de Blob Storage admiten mayores [límites de capacidad y escalabilidad](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) si se compara con GPv1. (Póngase en contacto con el servicio de soporte técnico de Azure para solicitar un aumento en el límite de almacenamiento). Estas opciones de cuenta pueden mejorar el rendimiento de las soluciones de Batch que contienen un gran número de tareas en paralelo que se leen o escriben en la cuenta de almacenamiento.

Cuando una cuenta de almacenamiento está vinculada a una cuenta de Batch, se considera la cuenta de *almacenamiento automático*. Se requiere una cuenta de almacenamiento automático si tiene previsto usar la funcionalidad de [paquetes de aplicación](batch-application-packages.md), ya que se usa para almacenar los archivos .zip del paquete de aplicación. También se puede usar para los archivos [de recursos de tarea](resource-files.md#storage-container-name-autostorage); dado que la cuenta de almacenamiento automático ya está vinculada a la cuenta de Batch, esto evita la necesidad de que las direcciones URL de firma de acceso compartido (SAS) accedan a los archivos de recursos.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [nodos y grupos](nodes-and-pools.md).
- Aprenda a crear y administrar cuentas de Batch mediante [Azure Portal](batch-account-create-portal.md) o [Batch Management .NET](batch-management-dotnet.md).
- Aprenda a usar [puntos de conexión privados](private-connectivity.md) con cuentas de Azure Batch.
