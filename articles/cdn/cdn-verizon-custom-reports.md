---
title: Informes personalizados de Verizon | Microsoft Docs
description: 'Puede ver los patrones de uso de la red CDN mediante los siguientes informes: Ancho de banda, Datos transferidos, Aciertos, Estados de la memoria caché, Frecuencia de aciertos de caché, Datos de IPV4/IPV6 transferidos.'
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/11/2017
ms.author: duau
ms.openlocfilehash: f461e310b44063035e2e21e4dbeb8dd340ea053b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434795"
---
# <a name="custom-reports-from-verizon"></a>Informes personalizados de Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Mediante los informes personalizados de Verizon a través del portal de administración para los perfiles de Verizon, puede definir el tipo de datos que se debe recopilar para los informes de CNAME perimetrales.


## <a name="accessing-verizon-custom-reports"></a>Acceso a los informes personalizados de Verizon
1. En la hoja de perfil de CDN, haga clic en el botón **Administrar** .
   
    ![Botón de administración del perfil de la red CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Se abre el Portal de administración de CDN.
2. Mueva el puntero sobre la pestaña **Análisis** y después sobre el control flotante **Informes personalizados**. Haga clic en **Edge CNAMEs** (CNAME perimetrales).
   
    ![Portal de administración de la red CDN: menú de informes personalizados](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Informe personalizado de CNAME perimetrales
El informe personalizado de CNAME perimetrales proporciona aciertos y estadísticas transferidas de datos para CNAME perimetrales sobre los que se ha habilitado el registro de informes personalizados. Los CNAME perimetrales constan de nombres de host del punto de conexión de CDN de Azure y nombres de host de dominio personalizado asociados. 

El registro de datos de informes personalizados comienza una hora después de habilitar la funcionalidad de informes personalizados de CNAME perimetrales. Puede ver datos de informe mediante la generación de un informe de CNAME perimetrales para una plataforma específica o para todas las plataformas. La cobertura para este informe se limita a los CNAME perimetrales para los que se recopilaron los datos de informe personalizado durante un período especificado. El informe de CNAME de borde se compone de una tabla de gráfico y los datos para el borde superior de 10 CNAME según la métrica definida en la opción de métricas. 

Generación de un informe personalizado mediante la definición de las siguientes opciones de informe:

- Métricas: se admiten las siguientes opciones:

   - Aciertos: indica el número total de solicitudes que se dirigen a un CNAME perimetral en el que la funcionalidad de informes personalizados está habilitada. Esta métrica no incluye el código de estado devuelto al cliente.

   - Transferencia de datos: indica la cantidad total de datos transferidos entre los servidores perimetrales a los clientes HTTP (por ejemplo, los exploradores web) para las solicitudes que se dirigen a un CNAME perimetral en el que la funcionalidad de informes personalizados está habilitada. La cantidad de datos transferidos se calcula agregando encabezados de respuesta HTTP al cuerpo de respuesta. Como resultado, la cantidad de datos transferidos para cada recurso es mayor que el tamaño real del archivo.

- Agrupaciones: determina el tipo de estadísticas que se muestra debajo del gráfico de barras. Se admiten las siguientes opciones:

   - Códigos de respuesta HTTP: organiza las estadísticas por código de respuesta HTTP (por ejemplo, 200, 403, etc.) devuelto al cliente. 

   - Estado de la caché: organiza las estadísticas por estado de la caché.


Para establecer el intervalo de fechas para el informe, puede seleccionar un intervalo de fechas predefinido, como **Hoy** o **Esta semana**, desde la lista desplegable o puede seleccionar **Personalizado** y escribir manualmente un intervalo de fechas haciendo clic en los iconos del calendario. 

Después de haber seleccionado el intervalo de fechas, haga clic en **Ir** para generar el informe.

Puede exportar los datos en formato de Excel haciendo clic en el símbolo de Excel a la derecha del botón **Ir**.

![Informe de CNAME](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Campos de informes personalizados de CNAME perimetrales

| Campo                     | Descripción   |
|---------------------------|---------------|
| 2xx                       | Indica el número total de solicitudes o datos transferidos (MB) para el CNAME perimetral que da como resultado un código de estado HTTP 2xx (por ejemplo, 200 - Correcto. |
| 3xx                       | Indica el número total de solicitudes o datos transferidos (MB) para el CNAME perimetral que da como resultado un código de estado HTTP 3xx (por ejemplo, 302 - Encontrado o 304 - No modificado). |
| 4xx                       | Indica el número total de solicitudes o datos transferidos (MB) para el CNAME perimetral que da como resultado un código de estado HTTP 4xx (por ejemplo,400 - Solicitud incorrecta, 403 - Prohibido, 404 - No encontrado). |
| 5xx                       | Indica el número total de solicitudes o datos transferidos (MB) para el CNAME perimetral que da como resultado un código de estado HTTP 5xx (por ejemplo, 500 - Error interno del servidor o 502 - Puerta de enlace incorrecta). |
| Porcentaje de aciertos de caché               | Indica el porcentaje de solicitudes almacenadas en caché que se sirven directamente desde la memoria caché al solicitante. |
| Aciertos de caché                | Indica el número total de solicitudes o datos transferidos (MB) para el CNAME perimetral que da como resultado un acierto de caché (por ejemplo, TCP_EXPIRED_HIT, TCP_HIT o TCP_PARTIAL_HIT). Un acierto de caché se produce cuando se detecta una versión almacenada en caché del contenido solicitado. |
| Datos transferidos (MB)     | Indica la cantidad total de datos transferidos (MB) desde los servidores perimetrales a clientes HTTP (exploradores web) para el CNAME perimetral. La cantidad de datos transferidos se calcula agregando encabezados de respuesta HTTP al cuerpo de respuesta. Como resultado, la cantidad de datos transferidos para cada recurso es mayor que el tamaño real del archivo. |
| Descripción               | Identifica un CNME perimetral por su nombre de host. |
| Aciertos                      | Indica el número total de solicitudes para el CNAME perimetral. |
| Errores                    | Indica el número total de solicitudes o datos transferidos (MB) para el CNAME perimetral que da como resultado un error de caché (por ejemplo, TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS o TCP_MISS). Se produce un error de caché cuando el contenido solicitado no se ha almacenado en caché en el servidor perimetral que respetaba la solicitud. | 
| No Cache (Sin caché)                  | Indica el número total de solicitudes o datos transferidos (MB) para el CNAME perimetral que da como resultado un código de estado de la caché CONFIG_NOCACHE.  |
| Otros                     | Indica el número total de solicitudes o datos transferidos (MB) para el CNAME perimetral indicado que da como resultado un código de estado HTTP fuera del intervalo 2xx-5xx. |
| Plataforma                  | Indica la plataforma que controla el tráfico de CNAME perimetrales. |
| Sin asignar               | Indica el número total de solicitudes y datos transferidos (MB) para el CNAME perimetral para el que no se registró la información del código de estado HTTP o el código de estado de la caché.  |
| Uncacheable (No almacenable en caché)               | Indica el número total de solicitudes o datos transferidos (MB) para el CNAME perimetral que da como resultado un código de estado de la caché UNCACHEABLE.  |


## <a name="considerations"></a>Consideraciones
Los informes solo pueden generarse en los últimos 18 meses.

