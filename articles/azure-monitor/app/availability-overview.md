---
title: Pruebas de disponibilidad de Application Insights
description: Configure pruebas web periódicas para supervisar la disponibilidad y la capacidad de respuesta de la aplicación o el sitio web.
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 2e87d9eca75cfd507549213999882dfe6ff3ffa6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737091"
---
# <a name="application-insights-availability-tests"></a>Pruebas de disponibilidad de Application Insights

Después de haber implementado la aplicación o el sitio web, puede configurar pruebas periódicas para supervisar la disponibilidad y capacidad de respuesta. [Application Insights](./app-insights-overview.md) envía solicitudes web a la aplicación a intervalos regulares desde puntos de todo el mundo. Puede enviar una alerta si la aplicación no responde o si responde de manera demasiada lenta.

Puede configurar pruebas de disponibilidad para cualquier punto de conexión HTTP o HTTPS que sea accesible desde la red pública de Internet. No hace falta realizar cambios en el sitio web que está probando. De hecho, ni siquiera es necesario que sea un sitio de su propiedad. Puede probar la disponibilidad de una API de REST de la que depende su servicio.

## <a name="types-of-tests"></a>Tipos de pruebas

Hay cuatro tipos de pruebas de disponibilidad:

* [Prueba de ping de URL (clásica)](monitor-web-app-availability.md): puede crear esta prueba sencilla a través del portal para validar si un punto de conexión responde y medir el rendimiento asociado a esa respuesta. También puede establecer criterios de éxito personalizados junto con características más avanzadas, como analizar solicitudes dependientes y permitir reintentos.
* [Prueba estándar (versión preliminar)](availability-standard-tests.md): esta prueba de solicitud única es similar a la prueba de ping de URL. Incluye la validez del certificado SSL, la comprobación proactiva de la duración, el verbo de solicitud HTTP (por ejemplo, `GET`, `HEAD` o `POST`), los encabezados personalizados y los datos personalizados asociados a la solicitud HTTP.
* [Prueba web de varios pasos (clásica)](availability-multistep.md): puede reproducir esta grabación de una secuencia de solicitudes web para probar escenarios más complejos. Las pruebas web de varios pasos se crean en Visual Studio Enterprise y se cargan en el portal, donde puede ejecutarlas.
* [Prueba TrackAvailability personalizada](availability-azure-functions.md): si decide crear una aplicación personalizada para ejecutar pruebas de disponibilidad, puede usar el método [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) para enviar los resultados a Application Insights.

> [!IMPORTANT]
> Tanto la [prueba de ping de URL](monitor-web-app-availability.md) como la [prueba web de varios pasos](availability-multistep.md) se basan en la infraestructura DNS de la red pública de Internet para resolver los nombres de dominio de los puntos de conexión probados. Si usa DNS privado, debe asegurarse de que los servidores de nombres de dominio públicos puedan quitar todos los nombres de dominio de la prueba. Cuando esto no sea posible, puede usar [pruebas TrackAvailability personalizadas](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) en su lugar.

Puede crear hasta 100 pruebas de disponibilidad por recurso de Application Insights.

## <a name="troubleshooting"></a>Solución de problemas

Consulte el [artículo de solución de problemas](troubleshoot-availability.md) dedicado.

## <a name="next-steps"></a>Pasos siguientes

* [Alertas de disponibilidad](availability-alerts.md)
* [Pruebas web de varios pasos](availability-multistep.md)
* [Pruebas de direcciones URL](monitor-web-app-availability.md)
* [Creación y ejecución de pruebas de disponibilidad personalizadas mediante Azure Functions](availability-azure-functions.md)
* [Plantilla de Azure Resource Manager para pruebas web](/azure/templates/microsoft.insights/webtests?tabs=json)