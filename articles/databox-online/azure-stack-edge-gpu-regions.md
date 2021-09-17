---
title: Elegir una región de Microsoft Azure Stack Edge Pro con GPU | Microsoft Docs
description: Se explican las opciones de región para el servicio Azure Stack Edge, el almacenamiento de datos y los dispositivos de Azure Stack Edge Pro con GPU, Azure Stack Edge Pro R y Azure Stack Edge Mini R.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/18/2021
ms.author: alkohli
ms.openlocfilehash: 7b585c61dd46339be71e66be66b402a0e107c194
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429851"
---
# <a name="choosing-a-region-for-azure-stack-edge"></a>Elegir una región de Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

En este artículo se describe la disponibilidad de regiones para el servicio Azure Stack Edge, el almacenamiento de datos y los envíos de dispositivos relativos a un dispositivo Azure Stack Edge, y se explica cómo la preferencia de región repercute en el servicio, el rendimiento y la facturación.


## <a name="overview"></a>Información general

Los centros de datos de Azure funcionan en varias regiones geográficas de todo el mundo para cumplir con las exigencias de rendimiento del cliente, los requisitos y las preferencias en cuanto a ubicación de los datos. Una ubicación geográfica de Azure es un área definida del mundo que contiene al menos una región de Azure. Una región de Azure es un área dentro de una ubicación geográfica que contiene uno o varios centros de datos.

La elección de una región de Azure es muy importante y se ve afectada por factores como la residencia y soberanía de los datos, la disponibilidad del servicio, el rendimiento, el coste y la redundancia. Para obtener más información sobre cómo elegir una región, vaya a [¿Qué región de Azure es la adecuada para mí?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

En el caso de un dispositivo Azure Stack Edge Pro con GPU, la elección de la región viene determinada por los siguientes factores:

- Regiones en las que el servicio Azure Stack Edge está disponible
- Regiones en las que deben estar ubicadas las cuentas de almacenamiento que almacenan datos de Azure Stack Edge para obtener el mejor rendimiento
- Países o regiones a los que se puede enviar el dispositivo Azure Stack Edge


## <a name="region-availability-for-the-service"></a>Disponibilidad de regiones para el servicio

Actualmente, el servicio Azure Stack Edge se puede usar en las regiones públicas de Este de EE. UU., Oeste de Europa y Sudeste Asiático. **Estas tres regiones admiten ubicaciones geográficas de todo el mundo.**

La región del servicio es el país o región que tenga asignado el recurso de administración de Azure Stack Edge. El recurso de administración usa el servicio Azure Stack Edge para activar, implementar y devolver un dispositivo Azure Stack Edge.

El servicio Azure Stack Edge también supervisa el estado del dispositivo en busca de problemas, errores, alertas y si el dispositivo está "activo". Este servicio también supervisa los medidores de uso y consumo por motivos de facturación; es decir, la información del plano de control en el dispositivo. La región asignada al recurso de administración es la región donde se produce la facturación.

El dispositivo debe conectarse al servicio Azure Stack Edge para activarse. Si no desea ninguna interacción más con el servicio, puede poner el dispositivo en modo desconectado. 

Los datos no fluyen por el servicio de Azure Stack Edge, sino entre el dispositivo y la cuenta de almacenamiento implementada en la región de origen de datos del cliente. 

En general, se elige la ubicación más cercana a la región geográfica donde se implementa el dispositivo. Sin embargo, el dispositivo y el servicio también se pueden implementar en ubicaciones diferentes.

## <a name="region-availability-for-data-storage"></a>Disponibilidad de regiones para el almacenamiento de datos

Los datos de Azure Stack Edge se almacenan físicamente en las cuentas de Azure Storage, y estas cuentas están disponibles en todas las regiones de Azure. Al crear una cuenta de almacenamiento de Azure, hay que elegir la ubicación principal de dicha cuenta. Esa opción determina la región donde van a residir los datos.

La cuenta de almacenamiento se elige al [crear un recurso compartido en el dispositivo](azure-stack-edge-gpu-deploy-add-shares.md#add-a-share). El servicio Azure Stack Edge y la cuenta de almacenamiento de Azure pueden estar ubicaciones distintas.

En general, elija la región más cercana al servicio de la cuenta de almacenamiento. Sin embargo, es posible que la región de Microsoft Azure más cercana no sea la región con la latencia más baja. La latencia dicta el rendimiento del servicio de red y, por tanto, el rendimiento del dispositivo. Así pues, si se elige una cuenta de almacenamiento de una región distinta, es importante saber cuáles son las latencias entre el servicio y la región asociada a la cuenta de almacenamiento.

## <a name="region-of-device"></a>Disponibilidad de regiones para el dispositivo

Para averiguar los países y regiones geográficas en los que hay modelos de Azure Stack Edge disponibles, consulte la información general del producto:

- [Disponibilidad de regiones para Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-overview.md#region-availability)
- [Disponibilidad de regiones para Azure Stack Edge Pro R](azure-stack-edge-pro-r-overview.md#region-availability)
- [Disponibilidad de regiones para Azure Stack Edge Mini R](azure-stack-edge-mini-r-overview.md#region-availability)

Microsoft puede enviar hardware físico y proporcionar piezas de hardware de repuesto para Azure Stack Edge a esas regiones geográficas.

> [!IMPORTANT]
> No coloque un dispositivo físico de Azure Stack Edge en una región en la que no se pueda usar Azure Stack Edge, ya que Microsoft no puede enviar piezas de repuesto a los países o regiones donde no se puede usar Azure Stack Edge.


## <a name="next-steps"></a>Pasos siguientes

* Más información sobre los [precios de los distintos modelos de Azure Stack Edge](https://azure.microsoft.com/pricing/details/azure-stack/edge/)
* [Preparación de la implementación del dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
