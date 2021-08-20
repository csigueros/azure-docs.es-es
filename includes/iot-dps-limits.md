---
author: wesmc7777
ms.service: iot-dps
ms.topic: include
ms.date: 06/15/2021
ms.author: wesmc
ms.openlocfilehash: b3c218314f56fb53d0690ac0659192bb955e4301
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539020"
---
> [!NOTE]
> Los límites de algunas áreas de este servicio son ajustables. Esto se representa en las tablas siguientes con la columna *¿Ajustable?* . Cuando se puede ajustar el límite, el valor de *¿Ajustable?* es *Sí*.
>
> Si su empresa requiere el aumentar una cuota o límite ajustable por encima del límite predeterminado, puede [abrir una incidencia de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para solicitar más recursos.

En la tabla siguiente se enumeran los límites que se aplican a los recursos de Azure IoT Hub Device Provisioning Service.

| Recurso | Límite | ¿Ajustable? |
| --- | --- | --- |
| Servicios máximos de aprovisionamiento de dispositivos por suscripción de Azure | 10 | Sí |
| Número máximo de registros | 1 000 000 | Sí |
| Número máximo de inscripciones individuales | 1 000 000 | Sí |
| Número máximo de grupos de inscripciones *(certificado X.509)* | 100 | Sí |
| Número máximo de grupos de inscripciones *(clave simétrica)* | 100 | No |
| Número máximo de CA | 25 | No |
| Número máximo de centros de IoT vinculados | 50 | No |
| Tamaño máximo de mensaje | 96 KB| No |

> [!TIP]
> Si el límite máximo en los grupos de inscripción de clave simétrica es un problema de bloqueo, se recomienda usar inscripciones individuales como solución alternativa.

El servicio de aprovisionamiento de dispositivos tiene los siguientes límites de frecuencia.

| Tarifa | Valor por unidad | ¿Ajustable? |
| --- | --- | --- |
| Operations | 200/min/servicio | Sí |
| Registros de dispositivos | 200/min/servicio | Sí |
| Operación de sondeo de dispositivos | 5/10 s/dispositivo | No |

Cada llamada API en DPS se puede facturar como una *operación*. Esto incluye todas las API de servicio y la API de registro de dispositivos. La operación de sondeo de registro de dispositivos no se factura.