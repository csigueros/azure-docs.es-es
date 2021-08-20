---
ms.service: hpc-cache
ms.topic: include
ms.date: 06/17/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 37f2895ed476cc0dbe0bdb3def034a17448d8dca
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587231"
---
| Modelo de uso | Modo de almacenamiento en caché | Comprobación de back-end | Retraso máximo de reescritura |
|--|--|--|--|
| Lectura de textos densos y poco frecuentes <!--read_heavy_infreq-->| Lectura | Nunca | None |
| Mayor que el 15 % de textos <!--write_workload_15-->| Lectura/escritura | 8 horas | 1 hora |
| Los clientes omiten la memoria caché <!--write_around-->| Lectura | 30 segundos | None |
| Mayor que el 15 % de escrituras, comprobación de back-end frecuente (30 segundos) <!--write_workload_check_30-->| Lectura/escritura | 30 segundos | 1 hora |
| Mayor que el 15 % de escrituras, comprobación de back-end frecuente (60 segundos) <!--write_workload_check_60-->| Lectura/escritura | 60 segundos | 1 hora |
| Mayor que el 15 % de escrituras, reescritura frecuente <!--write_workload_cloudws-->| Lectura/escritura | 30 segundos | 30 segundos |
| Lectura intensiva, comprobación del servidor de copia de seguridad cada 3 horas <!--read_heavy_check_180-->| Lectura | 3 horas | None |
