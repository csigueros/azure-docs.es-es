---
title: Acceso multiprotocolo en Azure Data Lake Storage
description: Use API de blob y aplicaciones que emplean API de blob con Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 01e8cec9a77db207e5320386cbc39c8bbcfddd07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621155"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Acceso multiprotocolo en Azure Data Lake Storage

Las API de blob ya funcionan con las cuentas que tienen un espacio de nombres jerárquico. Esto desbloquea el ecosistema de herramientas, aplicaciones y servicios, así como varias características de Blob Storage para las cuentas que tienen un espacio de nombres jerárquico.

Hasta hace poco, es posible que tuviera que mantener soluciones de almacenamiento independientes para los objetos y para los análisis. Esto se debe a que Azure Data Lake Storage Gen2 tenía una compatibilidad limitada con el ecosistema. Además, tenía acceso limitado a características de Blob service, como el registro de diagnóstico. Las soluciones de almacenamiento fragmentado son difíciles de mantener porque es necesario trasladar los datos entre las cuentas para llevar a cabo distintos escenarios. Pero esto ya no es necesario.

Con el acceso multiprotocolo en Data Lake Storage, puede trabajar con los datos mediante el ecosistema de herramientas, aplicaciones y servicios. Además, incluye herramientas y aplicaciones de terceros. Puede hacer que apunten a cuentas que tienen un espacio de nombres jerárquico sin necesidad de modificarlas. Estas aplicaciones funcionan *tal cual* aunque llamen a las API de blob, dado que estas API ya pueden operar en los datos de cuentas que tienen un espacio de nombres jerárquico.

Las características de Blob Storage, como el [registro de diagnóstico](../common/storage-analytics-logging.md), los [niveles de acceso](storage-blob-storage-tiers.md) y las [directivas de administración del ciclo de vida de Blob Storage](./lifecycle-management-overview.md), ya funcionan con las cuentas que tienen un espacio de nombres jerárquico. Por tanto, puede habilitar los espacios de nombres jerárquicos en las cuentas de Blob Storage sin perder el acceso a estas características importantes.

> [!NOTE]
> El acceso multiprotocolo en Data Lake Storage está disponible de forma general y en todas las regiones. Algunos servicios de Azure o características de Blob Storage habilitados mediante el acceso multiprotocolo permanecen en versión preliminar. En estos artículos se resume la compatibilidad actual con las características de Blob Storage y las integraciones de servicios de Azure.
>
> [Compatibilidad con la característica Blob Storage en cuentas de Azure Storage](storage-feature-support-in-storage-accounts.md)
>
> [Servicios de Azure que admiten Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Funcionamiento del acceso multiprotocolo en Data Lake Storage

Las API de blob y las API de Data Lake Storage Gen2 pueden operar en los mismos datos en las cuentas de almacenamiento que tienen un espacio de nombres jerárquico. Data Lake Storage Gen2 enruta las API de blob a través del espacio de nombres jerárquico para que pueda aprovechar las ventajas de las operaciones de directorio de primera clase y las listas de control de acceso (ACL) compatibles con POSIX.

![Diagrama conceptual del acceso multiprotocolo en Data Lake Storage](./media/data-lake-storage-interop/interop-concept.png)

Las herramientas y aplicaciones existentes que usan la API de blob obtienen estos beneficios de forma automática. No es necesario que los desarrolladores las modifiquen. Data Lake Storage Gen2 aplica de forma coherente listas de control de acceso de nivel de archivo y de directorio independientemente del protocolo que usan las herramientas y las aplicaciones para acceder a los datos.

## <a name="see-also"></a>Consulte también

- [Compatibilidad con la característica Blob Storage en cuentas de Azure Storage](storage-feature-support-in-storage-accounts.md)
- [Servicios de Azure que admiten Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de código abierto que admiten Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Problemas conocidos con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
