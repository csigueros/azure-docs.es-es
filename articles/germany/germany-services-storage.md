---
title: Servicios de almacenamiento de Azure Alemania | Microsoft Docs
description: En este tema se proporciona una comparación de los servicios de almacenamiento de Azure Alemania. También encontrará otra información relevante.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 7a82dfa03c85ecf8ad5c80a7d037431b18a28fed
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029015"
---
# <a name="azure-germany-storage-services"></a>Servicios de almacenamiento de Azure Alemania

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="storage"></a>Almacenamiento
Para más información sobre Azure Storage y cómo usarlo, consulte la [documentación global de Azure Storage](../storage/index.yml).

Los datos almacenados en Azure Storage se replican para garantizar una alta disponibilidad. Para el almacenamiento con redundancia geográfica y el almacenamiento con redundancia geográfica con acceso de lectura, Azure replica datos entre *regiones emparejadas*. En el caso de Azure Alemania, estas regiones emparejadas son:

| Región primaria | Región secundaria (emparejamiento) |
| --- | --- |
| Centro de Alemania | Nordeste de Alemania |
| Nordeste de Alemania | Centro de Alemania |

La replicación de los datos conserva estos dentro de las fronteras alemanas. Las regiones principales y secundarias se emparejan para garantizar la distancia necesaria entre los centros de datos, con el fin de garantizar la disponibilidad en caso de una interrupción o un desastre en un área entera. Para el almacenamiento con redundancia geográfica de alta disponibilidad, seleccione un almacenamiento con redundancia geográfica o un almacenamiento con redundancia geográfica con acceso de lectura al crear una cuenta de almacenamiento.  

Storage Service Encryption protege los datos en reposo dentro de las cuentas de Azure Storage. Cuando se habilita esta característica, Azure cifra automáticamente los datos antes de continuar con el almacenamiento. Para ello, se usa el cifrado AES de 256 bits. Storage Service Encryption admite el cifrado de blobs en bloques, blobs en anexos y blobs en páginas.

### <a name="storage-service-availability-by-azure-germany-region"></a>Disponibilidad del servicio de almacenamiento por región de Azure Alemania

| Servicio | Centro de Alemania | Nordeste de Alemania |
| --- | --- | --- |
| [Blob Storage](../storage/common/storage-introduction.md#blob-storage) |GA |GA |
| [Archivos de Azure](../storage/common/storage-introduction.md#azure-files) | GA | GA |
| [Table Storage](../storage/common/storage-introduction.md#table-storage) |GA  |GA |
| [Queue Storage](../storage/common/storage-introduction.md#queue-storage) |GA | GA |
| [Niveles de Blob Storage de acceso frecuente y de acceso esporádico](../storage/blobs/storage-blob-storage-tiers.md) |GA |GA |
| [Storage Service Encryption](../storage/common/storage-service-encryption.md) |GA |GA |
| Import/Export |N/D |N/D |
| StorSimple |N/D |N/D |

### <a name="variations"></a>Variaciones
Las direcciones URL de las cuentas de almacenamiento de Azure Alemania son diferentes de las de Azure global:

| Tipo de servicio | Azure global | Azure Alemania |
| --- | --- | --- |
| Blob Storage | *.blob.core.windows.net | *.blob.core.cloudapi.de |
| Azure Files | *.file.core.windows.net | *.file.core.cloudapi.de | 
| Queue Storage | *.queue.core.windows.net | *.queue.core.cloudapi.de |
| Almacenamiento de tablas | *.table.core.windows.net | *.table.core.cloudapi.de |

> [!NOTE]
> Todos los scrips y todo el código deben tener en cuenta los puntos de conexión adecuados. Para obtener más información sobre las cadenas de conexión, consulte [Configuración de las cadenas de conexión de Azure Storage](../storage/common/storage-configure-connection-string.md). 
>
>

Para más información sobre las API, consulte el [constructor de cuenta de almacenamiento de nube](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.-ctor).

El sufijo del punto de conexión que se usará en estas sobrecargas es *core.cloudapi.de*.

> [!NOTE]
> Si aparece el error 53 ("No se ha encontrado la ruta de acceso de la red") mientras [monta el recurso compartido de archivos](../storage/files/storage-dotnet-how-to-use-files.md), un firewall podría bloquear el puerto de salida. Intentar montar el recurso compartido de archivos en la máquina virtual que se encuentre en la misma suscripción de Azure que la cuenta de almacenamiento.
>
>


## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones, suscríbase al [blog de Azure Alemania](/archive/blogs/azuregermany/).