---
title: Conexión de datos de Syslog a Azure Sentinel | Microsoft Docs
description: Conecte cualquier máquina o dispositivo que admita Syslog en Azure Sentinel mediante un agente en una máquina Linux entre el dispositivo y Azure Sentinel.
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
ms.date: 08/15/2021
ms.author: yelevin
ms.openlocfilehash: 807083fa77023753382abb96419c9cd5fe689b33
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254246"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Recopilación de datos de orígenes basados en Linux mediante Syslog

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Syslog** es un protocolo de registro de eventos que es común a Linux. Puede usar el demonio de Syslog integrado en dispositivos Linux para recopilar eventos locales de los tipos que especifique y hacer que envíe esos eventos a Azure Sentinel mediante el **agente de Log Analytics para Linux** (anteriormente conocido como agente de OMS).

En este artículo se describe cómo conectar los orígenes de datos a Azure Sentinel mediante Syslog. Para más información sobre los conectores compatibles que usan este método, consulte [Referencia de conectores de datos](data-connectors-reference.md).

## <a name="architecture"></a>Architecture

Al instalar el agente de Log Analytics en su VM o dispositivo, el script de instalación configura el demonio de Syslog local para que reenvíe mensajes al agente en el puerto UDP 25224. Después de recibir los mensajes, el agente los envía al área de trabajo de Log Analytics a través de HTTPS, donde se ingieren en la tabla Syslog en **Azure Sentinel > Registros**.

Para más información, consulte [Orígenes de datos de Syslog en Azure Monitor](../azure-monitor/agents/data-sources-syslog.md).

:::image type="content" source="media/connect-syslog/syslog-diagram.png" alt-text="En este diagrama se muestra el flujo de datos de los orígenes de Syslog al área de trabajo de Azure Sentinel, donde el agente de Log Analytics se instala directamente en el dispositivo de origen de datos.":::

Para algunos tipos de dispositivo que no permiten la instalación local del agente de Log Analytics, el agente se puede instalar en su lugar en un reenviador de registros dedicado basado en Linux. El dispositivo de origen debe configurarse para enviar eventos de Syslog al demonio de Syslog en este reenviador en lugar del demonio local. El demonio de Syslog en el reenviador envía eventos al agente de Log Analytics a través de UDP. Si se espera que este reenviador de Linux recopile un gran volumen de eventos de Syslog, su demonio de Syslog envía eventos al agente a través de TCP en su lugar. En cualquier caso, el agente envía los eventos desde allí al área de trabajo de Log Analytics en Azure Sentinel.

:::image type="content" source="media/connect-syslog/syslog-forwarder-diagram.png" alt-text="En este diagrama se muestra el flujo de datos de los orígenes de Syslog al área de trabajo de Azure Sentinel, donde el agente de Log Analytics se instala directamente en el dispositivo de origen de datos en un dispositivo independiente de reenvío de registros.":::

> [!NOTE]
> - Si el dispositivo admite **Common Event Format (CEF) en Syslog**, se recopila un conjunto de datos más completo y los datos se analizan en la colección. Debe elegir esta opción y seguir las instrucciones de [Conexión de registros con formato CEF de un dispositivo a Azure Sentinel](connect-common-event-format.md).
>
> - Log Analytics admite la recopilación de mensajes enviados por los demonios **rsyslog** o **syslog-ng**, donde rsyslog es el predeterminado. El demonio predeterminado de Syslog en la versión 5 de Red Hat Enterprise Linux (RHEL), CentOS y Oracle Linux (**sysklog**) *no se admite* para la recopilación de eventos de Syslog. Para recopilar datos de Syslog de esta versión de estas distribuciones, es necesario instalar configurar el demonio de Rsyslog para reemplazar Sysklog.

Hay tres pasos para configurar la recopilación de Syslog:

- **Configure el dispositivo Linux**. Esto hace referencia al dispositivo en el que se instalará el agente de Log Analytics, ya sea el mismo dispositivo donde se originan los eventos o un recopilador de registros que los reenvía.

- **Configure los valores de registro de la aplicación** correspondientes a la ubicación del demonio de Syslog que enviará eventos al agente.

- **Configure el propio agente de Log Analytics**. Esto se realiza desde dentro de Azure Sentinel, y la configuración se envía a todos los agentes instalados.

## <a name="configure-your-linux-machine-or-appliance"></a>Configuración de la máquina o el dispositivo Linux

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería de conectores, seleccione **Syslog** y luego **Abrir página del conector**.

    Si el tipo de dispositivo aparece en la **galería de conectores de datos** de Azure Sentinel, elija el conector para el dispositivo en lugar del conector genérico de Syslog. Si hay instrucciones adicionales o especiales para el tipo de dispositivo, las verá, junto con contenido personalizado, como libros y plantillas de reglas de análisis, en la página del conector del dispositivo.

1. Instale el agente de Linux. En **Elija dónde quiere instalar al agente:**

    |Tipo de máquina  |Instrucciones  |
    |---------|---------|
    |**Para una VM Linux de Azure**     |    1. Expanda **Instalación del agente en una máquina virtual Linux de Azure**. <br><br>2. Seleccione el vínculo **Descargar e instalar el agente para máquinas virtuales Linux de Azure >** .<br><br>3. En la hoja **Máquinas virtuales**, seleccione una máquina virtual en la que quiera instalar el agente y, después, seleccione **Conectar**. Repita este paso para cada VM que quiera conectar.     |
    |**Para cualquier otra máquina Linux**     |     1. Expanda **Instalación del agente en una máquina Linux que no sea de Azure**. <br><br>2. Seleccione el vínculo **Descargar e instalar el agente para máquinas Linux que no sean de Azure >** .<br><br>3. En la hoja **Administración de agentes**, seleccione la pestaña **Servidores Linux** y, a continuación, copie el comando para **descargar e incorporar el agente para Linux** y ejecútelo en la máquina Linux.<br><br>        Si desea mantener una copia local del archivo de instalación del agente de Linux, seleccione el vínculo **Descargar agente de Linux** encima del comando "Descargar e incorporar agente". |
    |     |         |

   > [!NOTE]
   > Asegúrese de configurar los valores de seguridad para estos dispositivos de acuerdo con la directiva de seguridad de su organización. Por ejemplo, puede configurar los valores de red para que se alineen con la directiva de seguridad de red de la organización y cambiar los puertos y protocolos del demonio para que se adapten a sus requisitos de seguridad.

### <a name="using-the-same-machine-to-forward-both-plain-syslog-and-cef-messages"></a>Uso del mismo equipo para reenviar Syslog sin formato *y* mensajes de CEF

Puede usar la [máquina del reenviador de registros de CEF](connect-log-forwarder.md) existente para recopilar y reenviar registros también desde orígenes de Syslog sin formato. Sin embargo, debe realizar los pasos siguientes para evitar el envío de eventos en ambos formatos a Azure Sentinel, ya que esto provocará la duplicación de eventos.

Cuando ya haya configurado la [recopilación de datos de los orígenes de CEF](connect-common-event-format.md) y haya configurado el agente de Log Analytics:

1. En cada máquina que envíe registros en formato CEF, debe editar el archivo de configuración de Syslog para quitar las funciones que se usan para enviar mensajes CEF. De este modo, las instalaciones que se envían en CEF no se enviarán también en Syslog. Consulte [Configuración de Syslog en agente de Linux](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent) para obtener instrucciones detalladas sobre cómo hacerlo.

1. Debe ejecutar el siguiente comando en esas máquinas para deshabilitar la sincronización del agente con la configuración de Syslog en Azure Sentinel. Esto garantiza que el cambio de configuración que ha realizado en el paso anterior no se sobrescriba.

    ```c
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
    ```

## <a name="configure-your-devices-logging-settings"></a>Configuración de los valores de registro del dispositivo

Muchos tipos de dispositivo tienen sus propios conectores de datos en la galería de **conectores de datos**. Algunos de estos conectores requieren instrucciones adicionales especiales para configurar correctamente la recopilación de registros en Azure Sentinel. Estas instrucciones pueden incluir la implementación de un analizador basado en una función de Kusto.

Todos los conectores enumerados en la galería mostrarán instrucciones específicas en sus respectivas páginas de conector en el portal, así como en sus secciones de la página [Referencia de conectores de datos de Azure Sentinel](data-connectors-reference.md).


## <a name="configure-the-log-analytics-agent"></a>Configuración del agente de Log Analytics

1. En la parte inferior de la hoja del conector de Syslog, seleccione el vínculo **Abrir la configuración de agentes del área de trabajo >** .

1. En la hoja **Agents configuration** (Configuración de agentes), seleccione la pestaña **Syslog**. A continuación, agregue los recursos del conector que se van a recopilar. Seleccione **Add facility** (Agregar instalación) y realice su selección en la lista desplegable de instalaciones.

    - Agregue los recursos que su dispositivo de Syslog incluye en sus encabezados de registro.

    - Si desea usar la detección de inicio de sesión de SSH anómalo con los datos que recopila, agregue **auth** y **authpriv**. Consulte la [siguiente sección](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) para conocer más detalles.

1. Cuando haya agregado todas las instalaciones que desea supervisar, desactive las casillas de las gravedades que no quiera recopilar. De manera predeterminada, están todas marcadas.

1. Seleccione **Aplicar**.

1. En la VM o dispositivo, asegúrese de que está enviando los recursos que ha especificado.

## <a name="find-your-data"></a>Búsqueda de sus datos

1. Para consultar los datos de registro de Syslog en **Registros**, escriba `Syslog` en la ventana de consulta.

1. Puede usar los parámetros de consulta que se describen en [Uso de funciones en consultas de registros de Azure Monitor](../azure-monitor/logs/functions.md) para analizar los mensajes de Syslog. Después, puede guardar la consulta como una nueva función de Log Analytics y usarla como un nuevo tipo de datos.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configuración del conector de Syslog para la detección de inicios de sesión de SSH anómalos

> [!IMPORTANT]
> La detección de inicios de sesión de SSH anómalos se encuentra actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Azure Sentinel puede aplicar aprendizaje automático (ML) a los datos de Syslog para identificar una actividad de inicio de sesión de Secure Shell anómala (SSH). Los escenarios incluyen:

- Viaje imposible: cuando se producen dos eventos de inicio de sesión correctos desde dos ubicaciones que son imposibles de alcanzar en el período de tiempo de los dos eventos de inicio de sesión.

- Ubicación inesperada: la ubicación desde donde se produjo un evento de inicio de sesión correcto es sospechosa. Por ejemplo, la ubicación no se ha visualizado recientemente.
 
Esta detección requiere una configuración específica del conector de datos de Syslog: 

1. En el paso 2 anterior, en [Configuración del agente de Log Analytics](#configure-the-log-analytics-agent), asegúrese de que **auth** y **authpriv** se seleccionan como instalaciones para supervisar y que se eligen todos los niveles de gravedad. 

2. Deje tiempo suficiente para que se recopile la información de Syslog. A continuación, vaya a **Azure Sentinel: Registros** y copie y pegue la siguiente consulta:
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    Cambie el **intervalo de tiempo** si es necesario y seleccione **Ejecutar**.
    
    Si el recuento resultante es cero, confirme la configuración del conector y que los equipos supervisados tienen una actividad de inicio de sesión correcta durante el período de tiempo que especificó para la consulta.
    
    Si el recuento resultante es mayor que cero, los datos de Syslog son adecuados para la detección de inicios de sesión de SSH anómalos. Puede habilitar esta detección en **Análisis** >  **Rule templates (Plantillas de reglas)**  >  **(Versión preliminar) Anomalous SSH Login Detection** (Detección de inicios de sesión de SSH anómalos).

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar dispositivos locales de Syslog a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.
