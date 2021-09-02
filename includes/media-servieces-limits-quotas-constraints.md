---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 95143e78947cd44faec6ed48bea1fb44cc0011e2
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867943"
---
> [!NOTE]
> En relación con los recursos que no están fijados, abra una incidencia de soporte técnico para solicitar un aumento en las cuotas. No cree cuentas adicionales de Azure Media Services para obtener límites mayores.

### <a name="account-limits"></a>Límites de cuentas

| Resource | Límite predeterminado |
| --- | --- |
| Cuentas de Media Services en una suscripción única | 100 (fijo) |

### <a name="asset-limits"></a>Límites de recursos

| Resource | Límite predeterminado |
| --- | --- |
| Recursos por cuenta de Media Services | 1 000 000|

### <a name="storage-media-limits"></a>Límites de almacenamiento (elementos multimedia)

| Resource | Límite predeterminado |
| --- | --- |
| Tamaño de archivo| En algunos casos, existe un límite máximo de tamaño de archivo admitido para el procesamiento en Media Services. <sup>(1)</sup> |
| Cuentas de almacenamiento | 100<sup>(2)</sup> (cantidad fija) |

<sup>1</sup> El tamaño máximo admitido para un único blob es actualmente de 5 TB en Azure Blob Storage. En Media Services, se aplican límites adicionales en función de los tamaños de máquina virtual utilizados por el servicio. El límite de tamaño se aplica a los archivos que se cargan y también a los que se generan como resultado del procesamiento (codificación o análisis) de Media Services. Si el archivo de origen es mayor de 260 GB, es muy probable que el trabajo presente un error.

<sup>2</sup> Las cuentas de almacenamiento deben proceder de la misma suscripción de Azure.

### <a name="jobs-encoding--analyzing-limits"></a>Límites de trabajos (codificación y análisis)

| Resource | Límite predeterminado |
| --- | --- |
| Trabajos por cuenta de Media Services | 500 000 <sup>(3)</sup> (cantidad fija)|
| Entradas de trabajo por trabajo | 50 (cantidad fija)|
| Salidas de trabajo por trabajo | 20 (cantidad fija) |
| Transformaciones por cuenta de Media Services | 100 (cantidad fija)|
| Salidas de transformación en una transformación | 20 (cantidad fija) |
| Archivos por entrada de trabajo|10 (cantidad fija)|

<sup>3</sup> Este número incluye los trabajos en cola, terminados, activos y cancelados. No incluye los trabajos eliminados. 

Se eliminarán automáticamente los registros de trabajo de más de 90 días de su cuenta, aunque el número total de registros no llegue a la cuota máxima. 

### <a name="live-streaming-limits"></a>Límites de streaming en vivo

| Resource | Límite predeterminado |
| --- | --- |
| Eventos en directo <sup>(4)</sup> por cuenta de Media Services |5|
| Salidas en directo por evento en directo |3 <sup>(5)</sup> |
| Duración máxima de la salida en directo | [Tamaño de la ventana de DVR](../articles/media-services/latest/live-event-cloud-dvr-time-how-to.md) |

<sup>4</sup> Para obtener información detallada sobre las limitaciones de eventos en directo, consulte [Comparación de tipos de objetos LiveEvent](../articles/media-services/latest/live-event-types-comparison-reference.md).

<sup>5</sup> Los objetos LiveOutput comienzan cuando se crean y se detienen cuando se eliminan.

### <a name="packaging--delivery-limits"></a>Límites de empaquetado y entrega

| Resource | Límite predeterminado |
| --- | --- |
| Puntos de conexión de streaming (detenidos o en ejecución) por cuenta de Media Services| 2 |
| Filtros de manifiesto dinámico|100|
| Directivas de streaming | 100 <sup>(6)</sup> |
| Localizadores de streaming únicos asociados con un recurso al mismo tiempo | 100<sup>(7)</sup> (cantidad fija) |

<sup>6</sup> Al usar una [directiva de streaming](/rest/api/media/streamingpolicies) personalizada, debe diseñar un conjunto limitado de dichas directivas para su cuenta de Media Service y reutilizarlas para sus localizadores de streaming siempre que se necesiten las mismas opciones y protocolos de cifrado. No debe crear una nueva directiva de streaming para cada localizador de streaming.

<sup>7</sup> Los localizadores de streaming no están diseñados para administrar el control de acceso por usuario. Para conceder derechos de acceso diferentes a usuarios individuales, use las soluciones de administración de derechos digitales (DRM).

### <a name="protection-limits"></a>Límites de protección

| Resource | Límite predeterminado |
| --- | --- |
| Opciones por directiva de clave de contenido | 30 |
| Licencias por mes para cada uno de los tipos DRM en el servicio de entrega de claves de Media Services por cuenta|1 000 000|

### <a name="support-ticket"></a>Incidencia de soporte técnico

En el caso de recursos que no son fijos, puede solicitar que se generen las cuotas abriendo una [incidencia de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Incluya información detallada en la solicitud en los cambios de cuota que se desean, en los escenarios de casos de uso y las regiones que se requieren. <br/>**No** cree cuentas adicionales de Azure Media Services para obtener límites mayores.