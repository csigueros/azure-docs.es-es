---
title: Panel de calidad de servicio de Centro de partners
description: Muestra los distintos informes disponibles sobre calidad de servicio en el Centro de partners (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: emuench
ms.author: smannepalle
ms.date: 09/27/2021
ms.openlocfilehash: c7df2a30d3de65966d289c0caad7bc6afaca6afd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088034"
---
# <a name="quality-of-service-qos-dashboard"></a>Panel de calidad de servicio (QoS)

Este panel muestra la calidad de las implementaciones de todas las ofertas. Un mayor éxito de implementación de las ofertas significa una mayor calidad de servicio que se ofrece a los clientes.

Puede ver representaciones gráficas de los elementos siguientes:

- [Calidad por ofertas](#quality-by-offers)
- [Calidad por duración de la implementación](#quality-by-deployment-duration)
- [Núm. de implementac.](#deployment-count)
- [Número de implementaciones por estado](#deployment-count-by-status)
- [Recursos y códigos de error de las implementaciones](#deployment-errors-codes-and-resources)
- [Errores de implementación por plan de oferta](#deployment-errors-by-offer-plan)
- [Confiabilidad de la implementación por ubicación](#deployment-reliability-by-location)

Además, puede ver los [detalles de implementación de la oferta](#offer-deployment-details) en formato tabular.

> [!IMPORTANT]
> Actualmente, este panel solo está disponible para las ofertas de **aplicaciones de Azure** disponibles para todos los usuarios (no para ofertas privadas).

Esta característica es aplicable actualmente a todos los asociados que realizan la implementación de las ofertas de aplicaciones de Azure mediante plantillas de Azure Resource Manager (ARM) (pero no para ofertas privadas). Este informe no mostrará los datos de otras ofertas de Marketplace.

## <a name="access-the-quality-of-service-dashboard"></a>Acceso al panel de calidad de servicio

[!INCLUDE [preview interface note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vista de áreas de trabajo](#tab/workspaces-view)

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).

1. En la página principal, seleccione el icono **Información**.

    [ ![Se muestra el icono Información en el Centro de partners.](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. En el menú de la izquierda, seleccione **Calidad de servicio**.

    [ ![Se muestra el panel de navegación izquierdo del panel Información.](./media/quality-of-service/insights-overview-workspaces.png) ](./media/quality-of-service/insights-overview-workspaces.png#lightbox)

#### <a name="current-view"></a>[Vista actual](#tab/current-view)

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).

1. En el menú de navegación izquierdo, seleccione **Marketplace comercial** > [**Analizar**](https://partner.microsoft.com/dashboard/commercial-marketplace/quality-of-service/summary) > **Calidad de servicio**.

---

## <a name="elements-of-the-quality-of-service-dashboard"></a>Elementos del panel de calidad de servicio

En las secciones siguientes, se describe cómo usar el panel de calidad de servicio (QoS) y cómo interpretar los datos.

### <a name="month-range"></a>Intervalo mensual

#### <a name="workspaces-view"></a>[Vista de áreas de trabajo](#tab/workspaces-view)

Hay una selección del intervalo mensual en la esquina superior derecha de cada página. Puede personalizar la salida de los gráficos de la página **Calidad de servicio** seleccionando un intervalo mensual basado en los últimos 6 o 12 meses, o seleccionando un intervalo mensual personalizado con una duración máxima de 12 meses. El intervalo mensual predeterminado (período de selección) es de seis meses.

[ ![Se muestran los filtros del panel de calidad de servicio.](./media/quality-of-service/qos-filters-workspaces.png) ](./media/quality-of-service/qos-filters-workspaces.png#lightbox)

#### <a name="current-view"></a>[Vista actual](#tab/current-view)

Hay una selección del intervalo mensual en la esquina superior derecha de cada página. Puede personalizar la salida de los gráficos de la página **Calidad de servicio** seleccionando un intervalo mensual basado en los últimos 6 o 12 meses, o seleccionando un intervalo mensual personalizado con una duración máxima de 12 meses. El intervalo mensual predeterminado (período de selección) es de seis meses.

---

### <a name="quality-by-offers"></a>Calidad por ofertas

En este gráfico, se muestra la calidad del servicio por ofertas y sus SKU correspondientes. Proporciona métricas y tendencias mensuales para las implementaciones de ofertas en las pestañas **Total**, **Correcta** y **Con errores**. El gráfico de barras representa el número de implementaciones.

El gráfico de líneas representa el cambio en porcentaje para:

- Total de implementaciones (pestaña **Todas**)
- Implementaciones correctas (pestaña **Correcta**)
- Implementaciones con errores (pestaña **Con errores**).

El gráfico muestra la métrica y las tendencias de todas las ofertas. Las ofertas principales se muestran en el gráfico y el resto se agrupan como **Restantes**.

:::image type="content" source="media/quality-of-service/quality-by-offers-1.png" alt-text="Se muestra un gráfico de calidad por oferta, versión 1.":::

Acerca de este gráfico:

- Seleccione las ofertas específicas de la leyenda para mostrar solo esa oferta y las SKU asociadas en el gráfico.
- Al mantener el puntero sobre un sector del gráfico, aparecerá el número de implementaciones y el porcentaje de esa oferta en comparación con el número total de implementaciones entre todas las ofertas.
- La tendencia de calidad por ofertas muestra tendencias de crecimiento o disminución mensuales.

:::image type="content" source="media/quality-of-service/quality-by-offers-2.png" alt-text="Se muestra un gráfico de calidad por oferta, versión 2.":::

Acerca de este gráfico:

- Seleccione las ofertas específicas y las SKU asociadas en la leyenda para que se muestren.
- La tendencia de calidad por ofertas muestra las métricas mes a mes.
- Al ver la tendencia mes a mes de una oferta, seleccione un máximo de tres SKU de esa oferta.
- El gráfico de líneas representa los mismos cambios expresados en porcentaje que los indicados en el gráfico anterior.

### <a name="quality-by-deployment-duration"></a>Calidad por duración de la implementación

Este gráfico muestra la métrica y la tendencia de la duración media de una implementación correcta y con errores. Para ver las métricas, seleccione una oferta en el menú desplegable. Seleccione una SKU en la vista tabular o escríbala en la barra de búsqueda. A continuación, se muestran diferentes duraciones medias de las implementaciones (en minutos):

- **Duración de implementaciones correctas**: tiempo medio de duración de las implementaciones con el estado de implementación de la oferta marcado como Correcta. Esta métrica agregada se calcula utilizando la duración entre las marcas de tiempo inicial y final de las implementaciones marcadas con estado correcto.
- **Duración de implementaciones con errores**: tiempo medio de duración de las implementaciones con el estado de implementación de la oferta marcado como Con errores. Esta métrica agregada se calcula utilizando la duración entre las marcas de tiempo inicial y final de las implementaciones marcadas con estado Con errores.
- **Duración de primeras implementaciones correctas**: tiempo medio de duración de las implementaciones con el estado de implementación de la oferta marcado como Correcta. Esta métrica agregada se calcula utilizando la duración entre la marca de tiempo inicial de la primera implementación y la marca de tiempo de finalización de la última implementación marcadas con estado correcto. Se calcula para cada implementación marcada para una SKU de oferta y un cliente específicos.

:::image type="content" source="media/quality-of-service/deployment-duration-quality.png" alt-text="Se muestra un gráfico de calidad sobre la duración de la implementación.":::

Acerca de este gráfico:

- Seleccione duración de las implementaciones con errores, correctas, o primera implementación correcta en la leyenda para que se muestren.
- El gráfico de líneas presenta la duración media de las implementaciones marcadas como correctas, con errores y correctas con intentos anteriores con errores.
- El tiempo medio de las primeras implementaciones factoriza el tiempo empleado en los intentos con errores antes de que la implementación se marque como correcta.

### <a name="deployment-count"></a>Núm. de implementac.

En este gráfico, se muestra la implementación total de ofertas. Las métricas y las tendencias de crecimiento se representan mediante un gráfico de líneas. Para ver el valor de cada mes, mantenga el puntero sobre el gráfico de líneas.

El valor en porcentaje situado bajo las métricas de implementaciones representa la cantidad de crecimiento o disminución durante el intervalo de meses seleccionado.

:::image type="content" source="media/quality-of-service/deployment-count.png" alt-text="Se muestra un gráfico de número de implementaciones.":::

Acerca de este gráfico:

- Número total de implementaciones de ofertas para el intervalo de fechas seleccionado.
- Cambio en el porcentaje de implementaciones de ofertas durante el intervalo de fechas seleccionado.
- Tendencia mes a mes del número total de implementaciones de ofertas.

### <a name="deployment-count-by-status"></a>Número de implementaciones por estado

En este gráfico, se muestra la métrica y la tendencia de implementaciones de ofertas correctas y con errores por parte de los clientes para el intervalo de meses seleccionado. Las implementaciones de ofertas pueden tener dos estados: **Correcta** o **Con errores**.

:::image type="content" source="media/quality-of-service/deployment-count-by-status.png" alt-text="Se muestra un gráfico del número de implementaciones por estado.":::

Acerca de este gráfico:

- Número total de implementaciones de ofertas correctas y con errores para el intervalo de fechas seleccionado.
- Cambio en el porcentaje de implementaciones de ofertas correctas y con errores para el intervalo de fechas seleccionado.
- Tendencia mes a mes del número de implementaciones de ofertas correctas y con errores.

### <a name="deployment-errors-codes-and-resources"></a>Recursos y códigos de error de las implementaciones

En este gráfico, se muestran las métricas y las tendencias de los códigos de error y los recursos básicos de las implementaciones de ofertas. La sección tabular se puede dinamizar sobre los códigos de error y los recursos. La primera pestaña secundaria proporciona los códigos de error, la descripción y el número de errores de base de análisis. La segunda proporciona una base de análisis de los recursos de la implementación. El gráfico de líneas proporciona los códigos de error y los recursos de base del número total de errores.

Para más información sobre los códigos de error, consulte [Solución de errores comunes de implementación de Azure](../azure-resource-manager/templates/common-deployment-errors.md) y [Proveedores de recursos para servicios de Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

:::image type="content" source="media/quality-of-service/deployment-error-codes-1.png" alt-text="Se muestra un gráfico de códigos de error de implementación, ejemplo 1.":::
:::image type="content" source="media/quality-of-service/deployment-error-codes-2.png" alt-text="Se muestra un gráfico de códigos de error de implementación, ejemplo 2.":::

Acerca de estos gráficos:

- Seleccione errores o recursos específicos en la leyenda para que se muestren.
- El widget de tendencia muestra el número de errores mes a mes.
- Al ver una tendencia de mes a mes por códigos de error o recursos, seleccione un máximo de tres elementos en la tabla.
- Ordene los códigos de error y los recursos de los errores de implementación por número de errores básicos en la tabla.

### <a name="deployment-errors-by-offer-plan"></a>Errores de implementación por plan de oferta

En este gráfico, el eje Y representa el número de errores de implementación y el eje X representa el percentil de los planes de oferta principales (por número de errores).

:::image type="content" source="media/quality-of-service/deployment-error-by-offer-plan.png" alt-text="Se muestra un gráfico de errores de implementación por plan de oferta.":::

Acerca de este gráfico:

- Los gráficos de barras representan el número de errores de implementación para el intervalo de meses seleccionado.
- Los valores del gráfico de líneas representan los porcentajes de errores acumulados por plan de oferta.

### <a name="deployment-reliability-by-location"></a>Confiabilidad de la implementación por ubicación

En este gráfico, se muestra el mapa térmico del número de implementaciones correctas y con errores para el intervalo de meses seleccionado. También se muestra el porcentaje de errores en cada región. La escala de colores de verde a rojo representa un valor de bajo a alto de las tasas de error. Seleccione un registro de la tabla para acercar una región de implementación.

:::image type="content" source="media/quality-of-service/deployment-reliability.png" alt-text="Se muestra un gráfico de confiabilidad de la implementación por ubicaciones.":::

Acerca de este gráfico:

- Puede mover el mapa para ver la ubicación exacta.
- Puede acercar una ubicación específica.
- El mapa térmico tiene una cuadrícula complementaria para ver los detalles de implementación sobre el número de implementaciones correctas, con errores y el porcentaje de errores en una ubicación específica.
- Las regiones en color rojo indican las mayores tasas de errores y el color verde indica una tasa menor.
- Puede buscar y seleccionar un país o región en la cuadrícula para ampliar la ubicación en el mapa. Vuelva a la vista original con el icono **Inicio**.

## <a name="offer-deployment-details"></a>Detalles de implementaciones de ofertas

En esta tabla, se muestran todos los detalles disponibles de implementaciones de ofertas. Descargue el informe para ver los datos sin procesar sobre implementaciones de ofertas.

:::image type="content" source="media/quality-of-service/deployment-details.png" alt-text="Se muestra una tabla de detalles de las implementaciones.":::

Acerca de esta tabla:

- Se muestra una lista numerada de las 1000 principales implementaciones ordenadas por fecha de implementación.
- Cada columna de la cuadrícula se puede ordenar.
- Expanda el control y exporte la tabla.
- La vista de detalles está paginada. Seleccione otras páginas en la parte inferior.

### <a name="dictionary-of-data-terms"></a>Diccionario de términos de datos

| Nombre de la columna | Nombre del atributo | Definición |
| --- | --- | --- |
| Id. de oferta | Id. de oferta | Nombre de la oferta implementada |
| SKU | SKU | El nombre del plan de oferta y SKU implementados |
| Estado de implementación | Estado de implementación | Estado de implementación de la oferta marcado como **Correcta** o **Con errores** |
| Id. de suscripción | Id. de suscripción | Identificador de suscripción del cliente |
| Identificador de inquilino de cliente | Identificador de inquilino de cliente | Identificador de inquilino del cliente |
| Nombre del cliente | Nombre del cliente | Nombre del cliente |
| Tipo de plantilla | Tipo de plantilla | Tipo de aplicación de Azure implementada. Puede ser una aplicación administrada o plantillas de solución y no puede ser privada. |
| Hora de inicio de la implementación | Hora de inicio de la implementación | Hora de inicio de la implementación |
| Hora de finalización de la implementación | Hora de finalización de la implementación | Hora de finalización de la implementación |
| Duración de la implementación: | Duración de la implementación: | Duración total de la implementación de la oferta en milisegundos. Se muestra expresada en minutos en el gráfico. |
| Región de la implementación | Región de la implementación | Ubicación de la implementación de la aplicación de Azure |
| Proveedor de recursos | Proveedor de recursos | Proveedor de recursos del recurso específico implementado |
| Uri de recurso | Uri de recurso | Identificador URI del recurso implementado |
| Grupo de recursos | Grupo de recursos | Grupo de recursos en el que se ha implementado el recurso |
| Tipo de recurso | Tipo de recurso | Tipo de recurso implementado |
| Nombre de recurso | Nombre de recurso | Nombre del recurso implementado |
| Código de error | Código de error | Código del error de implementación |
| Nombre del error | Nombre del error | Nombre del error de implementación |
| Mensaje de error | Mensaje de error | Nombre de error del error de implementación |
| Código de error en profundidad | Código de error en profundidad | Si está presente, contiene más información sobre el código de error. |
| Código del mensaje en profundidad | Código del mensaje en profundidad | Si está presente, contiene más información sobre el mensaje de error. |
| Id. de correlación | Id. de correlación | Identificador utilizado para distinguir las diferentes implementaciones. El mismo valor significa que todos los recursos que se implementan son para una implementación. |
|

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los errores de implementación, consulte [Solución de errores comunes de implementación de Azure](../azure-resource-manager/templates/common-deployment-errors.md).
- Para más información sobre los proveedores de recursos, consulte [Proveedores de recursos para servicios de Azure](../azure-resource-manager/management/azure-services-resource-providers.md).
- Para ver los gráficos, las tendencias y los valores de datos agregados que resumen la actividad de la oferta en el marketplace, consulte [Panel Resumen de los análisis de marketplace comercial](./summary-dashboard.md).
- Para información sobre los pedidos en un formato gráfico que se pueda descargar, consulte [Panel Pedidos de los análisis de marketplace comercial](./orders-dashboard.md).
- Para información sobre las métricas de uso y facturación de uso medido de las ofertas de máquina virtual, consulte [Panel Uso de los análisis de marketplace comercial](./usage-dashboard.md).
- Para información detallada sobre los clientes, incluidas las tendencias de crecimiento, consulte [Panel Cliente de los análisis de marketplace comercial](./customer-dashboard.md).
- Para información sobre licencias, consulte [Panel de licencias de análisis de marketplace comercial](./license-dashboard.md).
- Para obtener una lista de las solicitudes de descarga de los últimos 30 días, consulte [Panel Descargas de los análisis de marketplace comercial](./downloads-dashboard.md).
- Para tener una vista consolidada de comentarios de los clientes sobre las ofertas de Microsoft AppSource y Azure Marketplace, consulte [Panel "Calificaciones y opiniones" de análisis del marketplace comercial](./ratings-reviews.md).
- Para ver las preguntas más frecuentes sobre los análisis de Marketplace comercial y un diccionario completo de términos de datos, consulte [Preguntas comunes sobre análisis de Marketplace comercial](./analytics-faq.yml).