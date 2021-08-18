---
title: Conexión de Thycotic Secret Server con Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de datos de Thycotic Secret Server para extraer los registros de Thycotic Secret Server y exportarlos a Azure Sentinel. Vea los datos de Thycotic Secret Server en los libros, cree alertas y mejore la investigación.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: b023745d8882d639cf9a39fd483dd7b82d1a782d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780249"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>Conexión de Thycotic Secret Server con Azure Sentinel

> [!IMPORTANT]
> El conector de Thycotic Secret Server está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el dispositivo de Thycotic Secret Server con Azure Sentinel. El conector de datos de Thycotic Secret Server le permite conectar fácilmente los datos de registros de Thycotic Secret Server con Azure Sentinel, de modo que pueda verlos en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. La integración entre Thycotic y Azure Sentinel hace uso del conector de datos de CEF para analizar y mostrar correctamente los mensajes de Syslog de Secret Server.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener permisos de lectura para las claves compartidas del área de trabajo.

- Thycotic Secret Server debe estar configurado para exportar registros a través de Syslog.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Envío de registros de Thycotic Secret Server a Azure Sentinel

Para exportar los registros a Azure Sentinel, configure Thycotic Secret Server para enviar los mensajes de Syslog en formato CEF al servidor de reenvío de registros basado en Linux (que ejecuta rsyslog o syslog-ng). Este servidor tendrá instalado el agente de Log Analytics y este reenviará los registros a su área de trabajo de Azure Sentinel.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione **Thycotic Secret Server (versión preliminar)** y, a continuación, seleccione la página **Abrir conector**.

1. Siga las instrucciones que aparecen en la pestaña **Instrucciones**, en **Configuración**:

    1. En **1. Configuración del agente de Syslog para Linux**. Complete este paso si todavía no ejecuta un reenviador de registros o si necesita otro. Consulte [PASO 1: Implementación del reenviador de registros](connect-cef-agent.md) de la documentación de Azure Sentinel para una explicación e instrucciones más detalladas.

    1. En **2. Reenvío de los registros en formato de evento común (CEF) al agente de Syslog**. Siga las instrucciones de Thycotic para [configurar Secret Server](https://docs.thycotic.com/ssi/1.0.0/splunk/splunk-on-prem/config/syslog-events.md). Esta configuración debe incluir los elementos siguientes:
        - Destino de los registros: el nombre de host o la dirección IP del servidor de reenvío de registros
        - Protocolo y puerto: **TCP 514** (si se recomienda otro, asegúrese de realizar el cambio adecuado en el demonio de Syslog en el servidor de reenvío de registros)
        - Formato de registro: CEF
        - Tipos de registro: todos los disponibles

    1. En **3. Validación de la conexión**. Para comprobar la ingesta de datos, copie el comando en la página de conectores y ejecútelo en el reenviador de registros. Consulte [PASO 3: Validar conectividad](connect-cef-verify.md) de la documentación de Azure Sentinel para una explicación e instrucciones más detalladas.

        Hasta que los registros empiecen a aparecer en Log Analytics, pueden transcurrir hasta 20 minutos.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, debajo de la sección **Azure Sentinel** en la tabla *CommonSecurityLog*.

Para consultar los datos de Thycotic Secret Server en Log Analytics, copie lo siguiente en la ventana de consulta y aplique los filtros que considere necesarios:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver algunos ejemplos de consulta y libros útiles.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a conectar Thycotic Secret Server con Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.
