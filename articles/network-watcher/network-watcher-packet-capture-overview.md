---
title: Introducción a la captura de paquetes en Azure Network Watcher | Microsoft Docs
description: En esta página se proporciona una información general sobre las funcionalidades de la captura de paquetes de Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 78be49efd75db5a0bbff7f01734b418c9a0a7685
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290768"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introducción a la captura de paquetes variable en Azure Network Watcher

La captura variable de paquetes de Network Watcher permite crear sesiones de captura de paquetes para realizar el seguimiento del tráfico de entrada y salida de una máquina virtual. La captura de paquetes ayuda a diagnosticar anomalías de la red, tanto de forma activa como reactiva. Otros usos son la recopilación de estadísticas de red, la obtención de información sobre las intrusiones de red y la depuración de las comunicaciones cliente-servidor, entre otros.

La captura de paquetes es una extensión de máquina virtual que se inicia de forma remota a través de Network Watcher. Esta funcionalidad reduce la carga de la ejecución manual de una captura de paquetes en el equipo deseado, lo que permite ahorrar tiempo. La captura de paquetes puede realizarse a través del portal, PowerShell, CLI o API de REST. Un ejemplo de cómo se puede activar la captura de paquetes es con las alertas de la máquina virtual. La sesión de captura cuenta con filtros para asegurar la captura del tráfico que se desea supervisar. Los filtros se basan en la información de 5-tupla (protocolo, dirección IP local, dirección IP remota, el puerto local y puerto remoto). Los datos capturados se almacenan en el disco local o en un blob de almacenamiento. 

> [!IMPORTANT]
> La captura de paquetes requiere una extensión de máquina virtual `AzureNetworkWatcherExtension`. Para instalar la extensión en una máquina virtual Windows, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Windows](../virtual-machines/extensions/network-watcher-windows.md), y en una máquina virtual con Linux, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Linux](../virtual-machines/extensions/network-watcher-linux.md).

Para reducir la información que se captura únicamente a la información que desee, las siguientes opciones están disponibles para una sesión de captura de paquetes:

**Configuración de captura**

|Propiedad|Descripción|
|---|---|
|**Número máximo de bytes por paquete (bytes)** | El número de bytes de cada paquete que se captura, si se deja en blanco se capturan todos los bytes. El número de bytes de cada paquete que se captura, si se deja en blanco se capturan todos los bytes. Si necesita solo el encabezado de IPv4, indique 34 aquí |
|**Número máximo de bytes por sesión (bytes)** | Número total de bytes en esa captura, una vez que se alcanza este valor la sesión se finaliza.|
|**Límite de tiempo (segundos)** | Establece una restricción horaria en la sesión de captura de paquetes. El valor predeterminado es 18000 segundos o 5 horas.|

**Filtrado (opcional)**

|Propiedad|Descripción|
|---|---|
|**Protocolo** | El protocolo para filtrar la captura de paquetes. Los valores disponibles son TCP, UDP y All (Todos).|
|**Dirección IP local** | Este valor filtra la captura de paquetes a los paquetes en los que la dirección IP local coincide con este valor de filtro.|
|**Puerto local** | Este valor filtra la captura de paquetes a los paquetes en los que el puerto local coincide con este valor de filtro.|
|**Dirección IP remota** | Este valor filtra la captura de paquetes a los paquetes en los que la dirección IP remota coincide con este valor de filtro.|
|**Puerto remoto** | Este valor filtra la captura de paquetes a los paquetes en los que el puerto remoto coincide con este valor de filtro.|


## <a name="considerations"></a>Consideraciones
Hay un límite de 10 000 sesiones paralelas de captura de paquetes por región y suscripción. Este límite se aplica solo a las sesiones y no a los archivos de captura de paquetes guardados localmente, en la máquina virtual, o en una cuenta de almacenamiento. Consulte la [página de límites de servicio de Network Watcher](../azure-resource-manager/management/azure-subscription-service-limits.md#network-watcher-limits) para ver una lista completa de los límites. 

### <a name="next-steps"></a>Pasos siguientes

Aprenda a administrar las capturas de paquetes a través del portal consultando [Administración de capturas de paquetes con Azure Network Watcher mediante Azure Portal](network-watcher-packet-capture-manage-portal.md) o con PowerShell consultando [Administración de capturas de paquetes con Azure Network Watcher mediante PowerShell](network-watcher-packet-capture-manage-powershell.md).

Aprenda a crear capturas de paquetes proactivas basadas en las alertas de máquina virtual en el artículo sobre cómo [crear una captura de paquetes desencadenada por alertas](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png