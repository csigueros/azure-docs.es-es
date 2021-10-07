---
title: Creación visual
titleSuffix: Azure Data Factory & Azure Synapse
description: Información acerca de cómo utilizar la creación visual de Azure Data Factory y Synapse Analytics
ms.service: data-factory
ms.subservice: authoring
ms.custom: synapse
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 09/09/2021
ms.openlocfilehash: a9ee177015896e314d140166c2477e92fa9edf8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788098"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Creación visual de Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La experiencia de la interfaz de usuario (UI) de Azure Data Factory y Synapse Analytics le permite crear e implementar visualmente recursos para la factoría de datos o para canalizaciones de Synapse sin tener que escribir código. Puede arrastrar y colocar las actividades en un lienzo de canalización, realizar ejecuciones de prueba, depurar de forma iterativa e implementar y supervisar ejecuciones de canalizaciones.

Actualmente, la interfaz de usuario de Azure Data Factory solo se admite en Microsoft Edge y Google Chrome.

## <a name="authoring-canvas"></a>Lienzo de creación

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
Para abrir el **lienzo de creación**, haga clic en el icono de lápiz. 

:::image type="content" source="media/author-visually/authoring-canvas.png" alt-text="Lienzo de creación":::

A continuación, cree las canalizaciones, actividades, conjunto de datos y flujos de datos que componen la factoría. Del mismo modo, los servicios vinculados, los desencadenadores y los entornos de ejecución de integración se pueden editar en la [pestaña Administrar](#management-hub). Para empezar a crear una canalización mediante el lienzo de creación, consulte [Copiar datos mediante la actividad de copia](tutorial-copy-data-portal.md). 

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
Para abrir el **lienzo de creación**, haga clic en el icono de canalización o integración. 

:::image type="content" source="media/author-visually/authoring-canvas-synapse.png" alt-text="Lienzo de creación":::

Aquí, puede crear las canalizaciones y las actividades en Synapse. Del mismo modo, los flujos de datos usados por las canalizaciones se pueden editar en la pestaña Desarrollar, y los servicios vinculados, los desencadenadores y los entornos de ejecución de integración asociados se pueden editar en la [pestaña Administrar](#management-hub). Para empezar a crear una canalización mediante el lienzo de creación, consulte [Copiar datos mediante la actividad de copia](tutorial-copy-data-portal.md). 

---

La experiencia de creación visual predeterminada funciona directamente con el servicio. La integración de Git de Azure Repos o GitHub también es compatible para admitir el control del código fuente y la colaboración para trabajar en las canalizaciones. Para más información sobre las diferencias entre estas experiencias de creación, consulte [Control de código fuente](source-control.md).

### <a name="properties-pane"></a>Propiedades, panel

En el caso de los recursos de nivel superior, como canalizaciones, conjuntos de datos y flujos de datos, las propiedades de alto nivel se pueden editar en el panel Propiedades del lado derecho del lienzo. El panel Propiedades contiene propiedades como el nombre, la descripción, las anotaciones y otras propiedades de alto nivel. Los subrecursos, como las actividades de canalización y las transformaciones de flujo de datos, se editan mediante el panel situado en la parte inferior del lienzo. 

:::image type="content" source="media/author-visually/properties-pane.png" alt-text="Propiedades, panel":::

El panel Propiedades solo se abre de forma predeterminada en la creación de recursos. Para editarlo, haga clic en el icono del panel Propiedades situado en la esquina superior derecha del lienzo.

### <a name="related-resources"></a>Recursos relacionados

En el panel de propiedades, si selecciona la pestaña **Relacionado**, podrá ver los recursos que dependen del recurso seleccionado. Aparecerán todos los recursos que hagan referencia al recurso actual.

:::image type="content" source="media/author-visually/related-resources.png" alt-text="Recursos relacionados":::

Por ejemplo, en la imagen anterior, hay una canalización y dos flujos de datos que utilizan el conjunto de datos que está seleccionado actualmente.

## <a name="management-hub"></a>Centro de administración

El centro de administración, al que se accede en la pestaña *Administrar* de la UI, es un portal que hospeda acciones de administración globales para el servicio. Aquí puede administrar las conexiones a almacenes de datos y procesos externos, la configuración del control de código fuente y la configuración del desencadenador. Para obtener más información, consulte las capacidades del [centro de administración](author-management-hub.md).

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="Administración de servicios vinculados":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="Administración de servicios vinculados":::

---

## <a name="expressions-and-functions"></a>Expresiones y funciones

Se pueden usar expresiones y funciones en lugar de valores estáticos para especificar muchas propiedades en el servicio.

Para especificar una expresión para un valor de propiedad, seleccione **Agregar contenido dinámico** o haga clic en **Alt + P** mientras se centra en el campo.

:::image type="content" source="media/author-visually/dynamic-content-1.png" alt-text="Incorporación de contenido dinámico":::

Así se abre el **Generador de expresiones**, donde puede compilar expresiones a partir de variables del sistema admitidas, salidas de actividad, funciones y variables o parámetros especificados por el usuario. 

:::image type="content" source="media/author-visually/dynamic-content-2.png" alt-text="Generador de expresiones":::

Para más información sobre el lenguaje de expresión, consulte [Expresiones y funciones](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Envío de comentarios

Seleccione **Comentarios** para comentar sobre las características o para notificar a Microsoft sobre los problemas con la herramienta:

:::image type="content" source="media/author-visually/provide-feedback.png" alt-text="Comentarios":::

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la supervisión y la administración de canalizaciones, consulte el artículo [Supervisión y administración de canalizaciones mediante programación](monitor-programmatically.md).
