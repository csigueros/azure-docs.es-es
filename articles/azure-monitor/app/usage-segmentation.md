---
title: Análisis de usuarios, sesiones y eventos en Application Insights
description: Este artículo trata sobre el análisis de los usuarios de su aplicación web.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: 09dc9ba915b0ecf99219aadd9214192b9f4b1e19
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747681"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Análisis de usuarios, sesiones y eventos en Application Insights

Descubra cuándo utilizan los usuarios la aplicación web, en qué páginas están más interesados, en qué ubicación se encuentran dichos usuarios, y los sistemas operativos y exploradores que emplean. Analice la telemetría de uso y de negocio con [Application Insights](./app-insights-overview.md).

:::image type="content" source="./media/usage-segmentation/users.png" alt-text="Captura de pantalla que muestra la pestaña Usuarios con un gráfico de áreas." lightbox="./media/usage-overview/users.png":::

## <a name="get-started"></a>Introducción

Si aún no ve los datos en las hojas de usuarios, sesiones o eventos de Application Insights, [obtenga información sobre cómo empezar a trabajar con las herramientas de uso](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>La herramienta de segmentación de usuarios, sesiones y eventos

Tres de las hojas de uso usan la misma herramienta para segmentar y desglosar telemetría de su aplicación web desde tres perspectivas. Mediante el filtrado y la división de los datos, puede descubrir información detallada sobre el uso relativo de distintas páginas y características.

* **Herramienta Usuarios**: averigüe cuántas personas usan la aplicación y sus características.  Los usuarios se cuentan mediante el uso de identificadores anónimos almacenados en las cookies del explorador. Una sola persona que usa distintos exploradores o máquinas se contarán como más de un usuario.
* **Herramienta Sesiones**: la cantidad de sesiones de actividad de usuario que han incluido determinadas páginas y características de la aplicación. Una sesión se cuenta después de media hora de inactividad del usuario o después de 24 horas de uso continuo.
* **Herramienta Eventos**: la frecuencia con la que se usan ciertas páginas y características de la aplicación. Una vista de página se cuenta cuando un explorador carga una página de la aplicación, siempre que se haya [instrumentado](./javascript.md). 

    Un evento personalizado representa una repetición de algo que sucede en la aplicación, a menudo, una interacción del usuario como una selección en el botón o la finalización de alguna tarea. Inserte código en su aplicación para [generar eventos personalizados](./api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Consulta de determinados usuarios

Explore distintos grupos de usuarios mediante ajustando las opciones de consulta en la parte superior de la herramienta Usuarios:

- Durante: elija un intervalo de tiempo.
- Mostrar: elija una cohorte de usuarios que se va a analizar.
- Qué usaron: elija qué eventos personalizados, solicitudes y vistas de página.
- Eventos: Elija varios eventos, solicitudes y vistas de página que mostrarán los usuarios que realizaron al menos uno, aunque no necesariamente todas las opciones seleccionadas.
- Por eje X de valores: Elija cómo se desglosarán los datos, por intervalo de tiempo o por otra propiedad, como por explorador o ciudad.
- Dividir por: elija una propiedad por la que dividir o segmentar los datos. 
- Agregar filtros: limite la consulta a determinados usuarios, sesiones o eventos en función de sus propiedades, como un explorador o una ciudad. 
 
## <a name="meet-your-users"></a>Conozca a sus usuarios

La sección **Meet your users** (Conozca a sus usuarios) muestra información acerca de los cinco usuarios de ejemplo coincidentes con la consulta actual. Explorar los comportamientos de los usuarios individuales y en su conjunto puede brindar información sobre cómo se usa realmente la aplicación.

## <a name="next-steps"></a>Pasos siguientes

- Para habilitar las experiencias de uso, empiece por enviar [eventos personalizados](./api-custom-events-metrics.md#trackevent) o [vistas de páginas](./api-custom-events-metrics.md#page-views).
- Si ya ha enviado eventos personalizados o vistas de página, explore las herramientas de uso para obtener información sobre cómo los usuarios utilizan el servicio.
    - [Embudos](usage-funnels.md)
    - [Retención](usage-retention.md)
    - [Flujos de usuario](usage-flows.md)
    - [Libros](../visualize/workbooks-overview.md)
    - [Adición de contexto de usuario](./usage-overview.md)