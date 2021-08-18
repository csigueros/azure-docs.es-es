---
title: Validación de la conectividad a Azure Sentinel | Microsoft Docs
description: Valide la conectividad de la solución de seguridad para asegurarse de que los mensajes de CEF se reenvían a Azure Sentinel.
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
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: 98d97bd7c8ffab685475f50130d68668fe1c9f8b
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253290"
---
# <a name="step-3-validate-connectivity"></a>PASO 3: Validar conectividad

Una vez que haya implementado el reenviador de registros (en el paso 1) y configurado la solución de seguridad para enviar mensajes de CEF (en el paso 2), siga estas instrucciones para comprobar la conectividad entre la solución de seguridad y Azure Sentinel.

## <a name="prerequisites"></a>Prerrequisitos

- Debe tener permisos elevados (sudo) en la máquina del reenviador de registros.

- Debe tener instalado **Python 2.7** o **3** en la máquina de reenvío de registros.<br>
Use el comando `python –version` para comprobarlo.

- En algún momento del proceso, es posible que necesite el id. del área de trabajo y la clave principal del área de trabajo. Puede encontrarlos en el recurso del área de trabajo, en **Administración de agentes**.

## <a name="how-to-validate-connectivity"></a>Procedimientos para validar la conectividad

1. En el menú de navegación de Azure Sentinel, abra **Registros**. Ejecute una consulta con el esquema **CommonSecurityLog** para ver si recibe registros de la solución de seguridad.<br>
Tenga en cuenta que pueden transcurrir unos 20 minutos hasta que los registros empiecen a aparecer en **Log Analytics**.

1. Si no ve ningún resultado de la consulta, compruebe que se generan eventos en la solución de seguridad o intente generar algunos, y compruebe que se reenvían a la máquina del reenviador de Syslog que ha designado.

1. Ejecute el siguiente script en el reenviador de registros (aplicando el id. del área de trabajo en lugar del marcador de posición) para comprobar la conectividad entre la solución de seguridad, el reenviador de registros y Azure Sentinel. Este script comprueba que el demonio escucha en los puertos correctos, que el reenvío del demonio está configurado correctamente y que nada bloquee la comunicación entre el demonio y el agente de Log Analytics. También envía mensajes ficticios "TestCommonEventFormat" para comprobar la conectividad de un extremo a otro. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - Es posible que reciba un mensaje que le indique que debe ejecutar un comando para corregir un problema con la **asignación del campo *Equipo***. Consulte la [explicación en el script de validación](#mapping-command) para obtener más detalles.

    - Es posible que reciba un mensaje que le indique que debe ejecutar un comando para corregir un problema con el **análisis de los registros de firewall de Cisco ASA**. Consulte la [explicación en el script de validación](#parsing-command) para obtener más detalles.

## <a name="validation-script-explained"></a>Explicación del script de validación

El script de validación realiza las siguientes comprobaciones:

# <a name="rsyslog-daemon"></a>[demonio rsyslog](#tab/rsyslog)

1. Comprueba que el archivo<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe y es válido.

1. Comprueba que el archivo incluye el texto siguiente:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Comprueba que el análisis de los eventos del firewall de Cisco ASA esté configurado según lo previsto, con el siguiente comando:

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Si hay un problema con el análisis, el script generará un mensaje de error que le indicará que debe **ejecutar manualmente el siguiente comando** (debe agregar el id. del área de trabajo en lugar del marcador de posición). El comando garantizará que el análisis se realice correctamente y reiniciará el agente.

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Comprueba que el campo *Equipo* del origen de syslog se haya asignado correctamente en el agente de Log Analytics, mediante el siguiente comando:

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Si hay un problema con la asignación, el script generará un mensaje de error que le indicará que debe **ejecutar manualmente el siguiente comando** (debe agregar el id. del área de trabajo en lugar del marcador de posición). El comando garantizará que la asignación se realice correctamente y reiniciará el agente.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Comprueba si hay mejoras de seguridad en el equipo que podrían estar bloqueando el tráfico de red (por ejemplo, un firewall de host).

1. Comprueba que el demonio de syslog (rsyslog) esté configurado correctamente para enviar mensajes (que identifica como CEF) al agente de Log Analytics en el puerto TCP 25226:

    - Archivo de configuración: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
        ```

1. Reinicia el demonio de syslog y el agente de Log Analytics:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Comprueba que se han establecido las conexiones necesarias: TCP 514 para recibir datos, TCP 25226 para la comunicación interna entre el demonio de syslog y el agente de Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Comprueba que el demonio de syslog recibe datos en el puerto 514 y que el agente recibe datos en el puerto 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Envía datos ficticios al puerto 514 en localhost. Estos datos deben poderse observar en el área de trabajo de Azure Sentinel ejecutando la siguiente consulta:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[demonio syslog-ng](#tab/syslogng)

1. Comprueba que el archivo<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe y es válido.

1. Comprueba que el archivo incluye el texto siguiente:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Comprueba que el análisis de los eventos del firewall de Cisco ASA esté configurado según lo previsto, con el siguiente comando:

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Si hay un problema con el análisis, el script generará un mensaje de error que le indicará que debe **ejecutar manualmente el siguiente comando** (debe agregar el id. del área de trabajo en lugar del marcador de posición). El comando garantizará que el análisis se realice correctamente y reiniciará el agente.

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Comprueba que el campo *Equipo* del origen de syslog se haya asignado correctamente en el agente de Log Analytics, mediante el siguiente comando:

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Si hay un problema con la asignación, el script generará un mensaje de error que le indicará que debe **ejecutar manualmente el siguiente comando** (debe agregar el id. del área de trabajo en lugar del marcador de posición). El comando garantizará que la asignación se realice correctamente y reiniciará el agente.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Comprueba si hay mejoras de seguridad en el equipo que podrían estar bloqueando el tráfico de red (por ejemplo, un firewall de host).

1. Comprueba que el demonio de syslog (syslog-ng) esté configurado correctamente para enviar mensajes que identifica como CEF (mediante regex) al agente de Log Analytics en el puerto TCP 25226:

    - Archivo de configuración: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Reinicia el demonio de syslog y el agente de Log Analytics:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Comprueba que se han establecido las conexiones necesarias: TCP 514 para recibir datos, TCP 25226 para la comunicación interna entre el demonio de syslog y el agente de Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Comprueba que el demonio de syslog recibe datos en el puerto 514 y que el agente recibe datos en el puerto 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Envía datos ficticios al puerto 514 en localhost. Estos datos deben poderse observar en el área de trabajo de Azure Sentinel ejecutando la siguiente consulta:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---


## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar dispositivos CEF a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Obtenga información sobre la [asignación de campos de CEF y CommonSecurityLog](cef-name-mapping.md).
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.