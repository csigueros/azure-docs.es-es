---
title: Administración de versiones de plantilla para las reglas de análisis programados en Azure Sentinel
description: Aprenda a administrar la relación entre las plantillas de reglas de análisis programados y las reglas creadas a partir de esas plantillas. Combine las actualizaciones de las plantillas con las reglas y revierta los cambios de las reglas a la plantilla original.
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
ms.date: 10/03/2021
ms.author: yelevin
ms.openlocfilehash: c08b346e5ab6dad939b441d60f9f68908568c840
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730554"
---
# <a name="manage-template-versions-for-your-scheduled-analytics-rules-in-azure-sentinel"></a>Administración de versiones de plantilla para las reglas de análisis programados en Azure Sentinel

> [!IMPORTANT]
>
> Esta característica se encuentra en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="introduction"></a>Introducción

Azure Sentinel incluye [plantillas de reglas de análisis](detect-threats-built-in.md) que se convierten en reglas activas mediante la creación eficaz de una copia de ellas, que es lo que sucede cuando se crea una regla a partir de una plantilla. En ese momento, sin embargo, la regla activa ya no está conectada a la plantilla. Si los ingenieros de Microsoft o cualquier otra persona realizan cambios en una plantilla de regla, las reglas creadas a partir de esa plantilla de antemano ***no*** se actualizan dinámicamente para que coincidan con la nueva plantilla.

Sin embargo, las reglas creadas a partir plantillas ***recuerdan*** las plantillas de las que proceden, lo que le ofrece dos ventajas:

- Si ha realizado cambios en una regla al crearla a partir de una plantilla (o en cualquier momento posterior), siempre puede revertir la regla a su versión original (como una copia de la plantilla).

- Puede recibir una notificación cuando se actualice una plantilla y tendrá la opción de actualizar las reglas a la nueva versión de sus plantillas o dejarlas tal y como están.

En este artículo se muestra cómo administrar estas tareas y qué se debe tener en cuenta. Estos procedimientos se aplican a las reglas de análisis **[programadas](detect-threats-built-in.md#scheduled)** creadas a partir de plantillas.

## <a name="discover-your-rules-template-version-number"></a>Conocer el número de versión de la plantilla de la regla

Con la implementación del control de versiones de plantilla, puede ver y realizar un seguimiento de las versiones de las plantillas de regla y las reglas creadas a partir de ellas. Las reglas cuyas plantillas se han actualizado muestran una notificación *Actualización disponible* junto al nombre de la regla.

1. En la hoja **Análisis**, seleccione la pestaña **Reglas activas**.

1. Seleccione cualquier regla de tipo **Programada**.  

    - Si la regla muestra el distintivo *Actualización disponible*, su panel de detalles tendrá un botón **Revisar y actualizar** junto al botón **Editar** (vea la imagen 1 en el paso siguiente).

    - Si la regla se creó a partir de una plantilla, pero no muestra el distintivo *Actualización disponible*, su panel de detalles tendrá un botón **Comparar con la plantilla** junto al botón **Editar** (vea las imágenes 2 y 3 en el paso siguiente).

    - Si solo hay un botón **Editar**, significa que la regla se creó desde cero, no desde una plantilla.

        :::image type="content" source="media/manage-analytics-rule-templates/see-rules-with-updated-template.png" alt-text="Captura de pantalla de la lista de reglas activas, con un distintivo que indica que hay disponible una actualización de plantilla." lightbox="media/manage-analytics-rule-templates/see-rules-with-updated-template.png":::

1. Desplácese hacia abajo hasta la parte inferior del panel de detalles, donde verá dos números de versión: la versión de la plantilla a partir de la que se creó la regla y la versión más reciente disponible de la plantilla. 

    :::image type="content" source="media/manage-analytics-rule-templates/see-template-versions.png" alt-text="Captura de pantalla del panel de detalles. Desplácese hacia abajo para ver los números de versión de la plantilla." border="false":::

    El número está en formato "1.0.0": versión principal, versión secundaria y compilación.  
    Por el momento, el número de compilación no está en uso y siempre será 0.

    - Una diferencia en el *número de versión principal* indica que se ha cambiado algo esencial en la plantilla, lo que podría afectar a la forma en que la regla detecta amenazas o incluso a su capacidad de funcionar por completo. Se trata de un cambio que querrá incluir en las reglas.

    - Una diferencia en el número de *versión secundaria* indica una pequeña mejora en la plantilla ( un cambio de color o algo similar) que sería recomendable, pero que no es fundamental para mantener la funcionalidad, la eficacia o el rendimiento de la regla. Se trata de un cambio que podría aceptar o rechazar con facilidad.

    > [!NOTE]
    > Las imágenes 2 y 3 anteriores muestran dos ejemplos de reglas creadas a partir de plantillas, donde la plantilla no se ha actualizado.
    > - La imagen 2 muestra una regla que tiene un número de versión para su plantilla actual. Esto indica que la regla se creó después de la implementación inicial del control de versiones de plantilla por parte de Azure Sentinel en octubre de 2021.
    > - En la imagen 3 se muestra una regla que no tiene una versión de plantilla actual. Esto indica que la regla se había creado antes de octubre de 2021. Si hay una versión de plantilla más reciente disponible, es probable que sea una versión más reciente de la plantilla que la que se usó para crear la regla.

## <a name="compare-your-active-rule-with-its-template"></a>Comparación de la regla activa con su plantilla

Elija una de las pestañas siguientes según la acción que desee realizar para ver las instrucciones de esa acción:

# <a name="update-template"></a>[Actualización de la plantilla](#tab/update)

Después de seleccionar una regla y determinar que quiere plantearse actualizarla, seleccione **Revisar y actualizar** en el panel de detalles (véase más arriba). Verá que el **Asistente para reglas de análisis** ahora tiene una pestaña **Comparar con la versión más reciente**.

En esta pestaña verá una comparación en paralelo entre las representaciones de YAML de la regla existente y la versión más reciente de la plantilla. 

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions.png" alt-text="Captura de pantalla de la pestaña &quot;Comparar con la versión más reciente&quot; en el Asistente para reglas de análisis.":::

> [!NOTE]
> La actualización de esta regla sobrescribirá la regla existente con la versión más reciente de la plantilla.
Se debe comprobar cualquier paso de automatización o lógica que haga referencia a la regla existente, en caso de que los nombres a los que se hacen referencia cambien. Además, las personalizaciones realizadas al crear la regla original (cambios en la consulta, la programación, la agrupación u otra configuración) se pueden sobrescribir.

### <a name="update-your-rule-with-the-new-template-version"></a>Actualización de la regla con la nueva versión de plantilla

- Si los cambios realizados en la nueva versión de la plantilla son aceptables y nada más en la regla original se ha visto afectado, seleccione **Revisar y actualizar** para validar y aplicar los cambios. 

- Si desea personalizar aún más la regla o volver a aplicar los cambios que podrían sobrescribirse, seleccione **Siguiente: Cambios personalizados**. Si elige esto, tendrá que recorrer las pestañas restantes del [Asistente para reglas de análisis](detect-threats-custom.md) para realizar esos cambios, después de lo cual validará y aplicará los cambios en la pestaña **Revisar y actualizar**.

- Si no desea realizar ningún cambio en la regla existente, sino mantener la versión de plantilla, simplemente salga del asistente seleccionando la X en la esquina superior derecha.

# <a name="revert-to-template"></a>[Reversión a la plantilla](#tab/revert)

Después de seleccionar una regla y determinar que desea revertirla a su versión original, seleccione **Comparar con la plantilla** en el panel de detalles (véase más arriba). Verá que el **Asistente para reglas de análisis** ahora tiene una pestaña **Comparar con la versión más reciente**.

En esta pestaña verá una comparación en paralelo entre las representaciones de YAML de la regla existente y la versión más reciente de la plantilla. Estos dos números de versión pueden ser los mismos, pero el lado izquierdo muestra la regla activa, incluidos los cambios que se han realizado en ella durante o después de su creación desde la plantilla, mientras que el lado derecho muestra la plantilla sin cambios.

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions-2.png" alt-text="Captura de pantalla de la pestaña &quot;Comparar con la versión más reciente&quot; en el Asistente para reglas de análisis.":::

> [!NOTE]
> La actualización de esta regla sobrescribirá la regla existente con la versión más reciente de la plantilla.
Se debe comprobar cualquier paso de automatización o lógica que haga referencia a la regla existente, en caso de que los nombres a los que se hacen referencia cambien. Además, las personalizaciones realizadas al crear la regla original (cambios en la consulta, la programación, la agrupación u otra configuración) se pueden sobrescribir.

### <a name="revert-your-rule-to-its-original-template-version"></a>Reversión de la regla a su versión de plantilla original

- Si desea revertir completamente esta regla a su versión original (una copia limpia de la plantilla), seleccione **Revisar y actualizar** para validar y aplicar los cambios. 

- Si desea personalizar la regla de forma diferente o volver a aplicar los cambios que podrían sobrescribirse, seleccione **Siguiente: Cambios personalizados**. Si elige esto, tendrá que recorrer las pestañas restantes del [Asistente para reglas de análisis](detect-threats-custom.md) para realizar esos cambios, después de lo cual validará y aplicará los cambios en la pestaña **Revisar y actualizar**.

- Si no desea realizar ningún cambio en la regla existente, simplemente salga del asistente seleccionando la X en la esquina superior derecha.

---

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a realizar un seguimiento de las versiones de las plantillas de reglas de análisis de Azure Sentinel y a revertir las reglas activas a las versiones de plantilla existentes, o bien a actualizarlas a otras nuevas. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Obtenga más información sobre las [reglas de análisis](detect-threats-built-in.md).
- Consulte más detalles sobre el [Asistente para reglas de análisis](detect-threats-custom.md).
