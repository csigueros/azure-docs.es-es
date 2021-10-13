---
title: 'Pruebas de disponibilidad privadas: Application Insights de Azure Monitor'
description: Aprenda a usar pruebas de disponibilidad en servidores internos que se ejecutan detrás de un firewall con pruebas privadas.
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: 713a57db66042c562e212d65c6dd265c2aab47cd
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858450"
---
# <a name="private-testing"></a>Pruebas privadas

Si quiere usar pruebas de disponibilidad en servidores internos que se ejecutan detrás de un firewall, hay dos soluciones posibles: habilitación de pruebas de ping públicas y escenarios desconectados o sin entrada.

## <a name="public-ping-test-enablement"></a>Habilitación de pruebas de ping públicas

> [!NOTE]
> Si no quiere permitir ninguna entrada a su entorno, use el método de la sección [Escenarios desconectados o sin entrada](#disconnected-or-no-ingress-scenarios).

 Asegúrese de que tiene un registro DNS público para el sitio web interno. Se producirá un error en la prueba si no se puede resolver el DNS. [Cree un nombre de dominio personalizado para la aplicación interna.](../../cloud-services/cloud-services-custom-domain-name-portal.md#add-an-a-record-for-your-custom-domain)

Configure el firewall para que permita solicitudes entrantes desde nuestro servicio.

- Las [etiquetas de servicio](../../virtual-network/service-tags-overview.md) son una manera sencilla de habilitar los servicios de Azure sin tener que autorizar direcciones IP individuales ni mantener una lista actualizada. Las etiquetas de servicio se pueden usar en Azure Firewall y grupos de seguridad de red para permitir el acceso a nuestro servicio. **ApplicationInsightsAvailability** es la etiqueta de servicio dedicada a nuestro servicio de pruebas de ping.
    1. Si usa [grupos de seguridad de red de Azure](../../virtual-network/network-security-groups-overview.md), vaya al recurso del grupo seguridad de red y seleccione **Reglas de seguridad de entrada** en *Configuración* y, luego, elija **Agregar**.

         :::image type="content" source="media/availability-private-test/add.png" alt-text="Captura de pantalla de la pestaña Reglas de seguridad de entrada en el recurso del grupo de seguridad de red.":::

    1. Luego, seleccione *Etiqueta de servicio* como origen y *ApplicationInsightsAvailability* como etiqueta de servicio de origen. Use los puertos abiertos 80 (http) y 443 (https) para el tráfico entrante desde la etiqueta de servicio.

        :::image type="content" source="media/availability-private-test/service-tag.png" alt-text="Captura de pantalla de la pestaña Add inbound security rules (Agregar reglas de seguridad de entrada) con un origen de etiqueta de servicio.":::

- Si los puntos de conexión se hospedan fuera de Azure o las etiquetas de servicio no están disponibles para su escenario, deberá permitir individualmente las [direcciones IP de nuestros agentes de pruebas web](ip-addresses.md). Puede consultar los intervalos IP directamente mediante PowerShell, la CLI de Azure o una llamada REST con la [API de etiqueta de servicio](../../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api). También puede descargar un archivo [JSON](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) para obtener una lista de etiquetas de servicio actuales con detalles de las direcciones IP.
    1. En el recurso de grupo seguridad de red, seleccione **Reglas de seguridad de entrada** en *Configuración* y, luego, elija **Agregar**.
    1. Luego, seleccione *Direcciones IP* como origen y agregue las direcciones IP a una lista delimitada por comas en intervalos de direcciones IP/CIRD.

         :::image type="content" source="media/availability-private-test/ip-addresses.png" alt-text="Captura de pantalla de la pestaña Add inbound security rules (Agregar reglas de seguridad de entrada) con un origen de direcciones IP.":::

## <a name="disconnected-or-no-ingress-scenarios"></a>Escenarios desconectados o sin entrada

El servidor de prueba debe tener acceso saliente al punto de conexión de ingesta de Application Insights, lo que plantea un riesgo de seguridad mucho menor que la alternativa de permitir solicitudes entrantes. Los resultados aparecerán en la pestaña de pruebas web de disponibilidad, aunque la experiencia se verá ligeramente simplificada con respecto a lo que está disponible para las pruebas creadas mediante Azure Portal. Las pruebas de disponibilidad personalizadas también aparecerán como resultados de disponibilidad en los análisis, las búsquedas y las métricas.

1. Conecte el recurso Application Insights y el entorno desconectado mediante [Azure Private Link](../logs/private-link-security.md).
1. Escriba código personalizado para comprobar periódicamente el servidor interno o los puntos de conexión. Puede ejecutar el código mediante [Azure Functions](availability-azure-functions.md) o un proceso en segundo plano en un servidor de pruebas detrás del firewall. El proceso de prueba puede enviar sus resultados a Application Insights mediante `TrackAvailability()` API en el paquete de SDK principal.

## <a name="troubleshooting"></a>Solución de problemas

[Artículo de solución de problemas](troubleshoot-availability.md) dedicado.

## <a name="next-steps"></a>Pasos siguientes

* [Azure Private Link](../logs/private-link-security.md)
* [Alertas de disponibilidad](availability-alerts.md)
* [Pruebas de direcciones URL](monitor-web-app-availability.md)
* [Creación y ejecución de pruebas de disponibilidad personalizadas mediante Azure Functions](availability-azure-functions.md)
