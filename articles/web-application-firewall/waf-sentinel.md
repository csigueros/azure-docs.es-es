---
title: Uso de Microsoft Sentinel con Azure Web Application Firewall
description: En este artículo se muestra cómo usar Microsoft Sentinel con Azure Web Application Firewall (WAF).
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: c888b364bfac5687996bb88472b5725244ea1bcb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305472"
---
# <a name="using-microsoft-sentinel-with-azure-web-application-firewall"></a>Uso de Microsoft Sentinel con Azure Web Application Firewall

Azure Web Application Firewall (WAF) en combinación con Microsoft Sentinel puede proporcionar la administración de eventos de información de seguridad para los recursos de WAF. Azure Sentinel proporciona análisis de seguridad mediante Log Analytics, lo que permite desglosar y ver fácilmente los datos de WAF. Con Microsoft Sentinel, puede acceder a los libros precompilados y modificarlos para que se ajusten mejor a las necesidades de su organización. El libro puede mostrar el análisis de WAF en Microsoft Azure Content Delivery Network (CDN), WAF en Azure Front Door y WAF en Application Gateway en varias suscripciones y áreas de trabajo.

## <a name="waf-log-analytics-categories"></a>Categorías de análisis de registros de WAF

Los análisis de registros de WAF se dividen en las siguientes categorías:  

- Todas las acciones de WAF realizadas 
- Principales 40 direcciones URI de solicitud bloqueadas 
- Principales 50 desencadenadores de eventos  
- Mensajes a lo largo del tiempo 
- Detalles del mensaje completo 
- Eventos de ataque por mensajes  
- Eventos de ataque a lo largo del tiempo 
- Filtro de id. de seguimiento 
- Mensajes de id. de seguimiento 
- Principales 10 direcciones IP de ataque 
- Mensajes de ataque de direcciones IP 

## <a name="waf-workbook-examples"></a>Ejemplos del libro de WAF

En los siguientes ejemplos del libro de WAF se muestran datos de ejemplo:

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="Filtro de acciones de WAF":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="Principales 50 eventos":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="Eventos de ataque":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="Principales 10 direcciones IP de ataque":::

## <a name="launch-a-waf-workbook"></a>Inicio de un libro de WAF

El libro de WAF funciona en todas las instancias de Azure Front Door, Application Gateway y WAF de CDN. Para poder conectar los datos de estos recursos, Log Analytics debe estar habilitado en el recurso. 

Para habilitar Log Analytics para cada recurso, vaya al recurso concreto de Azure Front Door, Application Gateway o CDN:

1. Seleccione **Configuración de diagnóstico**.
2. Seleccione **+ Agregar configuración de diagnóstico**. 
3. En la página Configuración de diagnóstico:
   1. Escriba un nombre. 
   1. Seleccione **Enviar a Log Analytics**. 
   1. Elija el área de trabajo de destino del registro. 
   1. Seleccione los tipos de registro que quiera analizar:
      1. Application Gateway: "ApplicationGatewayAccessLog" y "ApplicationGatewayFirewallLog"
      1. Azure Front Door: "FrontDoorAccessLog" y "FrontDoorFirewallLog"
      1. CDN: "AzureCdnAccessLog"
   1. Seleccione **Guardar**.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="Configuración de diagnóstico":::

4. En la página principal de Azure, escriba *Microsoft Sentinel* en la barra de búsqueda y seleccione el recurso **Microsoft Sentinel**. 
2. Seleccione un área de trabajo ya activa o cree otra. 
3. En el panel de la izquierda, en **Configuración** seleccione **Conectores de datos**.
4. Busque **Firewall de aplicaciones web de Microsoft** y seleccione **Firewall de aplicaciones web (WAF) de Microsoft**. Seleccione **Abrir página del conector** en la parte inferior derecha.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="Conectores de datos":::

8. En **Configuración**, siga las instrucciones correspondientes a cada recurso de WAF cuyos datos de Log Analytics quiera tener, si no lo ha hecho anteriormente.
6. Cuando haya terminado de configurar los distintos recursos de WAF, seleccione la pestaña **Pasos siguientes**. Seleccione uno de los libros recomendados. Este libro usará todos los datos de Log Analytics habilitados previamente. Ahora debe existir un libro de trabajo de WAF para los recursos de WAF.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="Libros de WAF":::


## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre Microsoft Sentinel](../sentinel/overview.md)
- [Más información sobre los libros de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md)
