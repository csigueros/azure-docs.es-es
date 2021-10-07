---
title: Migración de datos de Data Lake y Data Warehouse a Azure
description: Use Azure Data Factory para migrar datos del lago de datos y el almacenamiento de datos a Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 6175447ddb249e04a939219caa722c55dae65749
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749080"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Uso de Azure Data Factory para migrar datos del lago de datos y el almacenamiento de datos a Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si desea migrar el lago de datos o el almacenamiento de datos empresariales (EDW) a Microsoft Azure, considere usar Azure Data Factory. Azure Data Factory es adecuado para los escenarios siguientes:

- Migración de la carga de trabajo de macrodatos desde Amazon Simple Storage Service (Amazon S3) o un Sistema de archivos distribuido de Hadoop en el entorno local (HDFS) a Azure
- Migración de EDW desde Oracle Exadata, Netezza, Teradata y Amazon Redshift a Azure

Azure Data Factory puede mover petabytes de datos para la migración del lago de datos y decenas de terabytes de datos para la migración del almacenamiento de datos.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Por qué usar Azure Data Factory para la migración de datos

- Azure Data Factory puede escalar fácilmente la cantidad de potencia de procesamiento necesaria para mover datos en modo sin servidor con alto rendimiento, resistencia y escalabilidad. Y pagará solo por lo que use. También tenga en cuenta lo siguiente: 
  - Azure Data Factory no tiene ninguna limitación en cuanto al volumen de datos y el número de archivos.
  - Azure Data Factory puede utilizar toda la red y el ancho de banda de almacenamiento para lograr el mayor rendimiento del movimiento de datos en su entorno.
  - Azure Data Factory usa un método de pago por uso, de modo que solo se paga por el tiempo que se usa realmente para ejecutar la migración de datos a Azure.  
- Azure Data Factory puede realizar una carga histórica única, así como una carga incremental programada.
- Azure Data Factory usa Azure Integration Runtime (IR) para mover datos entre los puntos de conexión de acceso público del lago de datos y el almacenamiento de datos. También puede usar IR autohospedado para mover datos entre los puntos de conexión del lago de datos y el almacenamiento situados dentro de una red virtual de Azure o detrás de un firewall.
- Azure Data Factory tiene seguridad de nivel empresarial: puede usar Windows Installer (MSI) o la identidad de servicio para una integración de servicio a servicio protegida, o bien aprovechar las ventajas de Azure Key Vault para la administración de credenciales.
- Azure Data Factory proporciona una experiencia de creación sin código y un completo panel de supervisión integrado.  

## <a name="online-vs-offline-data-migration"></a>Migración de datos en línea frente a sin conexión

Azure Data Factory es una herramienta de migración de datos en línea estándar para transferir datos a través de una red (Internet, ER o VPN). Mientras con la migración de datos sin conexión, los usuarios envían físicamente los dispositivos de transferencia de datos de su organización a un centro de datos de Azure.  

Hay tres consideraciones importantes a la hora de elegir entre un método de migración en línea frente al método sin conexión:  

- El tamaño de los datos que se van a migrar
- Ancho de banda de red
- El plazo de migración

Por ejemplo, suponga que tiene previsto usar Azure Data Factory para completar la migración de datos en un plazo de dos semanas (su *plazo de migración*). Observe la línea de corte rosa/azul en la tabla siguiente. La celda de color rosa inferior de cualquier columna determinada muestra el emparejamiento de ancho de banda de red o tamaño de datos cuyo plazo de migración es más próximo e inferior a dos semanas. (Cualquier emparejamiento de tamaño/ancho de banda en una celda azul tiene un plazo de migración en línea de más de dos semanas). 

:::image type="content" source="media/data-migration-guidance-overview/online-offline.png" alt-text="en línea frente a sin conexión":::
Esta tabla le ayuda a determinar si puede hacer frente al plazo de migración deseado de la migración en línea (Azure Data Factory) en función del tamaño de los datos y el ancho de banda de red disponible. Si el plazo de migración en línea es superior a dos semanas, querrá usar la migración sin conexión.

> [!NOTE]
> Con el enfoque de migración en línea, puede lograr la carga de datos históricos y las fuentes incrementales de un extremo a otro con una única herramienta.  Con este enfoque, los datos se pueden mantener sincronizados entre el almacén existente y el nuevo almacén durante todo el plazo de migración. Esto significa que puede volver a generar la lógica de ETL en el nuevo almacén con los datos actualizados.


## <a name="next-steps"></a>Pasos siguientes

- [Migración de datos de AWS S3 a Azure](data-migration-guidance-s3-azure-storage.md)
- [Migración de datos desde un clúster de Hadoop local a Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migración de datos desde un servidor de Netezza local a Azure](data-migration-guidance-netezza-azure-sqldw.md)
