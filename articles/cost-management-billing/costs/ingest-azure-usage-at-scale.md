---
title: Recuperación recurrente de conjuntos de datos de costos de gran tamaño con exportaciones desde Azure Cost Management
description: Este artículo lo ayuda a exportar periódicamente grandes cantidades de datos con exportaciones desde Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a27e64e7b5c94d033529c08ea02e757ba8857549
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705843"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Recuperación recurrente de conjuntos de datos de costos de gran tamaño con exportaciones

Este artículo lo ayuda a exportar periódicamente grandes cantidades de datos con exportaciones desde Cost Management. La exportación es el método recomendado para recuperar datos de costo no agregados. Especialmente cuando los archivos de uso son demasiado grandes para realizar llamadas y descargas de forma confiable mediante Usage Details API. Los datos exportados se colocan en la cuenta de Azure Storage que elija. Desde allí, los puede cargar en sus propios sistemas y analizarlos en función de sus necesidades. Para configurar las exportaciones en Azure Portal, consulte [Exportar datos](tutorial-export-acm-data.md).

Si desea automatizar las exportaciones en varios ámbitos, la solicitud de la API de ejemplo de la próxima sección es un buen punto de partida. Exports API se puede usar para crear exportaciones automáticas como parte de la configuración general del entorno. Las exportaciones automática sirven de ayuda para asegurarse que se tienen los datos necesarios y se pueden usar en los sistemas de cualquier organización cuando se expande el uso de Azure.

## <a name="common-export-configurations"></a>Configuraciones de exportación comunes

Antes de crear la primera exportación, tenga en cuenta el escenario y las opciones de configuración necesarias para habilitarlo. Considere las siguientes opciones de exportación:

- **Periodicidad**: determina la frecuencia con que se ejecuta el trabajo de exportación y cuándo se colocan los archivos en la cuenta de Azure Storage. Se puede elegir entre las siguientes opciones, Diaria, Semanal y Mensual. Intente configurar su periodicidad para que coincida con los trabajos de importación de datos que usa el sistema interno de su organización.
- **Periodo de periodicidad**: determina el periodo de validez de la exportación. Los archivos solo se exportan durante el período de periodicidad.
- **Período de tiempo**: determina la cantidad de datos que genera la exportación en una ejecución determinada. Las opciones comunes son MonthToDate y WeekToDate.
- **StartDate**: configura cuándo se desea que se inicie la programación de las exportaciones. Las exportaciones se crean en StartDate y, posteriormente, se basan en el valor de Periodicidad.
- **Tipo**: hay tres tipos de exportación:
  - ActualCost: muestra el uso total y los costos del período especificado, tal como se acumulan y se muestran en la factura.
  - AmortizedCost: muestra el uso total y los costos del período especificado, con la amortización aplicada a los costos de compra de la reserva que se aplican.
  - Usage: todas las exportaciones creadas antes del 20 de julio de 2020 son del tipo Usage. Actualice todas las exportaciones programadas para que sean de los tipos ActualCost o AmortizedCost.
- **Columnas**: define los campos de datos que se desean incluir en el archivo de exportación. Se corresponden con los campos disponibles en Usage Details API. Para más información, consulte [Usage Details API](/rest/api/consumption/usagedetails/list).

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Creación de una exportación diaria hasta la fecha de una suscripción

Dirección URL de la solicitud: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Copia de blobs de Azure Storage de gran tamaño

Puede usar Cost Management para programar exportaciones de los detalles de uso de Azure en sus cuentas de Azure Storage como blobs. Los tamaños de blob resultantes pueden tener un tamaño superior a gigabytes. El equipo de Cost Management ha colaborado con el equipo de Azure Storage para probar la copia de blobs de Azure Storage de gran tamaño. Los resultados se documentan en las secciones siguientes. Puede esperar tener resultados similares a cuando copia los blobs de Storage de una región de Azure a otra.

Para probar su rendimiento, el equipo transfirió blobs desde cuentas de almacenamiento de la región Oeste de EE. UU. a la misma región y a otras regiones. El equipo midió velocidades que van desde 2 GB por segundo en la misma región hasta 150 MB por segundo en las cuentas de almacenamiento de la región Sudeste de Asia.

### <a name="test-configuration"></a>Configuración de prueba

Para medir la velocidad de transferencia de blobs, el equipo creó una sencilla aplicación de consola de .NET que hace referencia a la versión más reciente (v2.0.1) de la biblioteca de movimiento de datos de Azure (DLM) mediante NuGet. DLM es un SDK proporcionado por el equipo de Azure Storage que facilita el acceso mediante programación a sus servicios de transferencia. A continuación, crearon cuentas de almacenamiento estándar V2 en varias regiones y utilizaron la región Oeste de EE. UU. como región de origen. Rellenaron las cuentas de almacenamiento con contenedores, donde cada una de ellas contenía diez blobs en bloques de 2 GB. Copiaron los contenedores en otras cuentas de almacenamiento mediante el método _TransferManager.CopyDirectoryAsync()_ de DLM con la opción _CopyMethod.ServiceSideSyncCopy_. Las pruebas se realizaron en un equipo que ejecuta Windows 10 con 12 núcleos y una red de 1 GbE.

Configuración de la aplicación utilizada:

- _TransferManager.Configurations.ParallelOperations_ = _Environment.ProcessorCount \* 32_. El equipo encontró la configuración que tiene el mayor efecto en el rendimiento general. Un valor de 32 veces el número de núcleos proporcionó el mejor rendimiento para el cliente de prueba.
- _ServicePointManager.DefaultConnectionLimit = int.MaxValue_. Si se establece en un valor máximo, se pasa el control total del paralelismo de transferencia al valor de la opción _ParallelOperations_ anterior.
- _TransferManager.Configurations.BlockSize = 4 194 304_. Tenía algún efecto en las velocidades de transferencia con 4 MB, lo que demuestra que es lo mejor para las pruebas.

Para más información y obtener el código de ejemplo, consulte los vínculos de la sección [Pasos siguientes](#next-steps).

### <a name="test-results"></a>Resultados de pruebas

| **Número de prueba** | **Región de destino** | **Blobs** | **Tiempo (segundos)** | **MB/s** | **Comentarios** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2 GB x 10 | 10 | 2\.000 |   |
| 2 | WestUS2 | 2 GB x 10 | 33 | 600 |   |
| 3 | EastUS | 2 GB x 10 | 67 | 300 |   |
| 4 | EastUS | 2 GB x 10 x 4 | 99 | 200 | 4 transferencias en paralelo que usan 8 cuentas de almacenamiento: una media de 4 de la región Oeste a 4 de la región Este por transferencia |
| 6 | EastUS | 2 GB x 10 x 4 | 92 | 870 | 4 transferencias en paralelo de 1 cuenta de almacenamiento a otra |
| 5 | EastUS | 2 GB x 10 x 8 | 148 | 135 | 8 transferencias en paralelo que usan 8 cuentas de almacenamiento: una media de 4 de la región Oeste a 4x2 de la región Este por transferencia |
| 7 | Sudeste Asiático | 2 GB x 10 | 133 | 150 |   |
| 8 | Sudeste Asiático | 2 GB x 10 x 4 | 444 | 180 | 4 transferencias en paralelo de 1 cuenta de almacenamiento a otra |

### <a name="sync-transfer-characteristics"></a>Características de la transferencia sincrónica

Estas son algunas de las características de la transferencia sincrónica del servicio que se usa con DML y que son importantes para su uso:

- DML puede transferir un solo blob o un directorio. Para la transferencia de directorios, puede usar un patrón de búsqueda para buscar coincidencias en el prefijo del blob.
- Las transferencias de blobs en bloques se producen en paralelo. Todo completado hacia el final del proceso de transferencia. Los blobs en bloques individuales se transfieren en paralelo.
- La transferencia se ejecuta de forma asincrónica en el cliente. El estado de la transferencia está disponible periódicamente mediante una devolución de llamada a un método que se puede definir en un objeto _TransferContext_.
- La transferencia crea puntos de control durante su progreso y expone un objeto _TransferCheckpoint_. El objeto representa el punto de control más reciente mediante el objeto _TransferContext_. Si el objeto _TransferCheckpoint_ se guarda antes de que se cancele o se anule la transferencia, la transferencia se puede reanudar desde el punto de comprobación hasta un plazo de siete días. La transferencia se puede reanudar desde cualquier punto de control, no solo el último.
- Si el proceso del cliente de transferencia se elimina y se reinicia sin implementar la característica de punto de control
  - antes de que se completen las transferencias de blobs, se reinicia la transferencia.
  - Una vez completados algunos de los blobs, la transferencia se reinicia solo para los blobs incompletos.
- Al poner en pausa la ejecución del cliente se ponen en pausa las transferencias.
- La característica de transferencia de blobs abstrae al cliente de errores transitorios. Por ejemplo, la limitación de la cuenta de almacenamiento normalmente no provocará un error en una transferencia, sino que ralentizará la transferencia.
- Las transferencias del lado del servicio tienen poco uso de recursos del cliente para la CPU y la memoria, y algo de ancho de banda de red y conexiones.

### <a name="async-transfer-characteristics"></a>Características de la transferencia asincrónica

Puede invocar el método _TransferManager.CopyDirectoryAsync()_ con la opción _CopyMethod.ServiceSideAsyncCopy_. Funciona de forma similar al mecanismo de transferencia sincrónica desde la perspectiva del cliente, pero con las siguientes diferencias de funcionamiento:

- Las velocidades de transferencia son mucho más lentas que la transferencia sincrónica equivalente (normalmente 10 MB/s o menos).
- La transferencia continúa incluso si finaliza el proceso del cliente.
- Aunque se admiten los puntos de control, reanudar una transferencia mediante un objeto _TransferCheckpoint_ no la reanudará en el momento del punto de control, sino en el estado actual de la transferencia.

### <a name="test-summary"></a>Resumen de la prueba

Azure Blob Storage admite altas velocidades de transferencia global con su característica de transferencia sincrónica del lado del servicio. El uso de la característica en aplicaciones de .NET es sencillo con la biblioteca de movimiento de datos. Para las exportaciones de Cost Management, es posible copiar de forma confiable cientos de gigabytes de datos en una cuenta de almacenamiento en menos de una hora.

## <a name="next-steps"></a>Pasos siguientes

- Consulte el código fuente de la [biblioteca de movimiento de datos de Microsoft Azure Storage](https://github.com/Azure/azure-storage-net-data-movement).
- [Transferencia de datos con la biblioteca de movimiento de datos](../../storage/common/storage-use-data-movement-library.md)
- Consulte el código fuente de la [aplicación de ejemplo AzureDmlBackup](https://github.com/markjbrown/AzureDmlBackup).
- Consulte [Alto rendimiento con Azure Blob Storage](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage).