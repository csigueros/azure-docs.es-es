---
title: Requisitos del sistema de Microsoft Azure Data Box | Microsoft Docs
description: Conozca los requisitos del sistema importantes de Azure Data Box y de los clientes que se conectan a Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 583ed98980ae2ac5e8f3bddd7c3267c3400b85a5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006154"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos del sistema de Azure Data Box

En este artículo se describen requisitos del sistema importantes tanto de Microsoft Azure Data Box como de los clientes de que se conectan a Data Box. Se recomienda leer detenidamente la información antes de implementar Data Box y volver a consultarla cuando sea necesario durante la implementación y el funcionamiento.

Los requisitos del sistema incluyen:

* **Requisitos de software:** se describen los sistemas operativos, los protocolos de transferencia de archivos, las cuentas de almacenamiento, los tipos de almacenamiento y los exploradores admitidos en la interfaz de usuario web local, para los hosts que se conectan a Data Box.
* **Requisitos de red:** se describen los requisitos de las conexiones y los puertos de red para el funcionamiento óptimo de Data Box.


## <a name="software-requirements"></a>Requisitos de software

Entre los requisitos de software se incluyen los sistemas operativos, protocolos de transferencia de archivos, cuentas de almacenamiento, tipos de almacenamiento y exploradores compatibles con la interfaz de usuario web local.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operativos compatibles para clientes

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Protocolos de transferencia de archivos compatibles para clientes

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> No se admite la conexión a recursos compartidos de Data Box a través de REST para los pedidos de exportación.

### <a name="supported-storage-accounts"></a>Cuentas de almacenamiento admitidas

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipos de almacenamiento compatibles

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Exploradores web compatibles

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisitos de red

El centro de datos debe tener una red de alta velocidad. Es muy recomendable tener una conexión de 10 GbE como mínimo. Si no hay una conexión de 10 GbE disponible, puede usar un vínculo de datos de 1 GbE para copiar los datos, pero las velocidades de copia se verán afectadas.

### <a name="port-requirements"></a>Requisitos de puerto

En la siguiente tabla se enumeran los puertos que deben abrirse en el firewall para permitir el tráfico de SMB o NFS. En esta tabla, *dentro* (*entrante*) hace referencia a la dirección desde la que el cliente entrante solicita acceso al dispositivo. *Salida* (o *saliente*) hace referencia a la dirección en la que el dispositivo Data Box envía datos externamente, después de la implementación. Por ejemplo, los datos pueden ser salientes hacia Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Pasos siguientes

* [Implementación de Azure Data Box](data-box-deploy-ordered.md)
