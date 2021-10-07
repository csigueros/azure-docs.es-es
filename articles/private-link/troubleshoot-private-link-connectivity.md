---
title: Solución de problemas de conectividad de Azure Private Link
description: Instrucciones paso a paso para diagnosticar la conectividad de Private Link
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 9aa0ac4334d50a7b3a55ff992fb3e5a984786384
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155028"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Solución de problemas de conectividad de Azure Private Link

En este artículo se proporcionan instrucciones detalladas para validar y diagnosticar la conectividad de su configuración de Azure Private Link.

Con Azure Private Link puede acceder a los servicios de plataforma como servicio (PaaS) de Azure como, por ejemplo, Azure Storage, Azure Cosmos DB y SQL Database, y a los servicios de asociados o clientes hospedados de Azure mediante un punto de conexión privado de la red virtual. El tráfico entre la red virtual y el servicio atraviesa la red troncal de Microsoft, lo cual elimina la exposición a la red pública de Internet. Puede crear su propio servicio Private Link en la red virtual y enviarlo de forma privada a los clientes.

Puede habilitar el servicio que se ejecuta de manera subyacente en el nivel Standard de Azure Load Balancer para el acceso de Private Link. Los consumidores del servicio pueden crear un punto de conexión privado dentro de su red virtual y asignarlo a este servicio para tener acceso a él de forma privada.

Estos son los escenarios de conectividad que están disponibles con Private Link:

- Red virtual de la misma región
- Redes virtuales emparejadas regionalmente
- Redes virtuales emparejadas globalmente
- Cliente local a través de VPN o circuitos de Azure ExpressRoute

## <a name="deployment-troubleshooting"></a>Solución de problemas de implementación

Revise la información sobre cómo [deshabilitar las directivas de red en el servicio Private Link](./disable-private-link-service-network-policy.md) para solucionar problemas en aquellos casos en los que no puede seleccionar la dirección IP de origen de la subred que prefiere para su servicio Private Link.

Asegúrese de que el valor **privateLinkServiceNetworkPolicies** está deshabilitado para la subred de la que está seleccionando la dirección IP de origen.

## <a name="diagnose-connectivity-problems"></a>Diagnóstico de problemas de conectividad

Si tiene problemas de conectividad con la configuración de Private Link, siga estos pasos para asegurarse de que todas las configuraciones habituales son las esperadas.

1. Revise la configuración del servicio Private Link examinando el recurso.

    a. Vaya al [Centro de Private Link](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/overview).

      ![Centro de Private Link](./media/private-link-tsg/private-link-center.png)

    b. En el panel izquierdo, seleccione **servicios Private Link**.

      ![Servicios Private Link](./media/private-link-tsg/private-link-service.png)

    c. Filtre y seleccione el servicio Private Link que quiera diagnosticar.

    d. Revise las conexiones del punto de conexión privado.
     - Asegúrese de que el punto de conexión privado desde el que está buscando la conectividad se muestra en la lista con el estado de conexión **Aprobado**.
     - Si el estado es **Pendiente**, selecciónelo y apruébelo.

       ![Conexiones de punto de conexión privado](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Para ir al punto de conexión privado desde el que se está conectando, seleccione el nombre. Asegúrese de que en el estado de la conexión se muestra el estado **Aprobado**.

       ![Información general de la conexión del punto de conexión privado](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Después de que se aprueben ambos lados, vuelva a intentar la conectividad.

    e. Revise la opción **Alias** en la pestaña **Información general** y el valor de **id. de recurso** en la pestaña **Propiedades**.
     - Asegúrese de que el valor de **Alias** o de **id. de recurso** coincide con el **alias** o el **id. de recurso** que va a usar para crear un punto de conexión privado a este servicio.

       ![Comprobación de la información de alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Comprobación de la información del identificador de recurso](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Revise el valor **Visibilidad** en la pestaña **Información general**.
     - Asegúrese de que la suscripción se encuentra en el ámbito **Visibilidad**.

       ![Comprobación de la información de visibilidad](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Revise el valor **Equilibrador de carga** en la pestaña **Información general**.
     - Para ir al equilibrador de carga, seleccione el vínculo del equilibrador de carga.

       ![Comprobación de la información del equilibrador de carga](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Asegúrese de que la configuración del equilibrador de carga esté configurada según sus expectativas.
       - Revise la **configuración IP de front-end**.
       - Revise los **grupos de back-end**.
       - Revise las **reglas de equilibrio de carga**.

       ![Comprobación de las propiedades del equilibrador de carga](./media/private-link-tsg/pls-ilb-properties.png)

     - Asegúrese de que el equilibrador de carga funciona según la configuración anterior.
       - Seleccione una máquina virtual en cualquier subred que no sea la subred en la que está disponible el grupo de back-end del equilibrador de carga.
       - Intente acceder al front-end del equilibrador de carga desde la máquina virtual anterior.
       - Si la conexión llega al grupo de back-end según las reglas de equilibrio de carga, significa que el equilibrador de carga está operativo.
       - También puede revisar la métrica del equilibrador de carga mediante Azure Monitor para ver si los datos fluyen a través de dicho equilibrador de carga.

1. Use [Azure Monitor](../azure-monitor/overview.md) para ver si los datos fluyen.

    a. En el recurso del servicio Private Link, seleccione **Métricas**.
     - Seleccione **Bytes de entrada** o **Bytes de salida**.
     - Vea si se transmiten los datos al intentar conectarse al servicio Private Link. Se considera normal un retraso de aproximadamente 10 minutos.

       ![Comprobación de las métricas del servicio Private Link](./media/private-link-tsg/pls-metrics.png)

1. Si desea obtener información y una vista de dependencia de los recursos, consulte [Azure Monitor: Redes](../azure-monitor/insights/network-insights-overview.md#dependency-view). Para ello, vaya a:
     - Azure Monitor
     - Redes
     - Servicios Private Link
     - Vista de dependencias 

![AzureMonitor](https://user-images.githubusercontent.com/20302679/135001735-56a9484b-f9b4-484b-a503-cfb9d20b264a.png)

![DependencyView](https://user-images.githubusercontent.com/20302679/135001741-8e848c52-d4bb-4646-b0d3-a85614ebe16c.png)

4. Póngase en contacto con el equipo de [soporte técnico de Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) si el problema sigue sin resolverse y el problema de conectividad persiste.

## <a name="next-steps"></a>Pasos siguientes

 * [Creación de un servicio Private Link (CLI)](./create-private-link-service-cli.md)
 * [Guía de solución de problemas de puntos de conexión privados de Azure](troubleshoot-private-endpoint-connectivity.md)
