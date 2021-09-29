---
title: Introducción a las plantillas
description: Aprenda a usar una plantilla predefinida para empezar a trabajar rápidamente con Azure Data Factory.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.author: susabat
author: ssabat
ms.custom: seo-lt-2019
ms.date: 09/09/2021
ms.openlocfilehash: c384985ae178f22c15c4a408d7a2cd42d0fbf416
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743383"
---
# <a name="templates"></a>Plantillas

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Las plantillas son canalizaciones de Azure Data Factory predefinidas que le permiten empezar a trabajar rápidamente con Data Factory. Las plantillas son útiles cuando no está familiarizado con Data Factory y desea empezar a trabajar rápidamente. Estas plantillas reducen el tiempo de desarrollo para la creación de proyectos de integración de datos, con lo que mejoran la productividad del desarrollador.

## <a name="create-data-factory-pipelines-from-templates"></a>Creación de canalizaciones de Data Factory a partir de plantillas

Puede empezar a crear una canalización de Data Factory a partir de una plantilla de las siguientes dos maneras:

1.  Seleccione **Plantillas de canalización** en la sección **Discover more** (Más información) de la página principal de Data Factory para abrir la galería de plantillas.

    :::image type="content" source="media/doc-common-process/home-page-pipeline-templates-tile.png" alt-text="Captura de pantalla que muestra cómo abrir la galería de plantillas desde la página principal de Data Factory.":::

1.  En la pestaña Author (Crear) del Explorador de recursos, seleccione **+** y, a continuación, seleccione **Pipeline from template** (Canalización a partir de una plantilla) para abrir la galería de plantillas.

    :::image type="content" source="media/solution-templates-introduction/templates-introduction-image-2.png" alt-text="Captura de pantalla que muestra cómo abrir la galería de plantillas desde la pestaña Crear.":::

## <a name="template-gallery"></a>Galería de plantillas

:::image type="content" source="media/solution-templates-introduction/templates-introduction-image-3.png" alt-text="Captura de pantalla que muestra la página Galería de plantillas.":::

### <a name="out-of-the-box-data-factory-templates"></a>Plantillas listas para usar de Data Factory

Data Factory usa plantillas de Azure Resource Manager para guardar las plantillas de canalización de las factorías de datos. Puede ver todas las plantillas de Resource Manager, junto con el archivo de manifiesto que se usa para las plantillas listas para usar de Data Factory, en el [repositorio de GitHub de Azure Data Factory oficial](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Las plantillas predefinidas proporcionadas por Microsoft incluyen, pero no se limitan, a los siguientes elementos:

-   Plantillas de copia:

    -   [Copia masiva desde base de datos](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copia de archivos nuevos por LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copiar varios contenedores de archivos entre almacenes basados en archivos](solution-template-copy-files-multiple-containers.md)

    -   [Traslado de archivos](solution-template-move-files.md)

    -   [Copia diferencial desde base de datos](solution-template-delta-copy-with-control-table.md)

    -   Copia de \<source\> a \<destination\>

        -   [De Amazon S3 a Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   De Google Big Query a Azure Data Lake Store Gen 2

        -   De HDF a Azure Data Lake Store Gen 2

        -   De Netezza a Azure Data Lake Store Gen 1

        -   De SQL Server local a Azure SQL Database

        -   De SQL Server local a Azure Synapse Analytics

        -   De Oracle local a Azure Synapse Analytics

-   Plantillas de SSIS

    -   Programación de Azure-SSIS Integration Runtime para ejecutar paquetes SSIS

-   Plantillas de transformación

    -   [ETL con Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Mis plantillas

También puede guardar una canalización como plantilla seleccionando **Guardar como plantilla** en la pestaña de la canalización.

:::image type="content" source="media/solution-templates-introduction/templates-introduction-image-4.png" alt-text="Captura de pantalla que muestra cómo guardar una canalización como plantilla.":::

Después de marcar la casilla **My templates** (Mis plantillas) en la página **Template gallery** (Galería de plantillas), puede ver las canalizaciones guardadas como plantillas en el panel derecho de esta página. 

:::image type="content" source="media/solution-templates-introduction/templates-introduction-image-5.png" alt-text="Captura de pantalla que muestra el panel Mis plantillas.":::

> [!NOTE]
> Para usar la característica Mis plantillas, tendrá que habilitar la integración de GIT. Se admiten Azure DevOps GIT y GitHub.
