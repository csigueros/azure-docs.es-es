---
title: Implementación de un reenviador de registros para la ingesta de registros de Syslog y CEF en Azure Sentinel | Microsoft Docs
description: Aprenda cómo implementar un reenviador de registros (compuesto de un demonio de Syslog y del agente de Log Analytics) como parte del proceso de la ingesta de registros de Syslog y CEF para Azure Sentinel.
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
ms.date: 01/05/2021
ms.author: bagol
ms.openlocfilehash: 5a7df5ba2de5b3d12e39b403be6a9e336734d144
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260976"
---
# <a name="deploy-a-log-forwarder-to-ingest-syslog-and-cef-logs-to-azure-sentinel"></a>Implementación de un reenviador de registros para la ingesta de registros de Syslog y CEF en Azure Sentinel

Para ingerir registros de Syslog y CEF en Azure Sentinel, sobre todo desde aplicaciones y dispositivos en los que no se puede instalar el agente de Log Analytics directamente, debe designar y configurar una máquina Linux para que recopile los registros de los dispositivos y los reenvíe al área de trabajo de Azure Sentinel. Esta máquina puede ser una máquina física o virtual del entorno local, una máquina virtual de Azure o una máquina virtual en otra nube. 

La máquina tiene dos componentes que participan en el proceso:

- Un demonio de syslog (**rsyslog** o **syslog-ng**) que recopila los registros.
- El **agente de Log Analytics** (también conocido como Agente de OMS), que reenvía los registros a Azure Sentinel.

Con el vínculo proporcionado a continuación, ejecutará un script en la máquina designada que realiza las tareas siguientes:

- Instala el agente de Log Analytics para Linux (también denominado agente de OMS) y lo configura para los siguientes fines:
    - escucha de mensajes CEF desde el demonio de Syslog de Linux integrado en el puerto TCP 25226
    - envío de mensajes de forma segura a través de TLS a su área de trabajo de Azure Sentinel, donde se analizan y enriquecen

- Configura el demonio de Syslog de Linux integrado (rsyslog.d/syslog-ng) para los siguientes fines:
    - escucha de mensajes de Syslog desde las soluciones de seguridad en el puerto TCP 514
    - reenvío de solo los mensajes que identifica como CEF al agente de Log Analytics en localhost mediante el puerto TCP 25226
 
## <a name="prerequisites"></a>Prerrequisitos

La máquina debe cumplir los requisitos siguientes:

- **Hardware (físico/virtual)**

    - La máquina Linux debe tener al menos **cuatro núcleos de CPU y 8 GB de RAM**.

        > [!NOTE]
        > - Una única máquina de reenviador de registros que use el demonio **rsyslog** tiene una capacidad admitida de **hasta 8500 eventos por segundo (EPS)** recopilados.

- **Sistema operativo**

    - CentOS 7 y 8 (no 6), incluidas las versiones secundarias (64/32 bits)
    - Amazon Linux 2017.09 (solo 64 bits)
    - Oracle Linux 7 (64/32 bits)
    - Red Hat Enterprise Linux (RHEL) Server 7 y 8 (no 6), incluidas las versiones secundarias (64/32 bits)
    - Debian GNU/Linux 8 y 9 (64/32 bits)
    - Ubuntu Linux 14.04 LTS y 16.04 LTS (64/32 bits) y 18.04 LTS (solo 64 bits)
    - SUSE Linux Enterprise Server 12, 15 (solo 64 bits)

- **Versiones de demonio**

    - Rsyslog: v8
    - Syslog-ng: 2.1 - 3.22.1

- **Paquetes**
    - Debe tener **Python 2.7** o **3** instalado en la máquina Linux.<br>Use el comando `python --version` o `python3 --version` para comprobarlo.

- **Compatibilidad con RFC de Syslog**
  - Syslog RFC 3164
  - RFC de Syslog 5424
 
- **Configuración**
    - Debe tener permisos elevados (sudo) en la máquina Linux designada.
    - La máquina Linux no debe estar conectada a ninguna área de trabajo de Azure antes de instalar el agente de Log Analytics.

- **Data**
    - En algún momento del proceso, es posible que necesite el **identificador del área de trabajo** y la **clave principal del área de trabajo** de Azure Sentinel. Puede encontrarlos en la configuración del área de trabajo, en **Administración de agentes**.

### <a name="security-considerations"></a>Consideraciones sobre la seguridad

Asegúrese de configurar la seguridad de la máquina de acuerdo con la directiva de seguridad de su organización. Por ejemplo, puede configurar la red para que se alinee con la directiva de seguridad de la red corporativa y cambiar los puertos y protocolos del demonio para que se adapten a sus requisitos. Puede usar las siguientes instrucciones para mejorar la configuración de seguridad de la máquina:  [Protección de VM en Azure](../virtual-machines/security-policy.md), [Prácticas recomendadas para la seguridad de red](../security/fundamentals/network-best-practices.md).

Si los dispositivos envían registros de Syslog y CEF sobre TLS (por ejemplo, porque el reenviador de registros está en la nube), deberá configurar el demonio de Syslog (rsyslog o syslog-ng) para que se comunique en TLS. Para obtener más detalles, consulte la documentación siguiente:  
- [Cifrado del tráfico de Syslog con TLS: rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [Cifrado de mensajes de registro con TLS: syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="run-the-deployment-script"></a>Ejecutar el script de implementación
 
1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**. Seleccione el conector para el producto desde la galería de conectores (o el **formato de evento común (CEF)** si el producto no aparece en la lista) y, a continuación, el botón **Open connector page** (Abrir página del conector) en la parte inferior derecha. 

1. En la página del conector, en las instrucciones de la sección **1.2 Instalación del recopilador de CEF en la máquina Linux**, copie el vínculo proporcionado en **Run the following script to install and apply the CEF collector** (Ejecutar el siguiente script para instalar y aplicar el recopilador de CEF).  
Si no tiene acceso a esa página, copie el vínculo del texto siguiente (debe copiar y pegar el **identificador del área de trabajo** y la **clave principal** de arriba en lugar de los marcadores de posición):

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```
1. Pegue el vínculo o el texto en la línea de comandos del reenviador de registros y ejecútelo.

1. Mientras se ejecuta el script, asegúrese de que no recibe ningún mensaje de error o de advertencia.
    - Es posible que reciba un mensaje que le indique que debe ejecutar un comando para corregir un problema con la asignación del campo *Equipo*. Consulte la [explicación en el script de implementación](#mapping-command) para obtener más detalles.

1. [Configure el dispositivo para enviar mensajes de CEF](connect-common-event-format.md#configure-your-device).

    > [!NOTE]
    > **Uso de la misma máquina para reenviar Syslog sin formato *y* mensajes de CEF**
    >
    > Si tiene previsto usar esta máquina de reenviador de registros para reenviar [mensajes de Syslog](connect-syslog.md), así como de CEF, haga lo siguiente para evitar la duplicación de eventos en las tablas Syslog y CommonSecurityLog:
    >
    > 1. En cada máquina de origen que envíe registros al reenviador en formato CEF, debe editar el archivo de configuración de Syslog para quitar las funciones que se usan para enviar mensajes de CEF. De este modo, las instalaciones que se envían en CEF no se enviarán también en Syslog. Consulte [Configuración de Syslog en agente de Linux](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent) para obtener instrucciones detalladas sobre cómo hacerlo.
    >
    > 1. Debe ejecutar el siguiente comando en esas máquinas para deshabilitar la sincronización del agente con la configuración de Syslog en Azure Sentinel. Esto garantiza que el cambio de configuración que ha realizado en el paso anterior no se sobrescriba.<br>
    > `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="deployment-script-explained"></a>Explicación del script de implementación

A continuación, se presenta una descripción, comando por comando, de las acciones del script de implementación.

Elija un demonio de syslog para ver la descripción adecuada.

# <a name="rsyslog-daemon"></a>[demonio rsyslog](#tab/rsyslog)

1. **Descarga e instalación del agente de Log Analytics:**

    - Descarga el script de instalación para el agente de Linux de Log Analytics (OMS).

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Instala el agente de Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Establecimiento de la configuración del agente de Log Analytics para escuchar en el puerto 25226 y reenviar mensajes de CEF a Azure Sentinel:**

    - Descarga la configuración del repositorio GitHub del agente de Log Analytics.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Configuración del demonio de Syslog:**

    - Abre el puerto 514 para la comunicación TCP mediante el archivo de configuración de syslog `/etc/rsyslog.conf`.

    - Configura el demonio para reenviar los mensajes de CEF al agente de Log Analytics en el puerto TCP 25226, insertando un archivo de configuración especial `security-config-omsagent.conf` en el directorio del demonio de syslog `/etc/rsyslog.d/`.

        Contenido del archivo `security-config-omsagent.conf`:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Reinicio del demonio de Syslog y el agente de Log Analytics:**

    - Reinicia el demonio de rsyslog.
    
        ```bash
        service rsyslog restart
        ```

    - Reinicia el agente de Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Comprobación de la asignación del campo *Equipo* según lo esperado:**

    - Se asegura de que el campo *Equipo* del origen de syslog se ha asignado correctamente en el agente de Log Analytics, mediante el siguiente comando: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Si hay un problema con la asignación, el script generará un mensaje de error que le indicará que debe **ejecutar manualmente el siguiente comando** (debe agregar el id. del área de trabajo en lugar del marcador de posición). El comando garantizará que la asignación se realice correctamente y reiniciará el agente.
    
        ```bash
        sudo sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[demonio syslog-ng](#tab/syslogng)

1. **Descarga e instalación del agente de Log Analytics:**

    - Descarga el script de instalación para el agente de Linux de Log Analytics (OMS).

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Instala el agente de Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Establecimiento de la configuración del agente de Log Analytics para escuchar en el puerto 25226 y reenviar mensajes de CEF a Azure Sentinel:**

    - Descarga la configuración del repositorio GitHub del agente de Log Analytics.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Configuración del demonio de Syslog:**

    - Abre el puerto 514 para la comunicación TCP mediante el archivo de configuración de syslog `/etc/syslog-ng/syslog-ng.conf`.

    - Configura el demonio para reenviar los mensajes de CEF al agente de Log Analytics en el puerto TCP 25226, insertando un archivo de configuración especial `security-config-omsagent.conf` en el directorio del demonio de syslog `/etc/syslog-ng/conf.d/`.

        Contenido del archivo `security-config-omsagent.conf`:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Reinicio del demonio de Syslog y el agente de Log Analytics:**

    - Reinicia el demonio de syslog-ng.
    
        ```bash
        service syslog-ng restart
        ```

    - Reinicia el agente de Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Comprobación de la asignación del campo *Equipo* según lo esperado:**

    - Se asegura de que el campo *Equipo* del origen de syslog se ha asignado correctamente en el agente de Log Analytics, mediante el siguiente comando: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Si hay un problema con la asignación, el script generará un mensaje de error que le indicará que debe **ejecutar manualmente el siguiente comando** (debe agregar el id. del área de trabajo en lugar del marcador de posición). El comando garantizará que la asignación se realice correctamente y reiniciará el agente.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a implementar el agente de Log Analytics para conectar dispositivos CEF a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Obtenga información sobre la [asignación de campos de CEF y CommonSecurityLog](cef-name-mapping.md).
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).
