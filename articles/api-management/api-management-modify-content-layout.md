---
title: Modificación del contenido de una página en el portal para desarrolladores de API Management
titleSuffix: Azure API Management
description: Aprenda a editar el contenido de una página en el portal para desarrolladores de Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: danlep
ms.openlocfilehash: 430da3f7fd4a46d7d8a17b1d13da975864cb48b4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573478"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Modificación del contenido y el diseño de páginas en el portal para desarrolladores de Azure API Management
Existen tres maneras fundamentales de personalizar el portal para desarrolladores en Azure API Management:

* [Editar el contenido de las páginas estáticas y los elementos de diseño de página][modify-content-layout] (que se explica en esta guía)
* [Actualizar los estilos usados para los elementos de página en el portal para desarrolladores][customize-styles]
* [Modificar las plantillas usadas en las páginas generadas por el portal][portal-templates] (por ejemplo, documentos de API, productos, autenticación de usuario, etc.)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="structure-of-developer-portal-pages"></a><a name="page-structure"> </a>Estructura de las páginas del portal para desarrolladores

El portal para desarrolladores se basa en el sistema de administración de contenido. El diseño de cada página se compila en función de un conjunto de pequeños elementos de página conocidos como widgets:

![Estructura de las páginas del portal para desarrolladores][api-management-customization-widget-structure]

Todos los widgets son editables.
* El contenido principal específico de cada página reside en el widget "Contenido". La edición de una página significa modificar el contenido de este widget.
* Todos los elementos de diseño de página están contenidos con los widgets restantes. Los cambios realizados en estos widgets se aplican a todas las páginas. Se hace referencia a ellos como "widgets de diseño".

En las páginas del día a día, a menudo solo se modificaría el widget Contenido, que tendrá contenido diferente para cada página.

## <a name="modifying-the-contents-of-a-layout-widget"></a><a name="modify-layout-widget"> </a>Modificación del contenido de un widget de diseño

El portal para desarrolladores es accesible desde Azure Portal.

1. Haga clic en **Portal para desarrolladores** en la barra de herramientas de la instancia de API Management.
2. Para editar el contenido de los widgets, haga clic en el icono formado por dos pinceles en el menú del portal **Developer** a la izquierda.
3. Para modificar el contenido del encabezado, desplácese hasta la sección **Encabezado** en la lista de la izquierda.

    Los widgets son editables dentro de los campos.
4. Una vez que esté preparado para publicar los cambios, haga clic en **publicar** en la parte inferior de la página.

Ahora debe aparecer el nuevo encabezado en todas las páginas del portal para desarrolladores.

## <a name="next-steps"></a><a name="next-steps"> </a>Pasos siguientes
* [Actualizar los estilos usados para los elementos de página en el portal para desarrolladores][customize-styles]
* [Modificar las plantillas usadas en las páginas generadas por el portal][portal-templates] (por ejemplo, documentos de API, productos, autenticación de usuario, etc.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
