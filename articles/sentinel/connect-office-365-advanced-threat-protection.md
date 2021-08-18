---
title: Conexión de los datos de Microsoft Defender para Office 365 (anteriormente Protección contra amenazas avanzada de Office 365) con Azure Sentinel | Microsoft Docs
description: Ingiera datos de Microsoft Defender para Office 365 en Azure Sentinel para obtener visibilidad y crear escenarios de respuesta automatizados.
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
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: 7c4482f788014aec45f7c2e2ca9c4820bd80bb33
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780369"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Conexión de alertas de Microsoft Defender para Office 365 

> [!IMPORTANT]
>
> - **Microsoft Defender para Office 365** se conocía anteriormente como **Protección contra amenazas avanzada de Office 365**.
>
>     Puede ver que el nombre anterior todavía está en uso en el producto (incluido su conector de datos en Azure Sentinel) durante un período de tiempo.
>
> - La ingesta de alertas de Microsoft Defender para Office 365 se encuentra actualmente en versión preliminar pública. Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

[Microsoft Defender para Office 365 ](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) protege su organización contra las amenazas de día cero y otras amenazas avanzadas planteadas por malware desconocido en mensajes de correo electrónico, vínculos URL malintencionados y herramientas de colaboración. Al ingerir las alertas de Microsoft Defender para Office 365 en Azure Sentinel, podrá usar información sobre el correo electrónico, el uso compartido de archivos y las amenazas basadas en direcciones URL en las operaciones de seguridad. Después, puede analizar de manera más exhaustiva los eventos de seguridad en su organización y crear cuadernos de estrategias para obtener una respuesta efectiva e inmediata.

El conector importa las siguientes alertas:

- Se detectó un clic en una dirección URL potencialmente malintencionada 

- Mensajes de correo electrónico que contienen malware quitados después de la entrega

- Mensajes de correo electrónico que contienen direcciones URL de cebo quitadas después de la entrega 

- Correo electrónico notificado por el usuario como malware o cebo 

- Se detectaron patrones de envío de correo electrónico sospechosos 

- Se restringe al usuario el envío de correo electrónico 

Los clientes de Office pueden ver estas alertas en el **Centro de seguridad y cumplimiento de Office**.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel cuando habilite el conector.

- Debe ser administrador global o de seguridad en el inquilino del área de trabajo de Azure Sentinel.

- Debe tener una licencia válida para [Office 365 ATP Plan 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) (incluida con las licencias de Office 365 E5, Office 365 A5 y Microsoft 365 E5, y disponible para su compra por separado). 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Conexión con Microsoft Defender para Office 365

Si se implementa Microsoft Defender para Office 365 y se han configurado las directivas, las alertas se pueden ingerir fácilmente en Azure Sentinel.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. Seleccione **Microsoft Defender para Office 365** (es posible que se le siga llamando *Protección contra amenazas avanzada de Office 365*) en la galería de conectores y seleccione **Open connector page** (Abrir página del conector).

1. En la sección **Configuration** (Configuración), haga clic en **Connect** (Conectar). 

1. En la sección **Create incidents** (Crear incidentes), haga clic en **Enable** (Habilitar).

1. Para usar el esquema correspondiente para consultar alertas de Protección contra amenazas avanzada de Office 365, busque **SecurityAlert** y, en **Nombre de proveedor**, especifique **MDATP**.

1. Seleccione la pestaña **Pasos siguientes** para ver y usar los ejemplos de consultas y las plantillas de reglas de análisis que se incluyen en el conector de Microsoft Defender para Office 365.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Microsoft Defender para Office 365 con Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).