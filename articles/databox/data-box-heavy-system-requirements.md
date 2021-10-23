---
title: Requisitos del sistema de Microsoft Azure Data Box Heavy | Microsoft Docs
description: Obtenga más información sobre los requisitos de software y red de Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 692623de1d1e13dbdd5d09feb3197ededb8be9ee
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000100"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Requisitos del sistema de Azure Data Box Heavy

En este artículo se describen requisitos del sistema importantes tanto de Microsoft Azure Data Box Heavy como de los clientes de que se conectan al dispositivo. Se recomienda leer detenidamente la siguiente información antes de implementar Data Box Heavy y volver a consultarla según sea necesario durante la implementación y el posterior funcionamiento.

Los requisitos del sistema incluyen:

* **Requisitos de software de los hosts que se conectan a Data Box Heavy**: describe las plataformas compatibles, los exploradores de la interfaz de usuario web local, los clientes SMB y los requisitos adicionales de los hosts que se pueden conectar a Data Box.
* **Requisitos de red de Data Box Heavy**: proporciona información acerca de los requisitos de red para el funcionamiento óptimo del dispositivo Data Box Heavy.

## <a name="software-requirements"></a>Requisitos de software

Los requisitos de software incluyen información acerca de los sistemas operativos y exploradores compatibles con la interfaz de usuario web local y los clientes SMB.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operativos compatibles para clientes

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Sistemas de archivos compatibles para clientes de Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Cuentas de almacenamiento admitidas

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipos de almacenamiento compatibles

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Exploradores web compatibles

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisitos de red

El centro de datos debe tener una red de alta velocidad. Para obtener velocidades de copia más rápidas, se pueden utilizar dos conexiones de 40-GbE (una por nodo) en paralelo. Si no dispone de una conexión de 40-GbE, le recomendamos que tenga al menos dos conexiones de 10-GbE (una por nodo).

### <a name="port-requirements"></a>Requisitos de puerto

En la siguiente tabla se enumeran los puertos que deben abrirse en el firewall para permitir el tráfico de SMB o NFS. En esta tabla, *dentro* o *entrante* hace referencia a la dirección desde la que el cliente entrante solicita acceso al dispositivo. *Fuera* o *saliente* hace referencia a la dirección en la que el dispositivo de Data Box Heavy envía datos externamente después de la implementación: por ejemplo, saliente a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de Azure Data Box](data-box-deploy-ordered.md)
