---
title: Conexión de TippingPoint de Trend Micro con Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de datos de TippingPoint de Trend Micro para extraer registros de SMS de TippingPoint y exportarlos a Azure Sentinel. Vea los datos de TippingPoint en los libros, cree alertas y mejore la investigación.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 9305ea9263c78886ca49ef7254813d225be0c882
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693757"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>Conexión de la solución TippingPoint de Trend Micro con Azure Sentinel

> [!IMPORTANT]
> El conector de TippingPoint de Trend Micro está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar la solución de sistema de protección contra amenazas de TippingPoint de Trend Micro con Azure Sentinel. El conector de datos de TippingPoint de Trend Micro le permite conectar fácilmente los datos de registros del sistema de administración de seguridad (SMS) de TippingPoint con Azure Sentinel, de modo que pueda verlos en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener permisos de lectura para las claves compartidas del área de trabajo.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Envío de registros de TippingPoint de Trend Micro a Azure Sentinel

Para exportar los registros a Azure Sentinel, configure la solución TPS de TippingPoint para enviar los mensajes de Syslog en formato CEF al servidor de reenvío de registros basado en Linux (que ejecuta rsyslog o syslog-ng). Este servidor tendrá instalado el agente de Log Analytics y este reenviará los registros a su área de trabajo de Azure Sentinel. El conector utiliza una función de analizador para convertir los datos que recibe en un esquema normalizado. 

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione **TippingPoint de Trend Micro (versión preliminar)** y, a continuación, seleccione la página **Abrir conector**.

1. Siga las instrucciones que aparecen en la pestaña **Instrucciones**, en **Configuración**:

    1. En **1. Configuración del agente de Syslog para Linux**. Complete este paso si todavía no ejecuta un reenviador de registros o si necesita otro. Consulte [PASO 1: Implementación del reenviador de registros](connect-cef-agent.md) de la documentación de Azure Sentinel para una explicación e instrucciones más detalladas.

    1. En **2. Reenvíe registros de TippingPoint SMS de Trend Micro a un agente de Syslog**. Esta configuración debe incluir los elementos siguientes:
        - Destino de los registros: el nombre de host o la dirección IP del servidor de reenvío de registros
        - Protocolo y puerto: **TCP 514** (si se recomienda otro, asegúrese de realizar el cambio adecuado en el demonio de Syslog en el servidor de reenvío de registros)
        - Formato de registro: **formato ArcSight CEF, versión 4.2**
        - Tipos de registro: todos los disponibles

    1. En **3. Validación de la conexión**. Para comprobar la ingesta de datos, copie el comando en la página de conectores y ejecútelo en el reenviador de registros. Consulte [Validación de la conectividad de CEF](troubleshooting-cef-syslog.md#validate-cef-connectivity) en la documentación de Azure Sentinel para una explicación e instrucciones más detalladas.

        Hasta que los registros empiecen a aparecer en Log Analytics, pueden transcurrir hasta 20 minutos.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, debajo de la sección **Azure Sentinel** en la tabla *CommonSecurityLog*.

Para obtener datos de Trend Micro TippingPoint en Log Analytics, debe consultar la función de analizador en lugar de la tabla. Copie lo siguiente en la ventana de consulta y aplique los filtros que considere necesarios:

```kusto
TrendMicroTippingPoint
| sort by TimeGenerated
```

Consulte la pestaña **Pasos siguientes** de la página de conectores para más ejemplos de consulta.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar TippingPoint de Trend Micro a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.