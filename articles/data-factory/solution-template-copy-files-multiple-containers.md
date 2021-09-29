---
title: Copia de archivos de varios contenedores
description: Aprenda a usar una plantilla de solución para copiar archivos de varios contenedores con Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: d1d1ffaecdf57817e5ef7be48791d0cb50ca9b74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743550"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Copia de varias carpetas con Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este artículo se describe una plantilla de solución en la que puede usar varias actividades de copia para copiar contenedores o carpetas entre almacenes basados en archivos, donde cada actividad de copia se supone que copie un solo contenedor o carpeta. 

> [!NOTE]
> Si desea copiar archivos desde un único contenedor, es más eficaz utilizar la [herramienta Copiar datos](copy-data-tool.md) para crear una canalización con una sola actividad de copia. La plantilla de este artículo es más de lo que necesita para esa situación.

## <a name="about-this-solution-template"></a>Acerca de esta plantilla de solución

Esta plantilla enumera las carpetas de una carpeta principal determinada en el almacén de almacenamiento de origen. A continuación, copia cada una de estas carpetas en el almacén de destino.

La plantilla contiene tres actividades:
- **GetMetadata** examina el almacén de almacenamiento de origen y obtiene la lista de subcarpetas de una carpeta principal determinada.
- **ForEach** obtiene la lista de subcarpetas de la actividad **GetMetadata** y, a continuación, recorre en iteración la lista y pasa cada carpeta a la actividad Copy.
- **Copy** copia cada carpeta desde el almacén de almacenamiento de origen al almacén de destino.

La plantilla define los parámetros siguientes:
- *SourceFileFolder* forma parte de la ruta de acceso de la carpeta principal del almacén de origen de datos: *SourceFileFolder/SourceFileDirectory*, donde puede obtener una lista de las subcarpetas. 
- *SourceFileDirectory* forma parte de la ruta de acceso de la carpeta principal del almacén de origen de datos: *SourceFileFolder/SourceFileDirectory*, donde puede obtener una lista de las subcarpetas. 
- *DestinationFileFolder* forma parte de la ruta de acceso de la carpeta principal: *DestinationFileFolder/DestinationFileDirectory* donde se copiarán los archivos en el almacén de destino. 
- *DestinationFileDirectory* forma parte de la ruta de acceso de la carpeta principal: *DestinationFileFolder/DestinationFileDirectory* donde se copiarán los archivos en el almacén de destino. 

Si quiere copiar varios contenedores debajo de carpetas raíz entre almacenes de almacenamiento, puede especificar los cuatro parámetros como */* . Al hacerlo, replicará todo lo que haya entre los almacenes de almacenamiento.

## <a name="how-to-use-this-solution-template"></a>Uso de esta plantilla de solución

1. Vaya a la plantilla **Copy multiple files containers between File Stores**(Copiar varios contenedores de archivos entre almacenes de archivos). Cree una **nueva** conexión con el almacén de almacenamiento de origen. El almacén de almacenamiento de origen es desde donde se van a copiar los archivos de varios contenedores.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png" alt-text="Creación de una nueva conexión con el origen":::

2. Cree una **nueva** conexión con el almacén de almacenamiento de destino.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png" alt-text="Creación de una nueva conexión con el destino":::

3. Seleccione **Usar esta plantilla**.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png" alt-text="Uso de esta plantilla":::
    
4. Verá la canalización, como en el ejemplo siguiente:

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png" alt-text="Visualización de la canalización":::

5. Seleccione **Depurar**, escriba los **parámetros** y, a continuación, seleccione **Finalizar**.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png" alt-text="Ejecución de la canalización":::

6. Revise el resultado.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png" alt-text="Revisión del resultado":::

## <a name="next-steps"></a>Pasos siguientes

- [Copia masiva de una base de datos con una tabla de control](solution-template-bulk-copy-with-control-table.md)

- [Copia de archivos de varios contenedores con Azure Data Factory](solution-template-copy-files-multiple-containers.md)
