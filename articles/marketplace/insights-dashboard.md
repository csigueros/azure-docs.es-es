---
title: Panel Información de marketplace de los análisis de marketplace comercial
description: Acceda a un resumen de análisis web de marketplace en el Centro de partners, que permite medir la involucración del cliente en Microsoft AppSource y Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: a8ee69e0c9c196f42a733c4cb6c3079228238b21
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082937"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Panel Información de marketplace de los análisis de marketplace comercial

En este artículo se proporciona información sobre el panel Información de Marketplace del Centro de partners. En este panel se muestra un resumen de los análisis web de marketplace comercial que permite a los editores medir la participación del cliente en sus respectivas páginas de detalles de productos en las tiendas en línea de marketplace comercial: Microsoft AppSource y Azure Marketplace.

Para obtener definiciones detalladas de la terminología de análisis, consulte [Terminología de análisis y preguntas comunes de Marketplace comercial](./analytics-faq.yml).

## <a name="marketplace-insights-dashboard"></a>Panel de información de Marketplace

El [panel Información de Marketplace](https://go.microsoft.com/fwlink/?linkid=2165936) muestra información general del rendimiento empresarial de las ofertas de Azure Marketplace y AppSource. Este panel proporciona información general extensa de lo siguiente:

- Tendencia de visitas a la página
- Tendencia de llamada a acciones
- Visitas a la página y llamada a acciones frente a ofertas, dominios de referencia e id. de campaña
- Información de Marketplace por geografía
- Tabla de detalles de Información de Marketplace

El panel de información de Marketplace se proporciona información de la secuencia de clic, que no debe correlacionarse con los clientes potenciales generados en el punto de conexión de destino del cliente potencial.

> [!NOTE]
> La latencia máxima entre los usuarios que visitan ofertas en Azure Marketplace o AppSource y los que presentan informes en el Centro de partners es de 48 horas.

## <a name="access-the-marketplace-insights-dashboard"></a>Acceso al panel de información de Marketplace

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vista de áreas de trabajo](#tab/workspaces-view)

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).
1. En la página principal, seleccione el icono **Información**.

    [ ![Se muestra el icono Información en la página principal del Centro de partners.](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. En el menú izquierdo, seleccione **Información de Marketplace**.

#### <a name="current-view"></a>[Vista actual](#tab/current-view)

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).
1. En el menú de navegación izquierdo, seleccione **Marketplace comercial** > **Analizar** > **Información de Marketplace**.

---

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Elementos del panel Información de Marketplace

En el panel Información de Marketplace se muestran los detalles de telemetría web para Azure Marketplace y AppSource en dos pestañas independientes. En las secciones siguientes se describe cómo usar el panel Marketplace Insights y cómo leer los datos.

### <a name="month-range"></a>Intervalo mensual

#### <a name="workspaces-view"></a>[Vista de áreas de trabajo](#tab/workspaces-view)

Puede encontrar una selección del intervalo mensual en la esquina superior derecha de cada página. Puede personalizar la salida de los gráficos de la página **Información de Marketplace** seleccionando un intervalo mensual basado en los últimos 6 o 12 meses, o seleccionando un intervalo mensual personalizado con una duración máxima de 12 meses. El intervalo mensual predeterminado (período de cálculo) es de seis meses.

[ ![Ilustra los filtros del mes en el panel de Información de Marketplace.](./media/insights-dashboard/marketplace-insights-filters.png) ](./media/insights-dashboard/marketplace-insights-filters.png#lightbox)

#### <a name="current-view"></a>[Vista actual](#tab/current-view)

Puede encontrar una selección del intervalo mensual en la esquina superior derecha de cada página. Puede personalizar la salida de los gráficos de la página **Información de Marketplace** seleccionando un intervalo mensual basado en los últimos 6 o 12 meses, o seleccionando un intervalo mensual personalizado con una duración máxima de 12 meses. El intervalo mensual predeterminado (período de cálculo) es de seis meses.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Ilustra los filtros del mes en el panel Información de Marketplace.":::

---

> [!NOTE]
> Todas las métricas de los widgets de visualización y los informes de exportación respetan el período de cálculo seleccionado por el usuario.

### <a name="page-visits-trends"></a>Tendencias de visitas a la página

En el gráfico **Visitantes** de Información de Marketplace se muestra un recuento de las _visitas a la página_ y los _visitantes únicos_ para el período de cálculo seleccionado.

**Visitas a la página**: este número representa el número de sesiones de usuario distintas en la página de descripción de la oferta (página de detalles del producto) para un período de cálculo seleccionado. Los indicadores de porcentaje rojos y verdes representan el porcentaje de crecimiento de las visitas a la página. El gráfico de tendencias representa el recuento mensual de visitas a la página.

**Visitantes únicos**: este número representa el recuento de visitantes distintivo durante el período de cálculo seleccionado para las ofertas de Azure Marketplace y AppSource. Un visitante que haya visitado una o varias páginas de detalles de productos se contará como un visitante único.

[![Ilustra el gráfico Visitantes en el panel Información de Marketplace.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Tendencia de llamada a acciones

Este número representa el recuento de clics en el botón **Llamada a la acción** completados en la página de descripción de la oferta (página de detalles del producto). Las _llamadas a la acción_ se cuentan cuando los usuarios seleccionan los botones **Obtener ahora**, **Prueba gratuita**, **Contacto** o **Versión de prueba**. El *consentimiento dado* representa el recuento total de clics para el consentimiento proporcionado por el cliente para Microsoft o el asociado, y es igual al número de clientes adquiridos para sus ofertas. En los dos ejemplos siguientes se muestra dónde aparecen los clics *dados por consentimiento*:

:::image type="content" source="./media/insights-dashboard/consent-screen.png" alt-text="Muestra una ubicación donde se selecciona un botón de consentimiento.":::

En el gráfico siguiente se muestra la métrica *CTA* frente al *consentimiento dado*:

:::image type="content" source="./media/insights-dashboard/consent-given-graph.png" alt-text="Muestra un gráfico de ejemplo de Llamadas a la acción frente al consentimiento dado.":::

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Visitas a la página y llamada a acciones frente a ofertas, dominios de referencia e id. de campaña

#### <a name="offers"></a>Ofertas

Seleccione una oferta específica para ver la tendencia mensual de las visitas a la página, las llamadas a la acción y los clics de consentimiento dado en el gráfico.

:::image type="content" source="./media/insights-dashboard/offers-funnel-graph.png" alt-text="Muestra un gráfico de embudo de ejemplo de ofertas.":::

#### <a name="referral-domains"></a>Dominios de referencia

Al seleccionar un dominio de referencia específico, se muestra la tendencia mensual de las visitas a la página, las llamadas a la acción y los clics con consentimiento en el gráfico de la derecha. Además, hay una columna para Plataforma: sitio web y cliente, que solo se muestra para las ofertas de AppSource. La vista de embudo muestra las tasas de conversiones entre vistas de página, llamadas a la acción y clics con consentimiento dado en el gráfico.

:::image type="content" source="./media/insights-dashboard/referral-domains-funnel-graph.png" alt-text="Muestra un gráfico de embudo de ejemplo de dominios de referencia.":::

#### <a name="campaign-ids"></a>Identificadores de campaña

al seleccionar un identificador de campaña específico, debería poder comprender el éxito de la campaña. En cada campaña, debería poder ver la tendencia mensual de las visitas a la página, las llamadas a la acción y los clics con consentimiento dado en el gráfico.

:::image type="content" source="./media/insights-dashboard/campaign-id-funnel-graph.png" alt-text="Ilustra el gráfico Campaña en el panel Información de Marketplace.":::

### <a name="marketplace-insights-by-geography"></a>Información de Marketplace por geografía

Durante el período de cálculo seleccionado, en el mapa térmico se muestra el recuento de visitas a la página, visitantes únicos y llamadas a la acción (CTA). El tono del color de claro a oscuro del mapa representa el número de visitantes únicos de bajo a alto. Seleccione un registro de la tabla para acercar un país o región.

[ ![Ilustra el gráfico Diseminación geográfica en el panel Información de Marketplace.](./media/insights-dashboard/geographical-spread.png) ](./media/insights-dashboard/geographical-spread.png#lightbox)

Tenga en cuenta lo siguiente:

- Puede mover el mapa para ver la ubicación exacta.
- Puede hacer zoom en una ubicación específica.
- El mapa térmico tiene una cuadrícula complementaria para ver los detalles del recuento de clientes, el recuento de pedidos y las horas de uso normalizado en la ubicación específica.
- Puede buscar y seleccionar un país o región en la cuadrícula para ampliar la ubicación en el mapa. Vuelva a la vista original presionando el botón **Inicio** en el mapa.

### <a name="marketplace-insights-details-table"></a>Tabla de detalles de Información de Marketplace

En esta tabla se proporciona una vista de lista de las visitas a la página y las llamadas a la acción de las páginas de las ofertas seleccionadas ordenadas por fecha.

- Los datos se pueden extraer en un archivo .TSV o .CSV si el recuento de los registros es inferior a 1000.
- Si el número de registros es superior a 1000, los datos exportados se colocarán de forma asincrónica en una página de descargas durante los próximos 30 días.
- Filtre los datos por los nombres de las ofertas y las campañas para mostrar los datos que le interesan.

> [!TIP]
> Puede usar el icono de descarga que está en la esquina superior derecha de cualquier widget para descargar los datos. Igualmente, para proporcionar comentarios sobre cada uno de los widgets, haga clic en el icono "pulgar hacia arriba" o "pulgar hacia abajo".

| Nombre de la columna en la<br>interfaz de usuario | Nombre del atributo | Definición | Nombre de la columna en informes<br>de acceso mediante programación |
| ------------ | ------------- | ------------- | ------------- |
| Date | Fecha de la visita | Fecha de la visita a la página o de la generación de evento de clic de llamada a la acción en la página de la oferta de Azure Marketplace o AppSource. | Date |
| Nombre de la oferta | Nombre de la oferta | Nombre de la oferta de Marketplace comercial. | OfferName |
| Dominio de referencia | Dominio de referencia | Nombre del dominio de referencia desde donde se produjo la visita a la página. Si no se ha capturado ningún dominio de referencia para la visita a la página, la entrada correspondiente es "Referral domain not present" (El dominio de referencia no está presente). |  ReferralDomain |
| Nombre de país | Nombre de país | Nombre del país desde el que se produjo la visita a la página. | CountryName |
| Visitas de página | Visitas de página | Número de visitas de página asociadas al nombre de la oferta para una fecha determinada. | PageVisits |
| Obténgalo ahora | Obténgalo ahora | Número de clics en la llamada a la acción "Obténgalo ahora" de la página de la oferta para una fecha determinada. | GetItNow |
| Ponerse en contacto conmigo | Ponerse en contacto conmigo | Número de clics en la llamada a la acción "Ponerse en contacto conmigo" de la página de la oferta para una fecha determinada. | ContactMe |
| Versión de prueba | Versión de prueba | Número de clics en la llamada a la acción "Versión de prueba" de la página de la oferta para una fecha determinada. | TestDrive |
| Versión de prueba gratuita | Versión de prueba gratuita | Número de clics en la llamada a la acción "Versión de prueba gratuita" de la página de la oferta para una fecha determinada. | FreeTrial |
| Campaña | El nombre de la campaña. | Capacidad de comprender la telemetría web (visita de páginas y clics de llamadas a la acción) con el nombre de la campaña. | Campaña |
| Consentimiento dado | Consentimiento dado | Recuento total de clics para el consentimiento proporcionado por el cliente a Microsoft o al asociado | consentGivenCount |
| Plataforma | Plataforma | Indica el sitio web o cliente (en el almacén del producto) como origen de vista de página, CTA o clics de consentimiento | plataformas |
| N/D | Sitio | El nombre del escaparate desde el que se produjo la visita a la página o el clic en la llamada a la acción. Los valores posibles son:<br><ul><li>AZUREMARKETPLACE<li>APPSOURCE</ul> | Sitio |
|

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre los informes de análisis disponibles en marketplace comercial, consulte [Acceso a los informes de análisis de marketplace comercial en el Centro de partners](analytics.md).
- Para información sobre los pedidos en un formato gráfico que se pueda descargar, consulte [Panel Pedidos de los análisis de marketplace comercial](orders-dashboard.md).
- Para información sobre las métricas de uso y facturación de uso medido de las ofertas de máquina virtual, consulte [Panel Uso de los análisis de marketplace comercial](usage-dashboard.md).
- Para obtener información detallada acerca de los clientes, incluidas las tendencias de crecimiento, consulte [Panel Cliente de los análisis de marketplace comercial](customer-dashboard.md).
- Para obtener una lista de las solicitudes de descarga de los últimos 30 días, consulte [Panel Descargas de los análisis de marketplace comercial](downloads-dashboard.md).
- Para obtener una vista consolidada con los comentarios de los clientes sobre las ofertas de Azure Marketplace y AppSource, consulte [Panel de análisis de calificaciones y opiniones en el Centro de partners](ratings-reviews.md).
- Para ver las preguntas más frecuentes sobre los análisis de marketplace comercial y un diccionario completo de términos de datos, consulte [Preguntas más frecuentes y terminología del análisis de marketplace comercial](analytics-faq.yml).
