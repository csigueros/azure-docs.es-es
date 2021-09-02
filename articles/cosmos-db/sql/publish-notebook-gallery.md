---
title: Publicación de cuadernos en la galería de cuadernos de Azure Cosmos DB
description: Aprenda a descargar cuadernos desde la galería pública, a editarlos y a publicar sus propios cuadernos en la galería.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 016bd8c73e12297988f3a2d47cde0163821d2dcd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113849"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Publicación de cuadernos en la galería de cuadernos de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Los cuadernos de Jupyter Notebook integrados en Azure Cosmos DB se integran directamente en las cuentas de Azure Cosmos DB en Azure Portal. Con estos cuadernos puede analizar y visualizar los datos desde Azure Portal. Los cuadernos integrados para Azure Cosmos DB están disponibles actualmente en [muchas regiones](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Para usar cuadernos, [cree una cuenta de Cosmos](create-cosmosdb-resources-portal.md) o [habilite los notebooks de una cuenta existente](enable-notebooks.md) en una de estas regiones.

El entorno de Notebook de Azure Portal tiene algunas muestras publicadas por el equipo de Azure Cosmos DB. También tiene una galería pública en la que puede publicar y compartir sus propios cuadernos. Una vez que un cuaderno se publica en la galería, está disponible para que todos los usuarios de Azure Cosmos DB puedan verlo y usarlo. En este artículo va a aprender a usar cuadernos de la galería pública y a publicar su cuaderno en ella.

## <a name="download-a-notebook-from-the-gallery"></a>Descarga de un cuaderno de la galería

Siga los pasos que se indican a continuación para ver y descargar cuadernos de la galería en su área de trabajo de Notebook:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya a la cuenta de Azure Cosmos DB habilitada con el entorno de Notebook.

1. Vaya a la pestaña **Explorador de datos** > **Notebooks** > **Galería** > **Galería pública**.

1. Acepte el [código de conducta](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/) para ver o publicar cuadernos en la galería. El código de conducta se registra por usuario, por nivel de suscripción. Al cambiar a otra suscripción, debe aceptarlo de nuevo para acceder a la galería. Para aceptar el código de conducta, active la casilla y haga clic en **Continuar**:

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Galería pública de cuadernos y aceptación del código de conducta.":::

1. Luego puede agregar un cuaderno específico a la pestaña de favoritos o descargarlo. Al descargar un cuaderno, se copia en el área de trabajo de Notebook, donde se puede ejecutar o editar.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Descarga de un cuaderno de la galería en el área de trabajo.":::

## <a name="publish-a-notebook-to-the-gallery"></a>Publicación de un cuaderno en la galería

Si compila sus propios cuadernos o edita los existentes para que se ajusten a otro escenario, se recomienda publicarlos en la galería. Una vez que un cuaderno se publica en la galería, otros usuarios pueden acceder a él. Puede [explorar la galería de muestras de Notebook](https://cosmos.azure.com/gallery.html) para ver los cuadernos disponibles actualmente.

Siga estos pasos para publicar un cuaderno:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya a la cuenta de Azure Cosmos DB habilitada con el entorno de Notebook.

1. Vaya a la pestaña **Explorador de datos** > **Notebooks** > **Mis blocs de notas**.

1. Vaya al cuaderno que quiere publicar. Haga clic en el botón **Guardar** de la barra de comandos y luego seleccione **Publicar en la galería**:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Selección de un cuaderno para publicarlo en la galería.":::

   También puede buscar la opción **Publicar en la galería** si selecciona el botón **...** situado junto al nombre del cuaderno:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="Otro enfoque para elegir un cuaderno para publicarlo en la galería.":::

1. Rellene el formulario **Publicar en la galería** con los detalles siguientes:

   * **Nombre:** nombre descriptivo para identificar el cuaderno.
   * **Descripción:** breve descripción de lo que hace el cuaderno.
   * **Etiquetas:** las etiquetas son opcionales y se usan para filtrar los resultados cuando se busca por una palabra clave.
   * **Imagen de portada:** imagen que se usa en la portada cuando se publica el cuaderno. Puede elegir una de las siguientes opciones:
   * **Imagen personalizada:** puede cargar una imagen del equipo. Elija un archivo de imagen con una relación de aspecto 256x144.
   * **URL:** proporcione una dirección URL de acceso público en la que se encuentre la imagen.
   * **Obtener captura de pantalla:** se realiza automáticamente una captura de pantalla del cuaderno abierto y se carga en la versión preliminar.
   * **Use first display output** (Usar primera salida de pantalla): salida de la primera celda con una salida de pantalla. Las celdas que solo muestran texto Markdown no cuentan como salida de pantalla.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Formulario Publicar en la galería.":::

   > [!NOTE]
   > Si usa la opción **Publicar en la galería** desde el botón **...** situado junto al nombre del cuaderno no verá todas las opciones de **Imagen de portada**. Esto se debe a que es posible que el cuaderno no esté abierto y Azure Cosmos DB no tenga acceso para realizar una captura de pantalla ni tampoco a la primera salida de pantalla.

1. Compruebe que la versión preliminar tiene buen aspecto y seleccione **Publicar**. Una vez publicado, este cuaderno aparece en la galería pública de cuadernos de Azure Cosmos DB. Asegúrese de que ha quitado los datos confidenciales o la salida antes de publicar. El contenido del cuaderno se examina para detectar cualquier infracción de las directivas de Microsoft antes de su publicación. Este proceso normalmente tarda unos segundos. Si se detecta alguna infracción, aparece un mensaje en la pestaña de notificación. Si se detecta una infracción, el cuaderno no se publica hasta que se elimina esa infracción y se vuelve a publicar.

   > [!NOTE]
   > Una vez que los cuadernos se publican en la galería pública, todos los usuarios de Azure Cosmos DB pueden verlos. El acceso no está limitado por suscripción ni nivel de organización.

1. Una vez que el cuaderno se ha publicado en la galería, se puede ver en la pestaña **My published work** (Mi trabajo publicado). También es posible eliminar o quitar cuadernos publicados por uno mismo de la galería pública.

1. También se puede denunciar que un cuaderno infringe el código de conducta. Si se detecta una infracción, Cosmos DB quita automáticamente el cuaderno de la galería. Si se quita un cuaderno, los usuarios pueden verlo en la pestaña **My published work** (Mi trabajo publicado) con la nota quitada. Para denunciar un cuaderno, vaya a la pestaña **Explorador de datos** > **Notebooks** > **Galería** > **Galería pública**. Abra el cuaderno que quiere denunciar, colóquese sobre el botón **Ayuda** de la esquina derecha y seleccione **Denunciar abuso**.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Denuncia de un cuaderno que infringe el código de conducta.":::

## <a name="next-steps"></a>Pasos siguientes

* Conozca las ventajas de los [cuadernos de Jupyter para Azure Cosmos DB](../cosmosdb-jupyter-notebooks.md).
* [Uso de características y comandos de cuadernos de Python](use-python-notebook-features-and-commands.md)
* [Uso de características y comandos de cuadernos de C#](use-csharp-notebook-features-and-commands.md)
* [Importación de cuadernos desde un repositorio de GitHub](import-github-notebooks.md)
