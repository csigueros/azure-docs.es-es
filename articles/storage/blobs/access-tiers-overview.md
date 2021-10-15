---
title: Niveles de acceso frecuente, esporádico y de archivo para los datos de blobs
titleSuffix: Azure Storage
description: Azure Storage ofrece diferentes niveles de acceso para que pueda almacenar los datos de blobs de la manera más rentable en función de cómo se usen. Obtenga información sobre los niveles de acceso frecuente, esporádico y de archivo para los datos de blobs.
author: tamram
ms.author: tamram
ms.date: 09/29/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: ee0a9714f305a63ae3f25628f1bd9e9b14ce6e01
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293521"
---
# <a name="hot-cool-and-archive-access-tiers-for-blob-data"></a>Niveles de acceso frecuente, esporádico y de archivo para los datos de blobs

Los datos almacenados en la nube están creciendo a un ritmo exponencial. Para administrar los costos de las necesidades de almacenamiento en expansión, puede resultar útil organizar los datos en función de la frecuencia con la que se accederá a los ellos y del tiempo que se conservarán. Azure Storage ofrece diferentes niveles de acceso para que pueda almacenar los datos de blobs de la manera más rentable en función de cómo se usen. Los niveles de acceso de Azure Storage incluyen:

- **Nivel de acceso frecuente**: nivel en línea optimizado para almacenar datos a los que se accede o se modifican con frecuencia. El nivel de acceso frecuente tiene los costos de almacenamiento más altos, pero los costos de acceso más bajos.
- **Nivel de acceso esporádico**: nivel en línea optimizado para almacenar datos a los que se accede o se modifican con poca frecuencia. Los datos del nivel de acceso esporádico se deben almacenar durante un mínimo de 30 días. El nivel de acceso esporádico tiene menores costos de almacenamiento y mayores costos de acceso en comparación con el nivel de acceso frecuente.
- **Nivel de acceso de archivo**: nivel sin conexión optimizado para almacenar datos a los que se accede muy pocas veces y que tienen requisitos de latencia flexibles, del orden de horas. Los datos del nivel de acceso de archivo se deben almacenar durante un mínimo de 180 días.

Los límites de capacidad de almacenamiento de Azure se establecen en el nivel de cuenta, en lugar de según el nivel de acceso. Puede optar por maximizar el uso de la capacidad en un nivel o distribuir la capacidad entre dos o más niveles.

## <a name="online-access-tiers"></a>Niveles de acceso en línea

Cuando los datos se almacenan en un nivel de acceso en línea (frecuente o esporádico), los usuarios pueden acceder a ellos inmediatamente. El nivel de acceso frecuente es la mejor opción para los datos que están en uso activo, mientras que el nivel de acceso esporádico es ideal para los datos a los que se accede con menos frecuencia, pero que todavía deben estar disponibles para lectura y escritura.

Entre los ejemplos de escenarios de uso del nivel de acceso frecuente, se encuentran:

- Datos que están en uso o que se espera leer y escribir con frecuencia.
- Los datos que se almacenan provisionalmente para el procesamiento y, posteriormente, para la migración al nivel de acceso esporádico.

Entre los ejemplos de escenarios de uso del nivel de acceso esporádico, se encuentran:

- Copia de seguridad y recuperación ante desastres de los datos a corto plazo.
- Conjuntos de datos antiguos que no se usan con frecuencia, pero que se espera que estén disponibles para el acceso inmediato.
- Conjuntos de datos de gran tamaño que se deben almacenar de forma rentable mientras se recopilan datos adicionales para su procesamiento.

Los datos del nivel de acceso esporádico tienen una disponibilidad ligeramente inferior, pero ofrecen las mismas características de alta durabilidad, latencia de recuperación y rendimiento similares a las del nivel de acceso frecuente. En el caso de los datos del nivel de acceso esporádico, una disponibilidad ligeramente menor y mayores costos de acceso pueden ser unas desventajas aceptables frente a costos de almacenamiento generales más bajos en comparación con el nivel de acceso frecuente. Para más información, consulte [Contrato de nivel de servicio para Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).

Un blob del nivel de acceso esporádico de una cuenta de uso general v2 está sujeto a una penalización por eliminación anticipada si se elimina o se mueve a otro nivel antes de que transcurran 30 días. Este cargo se prorratea. Por ejemplo, si un blob se mueve al nivel de acceso esporádico y, a continuación, se elimina pasados 21 días, se le cobrará una cuota por eliminación anticipada equivalente a 9 (30 menos 21) días a partir del almacenamiento de ese blob en el nivel de acceso esporádico.

Los niveles de acceso frecuente y esporádico admiten todas las opciones de redundancia. Para más información sobre las opciones de redundancia de datos en Azure Storage, consulte [Redundancia de Azure Storage](../common/storage-redundancy.md).  

## <a name="archive-access-tier"></a>Nivel de acceso de archivo

El nivel de acceso de archivo es un nivel sin conexión para almacenar datos a los que rara vez se accede. El nivel de acceso de archivo tiene el menor costo de almacenamiento, pero mayores costos de recuperación de datos y latencia en comparación con los niveles de acceso frecuente y esporádico. Entre los ejemplos de escenarios de uso del nivel de acceso de archivo se incluyen:

- Copia de seguridad a largo plazo, copia de seguridad secundaria y conjuntos de datos de archivado
- Datos originales (sin procesar) que deben conservarse, incluso después de que se hayan procesado en un formato útil final
- Datos de cumplimiento y archivado que se deben almacenar durante un largo período de tiempo y a los que casi nunca se accede

Los datos deben permanecer en el nivel de archivo durante al menos 180 días o estar sujetos a un cargo por eliminación temprana. Por ejemplo, si un blob se mueve al nivel de acceso de archivo y, a continuación, se elimina o se mueve al nivel de acceso frecuente al cabo de 45 días, se le cobrará una cuota de eliminación temprana equivalente a 135 (180 menos 45) días a partir del almacenamiento de ese blob en el nivel de acceso de archivo.

Un blob no se puede leer ni modificar mientras se encuentre en el nivel de archivo. Para leer o descargar un blob en el nivel de acceso de archivo, primero debe rehidratarlo en un nivel en línea, ya sea de acceso esporádico o de acceso frecuente. Los datos del nivel de acceso de archivo pueden tardar hasta 15 horas en rehidratarse. Para más información sobre la rehidratación de blobs, consulte [Rehidratación de blobs desde el nivel de acceso de archivo](archive-rehydrate-overview.md).

Los metadatos de un blob archivado permanecen disponibles para el acceso de lectura, por lo que puede enumerar el blob y sus propiedades, metadatos y etiquetas de índice. Los metadatos de un blob del nivel de acceso de archivo son de solo lectura, mientras que las etiquetas de índice del blob se pueden leer o escribir. No se admiten instantáneas para blobs archivados.

Se admiten las siguientes operaciones para los blobs del nivel de archivo:

- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Establecer etiquetas de blobs](/rest/api/storageservices/set-blob-tags)
- [Obtener etiquetas de blobs](/rest/api/storageservices/get-blob-tags)
- [Buscar blobs por etiquetas](/rest/api/storageservices/find-blobs-by-tags)
- [Enumeración de blobs](/rest/api/storageservices/list-blobs)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url)
- [Delete Blob](/rest/api/storageservices/delete-blob)

> [!NOTE]
> El nivel de archivo no se admite en las cuentas de ZRS, GZRS o RA-GZRS. Se admite la migración de LRS a GRS siempre que no se hayan trasladado blobs al nivel de archivo mientras la cuenta estaba establecida en LRS. Una cuenta se puede devolver a GRS si la actualización se realiza en menos de 30 días desde el momento en que la cuenta se convirtió en LRS y no se ha movido ningún blob al nivel de archivo mientras la cuenta estaba establecida en LRS.

## <a name="default-account-access-tier-setting"></a>Configuración predeterminada del nivel de acceso de la cuenta

Las cuentas de almacenamiento tienen una configuración predeterminada del nivel de acceso que indica el nivel en línea en el que se crea un nuevo blob. La configuración predeterminada del nivel de acceso se puede establecer en frecuente o esporádico. Los usuarios pueden invalidar la configuración predeterminada de un blob individual al cargar el blob o cambiar su nivel.

De manera predeterminada, el nivel de acceso predeterminado de una nueva cuenta de almacenamiento se establece en el nivel de acceso frecuente. Puede cambiar la configuración predeterminada del nivel de acceso al crear una cuenta de almacenamiento o después de crearla. Si no cambia esta configuración, los blobs se cargan en el nivel de acceso frecuente de manera predeterminada.

Un blob que no tiene un nivel asignado explícitamente infiere el nivel de la configuración predeterminada del nivel de acceso de la cuenta. Si el nivel de acceso de un blob se infiere de la configuración predeterminada del nivel de acceso de la cuenta, Azure Portal muestra el nivel de acceso como **Frecuente (inferido)** o **Esporádico (inferido)** .

El cambio de la configuración predeterminada del nivel de acceso de la cuenta se aplica a todos los blobs de la cuenta para los que no se ha establecido explícitamente un nivel de acceso. Si alterna la configuración predeterminada del nivel de acceso de la cuenta de frecuente a esporádico en una cuenta de uso general v2, se le cobrarán las operaciones de escritura (por cada 10 000) para todos los blobs para los que se infiere el nivel de acceso. En las cuentas de Blob Storage no se realiza ningún cargo por este cambio. Si cambia el nivel de acceso de las cuentas de Blob Storage o v2 de uso general de esporádico a frecuente, se le cobran tanto las operaciones de lectura (por cada 10 000) como las de recuperación de datos (por GB).

> [!NOTE]
> El nivel de acceso de archivo no se admite como nivel de acceso predeterminado para una cuenta de almacenamiento.

## <a name="setting-or-changing-a-blobs-tier"></a>Establecimiento o cambio del nivel de un blob

Para establecer explícitamente el nivel de un blob al crearlo, especifique el nivel al cargar el blob.

Después de crear un blob, puede cambiar su nivel de cualquiera de las maneras siguientes:

- Mediante una llamada a la operación [Set Blob Tier](/rest/api/storageservices/set-blob-tier), ya sea directamente o mediante una directiva de [administración del ciclo de vida](#blob-lifecycle-management). Llamar a [Set Blob Tier](/rest/api/storageservices/set-blob-tier) suele ser la mejor opción para cambiar el nivel de un blob de un nivel más frecuente a uno más esporádico.
- Mediante una llamada a la operación [Copy Blob](/rest/api/storageservices/copy-blob) para copiar un blob de un nivel a otro. Se recomienda llamar a [Copy Blob](/rest/api/storageservices/copy-blob) en la mayoría de los escenarios en los que se va a rehidratar un blob desde el nivel de archivo a un nivel en línea o al mover un blob de esporádico a frecuente. Al copiar un blob, puede evitar la penalización por eliminación anticipada si aún no ha transcurrido el intervalo de almacenamiento necesario para el blob de origen. Sin embargo, la copia de un blob da como resultado cargos de capacidad para dos blobs, el blob de origen y el blob de destino.

El cambio de nivel de un blob de acceso frecuente a esporádico o de archivo es instantáneo, al igual que el cambio de esporádico a frecuente. La rehidratación de un blob desde el nivel de acceso de archivo al nivel de acceso frecuente o esporádico puede tardar hasta 15 horas.

Tenga en cuenta los siguientes puntos al mover un blob entre los niveles de acceso esporádico y de archivo:

- Si se infiere un blob como esporádico en el nivel de acceso predeterminado de la cuenta de almacenamiento y se mueve al nivel de acceso de archivo, no hay ningún cargo de eliminación temprana.
- Si un blob se mueve explícitamente al nivel de acceso esporádico y luego se mueve al nivel de acceso de archivo, se aplica el cargo de eliminación temprana.

## <a name="blob-lifecycle-management"></a>Administración del ciclo de vida de blobs

La administración del ciclo de vida del almacenamiento de blobs ofrece una directiva basada en reglas que puede usar para realizar la transición de los datos al nivel de acceso deseado cuando se cumplan las condiciones especificadas. También puede usar la administración del ciclo de vida para expirar los datos al final de su ciclo de vida. Vea [Optimización de los costos mediante la automatización de los niveles de acceso de Azure Blob Storage](./lifecycle-management-overview.md) para obtener más información.

> [!NOTE]
> Los datos almacenados en una cuenta de almacenamiento de blobs en bloques Premium no se pueden organizar en niveles de acceso frecuente, esporádico o de archivo con [Set Blob Tier](/rest/api/storageservices/set-blob-tier) ni mediante la administración del ciclo de vida de Azure Blob Storage. Para mover datos, debe copiar los blobs de forma sincrónica desde la cuenta de almacenamiento de blobs en bloques al nivel de acceso frecuente de otra cuenta mediante la [API Put Block From URL](/rest/api/storageservices/put-block-from-url) o una versión de AzCopy que admita esta API. **Put Block From URL** API copia datos de forma asíncrónica en el servidor, lo que significa que la llamada solo se completa una vez que todos los datos se han movido de la ubicación original del servidor a la ubicación de destino.

## <a name="summary-of-access-tier-options"></a>Resumen de las opciones de niveles de acceso

En la tabla siguiente, se resumen las características de los niveles de acceso frecuente, esporádico y de archivo.

|  | **Nivel frecuente** | **Nivel esporádico** | **Nivel de archivo** |
|--|--|--|--|
| **Disponibilidad** | 99,9 % | 99% | Sin conexión |
| **Disponibilidad** <br> **(lecturas de RA-GRS)** | 99,99% | 99,9 % | Sin conexión |
| **Cargos de uso** | Mayores costos de almacenamiento, pero menores costos de acceso y de transacciones | Menores costos de almacenamiento, pero mayores costos de acceso y de transacciones | Los menores costos de almacenamiento, pero los mayores costos de acceso y de transacciones |
| **Período mínimo de retención de datos recomendado** | N/D | 30 días<sup>1</sup> | 180 días |
| **Latency** <br> **(Tiempo hasta el primer byte)** | Milisegundos | Milisegundos | Horas<sup>2</sup> |
| **Configuraciones de redundancia admitidas** | All | All | Solo LRS, GRS y RA-GRS<sup>3</sup> |

<sup>1</sup> Los objetos del nivel de acceso esporádico en cuentas de uso general v2 tienen una duración mínima de la retención de 30 días. Para las cuentas de Blob Storage, no hay ninguna duración mínima de la retención para el nivel de acceso esporádico.

<sup>2</sup> Al rehidratar un blob desde el nivel de acceso de archivo, puede elegir una opción de prioridad de rehidratación estándar o alta. Cada una ofrece diferentes latencias y costos de recuperación. Para más información, consulte [Introducción a la rehidratación de blobs desde el nivel de archivo](archive-rehydrate-overview.md).

<sup>3</sup> Para más información sobre las configuraciones de redundancia en Azure Storage, consulte [Redundancia de Azure Storage](../common/storage-redundancy.md).

## <a name="pricing-and-billing"></a>Precios y facturación

Todas las cuentas de almacenamiento usan un modelo de precios para el almacenamiento de blobs en bloques basado en el nivel de un blob. Tenga en cuenta las consideraciones de facturación que se describen en las secciones siguientes.

Para más información sobre los precios de los blobs en bloques, consulte [Precios de Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="storage-capacity-costs"></a>Costos de la capacidad de almacenamiento

Además de la cantidad de datos almacenados, el costo varía en función del nivel de acceso. El costo de capacidad por gigabyte disminuye a medida que el nivel es más esporádico.

### <a name="data-access-costs"></a>Costos de acceso a datos

los gastos de acceso a los datos aumentan a medida que el nivel es más esporádico. En el nivel de acceso esporádico y de archivo se cobra un cargo de acceso a datos por gigabyte por las operaciones de lectura.

### <a name="transaction-costs"></a>Costos de transacciones

Se aplica un cargo por transacción a todos los niveles, que aumenta a medida que el nivel es más esporádico.

### <a name="geo-replication-data-transfer-costs"></a>Costos de transferencia de datos de replicación geográfica

este cargo solo se aplica a las cuentas con replicación geográfica configurada, incluidas GRS y RA-GRS. La transferencia de datos de replicación geográfica incurre en un cargo por gigabyte.

### <a name="outbound-data-transfer-costs"></a>Costes de transferencia de datos salientes

Las transferencias de datos de salida (aquellos que se transfieren fuera de una región de Azure) generan cargos de facturación por el uso del ancho de banda por gigabyte. Para obtener más información sobre los cargos por transferencia de datos salientes, vea la página [Detalles de precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="changing-the-default-account-access-tier"></a>Cambio del nivel de acceso predeterminado de la cuenta

El cambio del nivel de acceso de la cuenta da como resultado cargos por cambio de nivel para todos los blobs que no tengan un nivel explícito establecido. Para más información, consulte la sección siguiente, [Cambio del nivel de acceso de un blob](#changing-a-blobs-access-tier).

### <a name="changing-a-blobs-access-tier"></a>Cambio del nivel de acceso de un blob

Tenga en cuenta los siguientes impactos en la facturación al cambiar el nivel de un blob:

- Cuando un blob se carga o se mueve entre niveles, se cobra a la tarifa correspondiente inmediatamente después de la carga o el cambio de nivel.
- Cuando se mueve un blob a un nivel de almacenamiento de acceso más esporádico (de frecuente a esporádico, de frecuente a archivo o de esporádico a archivo), la operación se factura como una operación de escritura del nivel de destino, donde se aplican los cargos de la operación de escritura (por cada 10 000) y de escritura de datos (por cada GB) del nivel de destino.
- Cuando se mueve un blob a un nivel más frecuente (de archivo a esporádico, de archivo a frecuente, o de esporádico a frecuente), la operación se factura como una lectura desde el nivel de origen, donde se aplican los cargos de la operación de lectura (por cada 10 000) y de recuperación de datos (por cada GB) del nivel de origen. También podrían aplicarse cargos por la eliminación temprana de algún blob que se haya trasladado desde el nivel de acceso esporádico o de archivo.
- Mientras se rehidrata un blob desde el nivel de archivo, los datos de ese blob se facturan como datos archivados hasta que se restauran los datos y el nivel del blob cambia a acceso frecuente o esporádico.

La tabla siguiente resume cómo se facturan los cambios de nivel:

| | **Cargos de escritura (operación + acceso)** | **Cargos de lectura (operación + acceso)** |
| ---- | ----- | ----- |
| Operación **Set Blob Tier** | De frecuente a esporádico<br> De frecuente a archivo<br> De esporádico a archivo | De archivo a esporádico<br> De archivo a frecuente<br> De esporádico a frecuente

Cambiar el nivel de acceso de un blob cuando el control de versiones está habilitado, o si el blob tiene instantáneas, puede dar lugar a cargos adicionales. Para obtener información sobre los blobs con el control de versiones habilitado, vea [Precios y facturación](versioning-overview.md#pricing-and-billing) en la documentación sobre el control de versiones de blob. Para obtener información sobre los blobs con instantáneas, vea [Precios y facturación](snapshots-overview.md#pricing-and-billing) en la documentación sobre instantáneas de blob.

## <a name="feature-support"></a>Compatibilidad de características

En esta tabla se muestra cómo se admite esta característica en la cuenta y el impacto en la compatibilidad al habilitar determinadas funcionalidades.

| Tipo de cuenta de almacenamiento | Blob Storage (compatibilidad predeterminada) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> |
|--|--|--|--|
| De uso general estándar, v2 | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |
| Blobs en bloques Premium | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |

<sup>1</sup> Tanto Data Lake Storage Gen2 como el protocolo Network File System (NFS) 3.0 necesitan una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado.

Para obtener información sobre la compatibilidad con características por región, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Pasos siguientes

- [Administración del nivel de acceso de un blob en una cuenta de Azure Storage](manage-access-tier.md)
- [Administración del nivel de acceso predeterminado de una cuenta de Azure Storage](../common/manage-account-default-access-tier.md)
- [Optimización de los costos mediante la automatización de los niveles de acceso de Azure Blob Storage](./lifecycle-management-overview.md)
