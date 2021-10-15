---
title: Solución de problemas de conexión entre Azure Sentinel y un conector de datos CEF o Syslog| Microsoft Docs
description: Aprenda a solucionar problemas con el conector de datos CEF o Syslog de Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2021
ms.author: bagol
ms.openlocfilehash: 9f6585cec5c52dd2255fca2a31e2f92853954370
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279020"
---
# <a name="troubleshoot-your-cef-or-syslog-data-connector"></a>Solución de problemas del conector de datos CEF o Syslog

En este artículo se describen los métodos comunes para verificar y solucionar problemas de un conector de datos CEF o Syslog para Azure Sentinel.

Por ejemplo, si los registros no aparecen en Azure Sentinel, ya sea en las tablas de Syslog o Common Security Log, es posible que el origen de datos no se pueda conectar o que haya otro motivo por el que no se ingieren los datos.

Otros síntomas de una implementación de conector con errores incluyen cuando faltan los archivos **security_events.conf** o **security-omsagent.config.conf**, o bien si el servidor rsyslog no realiza la escucha en el puerto 514.

Para obtener más información, consulte [Conexión de la solución externa con el formato de evento común](connect-common-event-format.md) y [Colección de datos de orígenes basados en Linux con Syslog](connect-syslog.md).

> [!NOTE]
> El agente de Log Analytics para Windows a menudo se conoce como *Microsoft Monitoring Agent (MMA)* . El agente de Log Analytics para Linux se conoce a menudo como *agente de OMS*.
>

> [!TIP]
> Al solucionar problemas, le recomendamos que siga los pasos descritos de este artículo en el orden en que se presentan para comprobar y resolver problemas en el recopilador de Syslog, el sistema operativo o el agente de OMS.
>
> Si ha implementado el conector mediante un método diferente al procedimiento documentado y tiene problemas, le recomendamos purgar la implementación e instalarla de nuevo como se documenta.
>

## <a name="validate-cef-connectivity"></a>Validación de la conectividad CEF

Una vez que haya [implementado el reenviador de registros](connect-common-event-format.md) y [configurado la solución de seguridad para enviarle mensajes CEF](./connect-common-event-format.md), siga los pasos de esta sección para verificar la conectividad entre la solución de seguridad y Azure Sentinel.

1. Asegúrese de que cumple los siguientes requisitos previos:

    - Debe tener permisos elevados (sudo) en la máquina del reenviador de registros.

    - Debe tener instalado **Python 2.7** o **3** en la máquina de reenvío de registros. Use el comando `python –version` para comprobarlo.

    - En algún momento del proceso, es posible que necesite el id. del área de trabajo y la clave principal del área de trabajo. Puede encontrarlos en el recurso del área de trabajo, en **Administración de agentes**.

1. En el menú de navegación de Azure Sentinel, abra **Registros**. Ejecute una consulta con el esquema **CommonSecurityLog** para ver si recibe registros de la solución de seguridad.

    Los registros pueden tardar hasta 20 minutos en empezar a aparecer en **Log Analytics**.

1. Si no ve ningún resultado de la consulta, compruebe que se generan eventos en la solución de seguridad o intente generar algunos, y compruebe que se reenvían a la máquina del reenviador de Syslog que ha designado.

1. Ejecute el siguiente script en el reenviador de registros (aplicando el id. del área de trabajo en lugar del marcador de posición) para comprobar la conectividad entre la solución de seguridad, el reenviador de registros y Azure Sentinel. Este script comprueba que el demonio escucha en los puertos correctos, que el reenvío del demonio está configurado correctamente y que nada bloquee la comunicación entre el demonio y el agente de Log Analytics. También envía mensajes ficticios "TestCommonEventFormat" para comprobar la conectividad de un extremo a otro. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - Es posible que reciba un mensaje que le indique que debe ejecutar un comando para corregir un problema con la **asignación del campo *Equipo***. Consulte la [explicación en el script de validación](#mapping-command) para obtener más detalles.

    - Es posible que reciba un mensaje que le indique que debe ejecutar un comando para corregir un problema con el **análisis de los registros de firewall de Cisco ASA**. Consulte la [explicación en el script de validación](#parsing-command) para obtener más detalles.

### <a name="cef-validation-script-explained"></a>Descripción del script de validación de CEF

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

## <a name="verify-cef-or-syslog-prerequisites"></a>Verificación de los requisitos previos de CEF o Syslog

Use las secciones siguientes para comprobar los requisitos previos del conector de datos CEF o Syslog.

### <a name="azure-virtual-machine-as-a-syslog-collector"></a>Máquina virtual de Azure como recopilador de Syslog

Si usa una máquina virtual de Azure como recopilador de Syslog, verifique lo siguiente:

- Mientras configura el conector de datos de Syslog, asegúrese de desactivar la [configuración de aprovisionamiento automático de Azure Security Center](../security-center/security-center-enable-data-collection.md) para el [agente MMA/OMS](connect-windows-security-events.md#connector-options).

    Puede volver a activarla una vez que el conector de datos esté completamente configurado.

- Antes de implementar el [script de Python del conector de datos de formato de evento común](./connect-log-forwarder.md), asegúrese de que la máquina virtual no esté conectada a un área de trabajo de Syslog existente. Puede encontrar esta información en la lista de máquinas virtuales del área de trabajo de Log Analytics, en la que una máquina virtual conectada a un área de trabajo de Syslog se muestra como **Conectada**.

- Asegúrese de que Azure Sentinel está conectado al área de trabajo de Syslog correcta, con la solución **SecurityInsights** instalada.

    Para obtener más información, vea [Paso 1: Implementar el reenviador de registros](./connect-log-forwarder.md).

- Asegúrese de que el tamaño de la máquina sea correcto con al menos los requisitos previos mínimos necesarios. Para obtener más información, consulte la sección [Requisitos previos de CEF](connect-common-event-format.md#prerequisites).

### <a name="on-premises-or-a-non-azure-virtual-machine"></a>Una máquina virtual local o que no es de Azure

Si usa una máquina local o una máquina virtual que no es de Azure para el conector de datos, asegúrese de que ha ejecutado el script de instalación en una instalación nueva de un sistema operativo Linux compatible:

> [!TIP]
> También puede encontrar este script en la página del conector de datos de **formato de evento común** en Azure Sentinel.
>

```cli
sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py <WorkspaceId> <Primary Key>
```

### <a name="enable-your-syslog-facility-and-log-severity-collection"></a>Habilitación de la instalación de Syslog y la recopilación de gravedad del registro

El servidor de Syslog, rsyslog o syslog-ng, reenvía los datos definidos en el archivo de configuración correspondiente, que se rellena automáticamente con la configuración definida en el área de trabajo de Log Analytics.

Asegúrese de agregar detalles sobre las instalaciones y los niveles de registro de gravedad que quiere ingerir en Azure Sentinel. El proceso de configuración puede tardar unos 20 minutos en procesarse.

Para obtener más información, vea [Explicación del script de implementación](./connect-log-forwarder.md#deployment-script-explained) y [Configuración de Syslog en Azure Portal](../azure-monitor/agents/data-sources-syslog.md).


**Por ejemplo, para un servidor rsyslog**, ejecute el siguiente comando para mostrar la configuración actual del reenvío de Syslog y revise los cambios en el archivo de configuración:

```bash
cat /etc/rsyslog.d/95-omsagent.conf
```

En este caso, para rsyslog, debería mostrarse una salida similar a la siguiente. El contenido de este archivo debe reflejar lo que se define en la configuración de Syslog de la pantalla **Log Analytics Workspace Client configuration - Syslog facility settings** (Configuración del cliente del área de trabajo de Log Analytics: configuración de la instalación de Syslog).


```bash
OMS Syslog collection for workspace c69fa733-da2e-4cf9-8d92-eee3bd23fe81
auth.=alert;auth.=crit;auth.=debug;auth.=emerg;auth.=err;auth.=info;auth.=notice;auth.=warning  @127.0.0.1:25224
authpriv.=alert;authpriv.=crit;authpriv.=debug;authpriv.=emerg;authpriv.=err;authpriv.=info;authpriv.=notice;authpriv.=warning  @127.0.0.1:25224
cron.=alert;cron.=crit;cron.=debug;cron.=emerg;cron.=err;cron.=info;cron.=notice;cron.=warning  @127.0.0.1:25224
local0.=alert;local0.=crit;local0.=debug;local0.=emerg;local0.=err;local0.=info;local0.=notice;local0.=warning  @127.0.0.1:25224
local4.=alert;local4.=crit;local4.=debug;local4.=emerg;local4.=err;local4.=info;local4.=notice;local4.=warning  @127.0.0.1:25224
syslog.=alert;syslog.=crit;syslog.=debug;syslog.=emerg;syslog.=err;syslog.=info;syslog.=notice;syslog.=warning  @127.0.0.1:25224
```


**En cuanto al reenvío de CEF, para un servidor rsyslog**, ejecute el siguiente comando para mostrar la configuración actual de su reenvío de Syslog y revise cualquier cambio en el archivo de configuración:

```bash
cat /etc/rsyslog.d/security-config-omsagent.conf
```

En este caso, para rsyslog, debería mostrarse una salida similar a la siguiente:

```bash
if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
```

## <a name="troubleshoot-operating-system-issues"></a>Solución de problemas del sistema operativo

En este procedimiento se describe cómo solucionar problemas que seguramente se derivan de la configuración del sistema operativo.

**Para solucionar problemas del sistema operativo**:

1. Si aún no lo ha hecho, compruebe que está trabajando con un sistema operativo compatible y una versión de Python. Para obtener más información, consulte [Requisitos previos de CEF](connect-common-event-format.md#prerequisites) y [Configuración de la máquina o el dispositivo Linux](connect-syslog.md#configure-your-linux-machine-or-appliance).

1. Si la máquina virtual está en Azure, verifique que el grupo de seguridad de red (NSG) permite la conectividad TCP/UDP entrante desde el cliente de registro (remitente) en el puerto 514.

1. Verifique que los paquetes llegan al recopilador de Syslog. Para capturar los paquetes de Syslog que llegan al recopilador de Syslog, ejecute:

    ```config
    tcpdump -Ani any port 514 and host <ip_address_of_sender> -vv
    ```

1. Realice una de las siguientes acciones:

    - Si ve que no llega ningún paquete, confirme los permisos del grupo de seguridad de NSG y la ruta de acceso de enrutamiento al recopilador de Syslog.

    - Si ve que llegan paquetes, confirme que no se rechazan.

    Si ve paquetes rechazados, confirme que las tablas IP no bloquean las conexiones.

    Para confirmar que los paquetes no se rechazan, ejecute:

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. Verifique si el servidor de Syslog procesa los registros. Ejecute:

    ```config
    tail -f /var/log/messages or tail -f /var/log/syslog
    ```

    Los registros de Syslog o CEF que se procesan se muestran en texto sin formato.

1. Confirme que el servidor rsyslog realiza escuchas en el puerto TCP/UDP 514. Ejecute:

    ```config
    netstat -anp | grep syslog
    ```

    Si tiene algún registro CEF o ASA que se envía al recopilador de Syslog, debería ver una conexión establecida en el puerto TCP 25226.

    Por ejemplo:

    ```config
    0 127.0.0.1:36120 127.0.0.1:25226 ESTABLISHED 1055/rsyslogd
    ```

    Si la conexión está bloqueada, es posible que tenga una [conexión SELinux bloqueada al agente de OMS](#selinux-blocking-connection-to-the-oms-agent) o un [proceso de firewall bloqueado](#blocked-firewall-policy). Use los siguientes conjuntos de instrucciones para determinar el problema.

### <a name="selinux-blocking-connection-to-the-oms-agent"></a>SELinux bloquea la conexión al agente de OMS

En este procedimiento se describe cómo se debe confirmar si SELinux actualmente está en un estado `permissive` o está bloqueando una conexión al agente de OMS. Este procedimiento resulta pertinente cuando el sistema operativo es una distribución de RedHat o CentOS.

> [!NOTE]
> La compatibilidad de Azure Sentinel con CEF y Syslog solo incluye el sistema de protección de FIPS. Actualmente no se admiten otros métodos de protección, como SELinux o CIS.
>

1. Ejecute:

    ```config
    sestatus
    ```

    El estado se muestra como uno de los siguientes:

    - `disabled`. Esta configuración es compatible con su conexión a Azure Sentinel.
    - `permissive`. Esta configuración es compatible con su conexión a Azure Sentinel.
    - `enforced`. Esta configuración no se admite y debe deshabilitar el estado o establecerlo en `permissive`.

1. Si el estado actualmente está establecido en `enforced`, deshabilítelo temporalmente para confirmar si se trata del bloqueador. Ejecute:

    ```config
    setenforce 0
    ```

    > [!NOTE]
    > Este paso desactiva SELinux solo hasta que se reinicie el servidor. Modifique la configuración de SELinux para mantenerla desactivada.
    >

1. Para verificar si el cambio se ha realizado correctamente, ejecute:

    ```
    getenforce
    ```

    Debe devolverse el estado `permissive`.

> [!IMPORTANT]
> Esta actualización de configuración se pierde cuando se reinicia el sistema. Para actualizar permanentemente esta configuración a `permissive`, cambie el valor `SELINUX` a `SELINUX=permissive` del archivo **/etc/selinux/config**.
>
> Para obtener más información, vea la [documentación de RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_selinux/changing-selinux-states-and-modes_using-selinux).


### <a name="blocked-firewall-policy"></a>Directiva de firewall bloqueada

En este procedimiento se describe cómo comprobar si una directiva de firewall bloquea la conexión desde el demonio Rsyslog al agente de OMS y cómo deshabilitarla según sea necesario.


1. Ejecute el siguiente comando para verificar si hay rechazos en las tablas IP, lo que indica el tráfico que descarta la directiva de firewall:

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. Para mantener habilitada la directiva de firewall, cree una regla de directiva para permitir las conexiones. Agregue reglas según sea necesario para permitir los puertos TCP/UDP 25226 y 25224 a través del firewall activo.

    Por ejemplo:

    ```config
    Every 2.0s: iptables -nvL                      rsyslog: Wed Jul  7 15:56:13 2021

    Chain INPUT (policy ACCEPT 6185K packets, 2466M bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain OUTPUT (policy ACCEPT 6792K packets, 6348M bytes)
     pkts bytes target     prot opt in     out     source               destination
    ```

1. Para crear una regla que permita los puertos TCP/UDP 25226 y 25224 a través del firewall activo, agregue reglas según sea necesario.

    1. Para instalar el editor de directivas de firewall, ejecute:

        ```config
        yum install policycoreutils-python
        ```

    1. Agregue las reglas de firewall a la directiva de firewall. Por ejemplo:

        ```config
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25226  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p udp --dport 25224  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25224  -j ACCEPT
        ```

    1. Verifique que se haya agregado la excepción. Ejecute:

        ```config
        sudo firewall-cmd --direct --get-rules ipv4 filter INPUT
        ```

    1. Vuelva a cargar el firewall. Ejecute:

        ```config
        sudo firewall-cmd --reload
        ```

> [!NOTE]
> Para deshabilitar el firewall, ejecute: `sudo systemctl disable firewalld`
>

## <a name="linux-and-oms-agent-related-issues"></a>Problemas relacionados con el agente de Linux y OMS

Si los pasos descritos anteriormente en este artículo no solucionan el problema, es posible que tenga un problema de conectividad entre el agente de OMS y el área de trabajo de Azure Sentinel.

En tales casos, siga solucionando problemas y verifique lo siguiente:

- Asegúrese de que puede ver los paquetes que llegan al puerto TCP/UDP 514 en el recopilador de Syslog.

- Asegúrese de que puede ver los registros que se escriben en el archivo de registro local, ya sea **/var/log/messages** o **/var/log/syslog**

- Asegúrese de que puede ver los paquetes de datos que fluyen en los puertos 25224, 25226 o en ambos.

- Asegúrese de que la máquina virtual tiene una conexión saliente al puerto 443 a través de TCP, o de que puede conectarse a los [puntos de conexión de Log Analytics](../azure-monitor/agents/log-analytics-agent.md#network-requirements).

- Asegúrese de que tiene acceso a las direcciones URL necesarias del recopilador de Syslog a través de la directiva de firewall. Para obtener más información, consulte [Requisitos del firewall del agente de Log Analytics](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements).

- Asegúrese de que la máquina virtual de Azure se muestra como conectada en la lista de máquinas virtuales del área de trabajo.

Ejecute el siguiente comando para determinar si el agente se comunica correctamente con Azure o si el agente de OMS tiene bloqueada la conexión al área de trabajo de Log Analytics.

```config
Heartbeat
 | where Computer contains "<computername>"
 | sort by TimeGenerated desc
```

Se devuelve una entrada de registro si el agente se comunica correctamente. De lo contrario, es posible que se bloquee el agente de OMS.

## <a name="next-steps"></a>Pasos siguientes

Si los pasos de solución de problemas de este artículo no le han ayudado a solucionar su problema, abra una incidencia de soporte técnico o use los recursos de la comunidad de Azure Sentinel. Para obtener más información, consulte [Recursos útiles al trabajar con Azure Sentinel](resources.md).

Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Obtenga información sobre la [asignación de campos de CEF y CommonSecurityLog](cef-name-mapping.md).
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.
