---
title: Conexión de datos de Aruba ClearPass a Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector Aruba ClearPass para extraer los registros de Aruba ClearPass y exportarlos a Azure Sentinel. Vea los datos de Aruba ClearPass en libros, cree alertas y mejore la investigación.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 08cdd477eb6729550e168dd292219ab8138eee1b
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253939"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>Conexión de Aruba ClearPass a Azure Sentinel

> [!IMPORTANT]
> El conector Aruba ClearPass está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el dispositivo Aruba ClearPass con Azure Sentinel. El conector de datos Aruba ClearPass le permite conectar fácilmente los registros de Aruba ClearPass con Azure Sentinel, de modo que pueda verlos en libros, consultarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. La integración entre Aruba ClearPass y Azure Sentinel usa el servidor de Syslog con formato CEF, un reenviador de registros basado en Linux y el agente de Log Analytics. También usa un analizador de registro personalizado basado en una función de Kusto.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener permisos de lectura para las claves compartidas del área de trabajo. [Obtenga más información sobre las claves del área de trabajo](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>Envío de registros de Aruba ClearPass a Azure Sentinel

Para exportar los registros a Azure Sentinel, configure el dispositivo de Aruba ClearPass para enviar los mensajes de Syslog en formato CEF al servidor de reenvío de registros basado en Linux (que ejecuta rsyslog o syslog-ng). Este servidor tendrá instalado el agente de Log Analytics y este reenviará los registros a su área de trabajo de Azure Sentinel.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione **Aruba ClearPass (Preview)** y, luego, **Open connector page** (Abrir página del conector).

1. Siga las instrucciones que aparecen en la pestaña **Instrucciones**, en **Configuración**:

    1. En **1. Configuración del agente de Syslog para Linux**. Complete este paso si todavía no ejecuta un reenviador de registros o si necesita otro. Consulte [PASO 1: Implementación del reenviador de registros](connect-cef-agent.md) de la documentación de Azure Sentinel para una explicación e instrucciones más detalladas.

    1. En **2. Reenvíe los registros de Aruba ClearPass a un agente de Syslog**, siga las instrucciones de Aruba para [configurar ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). Esta configuración debe incluir los elementos siguientes:
        - Destino de los registros: el nombre de host o la dirección IP del servidor de reenvío de registros
        - Protocolo y puerto: TCP 514 (si se recomienda otro, asegúrese de realizar el cambio adecuado en el demonio de Syslog en el servidor de reenvío de registros)
        - Formato de registro: CEF
        - Tipos de registro: todos los disponibles o todos los adecuados

    1. En **3. Validación de la conexión**. Para comprobar la ingesta de datos, copie el comando en la página de conectores y ejecútelo en el reenviador de registros. Consulte [PASO 3: Validar conectividad](connect-cef-verify.md) de la documentación de Azure Sentinel para una explicación e instrucciones más detalladas.

        Hasta que los registros empiecen a aparecer en Log Analytics, pueden transcurrir hasta 20 minutos.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, debajo de la sección **Azure Sentinel** en la tabla *CommonSecurityLog*.

Este conector de datos depende de un analizador basado en una función de Kusto para funcionar según lo previsto. [Siga estos pasos](https://aka.ms/sentinel-arubaclearpass-parser) para crear el alias de la función **ArubaClearPass** de Kusto.

A continuación, para consultar los datos de Aruba ClearPass en Log Analytics, escriba `ArubaClearPass` en la parte superior de la ventana de consulta.

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver algunos ejemplos de consulta útiles.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Aruba ClearPass con Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.