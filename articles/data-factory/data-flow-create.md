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
ms.openlocfilehash: 1ab0a225cc38290ef8f42eb8375ddc2f1f67c677
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735361"
---
# <a name="create-azure-data-factory-data-flow"></a>Creación de un flujo de datos con Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Los flujos de datos de asignación de ADF proporcionan una manera de transformar los datos a escala sin necesidad de programar. Puede diseñar un trabajo de transformación de datos en el diseñador de flujos de datos realizando una serie de transformaciones. Comience realice todas las transformaciones que desee en el origen y, después, continúe con los pasos de transformación de datos. A continuación, complete el flujo de datos con los receptores para enviar los resultados a un destino.

Primero, cree una factoría de datos V2 en Azure Portal. Después de crear la nueva factoría, seleccione "Abrir" en el icono "Abrir Azure Data Factory Studio" para iniciar la interfaz de usuario de Data Factory.

![Captura de pantalla en la que se muestra el panel Nueva factoría de datos con la opción V2 seleccionada en la versión](media/data-flow/v2portal.png "creación de un flujo de datos").

Cuando esté en la interfaz de usuario de Data Factory, puede usar flujos de datos de ejemplo. Los ejemplos están disponibles en la galería de plantillas de ADF. En ADF, seleccione el icono "Plantillas de canalización" en la sección "Detectar más" de la página principal y elija la categoría Data Flow en la galería de plantillas.

![Captura de pantalla en la que se muestra la pestaña Data Flow con la opción para transformar datos con Data Flow seleccionada](media/data-flow/template.png "creación de un flujo de datos").

Se le pedirá que escriba la información de la cuenta de Azure Blob Storage.

![Captura de pantalla en la que se muestra el panel para la transformación de datos con Data Flow, donde puede especificar entradas de usuario](media/data-flow/template2.png "creación de un flujo de datos 2").

[Aquí encontrará los datos usados en estos ejemplos](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Descargue los datos de ejemplo y guarde los archivos en las cuentas de Azure Blob Storage para poder ejecutar los ejemplos.

## <a name="create-new-data-flow"></a>Creación de un flujo de datos

Use el botón más situado en la interfaz de usuario de ADF para crear flujos de datos.

![Captura de pantalla en la que se muestra Data Flow seleccionado en el menú Recursos de fábrica](media/data-flow/newresource.png "Nuevo recurso").

## <a name="next-steps"></a>Pasos siguientes

Empiece a crear la transformación de datos con una [transformación de origen](data-flow-source.md).
