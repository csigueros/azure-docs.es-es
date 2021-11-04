---
title: Tutorial para enviar Azure Data Box en el pedido de exportación | Microsoft Docs
description: Aprenda a enviar Azure Data Box a Microsoft una vez completado el orden de exportación
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: ea9a918ee32a3ada83651085290fa7c7b26c6e0e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024379"
---
# <a name="tutorial-return-azure-data-box"></a>Tutorial: Devolución de Azure Data Box

En este tutorial se describe cómo devolver Azure Data Box y borrar los datos una vez que el dispositivo se reciba en los datos de Azure.

En este tutorial, aprenderá sobre temas como:

> [!div class="checklist"]
>
> * Prerrequisitos
> * Preparación para el envío
> * Envío de Data Box a Microsoft
> * Eliminación de datos de Data Box

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que:

* Ha completado el [Tutorial: Copia de datos desde Azure Data Box](data-box-deploy-export-copy-data.md).
* Los trabajos de copia están completos. Preparación para el envío no se puede ejecutar mientras que los trabajos de copia están en curso.

## <a name="prepare-to-ship"></a>Preparación para el envío

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

Los siguientes pasos vienen determinados por el lugar al que se vaya a devolver el dispositivo.

## <a name="ship-data-box-back"></a>Devolución de Data Box

Asegúrese de que la copia de datos se ha completado en el dispositivo y que la **Preparación para el envío** se ha realizado correctamente.

Según la región a dónde envíe el dispositivo, el procedimiento es distinto. En muchos países o regiones, puede usar el envío administrado por Microsoft o el autoadministrado.

### <a name="microsoft-managed-shipping"></a>Envío administrado por Microsoft

Si usa el envío administrado por Microsoft, siga las instrucciones correspondientes a la región de origen del envío.

## <a name="us--canada"></a>[Estados Unidos y Canadá](#tab/in-us-canada)

[!INCLUDE [data-box-shipping-in-us-canada](../../includes/data-box-shipping-in-us-canada.md)]

## <a name="eu"></a>[EU](#tab/in-eu)

[!INCLUDE [data-box-shipping-in-eu](../../includes/data-box-shipping-in-eu.md)]

**Si el envío es desde Alemania o Suiza,** también puede [usar el envío autoadministrado](#self-managed-shipping).

## <a name="uk"></a>[Reino Unido](#tab/in-uk)

[!INCLUDE [data-box-shipping-in-uk](../../includes/data-box-shipping-in-uk.md)]

## <a name="australia"></a>[Australia](#tab/in-australia)

[!INCLUDE [data-box-shipping-in-australia](../../includes/data-box-shipping-in-australia.md)]

## <a name="japan"></a>[Japón](#tab/in-japan)

[!INCLUDE [data-box-shipping-in-japan](../../includes/data-box-shipping-in-japan.md)]

## <a name="singapore"></a>[Singapur](#tab/in-singapore)

[!INCLUDE [data-box-shipping-in-singapore](../../includes/data-box-shipping-in-singapore.md)]

## <a name="hong-kong"></a>[RAE de Hong Kong](#tab/in-hk)

[!INCLUDE [data-box-shipping-in-hk](../../includes/data-box-shipping-in-hk.md)]

## <a name="korea"></a>[Corea](#tab/in-korea)

[!INCLUDE [data-box-shipping-in-korea](../../includes/data-box-shipping-in-korea.md)]

## <a name="s-africa"></a>[Sudáfrica](#tab/in-sa)

[!INCLUDE [data-box-shipping-in-sa](../../includes/data-box-shipping-in-sa.md)]

## <a name="uae"></a>[Emiratos Árabes Unidos](#tab/in-uae)

[!INCLUDE [data-box-shipping-in-uae](../../includes/data-box-shipping-in-uae.md)]

### <a name="self-managed-shipping"></a>Envío autoadministrado

[!INCLUDE [data-box-shipping-self-managed](../../includes/data-box-shipping-self-managed.md)]

---

## <a name="erasure-of-data-from-data-box"></a>Eliminación de datos de Data Box

Una vez que el dispositivo llegue al centro de datos de Azure, Data Box elimina los datos de los discos según las [directrices de la revisión 1 de NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido sobre temas relacionados; por ejemplo:

> [!div class="checklist"]
> * Requisitos previos
> * Preparación para el envío
> * Envío de Data Box a Microsoft
> * Eliminación de datos de Data Box

Avance al siguiente artículo para obtener información sobre cómo administrar Data Box.

> [!div class="nextstepaction"]
> [Administrar Data Box a través de Azure Portal](./data-box-portal-admin.md)
