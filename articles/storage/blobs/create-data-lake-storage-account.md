---
title: Creación de una cuenta de almacenamiento para Azure Data Lake Storage Gen2
description: Obtenga información sobre cómo crear una cuenta de almacenamiento para su uso con Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 10/14/2021
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 35761e10cecf1cb209f004f99f773c09d91dc0e5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046874"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Creación de una cuenta de almacenamiento para su uso con Azure Data Lake Storage Gen2 habilitado

Para usar las funcionalidades de Data Lake Storage Gen2, cree una cuenta de almacenamiento que tenga un espacio de nombres jerárquico.

Para obtener instrucciones paso a paso, consulte [Creación de una cuenta de almacenamiento](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

Al crear la cuenta, asegúrese de seleccionar las opciones descritas en este artículo.

## <a name="choose-a-storage-account-type"></a>Selección de un tipo de cuenta de almacenamiento

Las funcionalidades de Data Lake Storage se admiten en los siguientes tipos de cuentas de almacenamiento:

- De uso general estándar, v2
- Blobs en bloques Premium

Para más información sobre cómo elegir entre ellos, consulte la [introducción a la cuenta de almacenamiento](../common/storage-account-overview.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

Puede elegir entre estos dos tipos de cuentas en la pestaña **Aspectos básicos** de la página **Crear una cuenta de almacenamiento**.

Para crear una cuenta estándar de uso general v2, seleccione **Estándar**.

Para crear una cuenta de blob en bloques Premium, seleccione **Premium**. A continuación, en la lista desplegable **Tipo de cuenta prémium**, seleccione **Blobs en bloques**.

> [!div class="mx-imgBorder"]
> ![Opción de blobs en bloques Premium](./media/create-data-lake-storage-account/premium-block-blob-option.png)

## <a name="enable-the-hierarchical-namespace"></a>Habilitación del espacio de nombres jerárquico

Desbloquee las funcionalidades de Data Lake Storage al seleccionar la opción **Espacio de nombres jerárquico** en la pestaña **Avanzadas** de la página **Crear cuenta de almacenamiento**. 

En la siguiente imagen se muestra esta configuración en la página **Crear cuenta de almacenamiento**.

> [!div class="mx-imgBorder"]
> ![Opción Servicio de espacio de nombres jerárquico](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Para habilitar las capacidades de Data Lake Storage en una cuenta existente, vea [Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md).

> [!NOTE]
> Las opciones de **Protección de datos** y espacio de nombres jerárquico no pueden estar habilitadas simultáneamente.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a las cuentas de almacenamiento](../common/storage-account-overview.md)
- [Actualización de Azure Blob Storage con funcionalidades de Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md)
- [Control de acceso en Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
