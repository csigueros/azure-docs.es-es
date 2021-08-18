---
title: Conexión del recopilador Akamai Security Events a Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector Akamai Security Events para extraer los registros de seguridad de las soluciones de Akamai en Azure Sentinel. Vea los datos de Akamai en libros, cree alertas y mejore la investigación.
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
ms.openlocfilehash: df17c8abca5333f23c26ab63479f398ba766aed6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253936"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Conexión del recopilador Akamai Security Events a Azure Sentinel

> [!IMPORTANT]
> El conector Akamai Security Events está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el recopilador Akamai Security Events a Azure Sentinel. El conector de datos Akamai Security Events le permite conectar fácilmente los registros de Akamai con Azure Sentinel, de modo que pueda verlos en libros, consultarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. La integración entre el recopilador Akamai Security Events y Azure Sentinel hace uso de syslog con formato CEF, un reenviador de registros basado en Linux, y del agente de Log Analytics. También se usa un analizador de registros personalizado basado en una función de Kusto.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener permisos de lectura para las claves compartidas del área de trabajo. [Obtenga más información sobre las claves del área de trabajo](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Envío de registros de Akamai Security Events a Azure Sentinel

Para exportar los registros a Azure Sentinel, configure el recopilador Akamai Security Events para que envíe mensajes de Syslog en formato CEF al servidor de reenvío de registros basado en Linux (que ejecuta rsyslog o syslog-ng). Este servidor tendrá instalado el agente de Log Analytics y este reenviará los registros a su área de trabajo de Azure Sentinel.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Data connectors** (Conectores de datos), seleccione **Akamai Security Events (Preview)** (Akamai Security Events [versión preliminar]) y, luego, **Open connector page** (Abrir página del conector).

1. Siga las instrucciones que aparecen en la pestaña **Instrucciones**, en **Configuración**:

    1. En **1. Configuración del agente de Syslog para Linux**. Complete este paso si todavía no ejecuta un reenviador de registros o si necesita otro. Consulte [PASO 1: Implementación del reenviador de registros](connect-cef-agent.md) en la documentación de Azure Sentinel para información sobre el ajuste de tamaño, así como instrucciones y explicaciones más detalladas.

    1. En **2. Reenviar registros del formato de evento común (CEF) al agente de Syslog**, siga las instrucciones de Akamai para [configurar la integración de SIEM](https://developer.akamai.com/tools/integrations/siem) y para [instalar un conector de CEF](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). Este conector recibe eventos de seguridad de las soluciones de Akamai casi en tiempo real mediante SIEM OPEN API y los pasa de JSON al formato CEF.
    
        Esta configuración debe incluir los elementos siguientes:
    
        - Destino de los registros: el nombre de host o la dirección IP del servidor de reenvío de registros
        - Protocolo y puerto: TCP 514 (si se recomienda otro, asegúrese de realizar el cambio adecuado en el demonio de Syslog en el servidor de reenvío de registros)
        - Formato de registro: CEF
        - Tipos de registro: todos los disponibles

    1. En **3. Validación de la conexión**. Para comprobar la ingesta de datos, copie el comando en la página de conectores y ejecútelo en el reenviador de registros. Consulte [PASO 3: Validar conectividad](connect-cef-verify.md) de la documentación de Azure Sentinel para una explicación e instrucciones más detalladas.

        Hasta que los registros empiecen a aparecer en Log Analytics, pueden transcurrir hasta 20 minutos.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, debajo de la sección **Azure Sentinel** en la tabla *CommonSecurityLog*.

Este conector de datos depende de un analizador basado en una función de Kusto para funcionar según lo previsto. Utilice los pasos siguientes para configurar la función de Kusto **AkamaiSIEMEvent** para usar en consultas y libros.

1. En el menú de navegación de Azure Sentinel, seleccione **Registros**.

1. Copie la consulta siguiente y péguela en la ventana de consulta.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. Haga clic en el menú desplegable **Guardar** y haga clic en **Guardar**. En el panel **Guardar**:

    1. En **Nombre**, escriba **AkamaiSIEMEvent**.

    1. En **Guardar como**, elija **Función**.

    1. En **Alias de función**, escriba **AkamaiSIEMEvent**.

    1. En **Categoría**, escriba **Funciones**.

    1. Haga clic en **Save**(Guardar).

    Las aplicaciones de funciones normalmente tardan entre 10 y 15 minutos en activarse.

Ahora está listo para consultar datos de Akamai; para ello, escriba `AkamaiSIEMEvent` en la línea superior de la ventana de consulta.

Consulte la pestaña **Pasos siguientes** de la página de conectores para más ejemplos de consulta.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Akamai Security Events a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.