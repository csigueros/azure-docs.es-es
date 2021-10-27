---
title: Uso de Azure Advisor para Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Obtenga más información sobre las ofertas de Azure Advisor para Azure Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 6c229f4afddc77e9b85f0dbd7fb4669facf8788e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129983995"
---
# <a name="azure-advisor-for-azure-communication-services"></a>Azure Advisor para Azure Communication Services

[Azure Advisor](../../advisor/advisor-overview.md) es un consultor en la nube personalizado que ayuda a seguir los procedimientos recomendados para optimizar las implementaciones de Azure. Azure Communication Services se incorpora a Azure Advisor y se publican recomendaciones sobre formas de optimizar los recursos de comunicación. Puede ver estas recomendaciones en [Azure Portal](https://portal.azure.com) en la [hoja Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview). Las recomendaciones se almacenan en el [registro de actividad de Azure](../../azure-monitor/essentials/platform-logs-overview.md) y puede configurar alertas para estas recomendaciones mediante [plantillas de ARM](../../advisor/advisor-alerts-arm.md) o el [portal](../../advisor/advisor-alerts-portal.md). 

## <a name="install-the-latest-sdks"></a>Instalación de los SDK más recientes

Para garantizar todas las correcciones y actualizaciones recientes, se recomienda mantenerse siempre al día con los SDK más recientes disponibles. Si hay disponible una versión más reciente de los SDK que usa, verá que aparece una recomendación en la categoría **Rendimiento** para actualizar al SDK más reciente.

![Ejemplo de Azure Advisor que muestra la recomendación de actualizar el SDK de chat.](./media/advisor-chat-sdk-update-example.png)

Los siguientes SDK son compatibles con esta característica, junto con todos los idiomas admitidos. Tenga en cuenta que esta característica solo enviará recomendaciones para las versiones principales más recientes disponibles con carácter general de los SDK. Las versiones beta o preliminar no desencadenarán ninguna recomendación ni alerta. Puede obtener más información sobre las [opciones de los SDK](./sdk-options.md) disponibles.

* Llamada (cliente)
* Chat
* SMS
* Identidad
* Números de teléfono
* Administración
* Network Traversal
* Automatización de llamadas

## <a name="next-steps"></a>Pasos siguientes

Puede que los siguientes documentos le resulten interesantes:

- [Registro y diagnósticos](./logging-and-diagnostics.md)
- [Métricas](./metrics.md)
