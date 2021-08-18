---
title: Conexión de datos de Cisco Meraki con Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de Cisco Meraki para extraer registros de Cisco Meraki y exportarlos a Azure Sentinel. Vea los datos de Cisco Meraki en los libros, cree alertas y mejore la investigación.
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
ms.openlocfilehash: 64828011cc92145e6635c995294f234754999a8b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179380"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Conexión de Cisco Meraki a Azure Sentinel

> [!IMPORTANT]
> El conector de Cisco Meraki está actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar dispositivos de Cisco Meraki (MX/MS/MR) a Azure Sentinel. El conector de datos de Cisco Meraki permite conectar fácilmente los registros de Cisco Meraki con Azure Sentinel para ver paneles, crear alertas personalizadas y mejorar la investigación. La integración entre Cisco Meraki y Azure Sentinel permite el uso de un servidor de Syslog con el agente de Log Analytics instalado. También usa un analizador de registro personalizado basado en una función de Kusto.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permiso de lectura y escritura en el área de trabajo de Azure Sentinel.

- La solución de Cisco Meraki debe estar configurada para exportar registros a través de Syslog.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Envío de registros de Cisco Meraki a Azure Sentinel a través del agente de Syslog  

Configure Cisco Meraki para reenviar mensajes de Syslog a su área de trabajo de Azure Sentinel a través del agente de Syslog.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione el conector **Cisco Meraki (versión preliminar)** y, a continuación, seleccione la página **Abrir conector**.

1. Siga las instrucciones que aparecen en la página del conector **Cisco Meraki**:

    1. Instale e incorpore el agente para Linux.

        - Elija una VM Linux de Azure o una máquina Linux que no sea de Azure (física o virtual).

    1. Configure los registros que se van a recopilar.

        - Seleccione los recursos y los niveles de gravedad en la **configuración de los agentes del área de trabajo**.

    1. Configuración y conexión de dispositivos de Cisco Meraki

        - Siga [estas instrucciones](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) para configurar los dispositivos de Cisco Meraki para reenviar Syslog. En el caso del servidor remoto, use la dirección IP de la máquina Linux en la que instaló el agente de Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Validación de la conectividad y búsqueda de los datos

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Azure Sentinel. 

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, en la sección *Administración del registro*, en la tabla *Syslog*.

Este conector de datos depende de un analizador basado en una función de Kusto para funcionar según lo previsto. [Siga estos pasos](https://aka.ms/sentinel-ciscomeraki-parser) para crear el alias de la función **CiscoMeraki** de Kusto. Después, puede escribir `CiscoMeraki` en la ventana de consulta para consultar los datos.

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver algunos ejemplos de consulta útiles.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a conectar Cisco Meraki con Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.