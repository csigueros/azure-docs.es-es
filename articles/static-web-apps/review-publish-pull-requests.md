---
title: Revisión de las solicitudes de incorporación de cambios en entornos de pre-producción en Azure Static Web Apps
description: Aprenda a usar los entornos de preproducción para revisar los cambios en las solicitudes de incorporación de cambios en Azure Static Web Apps.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 3869b18f580d0a12b2581554acf63258cb08f64a
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112380687"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps"></a>Revisión de las solicitudes de incorporación de cambios en entornos de pre-producción en Azure Static Web Apps

En este artículo se muestra cómo usar los entornos de preproducción para revisar los cambios en las aplicaciones implementadas con [Azure Static Web Apps](overview.md).

Un entorno de preproducción (stage) es una versión de ensayo totalmente funcional de la aplicación que incluye los cambios que no están disponibles en producción.

Azure Static Web Apps genera un flujo de trabajo de Acciones de GitHub en el repositorio. Cuando se crea una solicitud de incorporación de cambios en una rama inspeccionada por el flujo de trabajo, se crea el entorno de preproducción. El entorno de preproducción agrega la aplicación al "stage", lo que permite que usted realice revisiones antes de la inserción en producción.

Varios entornos de preproducción pueden coexistir al mismo tiempo cuando se usa Azure Static Web Apps. Cada vez que cree una solicitud de incorporación de cambios en la rama inspeccionada, se implementa en un entorno de preproducción distinto una versión de ensayo con los cambios.

El uso de entornos de preproducción ofrece muchas ventajas. Por ejemplo, puede:

- Revisar los cambios visuales entre producción y ensayo. Por ejemplo, ver las actualizaciones del contenido y el diseño.
- Mostrar los cambios a su equipo.
- Comparar distintas versiones de la aplicación.
- Validar cambios mediante pruebas de aceptación.
- Realizar comprobaciones de integridad antes de la implementación en producción.

> [!NOTE]
> Actualmente, las solicitudes de incorporación de cambios y los entornos de preproducción solo se admiten en implementaciones de Acciones de GitHub.

## <a name="prerequisites"></a>Prerrequisitos

- Un repositorio de GitHub existente configurado con Azure Static Web Apps. Consulte [Compilación de su primera aplicación estática](getting-started.md) si no tiene una.

## <a name="make-a-change"></a>Realización de cambios

Comience por realizar un cambio en su repositorio. Puede hacerlo directamente en GitHub, tal como se muestra en los pasos siguientes.

1. Vaya al repositorio del proyecto en GitHub y, a continuación, haga clic en el botón **Rama** para crear una rama.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Creación de una nueva rama con la interfaz de GitHub":::]

    Escriba un nombre de rama y haga clic en **Crear rama**.

1. Vaya a la carpeta _app_ y cambie el contenido de texto. Por ejemplo, puede cambiar un título o párrafo. Una vez que encuentre el archivo que desea editar, haga clic en **Editar** para hacer el cambio.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="Botón Editar archivo en la interfaz de GitHub":::

1. Después de realizar los cambios, haga clic en **Confirmar cambios** para confirmar los cambios en la rama.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Botón Confirmar cambios en la interfaz de GitHub":::

## <a name="create-a-pull-request"></a>Creación de una solicitud de incorporación de cambios

A continuación, cree una solicitud de incorporación de cambios a partir de este cambio.

1. Abra la pestaña **Solicitud de incorporación de cambios** del proyecto en GitHub:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Pestaña Solicitud de incorporación de cambios en un repositorio de GitHub":::

1. Haga clic en el botón **Compare & pull request** (Solicitud de comparación e incorporación de cambios) de la rama.

1. Opcionalmente, puede rellenar algunos detalles sobre los cambios y, luego, hacer clic en **Crear solicitud de incorporación de cambios**.

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Creación de una solicitud de incorporación de cambios en GitHub":::

Puede asignar revisores y agregar comentarios para analizar los cambios si es necesario.

> [!NOTE]
> Puede realizar varios cambios insertando nuevas confirmaciones en la rama. La solicitud de incorporación de cambios luego se actualiza automáticamente para reflejar todos los cambios.

## <a name="review-changes"></a>Revisión de los cambios

Una vez creada la solicitud de incorporación de cambios, el flujo de trabajo de implementación de [Acciones de GitHub](https://github.com/features/actions) se ejecuta e implementa los cambios en un entorno de preproducción.

Una vez que el flujo de trabajo ha terminado de compilar e implementar la aplicación, el bot de GitHub agrega un comentario a la solicitud de incorporación de cambios que contiene la dirección URL del entorno de preproducción. Puede hacer clic en este vínculo para ver los cambios "staged".

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Comentario de la solicitud de incorporación de cambios con la dirección URL de preproducción":::

Haga clic en la dirección URL generada para ver los cambios.

Si echa un vistazo más detenidamente a la dirección URL, podrá ver que se compone de la siguiente forma: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net`.

Para una solicitud de incorporación de cambios dada, la dirección URL sigue siendo la misma aunque se inserten nuevas actualizaciones. Además de que la dirección URL permanece constante, se reutiliza el mismo entorno de preproducción durante la vigencia de la solicitud de incorporación de cambios.

## <a name="publish-changes"></a>Publicación de cambios

Una vez que se aprueben los cambios, podrá publicar los cambios en producción mediante la combinación de la solicitud de incorporación de cambios.

Haga clic en **Merge pull request** (Combinar solicitud de incorporación de cambios):

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="Botón Combinar solicitud de incorporación de cambios en la interfaz de GitHub":::

La combinación copia los cambios en la rama de seguimiento (la rama de "producción"). Después, el flujo de trabajo de implementación se inicia en la rama de seguimiento, y los cambios estarán activos después de que la aplicación se haya recompilado.

Para verificar los cambios en producción, abra la dirección URL de producción para cargar la versión en directo del sitio web.

## <a name="limitations"></a>Limitaciones

- Actualmente, es posible acceder públicamente a las versiones de ensayo de la aplicación por su dirección URL, aunque el repositorio de GitHub sea privado.

    > [!WARNING]
    > Tenga cuidado al publicar contenido confidencial en versiones de ensayo, ya que el acceso a los entornos de preproducción no está restringido.

- El número de entornos de preproducción disponibles para cada aplicación implementada con Static Web Apps depende del [plan de hospedaje](plans.md) que use. Por ejemplo, con el nivel Gratis, puede tener tres entornos de preproducción además del entorno de producción.

- Los entornos de preproducción no están distribuidos geográficamente.

- Actualmente, solo las implementaciones de Acciones de GitHub admiten los entornos de preproducción.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de un dominio personalizado](custom-domain.md)
