---
title: Conexión del servidor HTTP de Apache a Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de servidor HTTP de Apache para extraer los registros de Apache en Azure Sentinel. Vea los datos de Apache en libros, cree alertas y mejore la investigación.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 6d93854d47c65b500f280107e0bf6d0af3f80681
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252593"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Conexión del servidor HTTP de Apache a Azure Sentinel

> [!IMPORTANT]
> El conector de servidor HTTP de Apache está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el servidor HTTP de Apache a Azure Sentinel. El conector de servidor HTTP de Apache le permite ingerir fácilmente los registros de servidor HTTP de Apache en Azure Sentinel, de modo que puede ver los datos en libros, consultarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. La integración entre el servidor HTTP de Apache y Azure Sentinel hace uso del procesamiento de archivos local por el agente de Log Analytics.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permiso de escritura en el área de trabajo de Azure Sentinel.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Configuración e integración de registros del servidor HTTP de Apache a través del agente de Log Analytics

Configure el servidor HTTP de Apache para enviar archivos de registro a su área de trabajo de Azure a través del agente de Log Analytics.
Configure el agente de Log Analytics para que lea los archivos de registro del servidor HTTP de Apache.

1. Para configurar la ubicación de los archivos de registro en el servidor HTTP de Apache, siga las instrucciones que se indican en https://httpd.apache.org/docs/2.4/logs.html.

1. En el menú de navegación de Azure Sentinel, seleccione **Data Connectors** (Conectores de datos) y, luego, elija **Apache HTTP Server (Preview)** (Servidor HTTP de Apache [versión preliminar]).

1. Seleccione **Open connector page** (Abrir página del conector).

1. Siga las instrucciones de la página del servidor HTTP de Apache.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida la conexión, los datos aparecen en Log Analytics en ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a conectar el servidor HTTP de Apache a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.