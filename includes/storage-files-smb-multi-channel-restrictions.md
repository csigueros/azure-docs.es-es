---
title: Archivo de inclusión
description: archivo de inclusión
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/14/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ed6b2b47ead64eac664f789aa8eba7887e68e7
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228810"
---
SMB multicanal para los recursos compartido de archivos de Azure tiene actualmente las siguientes restricciones:
- Solo se admite en clientes [Windows](../articles/storage/files/storage-how-to-use-files-windows.md) y [Linux](../articles/storage/files/storage-how-to-use-files-linux.md) que usan SMB 3.1.1
- El número máximo de canales es cuatro. Para más información, consulte [este artículo](../articles/storage/files/storage-troubleshooting-files-performance.md#cause-4-number-of-smb-channels-exceeds-four).
- No se admite SMB directo.
- Los puntos de conexión privados para las cuentas de almacenamiento no se admiten.