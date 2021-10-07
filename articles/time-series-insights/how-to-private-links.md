---
title: 'Habilitación de vínculos privados: Azure Time Series Insights Gen2 | Microsoft Docs'
description: Vea cómo habilitar el acceso privado para las soluciones de Azure Time Series Insights Gen2 con Private Link mediante Azure Portal.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 21a6c529aaa086fa75aef9e665cbbdce882baf5a
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061940"
---
# <a name="enable-private-access-for-tsi-with-private-link-preview"></a>Habilitación del acceso privado para TSI con Private Link (versión preliminar)

En este artículo se indica cómo [habilitar Private Link con un punto de conexión privado para un entorno de Azure Time Series Insights Gen2](concepts-private-links.md) (actualmente en versión preliminar). La configuración de un punto de conexión privado para el entorno de Azure Time Series Insights Gen2 le permite protegerlo y eliminar la exposición pública, además de evitar la filtración de datos desde la instancia de [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

En este artículo se describe el proceso que utiliza [**Azure Portal**](https://portal.azure.com).

Estos son los pasos que se describen en este artículo: 
1. Active Private Link y configure un punto de conexión privado para un entorno de Time Series Insights Gen2.
1. Deshabilitación o habilitación de marcas de acceso a la red pública para restringir el acceso únicamente a las conexiones de Private Link.

## <a name="prerequisites"></a>Requisitos previos

Para poder configurar un punto de conexión privado, necesitará una [**red virtual de Azure (VNet)**](../virtual-network/virtual-networks-overview.md) en la que se pueda implementar el punto de conexión. Si todavía no tiene una red virtual, puede seguir uno de los [inicios rápidos](../virtual-network/quick-create-portal.md) de Azure Virtual Network para configurarla.

## <a name="add-a-private-endpoint-for-an-azure-time-series-insights-gen2-environment"></a>Adición de un punto de conexión privado para un entorno de Azure Time Series Insights Gen2 

Al usar [Azure Portal](https://portal.azure.com) puede activar Private Link con un punto de conexión privado para un entorno de Azure Time Series Insights Gen2 como parte de la configuración inicial de la instancia, o bien habilitarlo más adelante en un entorno que ya exista. 

Cualquiera de estos métodos de creación proporcionará las mismas opciones de configuración y el mismo resultado final para el entorno. En esta sección se describe cómo realizar ambos.

>[!TIP]
> También puede configurar un punto de conexión de Private Link mediante el servicio Private Link, en lugar de hacerlo con el entorno de Azure Time Series Insights Gen2. Esto también proporciona las mismas opciones de configuración y el mismo resultado final.
>
> Para obtener más información sobre la configuración de recursos de Private Link, vea la documentación de Azure Private Link para [Azure Portal](../private-link/create-private-endpoint-portal.md), la [CLI de Azure](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md) o [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-environment-creation"></a>Adición de un punto de conexión privado durante la creación del entorno

En esta sección, habilitará Private Link con un punto de conexión privado en un entorno de Azure Time Series Insights Gen2 que se está creando actualmente. Esta sección se centra en el paso de red del proceso de creación. Para ver un tutorial completo sobre cómo crear un nuevo entorno de Azure Time Series Ideas Gen2, consulte [*Configuración de un entorno*](tutorial-set-up-environment.md).

Las opciones de Private Link se encuentran en la pestaña **Redes** de configuración del entorno.

En esta pestaña, puede habilitar puntos de conexión privados si selecciona la opción **Punto de conexión privado** para el **Método de conectividad**.

Esto agregará una sección llamada **Puntos de conexión privados** donde puede configurar los detalles del punto de conexión privado. Seleccione el botón **+ Agregar** para continuar.

:::image type="content" source="media/private-links/create-instance-networking.png" alt-text="Captura de pantalla de Azure Portal que muestra la pestaña Redes del cuadro de diálogo Crear recurso para Time Series Insights Gen2. Hay un resaltado alrededor del nombre de la pestaña, la opción Punto de conexión privado para el método de conectividad y el botón + Agregar para crear una nueva conexión de punto de conexión privado." lightbox="media/private-links/create-instance-networking.png":::

Se abrirá una página para especificar los detalles de un nuevo punto de conexión privado.

:::image type="content" source="media/private-links/create-private-endpoint.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la página Crear punto de conexión privado. Contiene los campos que se describen a continuación.":::

1. Rellene las selecciones para la **Suscripción** y el **Grupo de recursos**. Establezca la **Ubicación** en la misma de la red virtual que va a usar. Elija un **Nombre** para el punto de conexión y, para **Subrecursos de destino**, seleccione *entorno* o *tsiExplorer*.

1. A continuación, seleccione la **Red virtual** y la **Subred** que quiera usar para implementar el punto de conexión.

1. Por último, seleccione si quiere **Integrar con la zona DNS privada**. Puede usar el valor predeterminado de **Sí**, o bien, para obtener ayuda con esta opción, puede seguir el vínculo del portal para [obtener más información sobre la integración de DNS privado](../private-link/private-endpoint-overview.md#dns-configuration).

Después de rellenar las opciones de configuración, presione **Aceptar** para finalizar.

Esto le devolverá a la pestaña **Redes** de la configuración del entorno de Azure Time Series Insights Gen2, donde el nuevo punto de conexión debe poder verse en **Conexiones de punto de conexión privado**.

Después, puede usar los botones de navegación inferiores para continuar con el resto de la configuración del entorno.

### <a name="add-a-private-endpoint-to-an-existing-environment"></a>Adición de un punto de conexión privado a un entorno existente

En esta sección, habilitará Private Link con un punto de conexión privado en un entorno de Azure Time Series Insights Gen2 que ya existe.

1. En primer lugar, navegue a [Azure Portal](https://portal.azure.com) en un explorador. Abra la instancia de Azure Time Series Insights Gen2; para ello, busque su nombre en la barra de búsqueda del portal.

1. En el menú de la izquierda, seleccione **Redes (versión preliminar)** .

1. Cambie a la pestaña **Conexiones de punto de conexión privado**.

1. Seleccione **+ Punto de conexión privado** para abrir la configuración **Crear un punto de conexión privado**.

    :::image type="content" source="media/private-links/add-private-endpoint.png" alt-text="Captura de pantalla de Azure Portal que muestra la página Redes (versión preliminar) de un entorno de Azure Time Series Insights Gen2. La pestaña Conexiones de punto de conexión privado está resaltada y el botón + Punto de conexión privado también está resaltado." lightbox="media/private-links/add-private-endpoint.png":::

1. En la pestaña  **Aspectos básicos** , escriba o seleccione la **Suscripción** y el **Grupo de recursos** del proyecto, y un **Nombre** y una **Región** para el punto de conexión. La región debe ser la misma que la de la red virtual que usa.

    :::image type="content" source="media/private-links/create-private-endpoint-2.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la pestaña (Básico) del cuadro de diálogo Crear punto de conexión privado. Contiene los campos descritos antes.":::

    Cuando haya terminado, seleccione el botón **Siguiente: Recurso >** para ir a la pestaña siguiente.

1. En la pestaña **Recurso**, escriba o seleccione esta información: 
    * **Método de conexión**: seleccione **Conectarse a un recurso de Azure en mi directorio** para buscar el entorno de Azure Time Series Insights Gen2.
    * **Suscripción**: escriba la suscripción.
    * **Tipo de recurso**: seleccione **Microsoft.TimeSeriesInsights/environments**
    * **Recurso**: seleccione el nombre del entorno de Azure Time Series Insights Gen2.
    * **Subrecurso de destino**: seleccione **entorno** o **tsiExplorer**.

    :::image type="content" source="media/private-links/create-private-endpoint-3.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la segunda pestaña (Recurso) del cuadro de diálogo Crear punto de conexión privado. Contiene los campos descritos antes.":::

    Cuando haya terminado, seleccione el botón **Siguiente: Configuración >** para ir a la pestaña siguiente.    

1. En la pestaña  **Configuración**, escriba o seleccione esta información:
    * **Red virtual**: Seleccione la red virtual.
    * **Subred**: elija una subred de la red virtual.
    * **Integración con una zona DNS privada**: seleccione si quiere **Integrar con la zona DNS privada**. Puede usar el valor predeterminado de **Sí**, o bien, para obtener ayuda con esta opción, puede seguir el vínculo del portal para [obtener más información sobre la integración de DNS privado](../private-link/private-endpoint-overview.md#dns-configuration).
    Si selecciona **Sí**, puede dejar la información de configuración predeterminada.

    :::image type="content" source="media/private-links/create-private-endpoint-4.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la tercera pestaña (Configuración) del cuadro de diálogo Crear punto de conexión privado. Contiene los campos descritos antes.":::

    Cuando haya terminado, puede seleccionar el botón **Revisar y crear** para finalizar la instalación. 

1. En la pestaña **Revisar y crear**, revise las selecciones y seleccione el botón  **Crear**. 

Cuando finalice la implementación del punto de conexión, debe aparecer en las conexiones de punto de conexión privado del entorno de Azure Time Series Insights Gen2.

>[!TIP]
> El punto de conexión también se puede ver desde Private Link Center en Azure Portal.

## <a name="disable--enable-public-network-access-flags"></a>Habilitación o deshabilitación de las marcas de acceso a la red pública

Puede configurar el entorno de Azure Time Series Insights Gen2 para denegar todas las conexiones públicas y permitir solo las conexiones a través de puntos de conexión privados a fin de mejorar la seguridad de la red. Esta acción se realiza con una **marca de acceso a la red pública**. 

Esta directiva permite restringir el acceso únicamente a las conexiones de Private Link. Cuando la marca de acceso a la red pública está establecida en *deshabilitada*, todas las llamadas API REST al plano de datos del entorno de Azure Time Series Insights Gen2 desde la nube pública devolverán `403, Unauthorized`. Como alternativa, cuando la directiva se establece en *deshabilitada* y se realiza una solicitud a través de un punto de conexión privado, la llamada API se realizará correctamente.

Para deshabilitar o habilitar el acceso a la red pública en [Azure Portal](https://portal.azure.com), abra el portal y vaya al entorno de Azure Time Series Insights Gen2.

1. En el menú de la izquierda, seleccione **Redes (versión preliminar)** .

1. En la pestaña **Acceso público**, establezca **Permitir el acceso de red público**, en **Deshabilitado** o **Todas las redes**.

    :::row:::
        :::column:::
            :::image type="content" source="media/private-links/network-flag-portal.png" alt-text="Captura de pantalla de Azure Portal que muestra la página Redes (versión preliminar) de un entorno de Azure Time Series Insights Gen2. La pestaña Acceso público está resaltada y también aparece resaltada la opción para elegir si se permite el acceso a la red pública." lightbox="media/private-links/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre Private Link para Azure: 
* [*¿Qué es el servicio Azure Private Link?*](../private-link/private-link-service-overview.md)