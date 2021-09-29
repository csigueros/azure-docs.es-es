---
title: Introducción a la rehidratación de blobs desde el nivel de archivo
description: Mientras un blob se encuentra en el nivel de acceso de archivo, se considera que está sin conexión y no se puede leer ni modificar. Para leer o modificar los datos de un blob archivado, primero debe rehidratar el blob en un nivel en línea, ya sea el nivel de acceso frecuente o esporádico.
services: storage
author: tamram
ms.author: tamram
ms.date: 08/31/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: 2c4eac524ecda8a2b90036748fd2a6f2a389a3cd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823728"
---
# <a name="overview-of-blob-rehydration-from-the-archive-tier"></a>Introducción a la rehidratación de blobs desde el nivel de archivo

Mientras un blob se encuentra en el nivel de acceso de archivo, se considera que está sin conexión y no se puede leer ni modificar. Para leer o modificar los datos de un blob archivado, primero debe rehidratar el blob en un nivel en línea, ya sea el nivel de acceso frecuente o esporádico. Hay dos opciones para rehidratar un blob que se almacena en el nivel de archivo:

- [Copiar un blob archivado en un nivel en línea](#copy-an-archived-blob-to-an-online-tier): puede rehidratar un blob archivado si lo copia en un nuevo blob en el nivel de acceso frecuente o esporádico con la operación [Copy Blob](/rest/api/storageservices/copy-blob) o [Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url). Microsoft recomienda esta opción para la mayoría de los escenarios.

- [Cambio del nivel de acceso de un blob a un nivel en línea](#change-a-blobs-access-tier-to-an-online-tier): puede rehidratar un blob archivado a acceso frecuente o esporádico cambiando su nivel mediante la operación [Set Blob Tier](/rest/api/storageservices/set-blob-tier).

La rehidratación de un blob de un nivel de acceso de archivo puede tardar varias horas en completarse. Microsoft recomienda rehidratar blobs más grandes para obtener un rendimiento óptimo. La rehidratación de varios blobs pequeños a la vez puede requerir tiempo adicional.

Puede configurar [Azure Event Grid](../../event-grid/overview.md)para que genere un evento cuando rehidrate un blob desde el nivel de archivo a un nivel en línea y envíe el evento a un controlador de eventos. Para obtener más información, consulte [Control de un evento en la rehidratación de blobs](#handle-an-event-on-blob-rehydration).

Para más información sobre los niveles de acceso en Azure Storage, consulte [Azure Blob Storage: niveles de acceso frecuente, esporádico y de archivo](storage-blob-storage-tiers.md).

## <a name="rehydration-priority"></a>Prioridad de la rehidratación

Al rehidratar un blob, puede establecer la prioridad de la operación de rehidratación mediante el encabezado opcional *x-ms-rehydrate-priority* en una operación [Set Blob Tier](/rest/api/storageservices/set-blob-tier) o [Copy Blob](/rest/api/storageservices/copy-blob)/[Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url). Las opciones de prioridad de rehidratación incluyen:

- **Prioridad estándar**: la solicitud de rehidratación se procesará en el orden en que se recibió y puede tardar hasta 15 horas.
- **Alta prioridad**: la solicitud de rehidratación tendrá prioridad con respecto a las solicitudes de prioridad estándar y podría completarse en menos de una hora para objetos con un tamaño inferior a 10 GB.

Para comprobar la prioridad de rehidratación mientras se está en curso la operación de rehidratación, llame a [BLObGet Blob Properties](/rest/api/storageservices/get-blob-properties) para devolver el valor del encabezado `x-ms-rehydrate-priority`. La propiedad de prioridad de rehidratación devuelve *Standard* o *High*.

La prioridad estándar es la opción de rehidratación predeterminada. Una rehidratación de alta prioridad es más rápida, pero también cuesta más que una rehidratación de prioridad estándar. Una rehidratación de alta prioridad puede tardar más de una hora, según el tamaño de blob y la demanda actual. Microsoft recomienda reservar la rehidratación de alta prioridad para su uso en situaciones de restauración de datos de emergencia.

Para más información sobre las diferencias de precios entre las solicitudes de rehidratación de prioridad estándar y de alta prioridad, consulte [Precios de Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copia de un blob archivado en un nivel en línea

La primera opción para mover un blob del nivel de archivo a un nivel en línea es copiar el blob archivado a un nuevo blob de destino que se encuentre en el nivel de acceso frecuente o esporádico. Puede usar la operación [Copy Blob](/rest/api/storageservices/copy-blob) o [Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url) para copiar el blob. Al copiar un blob archivado en un nuevo blob en un nivel en línea, el blob de origen permanece sin modificar en el nivel de archivo.

Debe copiar el blob archivado en un nuevo blob con un nombre diferente o en un contenedor diferente. No se puede sobrescribir el blob de origen copiándolo en el mismo blob.

Microsoft recomienda realizar una operación de copia en la mayoría de los escenarios en los que es necesario mover un blob del nivel de archivo a un nivel en línea, por los siguientes motivos:

- Una operación de copia evita la cuota de eliminación anticipada que se aplica si cambia el nivel de un blob del nivel de archivo antes de que transcurra el período requerido de 180 días. Para más información, consulte [Nivel de acceso de archivo](storage-blob-storage-tiers.md#archive-access-tier).
- Si hay una directiva de administración del ciclo de vida en vigor para la cuenta de almacenamiento, la rehidratación de un blob con la operación [Set Blob Tier](/rest/api/storageservices/set-blob-tier) puede dar lugar a un escenario en el que la directiva de ciclo de vida mueva el blob de nuevo al nivel de archivo después de la rehidratación, ya que la hora de la última modificación supera el umbral establecido para la directiva. Una operación de copia deja el blob de origen en el nivel de archivo y crea un nuevo blob con un nombre diferente y una nueva hora de última modificación, por lo que no hay ningún riesgo de que la directiva de ciclo de vida devuelva el blob rehidratado al nivel de archivo.

La copia de un blob desde el nivel de archivo puede tardar horas en completarse, en función de la prioridad de rehidratación seleccionada. En segundo plano, una operación de copia lee el blob de origen de archivo para crear un nuevo blob en línea en el nivel de destino seleccionado. El nuevo blob puede estar visible cuando se enumeren los blobs en el contenedor primario antes de que se complete la operación de rehidratación, pero su nivel se establecerá en archivo; los datos no estarán disponibles hasta que se complete la operación de lectura del blob de origen en el nivel de archivo y el contenido del blob se haya escrito en el nuevo blob de destino en un nivel en línea. El nuevo blob es una copia independiente, por lo que modificarlo o eliminarlo no afecta al blob de origen en el nivel de archivo.

Para obtener información sobre cómo rehidratar un blob copiándolo en un nivel en línea, consulte [Rehidratación de un blob con una operación de copia](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation).

> [!IMPORTANT]
> No elimine el blob de origen hasta que la rehidratación se complete correctamente. Si se elimina el blob de origen, es posible que el blob de destino no finalice la copia. Puede controlar el evento que se genera cuando se completa la operación de copia para saber cuándo es seguro eliminar el blob de origen. Para obtener más información, consulte [Control de un evento en la rehidratación de blobs](#handle-an-event-on-blob-rehydration).

Solo se admite la copia de un blob archivado en un nivel de destino en línea dentro de la misma cuenta de almacenamiento. No se puede copiar un blob archivado en un blob de destino que también esté en el nivel de archivo.

En la tabla siguiente se muestra el comportamiento de una operación de copia de blob, en función de los niveles del blob de origen y de destino.

|  | **Origen de nivel de acceso frecuente** | **Origen de nivel de acceso esporádico** | **Origen de nivel de archivo** |
|--|--|--|--|
| **Destino de nivel de acceso frecuente** | Compatible | Compatible | Se admite dentro de la misma cuenta. Requiere la rehidratación de blobs. |
| **Destino de nivel de acceso esporádico** | Compatible | Compatible | Se admite dentro de la misma cuenta. Requiere la rehidratación de blobs. |
| **Destino de nivel de archivo** | Compatible | Compatible | No compatible |

## <a name="change-a-blobs-access-tier-to-an-online-tier"></a>Cambio del nivel de acceso de un blob a un nivel en línea

La segunda opción para rehidratar un blob del nivel de archivo a un nivel en línea es cambiar el nivel del blob mediante una llamada a [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Con esta operación, puede cambiar el nivel del blob archivado a nivel de acceso frecuente o esporádico.

Una vez que se inicia una solicitud [Set Blob Tier](/rest/api/storageservices/set-blob-tier), no se puede cancelar. Durante la operación de rehidratación, la configuración del nivel de acceso del blob continúa a modo de archivado hasta que se completa el proceso de rehidratación. Una vez completada la operación de rehidratación, la propiedad del nivel de acceso del blob se actualiza para reflejar el nuevo nivel.

Para obtener información sobre cómo rehidratar un blob cambiando su nivel a un nivel en línea, consulte [Rehidratación de un blob cambiando su nivel](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier).

> [!CAUTION]
> Cambiar el nivel de un blob no afecta a la hora de la última modificación. Si hay una directiva de [administración del ciclo de vida](./lifecycle-management-overview.md) en vigor para la cuenta de almacenamiento, la rehidratación de un blob con la operación **Set Blob Tier** puede dar lugar a un escenario en el que la directiva de ciclo de vida mueva el blob de nuevo al nivel de archivo después de la rehidratación, ya que la hora de la última modificación supera el umbral establecido para la directiva.
>
> Para evitar este escenario, en su lugar, rehidrate el blob archivado copiándolo, como se describe en la sección [Copia de un blob archivado en un nivel en línea](#copy-an-archived-blob-to-an-online-tier). La realización de una operación de copia crea una nueva instancia del blob con una hora de la última modificación actualizada, por lo que no desencadena la directiva de administración del ciclo de vida.

## <a name="check-the-status-of-a-blob-rehydration-operation"></a>Comprobación del estado de la operación de rehidratación del blob

Durante la operación de rehidratación del blob, puede llamar a la operación [Get Blob Properties](/rest/api/storageservices/get-blob-properties) para comprobar su estado. Para obtener información sobre cómo comprobar el estado de una operación de rehidratación, consulte [Comprobación del estado de una operación de rehidratación](archive-rehydrate-to-online-tier.md#check-the-status-of-a-rehydration-operation).

## <a name="handle-an-event-on-blob-rehydration"></a>Control de un evento en la rehidratación de blobs

La rehidratación de un blob archivado puede tardar hasta 15 horas y sondear repetidamente **Get Blob Properties** para determinar si la rehidratación se ha completado es ineficaz. Para capturar el evento que se produce cuando se completa la rehidratación, el uso de [Azure Event Grid](../../event-grid/overview.md) ofrece un mejor rendimiento y optimización de costos.

Azure Event Grid genera uno de los dos eventos siguientes en la rehidratación de blobs, en función de la operación que se usó para rehidratar el blob:

- El evento **Microsoft.Storage.BlobCreated** se desencadena cuando se crea un blob. En el contexto de la rehidratación de blobs, este evento se produce cuando una operación [Copy Blob](/rest/api/storageservices/copy-blob) o [Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url) crea un nuevo blob de destino en el nivel de acceso frecuente o esporádico y los datos del blob se rehidratan completamente desde el nivel de archivo.
- El evento **Microsoft.Storage.BlobTierChanged** se desencadena cuando se cambia el nivel de un blob. En el contexto de la rehidratación de blobs, este evento se desencadena cuando una operación [Set Blob Tier](/rest/api/storageservices/set-blob-tier) cambia correctamente el nivel de un blob archivado al nivel de acceso frecuente o esporádico.

Para obtener información sobre cómo capturar un evento de rehidratación y enviarlo a un controlador de eventos de Azure Function, consulte [Ejecución de una función de Azure en respuesta a un evento de rehidratación de blobs](archive-rehydrate-handle-event.md).

Para obtener más información sobre el control de eventos en Blob Storage, consulte [Reacción ante eventos de Blob Storage](storage-blob-event-overview.md) y [Azure Blob Storage como origen de Event Grid](../../event-grid/event-schema-blob-storage.md).

## <a name="pricing-and-billing"></a>Precios y facturación

Una operación de rehidratación con [Set Blob Tier](/rest/api/storageservices/set-blob-tier) se factura por las transacciones de lectura de datos y el tamaño de recuperación de datos. Una rehidratación de alta prioridad tiene mayores costos de operación y recuperación de datos que los de la prioridad estándar. La rehidratación de alta prioridad aparece como una partida independiente en la factura. Si una solicitud de prioridad alta para devolver un blob de archivo de varios gigabytes tarda más de cinco horas, no se le cobrará la tarifa de recuperación de prioridad alta. Sin embargo, se seguirán aplicando las tarifas de recuperación estándar.

La copia de un blob archivado en un nivel en línea con [Copy Blob](/rest/api/storageservices/copy-blob) o [Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url) se factura por las transacciones de lectura de datos y el tamaño de recuperación de datos. La creación del blob de destino en un nivel en línea se factura por las transacciones de escritura de datos. Las tarifas de eliminación anticipada no se aplican al realizar la copia en un blob en línea porque el blob de origen permanece sin modificar en el nivel de archivo. Si se selecciona, se aplican cargos por recuperación de alta prioridad.

Los blobs de nivel de archivo deben estar almacenados durante un mínimo de 180 días. La eliminación o el cambio del nivel de un blob archivado antes de que transcurra el período de 180 días conlleva una cuota de eliminación anticipada. Para más información, consulte [Nivel de acceso de archivo](storage-blob-storage-tiers.md#archive-access-tier).

Para obtener más información sobre los precios de los blobs en bloques, consulte la página [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). Para obtener más información sobre los cargos por la transferencia de datos salientes, consulte la página [Detalles de precios de ancho de banda](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="see-also"></a>Vea también

- [Azure Blob Storage: niveles de acceso frecuente, esporádico y de archivo](storage-blob-storage-tiers.md).
- [Rehidratación de un blob archivado en un nivel en línea](archive-rehydrate-to-online-tier.md)
- [Ejecución de una función de Azure en respuesta a un evento de rehidratación de blobs](archive-rehydrate-handle-event.md)
- [Reacción a eventos de Blob Storage](storage-blob-event-overview.md)