---
title: Aplicación automática de etiquetas de confidencialidad a los datos en Azure Purview
description: Obtenga información acerca de cómo crear etiquetas de confidencialidad y aplicarlas automáticamente a los datos durante un examen.
author: ajaykar
ms.author: ajaykar
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 98c1dba49283cd1f7a9e1c4c748af37b9a4fac02
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219389"
---
# <a name="how-to-automatically-apply-sensitivity-labels-to-your-data-in-azure-purview"></a>Aplicación automática de etiquetas de confidencialidad a los datos en Azure Purview

## <a name="create-or-extend-existing-sensitivity-labels-to-azure-purview"></a>Creación o extensión de etiquetas de confidencialidad existentes a Azure Purview

> [!IMPORTANT]
> Las etiquetas de confidencialidad de Azure Purview se encuentran actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>

Si aún no tiene etiquetas de confidencialidad, deberá crearlas y ponerlas a disposición de Azure Purview. También se pueden modificar las etiquetas de confidencialidad existentes para que estén disponibles para Azure Purview.

### <a name="step-1-licensing-requirements"></a>Paso 1: Requisitos de concesión de licencias

Las etiquetas de confidencialidad se crean y administran en el Centro de cumplimiento de Microsoft 365. Para crear etiquetas de confidencialidad para su uso en Azure Purview, debe tener una licencia Microsoft 365 activa que ofrezca la ventaja de aplicar automáticamente etiquetas de confidencialidad.

Para la lista completa de licencias, consulte [Etiquetas de confidencialidad en preguntas más frecuentes de Azure Purview](sensitivity-labels-frequently-asked-questions.yml). Si aún no tiene la licencia necesaria, puede registrarse para obtener una prueba de [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion).

### <a name="step-2-consent-to-use-sensitivity-labels-in-azure-purview"></a>Paso 2: Consentimiento para usar etiquetas de confidencialidad en Azure Purview

Los pasos siguientes permiten extender las etiquetas de confidencialidad y habilitarlas a fin de que estén disponibles para su uso en Azure Purview, donde puede aplicar las etiquetas de confidencialidad en archivos y columnas de bases de datos.

1. En Microsoft 365, vaya a la página **Information Protection**.</br>
   Si ha aprovisionado recientemente la suscripción para Information Protection, la página de **Information Protection** puede tardar unas horas en mostrarse.
1. En el área **Extend labeling to assets in Azure Purview** (Extender la etiqueta a los recursos de Azure Purview), seleccione el botón **Activar** y, a continuación, seleccione **Sí** en el cuadro de diálogo de confirmación que se muestra.

Por ejemplo:

:::image type="content" source="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-small.png" alt-text="Selección de &quot;Activar&quot; para extender las etiquetas de confidencialidad a Purview" lightbox="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview.png":::

Después de extender las etiquetas a los recursos de Azure Purview, todas las etiquetas de confidencialidad publicadas están disponibles para su uso en Purview.

### <a name="step-3-create-or-modify-existing-label-to-automatically-label-content"></a>Paso 3: Creación o modificación de etiquetas existentes para etiquetar automáticamente el contenido

**Para crear etiquetas de confidencialidad o modificar las ya existentes**:

1. Abra el [Centro de seguridad y cumplimiento de Microsoft 365](https://compliance.microsoft.com/).

1. En **Soluciones**, seleccione **Information Protection** y, a continuación, seleccione **Crear una etiqueta**.

    :::image type="content" source="media/how-to-automatically-label-your-content/create-sensitivity-label-full-small.png" alt-text="Creación de etiquetas de confidencialidad en el Centro de cumplimiento de Microsoft 365" lightbox="media/how-to-automatically-label-your-content/create-sensitivity-label-full.png":::

1. Asigne un nombre a la etiqueta. En **Define the scope for this label** (Definir el ámbito de esta etiqueta):

    - En todos los casos, seleccione **Azure Purview assets** (Recursos de Azure Purview).
    - Para etiquetar archivos, seleccione también **Files & emails** (Archivos y mensajes de correo electrónico). Esta opción no es necesaria para etiquetar solo recursos de base de datos.

    :::image type="content" source="media/how-to-automatically-label-your-content/create-label-scope-small.png" alt-text="Etiquetado automático en el Centro de cumplimiento de Microsoft 365" lightbox="media/how-to-automatically-label-your-content/create-label-scope.png":::

1. Siga el resto de los mensajes para la configuración de la etiqueta.

    En concreto, defina reglas de etiquetado automático para archivos y columnas de bases de datos:

    - [Definición de reglas de etiquetado automático para archivos](#autolabeling-for-files)
    - [Definición de reglas de etiquetado automático para columnas de base de datos](#autolabeling-for-database-columns)

    Para más información sobre las opciones de configuración, consulte [Qué pueden hacer las etiquetas de confidencialidad](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) en la documentación de Microsoft 365.

1. Repita los pasos indicados anteriormente para crear más etiquetas.

    Para crear una subetiqueta, seleccione la etiqueta primaria > **...**  > **Más acciones** > **Agregar subetiqueta**.

1. Para modificar las etiquetas existentes, vaya a **Information Protection** > **Etiquetas** y seleccione la etiqueta.

    A continuación, seleccione **Editar etiqueta** para abrir de nuevo la configuración **Edit sensitivity label** (Editar etiqueta de confidencialidad) con todas las opciones que se definieron al crear la etiqueta.

    :::image type="content" source="media/how-to-automatically-label-your-content/edit-sensitivity-label-full-small.png" alt-text="Edición de una etiqueta de confidencialidad existente" lightbox="media/how-to-automatically-label-your-content/edit-sensitivity-label-full.png":::

1. Cuando haya terminado de crear todas las etiquetas, asegúrese de ver el orden de las etiquetas y reordenarlas según sea necesario.

    Para cambiar el orden de una etiqueta, seleccione **...** **> Más acciones** > **Subir** o **Bajar**.

    Para obtener más información, consulte [Prioridad de etiqueta (el orden importa)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) en la documentación de Microsoft 365.

#### <a name="autolabeling-for-files"></a>Etiquetado automático de archivos

Defina las reglas de etiquetado automático para los archivos cuando cree o edite la etiqueta.

En la página **Etiquetado automático para aplicaciones de Office**, habilite el **etiquetado automático para las aplicaciones de Office** y, a continuación, defina las condiciones en las que quiere que la etiqueta se aplique automáticamente a los datos.

Por ejemplo:

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files-small.png" alt-text="Definición de reglas de etiquetado automático para archivos en el Centro de cumplimiento de Microsoft 365" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files.png":::

Para obtener más información, consulte [Aplicación automática de una etiqueta de confidencialidad a los datos](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) en la documentación de Microsoft 365.

#### <a name="autolabeling-for-database-columns"></a>Etiquetado automático de columnas de bases de datos

Defina las reglas de etiquetado automático para las columnas de base de datos cuando cree o edite la etiqueta.

En la opción **database columns** (columnas de base de datos):

1. Seleccione el control deslizante **Etiquetado automático para las columnas de bases de datos**.

1. Seleccione **Comprobar tipos de información confidencial** para elegir los tipos de información confidencial que quiere aplicar a la etiqueta.

Por ejemplo:

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns-small.png" alt-text="Definición de reglas de etiquetado automático para columnas de SQL en el Centro cumplimiento de Microsoft 365" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns.png":::

### <a name="step-4-publish-labels"></a>Paso 4: Publicación de etiquetas

Una vez que haya creado una etiqueta, examine los datos en Azure Purview para aplicar automáticamente las etiquetas que ha creado, según las reglas de etiquetado automático que haya definido.

## <a name="scan-your-data-to-apply-sensitivity-labels-automatically"></a>Examen de los datos para aplicar etiquetas de confidencialidad automáticamente

Examine los datos en Azure Purview para aplicar automáticamente las etiquetas que ha creado, según las reglas de etiquetado automático que haya definido. Espere 24 horas para que los cambios en la etiqueta de confidencialidad se reflejen en Purview.

Para obtener más información sobre cómo configurar exámenes en varios recursos de Azure Purview, consulte:

|Source  |Referencia  |
|---------|---------|
|**Archivos dentro de Storage** | [Registro y examen de Azure Blob Storage](register-scan-azure-blob-storage-source.md) </br> [Registro y examen de Azure Files](register-scan-azure-files-storage-source.md) [Registro y examen de Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[Registro y examen de Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)</br>[Registro y examen de Amazon S3](register-scan-amazon-s3.md) |
|**columnas de bases de datos** | [Registro y examen de una instancia de Azure SQL Database](register-scan-azure-sql-database.md) </br>[Registro y examen de una Instancia administrada de Azure SQL Database](register-scan-azure-sql-database-managed-instance.md) </br> [Registro y examen de grupos de SQL dedicados](register-scan-azure-synapse-analytics.md)</br> [Registro y examen de áreas de trabajo de Azure Synapse Analytics](register-scan-azure-synapse-analytics.md) </br> [Registro y examen de Azure Cosmos Database (SQL API)](register-scan-azure-cosmos-database.md) </br> [Registro y análisis de una base de datos de Azure MySQL](register-scan-azure-mysql-database.md) </br> [Registro y examen de una instancia de Azure Database for PostgreSQL](register-scan-azure-postgresql.md) |
| | |

## <a name="view-labels-on-assets-in-the-catalog"></a>Visualización de etiquetas en los recursos del catálogo

Una vez que se han definido las reglas de etiquetado automático para las etiquetas en Microsoft 365 y se han examinado los datos en Azure Purview, las etiquetas se aplican automáticamente a los recursos.

**Para ver las etiquetas aplicadas a los recursos en el catálogo de Azure Purview:**

En el catálogo de Azure Purview, use las opciones de filtrado de **Etiqueta** para mostrar solo los archivos con etiquetas específicas. Por ejemplo:

:::image type="content" source="media/how-to-automatically-label-your-content/filter-search-results-small.png" alt-text="Buscar recursos por etiqueta" lightbox="media/how-to-automatically-label-your-content/filter-search-results.png":::

Para ver los detalles de un recurso, incluidas las clasificaciones encontradas y la etiqueta aplicada, haga clic en el recurso en los resultados.

Por ejemplo:

:::image type="content" source="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage-small.png" alt-text="Visualización de una etiqueta de confidencialidad en un archivo en Azure Blob Storage" lightbox="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>Visualización de los informes de conclusiones para las clasificaciones y las etiquetas de confidencialidad

Busque información sobre los datos clasificados y etiquetados en Azure Purview mediante los informes de **clasificación** y **etiquetas de confidencialidad**.

> [!div class="nextstepaction"]
> [Conclusiones de clasificaciones](./classification-insights.md)

> [!div class="nextstepaction"]
> [Conclusiones de etiquetas de confidencialidad](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Descripción general del etiquetado en Azure Purview](create-sensitivity-label.md)

> [!div class="nextstepaction"]
> [Preguntas más frecuentes sobre el etiquetado](sensitivity-labels-frequently-asked-questions.yml)