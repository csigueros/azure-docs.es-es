---
title: Nivel Premium para Azure Data Lake Storage
description: Use el nivel de rendimiento Premium con Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: normesta
ms.openlocfilehash: e9cab4b65da62bddd47cdab97c6f586b07f379d4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044463"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Nivel Premium para Azure Data Lake Storage

Azure Data Lake Storage Gen2 ahora admite [cuentas de almacenamiento de blobs en bloques Premium](storage-blob-block-blob-premium.md). Las cuentas de almacenamiento de blobs en bloques Premium son ideales para aplicaciones de análisis de macrodatos y cargas de trabajo que requieren una latencia baja uniforme y tienen un gran número de transacciones. Entre las cargas de trabajo de ejemplo se incluyen cargas de trabajo interactivas, IoT, análisis de streaming, inteligencia artificial y aprendizaje automático. 

>[!TIP]
> Para obtener más información sobre las ventajas de rendimiento y costo del uso de una cuenta de almacenamiento de blobs en bloques Premium, y para ver cómo otros clientes de Data Lake Storage Gen2 han usado este tipo de cuenta, consulte [Cuentas de almacenamiento de blobs en bloques Premium](storage-blob-block-blob-premium.md).

## <a name="getting-started-with-premium"></a>Introducción a Premium

En primer lugar, asegúrese de que sus características favoritas de Blob Storage sean compatibles con las cuentas de almacenamiento de blobs en bloques Premium y, a continuación, cree la cuenta. 

>[!NOTE]
> No se puede convertir una cuenta de almacenamiento de uso general v2 estándar existente en una cuenta de almacenamiento de blobs en bloques Premium. Para migrar a una cuenta de almacenamiento de blobs en bloques Premium, debe crear una cuenta de blobs en bloques Premium y migrar los datos a la nueva cuenta. 

### <a name="check-for-blob-storage-feature-compatibility"></a>Comprobación de la compatibilidad de características de Blob Storage

Algunas características de Blob Storage aún no se admiten o tienen compatibilidad parcial en las cuentas de almacenamiento de blobs en bloques Premium. Antes de elegir Premium, revise el artículo [Compatibilidad con las características de Blob Storage en cuentas de Azure Storage](storage-feature-support-in-storage-accounts.md) para determinar si las características que tiene previsto utilizar son totalmente compatibles con la cuenta. La compatibilidad con características siempre se expande, así que asegúrese de revisar periódicamente este artículo para ver si hay actualizaciones.

### <a name="create-a-new-storage-account"></a>Creación de una nueva cuenta de Storage

Cree una nueva cuenta de Azure Storage. Para obtener una guía completa, consulte [Creación de una cuenta de Storage](../common/storage-account-create.md). 

Al crear la cuenta, elija la opción de rendimiento **Premium** y el tipo de cuenta **Blobs en bloques**. 

> [!div class="mx-imgBorder"]
> ![Creación de una cuenta de almacenamiento de blobs en bloques](./media/storage-blob-block-blob-premium/create-block-blob-storage-account.png)

Para desbloquear las funcionalidades de Azure Data Lake Storage Gen2, habilite la configuración **Espacio de nombres jerárquico** en la pestaña **Avanzadas** de la página **Crear cuenta de almacenamiento**. 

En la siguiente imagen se muestra esta configuración en la página **Crear cuenta de almacenamiento**.

> [!div class="mx-imgBorder"]
> ![Opción Servicio de espacio de nombres jerárquico](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="next-steps"></a>Pasos siguientes

Use el nivel Premium para Azure Data Lake Storage con su servicio de análisis favorito, como Azure Databricks, Azure HDInsight y Azure Synapse Analytics. Consulte [Tutoriales que usan los servicios de Azure con Azure Data Lake Storage Gen2](data-lake-storage-integrate-with-services-tutorials.md).
