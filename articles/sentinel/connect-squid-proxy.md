---
title: Conexión de datos de Squid Proxy a Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo usar el conector de datos de Squid Proxy para extraer los registros de Squid Proxy en Azure Sentinel. Consulte los datos de Squid Proxy en libros, cree alertas y mejore la investigación.
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
ms.openlocfilehash: 4bf5d3c4a4ad70f5fc5f09c50144b759900370b6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253414"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>Conexión de Squid Proxy a Azure Sentinel

> [!IMPORTANT]
> Este conector de Squid Proxy está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el dispositivo de Squid Proxy con Azure Sentinel. El conector de datos de Squid Proxy le permite conectar fácilmente los datos de registro de Squid con Azure Sentinel, de modo que pueda verlos en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. La integración entre Squid Proxy y Azure Sentinel hace uso del procesamiento de archivos local por el agente de Log Analytics.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permiso de lectura y escritura en el área de trabajo de Azure Sentinel.

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>Reenvío de registros de Squid Proxy al agente de Log Analytics  

Configure Squid Proxy para enviar archivos de registro a su área de trabajo de Azure a través del agente de Log Analytics.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione el conector **Squid Proxy (versión preliminar)** y, a continuación, seleccione la página **Abrir conector**.

1. Siga las instrucciones que aparecen en la página del conector **Squid Proxy**:

    1. Instale e incorpore el agente para Linux.

        - Elija una VM Linux de Azure o una máquina Linux que no sea de Azure (física o virtual).

    1. Configure los registros que se van a recopilar.

        - En la configuración avanzada del área de trabajo, agregue un tipo de registro personalizado, cargue un archivo de ejemplo y configúrelo como dirigido.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en la opción **Registros** de **Registros personalizados** de la tabla `SquidProxy_CL`.

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver algunas consultas de ejemplo útiles.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics. 

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Squid Proxy a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.