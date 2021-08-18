---
title: Conexión de datos de CyberArk EPV con Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo usar el conector de datos de CyberArk Enterprise Password Vault (EPV) para extraer sus registros en Azure Sentinel. Vea los datos de CyberArk EPV en los libros, cree alertas y mejore la investigación.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 3f18481821361feff525e3357543036784b21495
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253480"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>Conexión de CyberArk Enterprise Password Vault (EPV) con Azure Sentinel

> [!IMPORTANT]
> El conector de datos de CyberArk Enterprise Password Vault (EPV) de Azure Sentinel se encuentra actualmente en versión preliminar pública. Esta característica se proporciona sin un Acuerdo de Nivel de Servicio. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

El conector de Syslog de CyberArk permite conectar fácilmente todos los registros de la solución de seguridad de CyberArk con Azure Sentinel para ver los paneles, crear alertas personalizadas y mejorar la investigación. La integración entre CyberArk y Azure Sentinel hace uso del conector de datos de CEF para analizar y mostrar correctamente los mensajes de Syslog de CyberArk.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-cyberark-epv"></a>Configuración y conexión de CyberArk EPV

Los registros de CyberArk EPV se envían desde el almacén a un servidor de reenvío de registros basado en Linux (que ejecuta rsyslog o syslog-ng) con el agente de Log Analytics instalado, que exporta los registros a Azure Sentinel. Si no tiene ningún servidor de reenvío de registros de este tipo, consulte [estas instrucciones](connect-cef-agent.md) para instalar uno y ponerlo en funcionamiento.

1. En el portal de Azure Sentinel, haga clic en **Conectores de datos**, seleccione **Eventos de Enterprise Password Vault (EPV) de CyberArk (versión preliminar)** y, después, **Abrir página del conector**.

1. Siga las [instrucciones de CyberArk EPV](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) para configurar el envío de datos de syslog al servidor de reenvío de registros.

1. Valide la conexión y compruebe la ingesta de datos con [estas instrucciones](connect-cef-verify.md). Hasta que los registros empiecen a aparecer en Log Analytics, pueden transcurrir hasta 20 minutos.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, debajo de la sección **Azure Sentinel** en la tabla *CommonSecurityLog*.

Para consultar los registros de CyberArk EPV en Log Analytics, escriba `CommonSecurityLog` en la parte superior de la ventana de consulta.

## <a name="next-steps"></a>Pasos siguientes

En este documento aprendió a conectar los registros de CyberArk Enterprise Password Vault a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.