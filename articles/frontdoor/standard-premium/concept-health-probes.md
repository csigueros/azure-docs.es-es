---
title: Supervisión de sondeos de estado de Azure Front Door Estándar/Premium (versión preliminar)
description: Este artículo le ayuda a comprender cómo Azure Front Door supervisa el estado de los servidores back-end.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: a856ae7d38439d11ba0ddd65ebecdda7a3d2ce48
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750207"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Supervisión de sondeos de estado de Azure Front Door Estándar/Premium (versión preliminar)

> [!Note]
> Esta documentación trata sobre Azure Front Door Estándar/Prémium (versión preliminar). ¿Busca información sobre Azure Front Door? Consulte [aquí](../front-door-overview.md).

Azure Front Door envía periódicamente una solicitud HTTP o HTTPS a cada uno de los servidores back-end. Estas solicitudes permiten a Azure Front Door determinar el estado de cada punto de conexión en el grupo de back-end. Front Door usa entonces las respuestas de estos sondeos para determinar los "mejores" recursos de servidor back-end a los que enrutar las solicitudes de los clientes. 

> [!WARNING]
> Como Front Door tiene muchos entornos perimetrales en todo el mundo, el volumen de solicitudes de sondeo de estado a los back-end puede ser bastante superior, desde 25 solicitudes por minuto hasta un máximo de 1200 solicitudes por minuto, dependiendo de la frecuencia de sondeo de estado configurada. Con la frecuencia de sondeo predeterminada de 30 segundos, el volumen de sondeo del back-end debe ser de aproximadamente 200 solicitudes por minuto.

## <a name="supported-protocols"></a>Protocolos admitidos

Front Door admite el envío de sondeos a través de los protocolos HTTP o HTTPS. Estos sondeos se envían a través de los mismos puertos TCP configurados para enrutar las solicitudes de cliente y no se pueden reemplazar.

## <a name="supported-http-methods-for-health-probes"></a>Métodos HTTP admitidos para los sondeos de estado

Front Door admite los siguientes métodos HTTP para enviar los sondeos de estado:

* **GET:** el método GET significa recuperar cualquier información (en forma de entidad) que se identifique mediante el URI de solicitud.
* **HEAD:** El método HEAD es idéntico a GET, excepto que el servidor NO PUEDE devolver cuerpo de mensaje en la respuesta. En el caso de los nuevos perfiles de Front Door, de forma predeterminada, el método de sondeo se establece como HEAD.

> [!NOTE]
> Para reducir la carga y el costo de los back-end, Front Door recomienda el uso de solicitudes HEAD para sondeos de estado.

## <a name="health-probe-responses"></a>Respuestas de sondeo de estado

| Respuestas  | Descripción | 
| ------------- | ------------- |
| Determinación del estado  |  Un código de estado 200 - Correcto indica que el back-end está en buen estado. Todo lo demás se considera un error. Si por algún motivo (incluidos los errores de red) no se recibe una respuesta HTTP válida de un sondeo, este se considera un error.|
| Medida de la latencia  | La latencia es el tiempo de reloj medido desde el momento inmediatamente antes de enviar la solicitud de sondeo hasta el momento de recibir el último byte de la respuesta. Como se usa una nueva conexión TCP para cada solicitud, esta medida no está orientada a los servidores back-end con conexiones parcialmente activas existentes.  |

## <a name="how-front-door-determines-backend-health"></a>Cómo determina Front Door el mantenimiento de back-end

Para determinar el mantenimiento, Azure Front Door usa el mismo proceso de tres pasos que se describe a continuación.

1. Excluye los servidores back-end deshabilitados.

1. Excluye los servidores back-end que tienen errores de sondeo de estado:

    * Esta selección se realiza examinando las últimas _n_ respuestas de sondeo de estado. Si al menos _x_ están en buen estado, el back-end se considera correcto.

    * Para configurar _n_, se cambia la propiedad SampleSize de la configuración de equilibrio de carga.

    * Para configurar _x_, se cambia la propiedad SuccessfulSamplesRequired de la configuración de equilibrio de carga.

1. Para los conjuntos de servidores back-end con un estado correcto del grupo de servidores back-end, Front Door mide y mantiene también la latencia (tiempo de ida y vuelta) de cada back-end.

> [!NOTE]
> Si un único punto de conexión es miembro de varios grupos de back-end, Azure Front Door optimiza el número de sondeos de estado enviados al back-end para reducir la carga en él. Las solicitudes de sondeo de estado se enviarán en función del intervalo de ejemplo configurado más bajo. El estado del punto de conexión en todos los grupos se determinará mediante las respuestas de los mismos sondeos de estado.

## <a name="complete-health-probe-failure"></a>Error de sondeo de estado completo

Si se produce un error en los sondeos de estado de cada back-end de un grupo de back-end, Front Door considera que todos los servidores back-end están en buen estado y enruta el tráfico entre ellos en una distribución round robin.

Cuando alguno de los back-end vuelve a un estado correcto, Front Door reanuda el algoritmo de equilibrio de carga normal.

## <a name="disabling-health-probes"></a>Deshabilitación de sondeos de estado

Si tiene un único back-end en el grupo de back-end o solo hay un back-end activo en un grupo de back-end, puede deshabilitar los sondeos de estado. Al hacerlo, se reducirá la carga en el back-end de la aplicación.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [crear una instancia de Front Door Estándar/Prémium](create-front-door-portal.md).
