---
title: 'Solución de problemas: firewall de aplicaciones web de Azure'
description: En este artículo se proporciona información de solución de problemas para el firewall de aplicaciones web de Azure Application Gateway.
services: web-application-firewall
author: KumudD
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: kumud
ms.topic: conceptual
ms.openlocfilehash: 9db2e6a3c0ee90a81b4f6cb363c3513314c18944
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550995"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Solución de problemas del firewall de aplicaciones web (WAF) de Azure Application Gateway

Hay algunas cosas que puede hacer si se bloquean las solicitudes que se deben pasar a través del firewall de aplicaciones web (WAF).

En primer lugar, asegúrese de que ha leído los documentos [Introducción a WAF](ag-overview.md) y [Configuración de WAF](application-gateway-waf-configuration.md). Además, asegúrese de que ha habilitado la [supervisión de WAF](../../application-gateway/application-gateway-diagnostics.md). En estos artículos se explica cómo funcionan el firewall de aplicaciones web y los conjuntos de reglas de WAF, y cómo acceder a los registros del firewall de aplicaciones web.

Los conjuntos de reglas OWASP están diseñados para ser muy estrictos de forma predefinida y para ajustarse según las necesidades específicas de la aplicación o la organización que usa WAF. Es completamente normal y, en realidad, se espera en muchos casos que se creen exclusiones, reglas personalizadas e incluso se deshabiliten reglas que puedan estar causando problemas o falsos positivos. Las directivas por sitio y por URI permiten que estos cambios solo afecten a direcciones URI o sitios específicos, por lo que los cambios no deberían afectar a otros sitios que posiblemente no experimentan los mismos problemas. 

## <a name="understanding-waf-logs"></a>Descripción de los registros de WAF

El propósito de los registros de WAF es mostrar todas las solicitudes que coinciden con el firewall de aplicaciones web o que ha bloqueado. Es un libro de contabilidad de todas las solicitudes evaluadas que coinciden o se han bloqueado. Si observa que el firewall de aplicaciones web bloquea una solicitud que no debería (un falso positivo), puede hacer algunas cosas. En primer lugar, restrinja y busque la solicitud específica. Examine los registros para buscar el URI, marca de tiempo o identificador de transacción específico de la solicitud. Cuando encuentre las entradas del registro asociadas, puede empezar a actuar sobre los falsos positivos.

Por ejemplo, imagine que tiene un tráfico legítimo que contiene la cadena *1=1* que quiere pasar a través del WAF. Si intenta realizar la solicitud, el firewall de aplicaciones web bloquea el tráfico que contenga la cadena *1=1* en cualquier campo o parámetro. Esta cadena se suele asociar a un ataque por inyección de código SQL. Puede examinar los registros y ver la marca de tiempo de la solicitud y las reglas que se han bloqueado o que coinciden.

En el ejemplo siguiente, puede ver que se han desencadenado cuatro reglas durante la misma solicitud (mediante el campo TransactionId). La primera dice que coincide porque el usuario usa una dirección URL de IP o numérica para la solicitud, lo que aumenta la puntuación de anomalía en tres, ya que es una advertencia. La regla siguiente que ha coincidido es 942130, que es la que busca. Puede ver *1=1* en el campo `details.data`. Esto aumenta todavía más la puntuación de anomalía por tres de nuevo, ya que también es una advertencia. Por lo general, cada regla que tenga la acción **Matched** aumenta la puntuación de la anomalía y, en este momento, sería de seis. Para más información, vea [Modo de puntuación de anomalías](ag-overview.md#anomaly-scoring-mode).

En las dos últimas entradas del registro se muestra que la solicitud se ha bloqueado porque la puntuación de anomalía era lo suficientemente alta. Estas entradas tienen una acción diferente a las otras dos. Muestran que realmente *han bloqueado* la solicitud. Estas reglas son obligatorias y no se pueden deshabilitar. No se deberían considerar reglas, sino más bien infraestructura básica de los aspectos internos del firewall de aplicaciones web.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Corrección de falsos positivos

Con esta información y sabiendo que la regla 942130 es la que coincide con la cadena *1=1*, puede hacer algunas cosas para evitar que esto bloquee el tráfico:

- Uso de una lista de exclusión

   Vea [Configuración de WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) para obtener más información sobre las listas de exclusión.
- Deshabilite la regla.

### <a name="using-an-exclusion-list"></a>Uso de una lista de exclusión

Para tomar una decisión informada sobre el control de un falso positivo, es importante familiarizarse con las tecnologías que usa la aplicación. Por ejemplo, imagine que en la pila de tecnología no hay un servidor SQL y que recibe falsos positivos relacionados con esas reglas. La deshabilitación de esas reglas no debilita necesariamente la seguridad.

Una ventaja de usar una lista de exclusión es que solo se deshabilita una parte específica de una solicitud. Pero esto significa que una exclusión específica es aplicable a todo el tráfico que pasa a través del firewall de aplicaciones web porque es una configuración global. Por ejemplo, esto podría provocar un problema si *1=1* es una solicitud válida en el cuerpo de una aplicación concreta, pero no para otras. Otra ventaja es que puede elegir entre la exclusión de las cookies, los encabezados y el cuerpo si se cumple una condición concreta, frente a la exclusión de toda la solicitud.

En ocasiones, hay casos en los que parámetros específicos se pasan al firewall de aplicaciones web de una forma que puede no ser intuitiva. Por ejemplo, hay un token que se pasa al autenticarse con Azure Active Directory. Este token, *__RequestVerificationToken*, normalmente se pasa como una cookie de solicitud. Pero en algunos casos en los que las cookies están deshabilitadas, este token también se pasa como un atributo de solicitud o "arg". En ese caso, tendrá que asegurarse de que *__RequestVerificationToken* también se agregue a la lista de exclusión como un **nombre de atributo de solicitud**.

![Exclusiones](../media/web-application-firewall-troubleshoot/exclusion-list.png)

En este ejemplo, quiere excluir el **nombre de atributo de solicitud** que es igual a *text1*. Esto es evidente, ya que puede ver el nombre del atributo en los registros del firewall: **data: Matched Data: 1=1 found within ARGS:text1: 1=1**. El atributo es **text1**. También puede encontrar este nombre de atributo de otras formas; vea [Búsqueda de nombres de atributo de solicitud](#finding-request-attribute-names).

![Listas de exclusión del WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Deshabilitación de reglas

Otra forma de sortear un falso positivo consiste en deshabilitar la regla que coincide con la entrada que el firewall de aplicaciones web ha considerado malintencionada. Como ha analizado los registros de WAF y la regla se ha restringido a 942130, puede deshabilitarla en Azure Portal. Vea [Personalización de reglas de firewall de aplicaciones web mediante Azure Portal](application-gateway-customize-waf-rules-portal.md).

Una ventaja de deshabilitar una regla es que, si sabe que todo el tráfico que contiene una determinada condición que normalmente se va a bloquear es tráfico válido, puede deshabilitar esa regla en la totalidad del firewall de aplicaciones web. Pero si solo es tráfico válido en un caso de uso específico, al deshabilitar esa regla para la totalidad del firewall de aplicaciones web se abre una vulnerabilidad, ya que es una configuración global.

Si quiere usar Azure PowerShell, vea [Personalización de reglas de firewall de aplicaciones web mediante PowerShell](application-gateway-customize-waf-rules-powershell.md). Si quiere usar la CLI de Azure, vea [Personalización de reglas de firewall de aplicaciones web a través de la CLI de Azure](application-gateway-customize-waf-rules-cli.md).

![Reglas de WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Búsqueda de nombres de atributo de solicitud

Con la ayuda de [Fiddler](https://www.telerik.com/fiddler), puede inspeccionar solicitudes individuales y determinar a qué campos específicos de una página web se llama. Esto puede ayudar a excluir determinados campos de la inspección mediante listas de exclusión.

En este ejemplo, puede ver que el campo donde se ha escrito la cadena *1=1* se denomina **text1**.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="Captura de pantalla de Progress Telerik Fiddler Web Debugger. En la pestaña Raw, 1 = 1 es visible después del nombre text1." border="false":::

Es un campo que se puede excluir. Para más información sobre las lista de exclusión, vea [Listas de exclusión y límites de tamaño de solicitud del firewall de aplicaciones web](application-gateway-waf-configuration.md#waf-exclusion-lists). En este caso puede excluir la evaluación mediante la configuración de la exclusión siguiente:

![Exclusión de WAF](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

También puede examinar los registros del firewall para obtener la información y ver lo que tiene que agregar a la lista de exclusión. Para habilitar el registro, consulte [Mantenimiento del back-end, registros de recursos y métricas de Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Examine el registro del firewall y vea el archivo PT1H.json para la hora a la que se haya producido la solicitud que quiere inspeccionar.

En este ejemplo, puede ver que tiene cuatro reglas con el mismo valor TransactionID y que todas se han producido a la misma hora exacta:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Con sus conocimientos sobre cómo funciona la regla de CRS y que el conjunto de reglas de CRS 3.0 funciona con un sistema de puntuación de anomalías (consulte [Firewall de aplicaciones web para Azure Application Gateway](ag-overview.md)) sabe que las dos reglas inferiores con la acción de **: Blocked** se bloquean según la puntuación de anomalías total. Las reglas en las que se debe centrar son las dos primeras.

La primera entrada se registra porque el usuario ha utilizado una dirección IP numérica para navegar a la instancia de Application Gateway, lo que en este caso se puede ignorar.

La segunda (la regla 942130) es la interesante. En los detalles puede ver que ha coincidido con un patrón (1=1) y que el campo se denomina **text1**. Siga los mismos pasos anteriores para excluir el **nombre de atributo de solicitud** que es **igual** a **1=1**.

## <a name="finding-request-header-names"></a>Búsqueda de nombres de encabezado de solicitud

Fiddler vuelve a ser una herramienta útil para buscar nombres de encabezado de solicitud. En la captura de pantalla siguiente puede ver los encabezados para esta solicitud GET, que incluyen *Content-Type*, *User-Agent*, etc.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="Captura de pantalla de Progress Telerik Fiddler Web Debugger. En la pestaña Raw se muestran detalles del encabezado de solicitud como la conexión, el tipo de contenido y el agente del usuario." border="false":::

Otra manera de ver los encabezados de solicitud y respuesta consiste en buscar dentro de las herramientas de desarrollo de Chrome. Puede presionar F12 o hacer clic con el botón derecho -> **Inspeccionar** -> **Herramientas de desarrollo** y seleccionar la pestaña **Network** (Red). Cargue una página web y haga clic en la solicitud que quiera inspeccionar.

![F12 en Chrome](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Búsqueda de nombres de cookies de solicitud

Si la solicitud contiene cookies, se puede seleccionar la pestaña **Cookies** para verlas en Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Restricción de parámetros globales para eliminar los falsos positivos

- Deshabilitación de la inspección del cuerpo de solicitud

   Al establecer **Inspeccionar el cuerpo de la solicitud** en desactivado, el firewall de aplicaciones web no evaluará los cuerpos de solicitud de todo el tráfico. Esto puede ser útil si sabe que los cuerpos de solicitud no son malintencionados para la aplicación.

   Si deshabilita esta opción, lo único que no se inspecciona es el cuerpo de la solicitud. Se seguirán inspeccionando los encabezados y las cookies, a menos que se excluyan instancias individuales mediante la funcionalidad de lista de exclusión.

- Límites de tamaño de archivo

   Al limitar el tamaño de archivo del firewall de aplicaciones web, se limita la posibilidad de que los servidores web sufran un ataque. Al permitir la carga de archivos grandes, aumenta el riesgo de que el back-end se vea desbordado. Limitar el tamaño de archivo a un caso de uso normal para la aplicación es simplemente otra manera de evitar los ataques.

   > [!NOTE]
   > Si sabe que en la aplicación nunca va a ser necesario cargar archivos que superen un tamaño determinado, puede establecer un límite para restringirlo.

## <a name="firewall-metrics-waf_v1-only"></a>Métricas de Firewall (solo WAF_v1)

En el caso de los Firewalls de aplicaciones web v1, ahora están disponibles las siguientes métricas en el portal: 

1. Recuento de solicitudes bloqueados del Firewall de aplicaciones Web número de solicitudes bloqueadas
2. Recuento de reglas bloqueadas de Firewall de aplicaciones web Todas las reglas que coincidieron **y** se bloqueo la solicitud
3. Distribución total de reglas de Firewall de aplicaciones web Todas las reglas que coincidieron durante la evaluación
     
Para habilitar las métricas, seleccione la pestaña **Métricas** en el portal y seleccione una de las tres métricas.

## <a name="next-steps"></a>Pasos siguientes

Consulte los [procedimientos para configurar el firewall de aplicaciones web en Application Gateway](tutorial-restrict-web-traffic-powershell.md).
