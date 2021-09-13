---
author: vimeht
ms.author: vimeht
ms.date: 7/8/2021
ms.topic: include
ms.service: iot-hub-device-update
ms.openlocfilehash: 58977fdfb05a2f2b152d66fb12065d88a98b2b2e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802730"
---
 > [!NOTE]
 > Cuando un recurso o una operación determinados no tienen límites ajustables, los límites predeterminado y máximo son los mismos.
 > Si se puede ajustar el límite, la tabla incluye valores diferentes para los encabezados de Límite predeterminado y Límite máximo. El límite se puede aumentar por encima del límite predeterminado, pero no por encima del límite máximo.
 > Si desea aumentar el límite o la cuota por encima del límite predeterminado, abra una [solicitud de soporte técnico al cliente en línea](https://azure.microsoft.com/support/options/).


En esta tabla se proporcionan los límites para el recurso de Device Update for IoT Hub en Azure Resource Manager:

| Resource |  Límite predeterminado | Límite máximo | ¿Ajustable? |
| --- | --- | --- | --- |
| Cuentas por suscripción | 2 | 25 | Sí |
| Instancias por cuenta | 2 | 25 | Sí |
| Longitud del nombre de la cuenta | Mínimo: 3 <br/> Máximo: 24 | Mínimo: 3 <br/> Máximo: 24 | No |
| Longitud del nombre de la instancia | Mínimo: 3 <br/> Máximo: 36 | Mínimo: 3 <br/> Máximo: 36 | No |



En esta tabla se proporcionan los distintos límites asociados a las operaciones de Device Update for IoT Hub:

| Operación |  Límite predeterminado | Límite máximo | ¿Ajustable? |
| --- | --- | --- | --- |
| Número de dispositivos por instancia | 10 000 | 10 000 | No |
| Número de proveedores de actualizaciones por instancia | 25 | 25 | No |
| Número de nombres de actualización por proveedor por instancia | 25 | 25 | No |
| Número de versiones de actualización por proveedor de actualizaciones y nombre por instancia | 100 | 100 | No |
| Número total de actualizaciones por instancia | 100 | 100 | No |
| Tamaño máximo de archivo de actualización única | 800 MB | 800 MB | No |
| Tamaño combinado máximo de todos los archivos en una sola acción de importación | 800 MB | 800 MB | No |
| Número de grupos de dispositivos por instancia | 75 | 75 | No |
