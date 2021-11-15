---
title: Tutorial sobre la devolución de Azure Data Box | Microsoft Docs
description: En este tutorial, aprenderá a devolver un dispositivo Azure Data Box, lo que incluye la preparación del envío, el envío de Data Box, la comprobación de la carga de datos y la eliminación de datos de Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/29/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 95cce7e79ea8eaf3a542b52ac5c04b954ed56e0e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469110"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Devolución de Azure Data Box y comprobación de la carga de datos en Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Devolución de Data Box y comprobación de la carga de datos en Azure

::: zone-end

::: zone target="docs"

En este tutorial se describe cómo devolver Azure Data Box y comprobar los datos cargados en Azure.

En este tutorial, aprenderá sobre temas como:

> [!div class="checklist"]
>
> * Prerrequisitos
> * Preparación para el envío
> * Envío de Data Box a Microsoft
> * Comprobación de la carga de datos en Azure
> * Eliminación de datos de Data Box

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que:

* Ha completado el [Tutorial: Copia de datos a Azure Data Box y comprobación de](data-box-deploy-copy-data.md).
* Los trabajos de copia se han completado y no aparece ningún error en la página **Conectar y copiar**. **Preparación para el envío** no se puede ejecutar si los trabajos de copia están en curso o hay errores en la página **Conectar y copiar**.

## <a name="prepare-to-ship"></a>Preparación para el envío

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Una vez completada la copia de datos, prepare y envíe el dispositivo. Cuando el dispositivo llega al centro de datos de Azure, los datos se cargan automáticamente en Azure.

## <a name="prepare-to-ship"></a>Preparación para el envío

Antes de prepararse para enviar, asegúrese de que los trabajos de copia se han completado.

1. Vaya a la página **Preparar para enviar** de la interfaz de usuario web local y comience la preparación del envío.
2. Desactive el dispositivo desde la interfaz de usuario web local. Quite los cables del dispositivo.

Los siguientes pasos vienen determinados por el lugar al que se vaya a devolver el dispositivo.

::: zone-end


## <a name="ship-data-box-back"></a>Devolución de Data Box

Asegúrese de que la copia de datos en el dispositivo se ha completado y que la ejecución de **Preparación para el envío** se ha realizado correctamente. 

Según la región a la que envíe el dispositivo, el procedimiento es distinto. En muchos países o regiones, puede usar el envío administrado por Microsoft o el autoadministrado.

### <a name="microsoft-managed-shipping"></a>Envío administrado por Microsoft

Si usa el envío administrado por Microsoft, siga las instrucciones correspondientes a la región de origen del envío.

## <a name="us--canada"></a>[Estados Unidos y Canadá](#tab/in-us-canada)

[!INCLUDE [data-box-shipping-in-us-canada](../../includes/data-box-shipping-in-us-canada.md)]

## <a name="eu"></a>[EU](#tab/in-europe)

[!INCLUDE [data-box-shipping-in-eu](../../includes/data-box-shipping-in-eu.md)]

**Si va a realizar una devolución a los centros de datos de Alemania o Suiza,** también puede [utilizar el envío autoadministrado](#self-managed-shipping).

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Comprobación de la carga de datos en Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminación de datos de Data Box
 
Una vez que se completa la carga en Azure, Data Box elimina los datos de los discos según las [directrices de la revisión 1 de NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end


::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Comprobación de la carga de datos en Azure

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

