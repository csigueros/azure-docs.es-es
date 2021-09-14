---
title: Obtención de registros con formato CEF del dispositivo en Azure Sentinel | Microsoft Docs
description: Use el agente de Log Analytics, instalado en un reenviador de registros basado en Linux, para ingerir los registros enviados en formato de evento común (CEF) mediante Syslog en el área de trabajo de Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2021
ms.author: yelevin
ms.openlocfilehash: 6f261e69daa14e652d226c9b0d6402eb353c1744
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251894"
---
# <a name="get-cef-formatted-logs-from-your-device-or-appliance-into-azure-sentinel"></a>Obtención de registros con formato CEF del dispositivo en Azure Sentinel

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Muchos dispositivos de seguridad y redes envían sus registros del sistema mediante el protocolo Syslog en un formato especializado conocido como formato de evento común (CEF). Este formato incluye más información que el formato de Syslog estándar y presenta la información en una disposición clave-valor analizada. El agente de Log Analytics acepta los registros de CEF y los formatea especialmente para su uso con Azure Sentinel antes de reenviarlos al área de trabajo de Azure Sentinel.

En este artículo se describe el proceso de uso de registros con formato CEF para conectar los orígenes de datos. Para obtener información sobre los conectores de datos compatibles que usan este método, consulte [Referencia de conectores de datos de Azure Sentinel](data-connectors-reference.md).

Para hacer esta conexión, hay dos pasos principales que se explicarán a continuación en detalle:

- Designación de una máquina o VM Linux como reenviador de registros dedicado, instalación del agente de Log Analytics en ella y configuración del agente para reenviar los registros al área de trabajo de Azure Sentinel.
La instalación y configuración del agente se controlan mediante un script de implementación.

- Configuración del dispositivo para enviar sus registros en formato CEF a un servidor de Syslog.

> [!NOTE]
> Los datos se almacenan en la ubicación geográfica del área de trabajo en la que se ejecute Azure Sentinel.

## <a name="supported-architectures"></a>Arquitecturas compatibles

En el siguiente diagrama se describe la configuración en el caso de una máquina virtual Linux en Azure:

 ![CEF en Azure](./media/connect-cef/cef-syslog-azure.png)

También podrá usar la siguiente configuración si usa una VM en otra nube o en una máquina del entorno local.

 ![CEF local](./media/connect-cef/cef-syslog-onprem.png)

## <a name="prerequisites"></a>Requisitos previos

Se requiere un área de trabajo de Azure Sentinel para ingerir datos CEF en Log Analytics.

- Debe tener permisos de lectura y escritura en el área de trabajo.

- Debe tener permisos de lectura para las claves compartidas del área de trabajo. [Obtenga más información sobre las claves del área de trabajo](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key).

## <a name="designate-a-log-forwarder-and-install-the-log-analytics-agent"></a>Designe un reenviador de registros e instale el agente de Log Analytics.

En esta sección se describe cómo designar y configurar la máquina Linux que reenviará los registros desde el dispositivo al área de trabajo de Azure Sentinel.

La máquina Linux puede ser una máquina física o virtual del entorno local, una VM de Azure o una VM en otra nube.

Use el vínculo proporcionado en la **página del conector de datos de formato de evento común (CEF)** para ejecutar un script en la máquina designada y llevar a cabo las siguientes tareas:

- **Instala el agente de Log Analytics para Linux** (también denominado agente de OMS) y lo configura para los siguientes fines:
    - escucha de mensajes CEF desde el demonio de Syslog de Linux integrado en el puerto TCP 25226
    - envío de mensajes de forma segura a través de TLS a su área de trabajo de Azure Sentinel, donde se analizan y enriquecen

- **Configura el demonio de Syslog de Linux integrado** (rsyslog.d/syslog-ng) para los siguientes fines:
    - escucha de mensajes de Syslog desde las soluciones de seguridad en el puerto TCP 514
    - reenvío de solo los mensajes que identifica como CEF al agente de Log Analytics en localhost mediante el puerto TCP 25226

Para obtener más información, consulte [Implementación de un reenviador de registros para la ingesta de registros de Syslog y CEF en Azure Sentinel](connect-log-forwarder.md).

### <a name="security-considerations"></a>Consideraciones sobre la seguridad

Asegúrese de configurar la seguridad de la máquina de acuerdo con la directiva de seguridad de su organización. Por ejemplo, puede configurar la red para que se alinee con la directiva de seguridad de la red corporativa y cambiar los puertos y protocolos del demonio para que se adapten a sus requisitos.

Para obtener más información, consulte [Seguridad de máquina virtual en Azure](../virtual-machines/security-policy.md) y [Procedimientos recomendados de seguridad de la red](../security/fundamentals/network-best-practices.md).

Si los dispositivos envían registros de Syslog y CEF mediante TLS, por ejemplo, si el reenviador de registros está en la nube, deberá configurar el demonio de Syslog (rsyslog o syslog-ng) para que se comunique en TLS. 

Para más información, consulte:

- [Cifrado del tráfico de Syslog con TLS: rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [Cifrado de mensajes de registro con TLS: syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="configure-your-device"></a>Configuración del dispositivo

Busque y siga las instrucciones de configuración del proveedor de dispositivos para enviar registros en formato CEF a un servidor SIEM o de registro. 

Si el producto aparece en la galería de conectores de datos, puede consultar la [referencia de conectores de datos de Azure Sentinel](data-connectors-reference.md) para obtener ayuda. Las instrucciones de configuración deben incluir los valores de la lista siguiente.

   - Protocolo = TCP
   - Puerto = 514
   - Formato = CEF
   - Dirección IP: Asegúrese de enviar los mensajes CEF a la dirección IP de la máquina virtual que ha dedicado para tales efectos.

Esta solución admite RFC 3164 o RFC 5424 de Syslog.

> [!TIP]
> Defina otro protocolo o número de puerto en el dispositivo según sea necesario, siempre y cuando también haga los mismos cambios en el demonio de Syslog del reenviador de registros.
>

## <a name="find-your-data"></a>Búsqueda de sus datos

Los datos pueden tardar hasta 20 minutos en aparecer en Log Analytics después de hacer la conexión.

Para buscar eventos CEF en Log Analytics, consulte la tabla `CommonSecurityLog` en la ventana de consulta.

Algunos productos enumerados en la galería de conectores de datos requieren el uso de analizadores adicionales para obtener mejores resultados. Estos analizadores se implementan mediante el uso de funciones de Kusto. Para obtener más información, consulte la sección del producto en la página de [referencia de conectores de datos de Azure Sentinel](data-connectors-reference.md).

Para buscar eventos CEF de estos productos, escriba el nombre de la función de Kusto como asunto de consulta, en lugar de "CommonSecurityLog".

Puede encontrar consultas de ejemplo útiles, libros y plantillas de reglas de análisis hechas especialmente para el producto en la pestaña **Pasos siguientes** de la página de conectores de datos del producto en el portal de Azure Sentinel.

Si no ve ningún dato, consulte la página [Solución de problemas de CEF](connect-cef-verify.md) para obtener instrucciones.

### <a name="changing-the-source-of-the-timegenerated-field"></a>Cambio del origen del campo TimeGenerated

De forma predeterminada, el agente de Log Analytics rellena el campo *TimeGenerated* en el esquema con la hora en la que el agente recibió el evento del demonio de Syslog. Como resultado, la hora a la que se generó el evento en el sistema de origen no se registra en Azure Sentinel.

Sin embargo, puede ejecutar el siguiente comando que descargará y ejecutará el script `TimeGenerated.py`. Este script configura el agente de Log Analytics para rellenar el campo *TimeGenerated* con la hora original del evento en su sistema de origen, en lugar de usar la hora a la que lo recibió el agente.

```bash
wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
```

## <a name="next-steps"></a>Pasos siguientes

En este documento ha aprendido cómo recopila Azure Sentinel los registros de CEF de los dispositivos. Para obtener más información sobre cómo conectar el producto a Azure Sentinel, consulte los siguientes artículos:

- [Implementación de un reenviador Syslog o CEF](connect-log-forwarder.md)
- [Referencia de conectores de datos de Azure Sentinel](data-connectors-reference.md)
- [Solución de problemas de conectividad del reenviador de registros](troubleshooting-cef-syslog.md#validate-cef-connectivity)

Para obtener más información sobre qué hacer con los datos recopilados en Azure Sentinel, vea los siguientes artículos:

- Obtenga información sobre la [asignación de campos de CEF y CommonSecurityLog](cef-name-mapping.md).
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).