---
title: Administración de orígenes de datos de varias nubes
description: Obtenga información sobre cómo registrar nuevos orígenes de datos, administrar colecciones de orígenes de datos y ver orígenes en Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: c7ef1849859dca48f5d2489f2ac8668c3cd0fc31
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215143"
---
# <a name="manage-data-sources-in-azure-purview"></a>Administración de orígenes de datos en Azure Purview

En este artículo va a aprender a registrar nuevos orígenes de datos, a administrar colecciones de orígenes de datos y a ver orígenes en Azure Purview.

## <a name="register-a-new-source"></a>Registro de un dispositivo nuevo

Para registrar un nuevo origen, siga estos pasos.

1. Abra [Purview Studio](https://web.purview.azure.com/resource/), navegue hasta **Asignación de datos**, **Orígenes** y seleccione **Registrar**.

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure Purview Studio":::

1. Seleccione un tipo de origen. En este ejemplo se utiliza Azure Blob Storage. Seleccione **Continuar**.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="Selección de un tipo de origen de datos en la página Register sources (Registrar orígenes)":::

2. Rellene el formulario en la página **Register sources** (Registrar orígenes). Seleccione un nombre para el origen y escriba la información pertinente. Si eligió **From Azure subscription** (Desde la suscripción de Azure) como método de selección de cuenta, los orígenes de su suscripción aparecen en una lista desplegable.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="Formulario de información de orígenes de datos":::

3. Seleccione **Registrar**.

## <a name="view-sources"></a>Visualización de orígenes

Puede ver todos los orígenes registrados en la pestaña **Mapa de datos** de Azure Purview Studio. Hay dos tipos de vistas: vista de mapa y vista de lista.

### <a name="map-view"></a>Vista del mapa

En la vista de mapa, puede ver todos los orígenes y colecciones. En la siguiente imagen, hay un origen de Azure Blob Storage. Desde cada icono de origen, puede editar el origen, iniciar un nuevo examen o ver los detalles del origen.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Vista de mapa de orígenes de datos de Azure Purview":::

### <a name="table-view"></a>Vista Tabla

En la vista de tabla, puede ver una lista de orígenes que se pueden ordenar. Mantenga el mouse sobre el origen para ver opciones de edición, inicio de un nuevo examen o eliminación.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Vista de lista de orígenes de datos de Azure Purview":::

## <a name="manage-collections"></a>Administración de recopilaciones

Puede agrupar los orígenes de datos en colecciones. Para crear una colección, seleccione **+ New collection** (+ Nueva colección) en la página *Sources* (Orígenes) de Azure Purview Studio. Asigne un nombre a la colección y seleccione *None* (Ninguna) como Parent (Primaria). La nueva colección aparece en la vista de mapa.

Para agregar orígenes a una colección, seleccione el lápiz **Edit** (Editar) en el origen y elija una colección en el menú desplegable **Select a collection** (Seleccionar una colección).

Para crear una jerarquía de colecciones, asigne colecciones de nivel superior como elemento primario a las colecciones de nivel inferior. En la imagen siguiente, *Fabrikam* es un elemento primario de la colección *Finance*, que contiene un origen de datos de Azure Blob Storage. Para contraer o expandir las colecciones, seleccione el círculo asociado a la flecha entre niveles.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Una jerarquía de colecciones en Azure Purview Studio":::

Puede quitar orígenes de una jerarquía seleccionando *None* (Ninguno) en el elemento primario. Los orígenes no primarios se agrupan en un cuadro punteado en la vista de mapa sin ninguna flecha que los vincule a elementos primarios.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo registrar y examinar varios orígenes de datos:

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Inquilino de Power BI](register-scan-power-bi-tenant.md)
* [Azure SQL Database](register-scan-azure-sql-database.md)
