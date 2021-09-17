---
title: Conexión del dispositivo Imperva WAF Gateway a Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de Imperva WAF Gateway para extraer registros de Imperva WAF en Azure Sentinel. Vea los datos de Imperva WAF en los libros, cree alertas y mejore la investigación.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: fea80f26a914fac6ff55fcaa2bea9447aaa490fb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694012"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Conexión del dispositivo Imperva WAF Gateway a Azure Sentinel

> [!IMPORTANT]
> El conector de Imperva WAF Gateway está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el dispositivo Imperva WAF Gateway a Azure Sentinel. El conector de datos de Imperva WAF Gateway le permite conectar fácilmente los registros de Imperva WAF Gateway con Azure Sentinel, de modo que pueda verlos en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. La integración entre Imperva WAF Gateway y Azure Sentinel usa el servidor de Syslog con formato CEF, un reenviador de registros basado en Linux y el agente de Log Analytics.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener permisos de lectura para las claves compartidas del área de trabajo. [Obtenga más información sobre las claves del área de trabajo](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Envío de registros de Imperva WAF Gateway a Azure Sentinel

Para exportar los registros a Azure Sentinel, configure el dispositivo Imperva WAF Gateway para enviar los mensajes de Syslog en formato CEF al servidor de reenvío de registros basado en Linux (que ejecuta rsyslog o syslog-ng). Este servidor tendrá instalado el agente de Log Analytics y este reenviará los registros a su área de trabajo de Azure Sentinel.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione **Imperva WAF Gateway (versión preliminar)** y, a continuación, seleccione la **página Abrir conector**.

1. Siga las instrucciones que aparecen en la pestaña **Instrucciones**, en **Configuración**:

    1. En **1. Configuración del agente de Syslog para Linux**. Complete este paso si todavía no ejecuta un reenviador de registros o si necesita otro. Consulte [PASO 1: Implementación del reenviador de registros](connect-cef-agent.md) de la documentación de Azure Sentinel para una explicación e instrucciones más detalladas.

    1. En **2. Reenvío de registros de formato de evento común (CEF) al agente de Syslog**: este conector requiere que se creen una **interfaz de acción** y un **conjunto de acciones** en la consola de administración de **Imperva SecureSphere MX**. Siga las instrucciones de Imperva para [habilitar el registro de alertas de Imperva WAF Gateway en Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert). Esta configuración debe incluir los elementos siguientes:
        - Destino de los registros: el nombre de host o la dirección IP del servidor de reenvío de registros
        - Protocolo y puerto: TCP 514
        - Formato de registro: CEF
        - Tipos de registro: todos los disponibles

    1. En **3. Validación de la conexión**. Para comprobar la ingesta de datos, copie el comando en la página de conectores y ejecútelo en el reenviador de registros. Consulte [Validación de la conectividad de CEF](troubleshooting-cef-syslog.md#validate-cef-connectivity) en la documentación de Azure Sentinel para obtener una explicación e instrucciones más detalladas.

        Hasta que los registros empiecen a aparecer en Log Analytics, pueden transcurrir hasta 20 minutos.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, debajo de la sección **Azure Sentinel** en la tabla *CommonSecurityLog*.

Para consultar los datos de Imperva WAF Gateway en Log Analytics, copie lo siguiente en la ventana de consulta y aplique otros filtros de su elección:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver más ejemplos de consulta útiles.

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar Imperva WAF Gateway a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.