---
title: Conexión de datos de Azure DDoS Protection a Azure Sentinel
description: Aprenda a ingerir datos de Azure DDoS Protection en Azure Sentinel para que pueda verlos, analizarlos e investigarlos.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 999555624ad4e88058a575dce3fb9cd606228b18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780374"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Conexión de datos desde Azure DDoS Protection

Los ataques de denegación de servicio distribuido (DDoS) intentan agotar los recursos de una aplicación haciendo que esta no esté disponible para los usuarios legítimos. Los ataques DDoS pueden ir dirigidos a cualquier punto de conexión que sea públicamente accesible a través de Internet. [Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md), junto con los procedimientos recomendados de diseño de aplicaciones, constituyen una defensa sólida frente a los ataques DDoS. Los registros de Azure DDoS Protection se pueden conectar a Azure Sentinel, de forma que es posible ver los datos de registro en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar sus investigaciones.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener configurado un [plan de protección de Azure DDoS Standard](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan).

- Debe tener configurada una [red virtual con Azure DDoS Standard habilitado](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Conexión a Azure DDoS Protection
    
1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. Seleccione **Azure DDoS Protection** en la galería de conectores de datos y, luego, seleccione **Abrir página del conector** en el panel de vista previa.

1. Habilite **Registros de diagnóstico** en todas las direcciones IP públicas cuyos registros desea conectar:

    1. Seleccione el vínculo **Abrir Configuración de diagnóstico >** y elija un recurso de **IP pública** de la lista.

    1. Seleccione **+ Agregar configuración de diagnóstico**.

    1. En la pantalla **Configuración de diagnóstico**:
       - Escriba un nombre en el campo **Nombre de la configuración de diagnóstico**.

       - Marque la casilla **Enviar a Log Analytics**. Se mostrarán dos nuevos campos debajo. Elija la **suscripción** y el **área de trabajo de Log Analytics** pertinentes (donde reside Azure Sentinel).

       - Marque las casillas de los tipos de regla cuyos registros quiere ingerir. Se recomienda **DDoSProtectionNotifications**, **DDoSMitigationFlowLogs** y **DDoSMitigationReports**.

    1. Haga clic en **Guardar** en la parte superior de la pantalla. Repita este proceso para todos los firewalls adicionales (IP públicas) para los que haya habilitado la protección contra DDoS.

1. Para usar el esquema correspondiente de Log Analytics para las alertas de Azure DDoS Protection, busque **AzureDiagnostics**.

> [!NOTE]
>
> Con este conector de datos concreto, los indicadores de estado de conectividad (una franja de color en la galería de conectores de datos y los iconos de conexión situados junto a los nombres de tipo de datos) se mostrarán como *Conectado* (verde) solo si los datos se han ingerido en algún momento en las dos últimas semanas. Transcurridas dos semanas sin ingesta de datos, el conector se mostrará como desconectado. En el momento en que circulen más datos, se devolverá el estado *conectado*.

## <a name="next-steps"></a>Pasos siguientes

En este documento aprendió a conectar los registros de Azure DDoS Protection a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
