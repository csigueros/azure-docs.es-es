---
title: Azure Firewall Premium en Azure Portal
description: Obtenga información sobre Azure Firewall Premium en Azure Portal.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: cfb9f76751bbf5966b4aaa29e382a0946bf7cfd6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440436"
---
# <a name="azure-firewall-premium-in-the-azure-portal"></a>Azure Firewall Premium en Azure Portal


 Azure Firewall Premium es un firewall de próxima generación con funcionalidades que son necesarias en entornos de alta confidencialidad y regulados. Contiene las características siguientes:

- **Inspección de TLS**: descifra el tráfico saliente, procesa los datos y, luego, los cifra y los envía al destino.
- **IDPS**: sistema de detección y prevención de intrusiones en la red (IDPS) que permite supervisar la actividad malintencionada de la red, registrar información sobre esta actividad, notificarla y, opcionalmente, intentar bloquearla.
- **Filtrado de direcciones URL**: extiende la funcionalidad de filtrado de nombres de dominio completos de Azure Firewall para tener en cuenta una dirección URL completa. Por ejemplo, `www.contoso.com/a/c` en lugar de `www.contoso.com`.
- **Categorías web**: los administradores pueden permitir o denegar el acceso de los usuarios a categorías de sitios web como, por ejemplo, sitios web de apuestas, de redes sociales u otros.

Para más información, consulte [Características de Azure Firewall Prémium](premium-features.md).

## <a name="deploy-the-firewall"></a>Implementación del firewall

La implementación de una instancia de Azure Firewall Premium es similar a la implementación de la versión estándar de Azure Firewall:

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="Implementación en el portal":::

En **Firewall tier** (Nivel de firewall), seleccione **Premium** y, en **Firewall policy** (Directiva de firewall), seleccione una directiva Premium existente o cree una nueva.

## <a name="configure-the-premium-policy"></a>Configuración de la directiva prémium

La configuración de una directiva de firewall prémium es similar a la configuración de una directiva de firewall estándar. Con una directiva prémium, puede configurar las características prémium:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Implementación de directivas prémium":::

### <a name="rule-configuration"></a>Configuración de reglas

Al configurar reglas de aplicación en una directiva prémium, puede configurar características prémium adicionales:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Regla prémium":::

## <a name="next-steps"></a>Pasos siguientes

Para ver en acción las características de Azure Firewall Premium, consulte [Implementación y configuración de Azure Firewall Premium](premium-deploy.md).