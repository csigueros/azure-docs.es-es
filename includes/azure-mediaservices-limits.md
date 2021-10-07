---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: 10de63c842eeb60f5e7e725457c88961581370f0
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129063743"
---
>[!NOTE]
>En relación con los recursos que no están fijados, abra una incidencia de soporte técnico para solicitar un aumento en las cuotas. No cree cuentas adicionales de Azure Media Services para obtener límites mayores.

| Resource | Límite | 
| --- | --- | 
| Cuentas de Azure Media Services en una suscripción única | 25 (fijo) | 
| Trabajos por cuenta de Media Services | 50 000<sup>2</sup> |
| Tareas encadenadas por trabajo | 30 (fijo) |
| Recursos por cuenta de Media Services | 1 000 000|
| Recursos por tarea. | 50 |
| Recursos por trabajo | 100 |
| Localizadores únicos asociados a un recurso al mismo tiempo | 5<sup>4</sup> |
| Canales en vivo por cuenta de Media Services |5<sup>5</sup>|
| Programas en estado detenido por canal |50|
| Programas en estado de ejecución por canal |3|
| Puntos de conexión de streaming detenidos o en ejecución por cuenta de Media Services|2|
| Unidades de streaming por extremo de streaming |10 |
| Cuentas de almacenamiento | 100<sup>6</sup> (fijo) |
| Directivas | 1 000 000<sup>7</sup> |
| Tamaño de archivo| En algunos casos, existe un límite máximo de tamaño de archivo para el procesamiento en Media Services.<sup>8</sup> |

<sup>1</sup>Si cambia el tipo, por ejemplo, de S2 a S1, se restablecen los límites máximos de unidad reservada.

<sup>2</sup>Este número incluye los trabajos en cola, terminados, activos y cancelados. No incluye los trabajos eliminados. Puede eliminar los trabajos antiguos con **IJob.Delete** o con la solicitud HTTP **DELETE**.

A partir del 1 de abril de 2017, se eliminarán automáticamente los registros de trabajo de más de 90 días de la cuenta, junto con los registros de tarea asociados. La eliminación automática se produce aunque el número total de registros no llegue a la cuota máxima. Para archivar la información del trabajo y la tarea, use el código descrito en [Administración de activos con el SDK de Media Services para .NET](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>Al realizar una solicitud para obtener una lista de las entidades de trabajo, se devuelve un máximo de 1000 por solicitud. Para realizar el seguimiento de todos los trabajos enviados, puede usar las consultas top y skip, como se describe en [Opciones de consulta del sistema de OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>Los localizadores no están diseñados para administrar el control de acceso por usuario. Para conceder otros derechos de acceso a usuarios individuales, use las soluciones de administración de derechos digitales (DRM). Para obtener más información, vea el tema sobre [Cómo proteger su contenido con Azure Media Services](../articles/media-services/latest/drm-content-protection-concept.md).

<sup>5</sup>Según el caso de uso de streaming y el centro de datos regional que prefiera, AMS puede alojar más de cinco canales en vivo por cuenta de Media Services. Envíe una solicitud de soporte técnico para aumentar la cuota de su cuenta.

<sup>6</sup> Las cuentas de almacenamiento deben pertenecer a la misma suscripción de Azure.

<sup>7</sup>Hay un límite de 1 000 000 directivas para otras directivas de Media Services. Un ejemplo es para la directiva de localizador o ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Si siempre usa los mismos días y permisos de acceso, utilice el mismo identificador de directiva. Para obtener información y un ejemplo, vea [Administración de activos y entidades relacionadas con el SDK de Media Services para .NET](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>8</sup>El tamaño máximo permitido para un único blob es actualmente de 5 TB en Azure Blob Storage. En Media Services, se aplican límites adicionales en función de los tamaños de máquina virtual utilizados por el servicio. El límite de tamaño se aplica a los archivos que se cargan y también a los que se generan como resultado del procesamiento (codificación o análisis) de Media Services. Si el archivo de origen es mayor de 260 GB, es muy probable que el trabajo presente un error. 

