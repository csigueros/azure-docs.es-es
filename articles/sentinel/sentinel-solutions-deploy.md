---
title: Detección e implementación centralizadas de soluciones y contenido de serie de Microsoft Sentinel
description: En este artículo se muestra cómo los clientes pueden encontrar e implementar fácilmente herramientas de análisis de datos, empaquetadas junto con conectores de datos y otro contenido.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c1e10e0ee346358b095d6eb5bec4fafab1f56799
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711705"
---
# <a name="centrally-discover-and-deploy-microsoft-sentinel-out-of-the-box-content-and-solutions-public-preview"></a>Detección e implementación centralizadas de soluciones y contenido de serie de Microsoft Sentinel (versión preliminar pública)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Las soluciones y el centro de contenido de Microsoft Sentinel se encuentran en **VERSIÓN PRELIMINAR**, igual que todos los paquetes de soluciones individuales. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

El centro de contenido de Microsoft Sentinel proporciona acceso a contenido y soluciones de serie (integrados) de Microsoft Sentinel, que incluyen contenido para satisfacer las necesidades de producto, dominio o sector de un extremo a otro.

En este artículo se explica cómo instalar soluciones en el área de trabajo de Microsoft Sentinel, lo que permite que el contenido de estas esté disponible para su uso.

- Encuentre sus soluciones en el centro de contenido a partir de sus estados, el contenido incluido y el servicio de asistencia, entre otros.

- Instale la solución que quiera en su área de trabajo cuando encuentre una que se ajuste a las necesidades de su organización. Debe mantenerla actualizada con los cambios más recientes.

> [!TIP]
> Si es un asociado y quiere crear una solución propia, vea la [guía de compilación de soluciones de Microsoft Sentinel](https://aka.ms/sentinelsolutionsbuildguide) para crear y publicar soluciones.
>
## <a name="find-a-solution"></a>Búsqueda de una solución

1. En el menú de navegación de Microsoft Sentinel, en **Administración de contenido**, seleccione **Centro de contenido (versión preliminar)** .

1. La página **Centro de contenido** muestra una cuadrícula de soluciones en la que se pueden hacer búsquedas y aplicar filtros.

    Filtre la lista mostrada seleccionando valores específicos de los filtros o escribiendo una parte del nombre o la descripción de una solución en el campo **Buscar**.

    Para obtener más información, vea [Categorías del contenido y las soluciones integradas de Microsoft Sentinel](sentinel-solutions.md#microsoft-sentinel-out-of-the-box-content-and-solution-categories).

    > [!TIP]
    > Si una solución que ha implementado tiene alguna actualización desde que la implementó, se mostrará un triángulo naranja que indica que hay actualizaciones que implementar, indicadas en el triángulo azul de la parte superior de la página.
    >

En cada solución de la cuadrícula se muestran las categorías aplicadas a la solución y los tipos de contenido incluidos en ella.

Por ejemplo, en la imagen siguiente, la solución **Cisco Umbrella** muestra la categoría **Security - Others** (Seguridad: otros), y que esta solución incluye 10 reglas de análisis, 11 consultas de búsqueda, un analizador y tres cuadernos de estrategias, entre otros.

:::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="Captura de pantalla del centro de contenido de Microsoft Sentinel." lightbox="./media/sentinel-solutions-deploy/solutions-list.png":::

## <a name="install-or-update-a-solution"></a>Instalación o actualización de una solución

1. En el centro de contenido, seleccione una solución para ver más información a la derecha. Después, seleccione **Instalar** o **Actualizar**, si necesita actualizaciones. Por ejemplo:

1. En la página de detalles de la solución, seleccione **Crear** o **Actualizar** para iniciar el asistente para soluciones. En la pestaña **Datos básicos** del asistente, escriba la suscripción, el grupo de recursos y el área de trabajo en la que quiera implementar la solución. Por ejemplo:

    :::image type="content" source="media/sentinel-solutions-deploy/wizard-basics.png" alt-text="Captura de pantalla de un asistente de instalación de una solución, con la pestaña Datos básicos":::.

1. Seleccione **Siguiente** para desplazarse por las pestañas restantes (correspondientes a los componentes incluidos en la solución), donde puede obtener información sobre cada uno de los componentes de contenido y, en algunos casos, configurarlos.

    > [!NOTE]
    > Las pestañas que se muestran se corresponden con el contenido que ofrece la solución. Las distintas soluciones pueden tener distintos tipos de contenido, así que es posible que no vea las mismas pestañas en todas las soluciones.
    >
    > También es posible que se le pida que escriba las credenciales en un servicio de terceros para que Microsoft Sentinel pueda autenticarse en los sistemas. Por ejemplo, con los cuadernos de estrategias, es recomendable que efectúe las acciones de respuesta tal como indica el sistema.
    >

1. Por último, en la pestaña **Revisar y crear**, espere el mensaje `Validation Passed` y, después, seleccione **Crear** o **Actualizar** para implementar la solución. También puede seleccionar el vínculo **Descargar una plantilla para la automatización** para implementar la solución como código.

Para obtener más información, vea [Catálogo del centro de contenido de Microsoft Sentinel](sentinel-solutions-catalog.md) y [Búsqueda del conector de datos de Microsoft Sentinel](data-connectors-reference.md).

## <a name="find-the-support-model-for-your-solution"></a>Búsqueda del modelo de soporte técnico para su solución

Cada solución muestra los detalles sobre su modelo de soporte técnico en el panel de detalles correspondiente, en la casilla **Soporte técnico**, donde se muestra **Microsoft** o el nombre de un partner. Por ejemplo:

:::image type="content" source="media/sentinel-solutions-deploy/find-support-details.png" alt-text="Captura de pantalla de dónde se encuentra el modelo de soporte técnico de su solución." lightbox="media/sentinel-solutions-deploy/find-support-details.png":::

Cuando se ponga en contacto con el soporte técnico, puede que necesite otros detalles sobre su solución, como el editor, el proveedor y los valores del id. del plan. Encontrará esta información en la página de detalles de la solución, en la pestaña **Información de uso y soporte técnico**. Por ejemplo:

:::image type="content" source="media/sentinel-solutions-deploy/usage-support.png" alt-text="Captura de pantalla de los detalles de uso y soporte técnico de una solución.":::

## <a name="next-steps"></a>Pasos siguientes

En este documento ha obtenido información sobre las soluciones de Microsoft Sentinel y ha aprendido a buscar e implementar contenido integrado.

- Más información sobre las [soluciones de Microsoft Sentinel](sentinel-solutions.md).
- Vea el [Catálogo de soluciones de Microsoft Sentinel](sentinel-solutions-catalog.md) completo.
