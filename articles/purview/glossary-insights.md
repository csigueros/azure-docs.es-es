---
title: Informes de glosario sobre los datos con información detallada de Purview
description: En esta guía paso a paso se describe cómo ver y usar los informes de glosario con información detallada de Purview sobre los datos.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 315cfdc60de77ab4d6345c5d579351049c5129a2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218945"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Información detallada de glosario sobre los datos de Azure Purview

En esta guía paso a paso se describe cómo acceder a los informes de información detallada de glosario de Purview en los datos, y cómo verlos y filtrarlos.

> [!IMPORTANT]
> Azure Purview Insights se encuentra actualmente en versión preliminar. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

En esta guía paso a paso, aprenderá a:

> [!div class="checklist"]
> - Consultar información detallada desde la cuenta de Purview.
> - Obtener una visión general de los datos.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar a trabajar con la información detallada de Purview, asegúrese de que ha completado los pasos siguientes:

- Ha configurado los recursos de Azure y ha rellenado la cuenta con datos.

- Ha configurado y realizado un examen en el tipo de origen.

- Ha configurado un glosario y asociado recursos a los términos del glosario.

Para más información, consulte [Administración de orígenes de datos en Azure Purview](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Uso de información detallada de glosario de Purview

En Azure Purview, puede crear términos de glosario y asociarlos a los recursos. Más adelante, puede ver la distribución del glosario en la información detallada del glosario. Esto le indica el estado del glosario según los términos asociados a los recursos. También indica los términos por estado y la distribución de los roles por número de usuarios.

**Visualización de información detallada de glosarios:**

1. Vaya a la [pantalla de la instancia de **Azure Purview** en Azure Portal](https://aka.ms/purviewportal) y seleccione su cuenta de Purview.

1. En la página **Información general**, en la sección **Inicio**, seleccione el icono de cuenta **Open Purview Studio** (Abrir Purview Studio).

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Inicio de Purview desde Azure Portal":::

1. En la página **Inicio** de Purview, seleccione **Información** en el menú de la izquierda.

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Visualización de información detallada en Azure Portal":::

1. En el área **Información** seleccione **Glosario** para mostrar el informe **Glossary insights** (Información del glosario) de Purview.

**Glossary Insights** (Glosario de información detallada) le proporciona como usuario empresarial información valiosa para mantener un glosario bien definido para su organización.

1. El informe comienza con las **KPI de alto nivel** que muestran el **_total de términos_ *_ en la cuenta de Purview, los _* _Términos aprobados sin recursos_ *_ y los _* _Términos expirados con recursos_**. Cada uno de estos valores le ayudará a identificar el estado de su glosario.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="Visualización de la KPI de la información del glosario"::: 


2. La sección **Snapshot of terms** (Instantánea de términos) que se mostró anteriormente, muestra el estado de la condición como **_Borrador_ *_, _* _Aprobada_ *_, _* _Alerta_ *_ y _* _Expirado_**  para los términos con recursos y los términos sin recursos.

3. Seleccione **Ver más** para ver los nombres de los términos con varios estados y más detalles sobre los **_Administradores_ *_ y los _* _Expertos_**. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="Instantánea de los términos con y sin recursos":::  

4. Al seleccionar "Ver más" en la opción de ***Términos aprobados con recursos** _, la información le permitirá navegar a la página de detalles del término _ *Glosario**, desde donde puede ir a la lista de activos con los términos adjuntos. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="Información detallada del glosario"::: 

4. En la página Detalles del glosario, podrá ver una distribución de **términos incompletos**, organizados por el tipo de información que falta. En el gráfico se muestra el recuento de términos con los campos **_Missing definition_ *_ (Definición que falta), _* _Missing Expert_ *_ (Experto que falta), _* _Missing steward_ *_ (Administrador que falta) y _* _Missing multiple_** (Faltan varios elementos).

1. Seleccione ***Ver más** _ en _ *Términos incompletos** para ver los términos que tienen la información que falta. Puede ir a la página de detalles de Términos de glosario para especificar la información que falta y asegurarse de que los términos del glosario estén completos.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo crear un término de glosario mediante el [Glosario](./how-to-create-import-export-glossary.md).
