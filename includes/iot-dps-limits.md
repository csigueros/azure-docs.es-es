---
author: anastasia-ms
ms.service: iot-dps
ms.topic: include
ms.date: 10/10/2021
ms.author: v-stharr
ms.openlocfilehash: fa416ff3e3ef5b502b65ac414558692ccd7071b5
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129866128"
---
> [!NOTE]
> Los límites de algunas áreas de este servicio son ajustables. Esto se representa en las tablas siguientes con la columna *¿Ajustable?* . Cuando se puede ajustar el límite, el valor de *¿Ajustable?* es *Sí*.
>
>El valor real al que se puede ajustar un límite puede variar en función de la implementación de cada cliente. Es posible que se requieran varias instancias de DPS para implementaciones muy grandes.
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