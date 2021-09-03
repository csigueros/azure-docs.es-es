---
title: Límites de una matriz de puertas programables de Azure Stack Edge Pro con GPU/Pro | Microsoft Docs
description: Obtenga información sobre los límites y tamaños recomendados cuando se implementa y opera una matriz de puertas programables de Azure Stack Edge Pro con GPU/Pro, incluidos los límites de servicio, dispositivos y almacenamiento.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: 64be85db0e2d11d2aee5a61742f427087a77cef2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736720"
---
# <a name="azure-stack-edge-limits"></a>Límites de Azure Stack Edge

Tenga en cuenta estos límites cuando implemente y use su solución matriz de puertas programables de Microsoft Azure Stack Edge Pro GPU o Azure Stack Edge Pro. 

## <a name="azure-stack-edge-service-limits"></a>Límites del servicio de Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Límites del dispositivo Azure Stack Edge

En la tabla siguiente, se describen los límites de los dispositivos de Azure Stack Edge.

| Descripción | Value |
|---|---|
|No. de archivos por dispositivo |100 millones |
|No. de recursos compartidos por contenedor |1 |
|N.º máximo de puntos de conexión de uso compartido y puntos de conexión REST por dispositivo (solo dispositivos GPU)| 24 |
|N.º máximo de cuentas de almacenamiento con niveles por dispositivo (solo dispositivos GPU)| 24|
|Tamaño máximo de archivo escrito en un recurso compartido| 5 TB |
|Número máximo de grupos de recursos por dispositivo| 800 |

## <a name="azure-storage-limits"></a>Límites de almacenamiento de Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Advertencias al cargar los datos

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Límites de tamaño objeto y de tamaño de cuenta de almacenamiento en Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Límites de tamaño de objeto de Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Preparación de la implementación de Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-prep.md)
- [Preparación de la implementación de la matriz de puertas programables de Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
