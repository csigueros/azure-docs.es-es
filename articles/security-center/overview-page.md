---
title: Panel principal o página de información general de Microsoft Defender for Cloud
description: Información sobre las características de la página de información general de Defender for Cloud
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 640b6fedd532174ff86e222a7479007b9e72b5bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437835"
---
# <a name="microsoft-defender-for-clouds-overview-page"></a>Página de información general de Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Al abrir Microsoft Defender for Cloud, la primera página que aparece es la página de información general. 

Este panel interactivo proporciona una vista unificada de la posición de seguridad de las cargas de trabajo de la nube híbrida. Además, muestra alertas de seguridad, información de cobertura y mucho más.

Puede seleccionar cualquier elemento de la página para obtener información más detallada.

:::image type="content" source="./media/overview-page/overview.png" alt-text="Captura de pantalla de la página de información general de Defender for Cloud." lightbox="./media/overview-page/overview.png":::
## <a name="features-of-the-overview-page"></a>Características de la página de información general

:::image type="content" source="./media/overview-page/top-bar-of-overview.png" alt-text="Captura de pantalla de la barra superior de la página de información general de Defender for Cloud.":::

### <a name="metrics"></a>Métricas
La **barra de menús superior** ofrece:
- **Suscripciones**: puede ver y filtrar la lista de suscripciones seleccionando este botón. Defender for Cloud ajustará la pantalla para que refleje la posición de seguridad de las suscripciones seleccionadas.
- **Novedades**: abre las [notas de la versión](release-notes.md) para que pueda mantenerse al día de nuevas características, correcciones de errores y funcionalidad en desuso.
- **Números de alto nivel** para las cuentas de nube conectadas, para mostrar el contexto de la información en los iconos principales siguientes. También se muestran el número de recursos evaluados, las recomendaciones activas y las alertas de seguridad. Seleccione el número de recursos evaluados para acceder al [inventario de recursos](asset-inventory.md). Obtenga más información sobre la conexión de las [cuentas de AWS](quickstart-onboard-aws.md) y los [proyectos de GCP](quickstart-onboard-gcp.md).

### <a name="feature-tiles"></a>Iconos de características
En el centro de la página, están los **iconos de características**, cada uno de los cuales está vinculado a una característica de perfil alto o a un panel dedicado:
- **Puntuación segura**: Defender for Cloud evalúa continuamente los recursos, suscripciones y la organización en busca de problemas de seguridad. A continuación, agrega todos los resultados a una sola puntuación para que pueda conocer de un vistazo la situación de la seguridad actual: cuanto mayor sea la puntuación, menor será el nivel de riesgo identificado. [Más información](secure-score-security-controls.md).
- **Protecciones de cargas de trabajo**: se trata de la plataforma de protección de cargas de trabajo en la nube (CWPP) integrada en Defender for Cloud para una protección inteligente y avanzada de las cargas de trabajo que se ejecutan en Azure, máquinas locales u otros proveedores de nube. Para cada tipo de recurso, existe un plan de Microsoft Defender correspondiente. El icono muestra la cobertura de los recursos conectados (para las suscripciones seleccionadas actualmente) y las alertas recientes, codificadas con colores según la gravedad. Aprenda más sobre las [características de seguridad mejoradas](enhanced-security-features-overview.md).
- **Cumplimiento normativo**: Defender for Cloud proporciona información detallada acerca de su posición de cumplimiento basada en la evaluación continua de su entorno de Azure. Defender for Cloud analiza los factores de riesgo del entorno según los procedimientos recomendados de seguridad. Estas valoraciones se asignan a los controles de cumplimiento desde un conjunto de estándares compatible. [Más información](regulatory-compliance-dashboard.md).
- **Firewall Manager**: este icono muestra el estado de los concentradores y redes desde [Azure Firewall Manager](../firewall-manager/overview.md).
- **Inventario**: la página de inventario de recursos de Microsoft Defender for Cloud permite ver en una sola página la posición de seguridad de los recursos que ha conectado a Microsoft Defender for Cloud. Todos los recursos con recomendaciones de seguridad sin resolver se muestran en el inventario. Si ha habilitado la integración con Microsoft Defender para punto de conexión y ha habilitado Microsoft Defender para servidores, también tendrá acceso a un inventario de software. El icono de la página de información general muestra de un vistazo el número total de recursos correctos e incorrectos (para las suscripciones seleccionadas actualmente). [Más información](asset-inventory.md).
- **Protección de la información**: en un grafo de este icono se muestran los tipos de recursos que se han examinado con [Azure Purview,](../purview/overview.md), que contienen datos confidenciales y que tienen recomendaciones y alertas pendientes. Siga el vínculo de **examen** para acceder a las cuentas de Azure Purview y configurar nuevos exámenes, o seleccione cualquier otra parte del icono para abrir el [inventario de recursos](asset-inventory.md) y ver los recursos según las clasificaciones de confidencialidad de datos de Purview. [Más información](information-protection.md).

### <a name="insights"></a>Información detallada
En el panel de **Insights** se ofrecen elementos personalizados para su entorno, incluidos los siguientes:
- Recursos más atacados
- Los [controles de seguridad](secure-score-security-controls.md) que tienen el máximo potencial de aumentar la puntuación de seguridad.
- Recomendaciones activas con la mayoría de los recursos afectados
- Entradas de blog recientes por expertos de Microsoft Defender for Cloud

## <a name="next-steps"></a>Pasos siguientes

En esta página se presenta la página de información general de Defender for Cloud. Para obtener información relacionada, consulte:

- [Explore y administre los recursos con las herramientas de administración e inventario de recursos.](asset-inventory.md)
- [Puntuación segura en Microsoft Defender for Cloud](secure-score-security-controls.md)
