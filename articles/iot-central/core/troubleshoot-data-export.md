---
title: Solución de problemas con exportaciones datos en Azure IoT Central | Microsoft Docs
description: Solución de problemas con las exportaciones de datos en IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/26/2021
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 0fd283c1f3740e3e06f7a41cc66874596159e8f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093789"
---
# <a name="troubleshoot-issues-with-data-exports-from-your-azure-iot-central-application"></a>Solución de problemas con las exportaciones de datos desde la aplicación de Azure IoT Central

Este documento le ayuda a averiguar por qué los datos de la aplicación IoT Central no llegan a su destino previsto o no llegan con el formato correcto.

## <a name="managed-identity-issues"></a>Problemas de la identidad administrada

Usa una identidad administrada para autorizar la conexión a un destino de exportación. Los datos no llegan al destino de exportación.

Antes de configurar o habilitar el destino de exportación, asegúrese de completar los pasos siguientes:

- Habilite la identidad administrada de la aplicación.
- Configure los permisos para la identidad administrada.
- Configure las redes virtuales, los puntos de conexión privados y las directivas de firewall.

Para más información, consulte [Exportación de datos](howto-export-data.md?tabs=managed-identity).

## <a name="next-steps"></a>Pasos siguientes

Si necesita más ayuda, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/community/). Como alternativa, puede abrir una [incidencia de soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support).

Para más información, consulte [Opciones de ayuda y soporte técnico de IoT de Azure](../../iot-fundamentals/iot-support-help.md).
