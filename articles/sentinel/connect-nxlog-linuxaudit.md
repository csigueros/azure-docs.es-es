---
title: Conexión de datos de NXLog LinuxAudit con Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de datos NXLog LinuxAudit para extraer registros de LinuxAudit en Azure Sentinel. Vea los datos de LinuxAudit en libros, cree alertas y mejore la investigación.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 6b10a0c4b1a655d998d9a418dde679c0c6b68b10
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252927"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>Conexión de NXLog LinuxAudit con Azure Sentinel

> [!IMPORTANT]
> El conector NXLog LinuxAudit se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

El conector [NXLog LinuxAudit ](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) permite exportar fácilmente los eventos de seguridad de Linux a Azure Sentinel en tiempo real, lo que le proporciona información sobre la actividad del servidor de nombres de dominio en toda su organización. El módulo NXLog LinuxAudit admite reglas de auditoría personalizadas y recopila registros sin AuditD ni ningún otro software de espacio de usuario. Las direcciones IP y los id. de grupo/usuario se resuelven en sus respectivos nombres, lo que permite que los registros de [auditoría de Linux](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) sean más inteligibles para los analistas de seguridad. La integración entre NXLog LinuxAudit y Azure Sentinel se facilita a través de la API REST.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>Configuración y conexión de NXLog LinuxAudit

NXLog puede configurarse para enviar eventos en formato JSON directamente a Azure Sentinel.

1. En el portal de Azure Sentinel, haga clic en **Conectores de datos** y seleccione el conector **NXLog LinuxAudit**.

1. Seleccione **Open connector page** (Abrir página del conector).

1. Siga las instrucciones paso a paso del tema de integración [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) de *NXLog User Guide* (Guía de usuario de NXLog) para configurar el reenvío a través de la API REST.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez que se establece una conexión correcta, los datos aparecen en **Registros**, en la sección **Registros personalizados**, en la tabla *LinuxAudit_CL*.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a usar NXLog LinuxAudit para ingerir eventos de seguridad de Linux en Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.