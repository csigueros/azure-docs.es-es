---
title: Embudos de Application Insights
description: Obtenga información sobre cómo usar Funnels para descubrir la forma en que los clientes interactúan con la aplicación.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: 0c0ab7da554b85f9adcde4d4991e1271f2acb433
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732009"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Descubra cómo los clientes usan la aplicación con los embudos de Application Insights.

Conocer la experiencia de los clientes es de suma importancia para su negocio. Si la aplicación implica varias fases, debe saber si la mayoría de los clientes avanzan a través de todo el proceso, o si finalizan el proceso en algún momento. La progresión a través de una serie de pasos en una aplicación web se conoce como *embudo*. Puede usar embudos de Application Insights para obtener información sobre los usuarios y supervisar las tasas de conversión paso a paso. 

## <a name="create-your-funnel"></a>Creación de un embudo
Antes de crear el embudo, decida la pregunta a la que quiere obtener respuesta. Por ejemplo, es posible que desee saber cuántos usuarios están visualizando la página principal, viendo un perfil de cliente y creando un vale. 

Para crear un embudo:

1. En la pestaña **Embudos**, seleccione **Edición**.
1. Elija su *Paso principal*.

     :::image type="content" source="./media/usage-funnels/funnel.png" alt-text="Captura de pantalla de la pestaña Embudo y selección de los pasos en la pestaña Edición" lightbox="./media/usage-funnels/funnel.png":::.

1. Para aplicar filtros al paso, seleccione  **Agregar filtros**, que aparecerá después de que elija un elemento para el paso principal.
1. A continuación, elija el *Segundo paso* y así sucesivamente.
1. Seleccione la pestaña **Vista** para ver los resultados del embudo

      :::image type="content" source="./media/usage-funnels/funnel-2.png" alt-text="Captura de pantalla de la pestaña de embudo de la pestaña de vista que muestra los resultados del primer y segundo paso." lightbox="./media/usage-funnels/funnel-2.png":::

1. Para guardar el embudo y verlo en otro momento, seleccione **Guardar** en la parte superior. Puede usar **Abrir** para abrir los embudos que haya guardado.

### <a name="funnels-features"></a>Características de Embudos

- Si la aplicación está muestreada, se mostrará un banner de muestreo. Al seleccionar el banner, se abre un panel contextual, en el que se explica cómo desactivar el muestreo. 
- Seleccione un paso para ver más detalles a la derecha. 
- En el gráfico del historial de conversiones se muestran tasas de conversión de los últimos 90 días. 
- Acceda a la herramienta de los usuarios para comprenderlos mejor. Puede usar filtros en cada paso. 

## <a name="next-steps"></a>Pasos siguientes
  * [Información general del uso](usage-overview.md)
  * [Usuarios, sesiones y eventos](usage-segmentation.md)
  * [Retención](usage-retention.md)
  * [Libros](../visualize/workbooks-overview.md)
  * [Adición de contexto de usuario](./usage-overview.md)
  * [Exportación a Power BI](./export-power-bi.md)