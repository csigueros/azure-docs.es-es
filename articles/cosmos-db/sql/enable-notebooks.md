---
title: Habilitación de cuadernos en la cuenta de Azure Cosmos DB (versión preliminar)
description: La integración de cuadernos en Azure Cosmos DB permite analizar y visualizar los datos desde el portal. En este artículo se describe cómo habilitar esta característica para las cuentas de Cosmos.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/24/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 03a493807b0f7eb0f6e1f08794a53b18b3a7dbe4
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060543"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Habilitación de cuadernos para las cuentas de Azure Cosmos DB (versión preliminar)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Actualmente, la característica de cuaderno está deshabilitada temporalmente. El equipo de Azure Cosmos DB está trabajando para corregirlo.  
>
> Los cuadernos integrados para Azure Cosmos DB están disponibles actualmente en [29 regiones](#supported-regions). Para usar cuadernos, [cree una cuenta de Cosmos](#create-a-new-cosmos-account) o [habilite los notebooks de una cuenta existente](#enable-notebooks-in-an-existing-cosmos-account) en una de estas regiones. 

Los cuadernos de Jupyter integrados en Azure Cosmos DB permiten analizar y visualizar los datos desde Azure Portal. En este artículo se describe cómo habilitar esta característica para la cuenta de Azure Cosmos DB.

## <a name="create-a-new-cosmos-account"></a>Creación de una cuenta de Cosmos
A partir del 10 de febrero de 2021, las nuevas cuentas de Azure Cosmos creadas en una de las [regiones admitidas](#supported-regions) tendrán automáticamente los cuadernos habilitados. No se necesita ninguna configuración adicional para habilitar los cuadernos. Use estas instrucciones para crear una cuenta:
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
1. Seleccione **Crear un recurso** > **Bases de datos** > **Azure Cosmos DB**.
1. Especifique la configuración básica de la cuenta.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Página de la nueva cuenta de Azure Cosmos DB":::

1. Seleccione **Revisar + crear**. Puede omitir las opciones **Red** y **Etiquetas**. 
1. Revise la configuración de la cuenta y seleccione **Crear**. La operación de creación de la cuenta tarda unos minutos. Espere hasta que la página del portal muestre **Se completó la implementación** .

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Panel de notificaciones de Azure Portal":::

1. Seleccione **Ir al recurso** para ir a la página de la cuenta de Azure Cosmos DB.

   :::image type="content" source="../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Página de la cuenta de Azure Cosmos DB":::

1. Vaya al panel **Explorador de datos**. Ahora debería ver el área de trabajo de Notebooks.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Nueva área de trabajo de cuadernos en Azure Cosmos DB":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Habilitación de cuadernos en una cuenta de Cosmos existente

También puede habilitar cuadernos en las cuentas existentes. Este paso debe realizarse una sola vez por cuenta.

1. Vaya al panel **Explorador de datos** en su cuenta de Cosmos.
1. Seleccione **Habilitar Notebooks**.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Creación de una nueva área de trabajo de cuadernos en el Explorador de datos":::

1. Se le pedirá que cree un área de trabajo de Notebooks. Seleccione **Complete setup** (Completar configuración).
1. Ahora su cuenta está habilitada para usar Notebooks.

## <a name="create-and-run-your-first-notebook"></a>Creación y ejecución del primer cuaderno

Para comprobar que puede usar Notebooks, seleccione uno de los cuadernos de Sample Notebooks (Cuadernos de ejemplo). Se guardará una copia del cuaderno en el área de trabajo y se abrirá.

En este ejemplo, usaremos **GettingStarted.ipynb**.

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Visualización del cuaderno GettingStarted.ipynb":::

Para ejecutar el cuaderno:
1. Seleccione la primera celda de código que contiene el código de Python.
1. Seleccione **Ejecutar** para ejecutar la celda. También puede usar **Mayús+Entrar** para ejecutar la celda.
1. Actualice el panel de recursos para ver la base de datos y el contenedor que se han creado.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Ejecución del cuaderno inicial":::

También puede seleccionar **Nuevo Notebook** para crear un notebook o cargar un archivo de Notebook (.ipynb) existente al seleccionar **Cargar archivo** en el menú **Mis Notebooks**. 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Creación o carga de un cuaderno nuevo":::

## <a name="supported-regions"></a>Regiones admitidas
Los cuadernos integrados para Azure Cosmos DB están disponibles actualmente en 29 regiones de Azure. Las cuentas de Azure Cosmos creadas en estas regiones tendrán los cuadernos habilitados automáticamente. Los cuadernos son gratis con su cuenta. 

- Centro de Australia
- Centro de Australia 2
- Este de Australia
- Sudeste de Australia
- Sur de Brasil
- Centro de Canadá
- Este de Canadá
- Centro de la India
- Centro de EE. UU.
- Este de EE. UU.
- Este de EE. UU. 2
- Centro de Francia
- Sur de Francia
- Norte de Alemania
- Centro-oeste de Alemania
- Japón Occidental
- Corea del Sur
- Centro-Norte de EE. UU
- Norte de Europa
- Centro-sur de EE. UU.
- Sudeste de Asia
- Norte de Suiza
- Centro de Emiratos Árabes Unidos
- Sur de Reino Unido
- Oeste de Reino Unido
- Centro-Oeste de EE. UU.
- Oeste de Europa
- Oeste de la India
- Oeste de EE. UU. 2

## <a name="next-steps"></a>Pasos siguientes

* Conozca las ventajas de los [cuadernos de Jupyter para Azure Cosmos DB](../cosmosdb-jupyter-notebooks.md).
* [Exploración de la galería de ejemplos de Notebook](https://cosmos.azure.com/gallery.html)
* [Publicación de cuadernos en la galería de cuadernos de Azure Cosmos DB](publish-notebook-gallery.md)
* [Uso de características y comandos de cuadernos de Python](use-python-notebook-features-and-commands.md)
* [Uso de características y comandos de cuadernos de C#](use-csharp-notebook-features-and-commands.md)
* [Importación de cuadernos desde un repositorio de GitHub](import-github-notebooks.md)
