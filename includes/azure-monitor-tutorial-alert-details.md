---
author: bwren
ms.author: bwren
ms.service: azure-monitor
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 9db1bd652fed38cb03bca6a590ea217d0fadf169
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349994"
---
Configure diferentes opciones para la regla de alertas en la sección **Detalles de la regla de alerta**.

- **Nombre de la regla de alerta**, que debe ser descriptivo, ya que se mostrará cuando se active la alerta. 
- Opcionalmente, proporcione una **descripción** que se incluya en los detalles de la alerta.
- **Suscripción** y **Grupo de recursos** donde se almacenará la regla de alerta. No es necesario que esté en el mismo grupo de recursos que el recurso que está supervisando.
- **Gravedad** de la alerta. La gravedad le permite agrupar alertas con una importancia relativa similar. Una gravedad de **Error** es adecuada para una máquina virtual que no responde.
- Mantenga activada la casilla **Habilitar la alerta tras la creación**.
- Mantenga activada la casilla **Resolver automáticamente las alertas**. Esto resolverá automáticamente la alerta cuando el valor de la métrica caiga por debajo del umbral. 
