---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/14/2021
ms.author: alkohli
ms.openlocfilehash: acbb239a6fb839f12b31f8521e4a07fc6083458e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "130000085"
---
Aquí se proporciona una lista de los sistemas operativos admitidos para la operación de copia de datos a través de los clientes conectados al dispositivo.

| **Sistema operativo** | **Versiones** | **Notas** |
| --- | --- | --- |
| Windows Server |2016 RS1 y versiones posteriores<br> 2019 RS5 y versiones posteriores | Con las ediciones anteriores de estos sistemas operativos, no se puede usar RoboCopy en modo de copia de seguridad (`robocopy /B`) para copiar archivos que contienen flujos de datos alternativos (ADS) ni usar atributos extendidos (EA) en sus listas de control de acceso (ACL). |
| Windows |7, 8, 10 |   |
| Linux    |         |   |
