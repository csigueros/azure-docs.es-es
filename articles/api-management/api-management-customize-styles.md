---
title: Personalización del estilo de página en el portal para desarrolladores heredado de API Management
titleSuffix: Azure API Management
description: Siga los pasos de esta guía de inicio rápido para personalizar el estilo de los elementos del portal para desarrolladores de Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: article
ms.date: 11/04/2019
ms.author: danlep
ms.openlocfilehash: ebdb3e9d4cd2d0c4e1e418fd962f6f32edd2f749
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659122"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Personalización del estilo de las páginas del portal para desarrolladores

Existen tres maneras comunes de personalizar el portal para desarrolladores en Azure API Management:
 
* [Editar el contenido de las páginas estáticas y los elementos de diseño de página](api-management-modify-content-layout.md)
* Actualizar los estilos usados en los elementos de página en el portal para desarrolladores (que se explica en esta guía)
* [Modificar las plantillas usadas en las páginas generadas por el portal](api-management-developer-portal-templates.md) (por ejemplo, documentos de API, productos, autenticación de usuario)

En este artículo, aprenderá a personalizar el estilo de los elementos de las páginas del portal para **desarrolladores** heredado y a ver los cambios.

![Captura de pantalla que muestra dónde se cambia la configuración en el portal para desarrolladores heredado.](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Prerequisites

+ Conocer la [terminología de API Management de Azure](api-management-terminology.md).
+ Complete el siguiente inicio rápido: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Además, completar el tutorial siguiente: [Importación y publicación de la primera API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Personalización del portal para desarrolladores

1. Seleccione **Información general**.
2. Haga clic en el botón **Developer portal (legacy)** (Portal para desarrolladores [heredado]) en la parte superior de la ventana **Información general**.
3. En el lado superior izquierdo de la pantalla, verá un icono formado por dos pinceles. Mantenga el puntero sobre este icono para abrir el menú de personalización del portal.

    ![Captura de pantalla que resalta el icono con dos pinceles.](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Seleccione **Estilos** en el menú para abrir el panel de personalización del estilo.

    En la página aparecen todos los elementos que se pueden personalizar con **Estilos**.
5. Escriba "headings-color" en el campo **Change variable values to customize developer portal appearance:** (Cambiar valores de variables para personalizar la apariencia del portal para desarrolladores).

    El elemento **\@headings-color** aparece en la página. Esta variable controla el color del texto.

    ![Personalizar el estilo](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Haga clic en el campo correspondiente a la variable **\@headings-color**. 
    
    Se abre la lista desplegable del selector de colores.
7. En esta lista, seleccione un nuevo color.

    > [!TIP]
    > Existe una versión preliminar en tiempo real disponible de todos los cambios. Un indicador de progreso aparece en la parte superior del panel de personalización. Al cabo de unos segundos, el color del texto del encabezado cambia al que se acaba de seleccionar.

8. Seleccione **Publicar** en la parte inferior izquierda del menú del panel de personalización.
9. Seleccione **Publish customizations** (Publicar personalizaciones) para que los cambios estén disponibles públicamente.

## <a name="view-your-change"></a>Ver el cambio

1. Desplácese hasta el portal para desarrolladores.
2. Puede ver el cambio que ha realizado.

## <a name="next-steps"></a>Pasos siguientes

También podría estar interesado en aprender [cómo personalizar el portal para desarrolladores para Azure API Management mediante plantillas](api-management-developer-portal-templates.md).