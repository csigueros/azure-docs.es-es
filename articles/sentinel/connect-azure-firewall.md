---
title: Conexión de datos de Azure Firewall a Azure Sentinel
description: Aprenda a conectar datos de Azure Firewall a Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: f4683708f8fdd4eda2f483cc4112fe9fe9939ce6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780907"
---
# <a name="connect-data-from-azure-firewall"></a>Conexión de datos de Azure Firewall

Azure Firewall es un servicio de seguridad de red administrado y basado en la nube que protege los recursos de Azure Virtual Network. Se trata de un firewall como servicio con estado completo que incorpora alta disponibilidad y escalabilidad a la nube sin restricciones. 

Los registros de Azure Firewall se pueden conectar a Azure Sentinel, de forma que es posible ver los datos de registro en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar su investigación.

Más información sobre la [supervisión de los registros de Azure Firewall](../firewall/firewall-diagnostics.md).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

## <a name="connect-to-azure-firewall"></a>Conexión a Azure Firewall
    
1. En el menú de navegación de Azure Sentinel, seleccione **Data connectors** (Conectores de datos).

1. Seleccione **Azure Firewall** en la galería de conectores de datos y, luego, seleccione **Open Connector Page** (Abrir página del conector) en el panel de vista previa.

1. Habilite **Registros de diagnóstico** en todos los firewalls cuyos registros quiere conectar:

    1. Seleccione el vínculo **Open Azure Firewall resource >** (Abrir recurso de Azure Firewall).

    1. En el menú de navegación **Firewalls**, seleccione **Configuración de diagnóstico**.

    1. Seleccione **+ Agregar configuración de diagnóstico**.

    1. En la pantalla **Configuración de diagnóstico**, escriba un nombre en el campo **Nombre de la configuración de diagnóstico**.
    
    1. Marque la casilla **Enviar a Log Analytics**. Se mostrarán dos nuevos campos debajo. Elija la **suscripción** y el **área de trabajo de Log Analytics** pertinentes (donde reside Azure Sentinel).

    1. Marque las casillas de los tipos de regla cuyos registros quiere ingerir. Se recomiendan **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule** y **AzureFirewallDNSProxy**.

    1. En la parte superior de la pantalla, seleccione **Guardar**.

1. Para usar el esquema correspondiente de Log Analytics para las alertas de Azure Firewall, busque **AzureDiagnostics**.

> [!NOTE]
>
> Con este conector de datos concreto, los indicadores de estado de conectividad (una franja de color en la galería de conectores de datos y los iconos de conexión situados junto a los nombres de tipo de datos) se mostrarán como *Conectado* (verde) solo si los datos se han ingerido en algún momento en las dos últimas semanas. Transcurridas dos semanas sin ingesta de datos, el conector se mostrará como desconectado. En el momento en que circulen más datos, se devolverá el estado *conectado*.

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar registros de Azure Firewall a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
