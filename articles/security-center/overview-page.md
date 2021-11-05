---
title: Panel principal o página de información general de Microsoft Defender for Cloud
description: Información sobre las características de la página de información general de Defender for Cloud
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d0ab115e1e963283a2b8f7e7abd963653b9a39dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084107"
---
# <a name="microsoft-defender-for-clouds-overview-page"></a>Página de información general de Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Al abrir Microsoft Defender for Cloud, la primera página que aparece es la página de información general. 

Este panel interactivo proporciona una vista unificada de la posición de seguridad de las cargas de trabajo de la nube híbrida. Además, muestra alertas de seguridad, información de cobertura y mucho más.

Puede seleccionar cualquier elemento de la página para obtener información más detallada.

:::image type="content" source="media/overview-page/overview.png" alt-text="Página de información general de Defender for Cloud":::

## <a name="features-of-the-overview-page"></a>Características de la página de información general

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Barra superior de la página de información general de Defender for Cloud":::

La **barra de menús superior** ofrece:
- **Suscripciones**: puede ver y filtrar la lista de suscripciones seleccionando este botón. Defender for Cloud ajustará la pantalla para que refleje la posición de seguridad de las suscripciones seleccionadas.
- **Novedades**: abre las [notas de la versión](release-notes.md) para que pueda mantenerse al día de nuevas características, correcciones de errores y funcionalidad en desuso.
- **Números de alto nivel** para las cuentas de nube conectadas, para mostrar el contexto de la información en los iconos principales siguientes. También se muestran el número de recursos evaluados, las recomendaciones activas y las alertas de seguridad. Seleccione el número de recursos evaluados para acceder al [inventario de recursos](asset-inventory.md). Obtenga más información sobre la conexión de las [cuentas de AWS](quickstart-onboard-aws.md) y los [proyectos de GCP](quickstart-onboard-gcp.md).


En el centro de la página, hay **cuatro iconos centrales**, y cada uno de ellos está vinculado a un panel dedicado para obtener más información:
- **Puntuación segura**: Defender for Cloud evalúa continuamente los recursos, suscripciones y la organización en busca de problemas de seguridad. A continuación, agrega todos los resultados a una sola puntuación para que pueda conocer de un vistazo la situación de la seguridad actual: cuanto mayor sea la puntuación, menor será el nivel de riesgo identificado. [Más información](secure-score-security-controls.md).
- **Cumplimiento normativo**: Defender for Cloud proporciona información detallada acerca de su posición de cumplimiento basada en la evaluación continua de su entorno de Azure. Defender for Cloud analiza los factores de riesgo en su entorno de nube híbrida según los procedimientos recomendados de seguridad. Estas valoraciones se asignan a los controles de cumplimiento desde un conjunto de estándares compatible. [Más información](regulatory-compliance-dashboard.md).
- **Protecciones de cargas de trabajo**: se trata de la plataforma de protección de cargas de trabajo en la nube (CWPP) integrada en Defender for Cloud para una protección inteligente y avanzada de las cargas de trabajo que se ejecutan en Azure, máquinas locales u otros proveedores de nube. Para cada tipo de recurso, existe un plan de Microsoft Defender correspondiente. El icono muestra la cobertura de los recursos conectados (para las suscripciones seleccionadas actualmente) y las alertas recientes, codificadas con colores según la gravedad. Más información sobre los [planes de protección avanzados de Microsoft Defender](defender-for-cloud-introduction.md).
- **Firewall Manager**: este icono muestra el estado de los concentradores y redes desde [Azure Firewall Manager](../firewall-manager/overview.md). 


En el panel de **Insights** se ofrecen elementos personalizados para su entorno, incluidos los siguientes:
- Recursos más atacados
- Los controles de seguridad que tienen el máximo potencial de aumentar la puntuación de seguridad.
- Recomendaciones activas con la mayoría de los recursos afectados
- Entradas de blog recientes por expertos de Microsoft Defender for Cloud

## <a name="next-steps"></a>Pasos siguientes

En esta página se presenta la página de información general de Defender for Cloud. Para obtener información relacionada, consulte:

- [Explore y administre los recursos con las herramientas de administración e inventario de recursos.](asset-inventory.md)
- [Puntuación segura en Microsoft Defender for Cloud](secure-score-security-controls.md)
