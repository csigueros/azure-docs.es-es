---
title: Creación de un flujo de datos de asignación
description: Cómo crear un flujo de datos de asignación de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 83b40121de72dc45582ded94580154f5124817b0
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061017"
---
# <a name="create-azure-data-factory-data-flow"></a>Creación de un flujo de datos con Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Los flujos de datos de asignación de ADF proporcionan una manera de transformar los datos a escala sin necesidad de programar. Puede diseñar un trabajo de transformación de datos en el diseñador de flujos de datos realizando una serie de transformaciones. Comience realice todas las transformaciones que desee en el origen y, después, continúe con los pasos de transformación de datos. A continuación, complete el flujo de datos con los receptores para enviar los resultados a un destino.

Primero, cree una factoría de datos V2 en Azure Portal. Después de crear la nueva factoría, seleccione "Abrir" en el icono "Abrir Azure Data Factory Studio" para iniciar la interfaz de usuario de Data Factory.

:::image type="content" source="media/data-flow/v2portal.png" alt-text="Captura de pantalla en la que se muestra el panel Nueva factoría de datos con la opción V2 seleccionada en la versión":::.

Cuando esté en la interfaz de usuario de Data Factory, puede usar flujos de datos de ejemplo. Los ejemplos están disponibles en la galería de plantillas de ADF. En ADF, seleccione el icono "Plantillas de canalización" en la sección "Detectar más" de la página principal y elija la categoría Data Flow en la galería de plantillas.

:::image type="content" source="media/data-flow/template.png" alt-text="Captura de pantalla en la que se muestra la pestaña Data Flow con la opción para transformar datos con Data Flow seleccionada":::.

Se le pedirá que escriba la información de la cuenta de Azure Blob Storage.

:::image type="content" source="media/data-flow/template2.png" alt-text="Captura de pantalla en la que se muestra el panel para la transformación de datos con Data Flow, donde puede especificar entradas de usuario":::.

[Aquí encontrará los datos usados en estos ejemplos](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Descargue los datos de ejemplo y guarde los archivos en las cuentas de Azure Blob Storage para poder ejecutar los ejemplos.

## <a name="create-new-data-flow"></a>Creación de un flujo de datos

Use el botón más situado en la interfaz de usuario de ADF para crear flujos de datos.

:::image type="content" source="media/data-flow/newresource.png" alt-text="Captura de pantalla en la que se muestra Data Flow seleccionado en el menú Recursos de fábrica":::.

## <a name="next-steps"></a>Pasos siguientes

Empiece a crear la transformación de datos con una [transformación de origen](data-flow-source.md).
