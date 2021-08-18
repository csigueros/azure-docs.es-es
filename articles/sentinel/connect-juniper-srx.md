---
title: Conexión de datos de Juniper Networks SRX con Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de datos de Juniper SRX para extraer registros de Juniper SRX y exportarlos a Azure Sentinel. Vea los datos de Juniper SRX en los libros, cree alertas y mejore la investigación.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 39df2be86f4ca1d4410396b86aabee9a749ade92
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253590"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Conexión del firewall de Juniper SRX con Azure Sentinel

> [!IMPORTANT]
> El conector de Juniper SRX está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el dispositivo de firewall de Juniper SRX con Azure Sentinel. El conector de datos de Juniper SRX le permite conectar fácilmente los datos de registro de SRX con Azure Sentinel, de modo que pueda verlos en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. La integración entre Juniper SRX y Azure Sentinel usa Syslog.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permiso de lectura y escritura en el área de trabajo de Azure Sentinel.

- La solución de Juniper SRX debe estar configurada para exportar registros a través de Syslog.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Reenvío de los registros de Juniper SRX al agente de Syslog  

Configure Juniper SRX para reenviar mensajes de Syslog a su área de trabajo de Azure Sentinel a través del agente de Syslog.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione el conector **Juniper SRX (versión preliminar)** y, a continuación, seleccione la página **Abrir conector**.

1. Siga las instrucciones que aparecen en la página del conector **Juniper SRX**:

    1. Instale e incorpore el agente para Linux.

        - Elija una VM Linux de Azure o una máquina Linux que no sea de Azure (física o virtual).

    1. Configure los registros que se van a recopilar.

        - Seleccione los recursos y los niveles de gravedad en la configuración de los agentes del área de trabajo.

    1. Configure y conecte Juniper SRX

        - Siga estas instrucciones para configurar Juniper SRX para reenviar Syslog.
            - [Registros del tráfico (registros de directivas de seguridad)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [Registros del sistema](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - En el caso del servidor remoto, use la dirección IP de la máquina Linux en la que instaló el agente de Linux.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en Log Analytics bajo Syslog.

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver algunas consultas de ejemplo útiles.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a conectar Juniper SRX con Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.