---
title: Configuración de rutas en Azure Front Door
description: En este artículo, se explica cómo se configura una ruta entre los dominios y los grupos de orígenes.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 05/17/2021
ms.author: qixwang
ms.openlocfilehash: 8b25f56b238c7f0c42a1ba589f7da2d68344c915
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464397"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Configuración de una ruta en Azure Front Door Standard o Premium

> [!Note]
> Esta documentación trata sobre Azure Front Door Estándar/Prémium (versión preliminar). ¿Busca información sobre Azure Front Door? Consulte [aquí](../front-door-overview.md).

En este artículo, se explican todas las opciones de configuración que se utilizan para crear una ruta de un punto de conexión existente en Azure Front Door (AFD). Después de agregar un dominio personalizado y un origen al punto de conexión existente de Azure Front Door, debe configurar la ruta que va a establecer la asociación entre los dominios y los orígenes para enrutar el tráfico entre ellos.

> [!IMPORTANT]
> Azure Front Door Estándar/Prémium (versión preliminar) está disponible actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerrequisitos

Para poder configurar una ruta de Azure Front-Door, es necesario que haya creado al menos un grupo de orígenes y un dominio personalizado en el punto de conexión actual. 

Para configurar un grupo de orígenes, consulte este artículo sobre la [creación de un nuevo grupo de orígenes en Azure Front Door Standard o Premium](how-to-create-origin.md). 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Creación de una nueva ruta en Azure Front Door Standard o Premium

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a su perfil de Azure Front Door Estándar/Prémium.

1. En **Configuración**, seleccione **Endpoint Manager**.
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Instantánea de la configuración del servicio Endpoint Manager de Front Door." lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. A continuación, seleccione **Editar extremo** en el punto de conexión cuya ruta desee configurar.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Instantánea de la página para editar un punto de conexión.":::

1. Aparecerá la página **Editar extremo**. Seleccione **+ Agregar** en Rutas.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Instantánea de la opción para agregar una ruta en la página de edición del punto de conexión.":::    
    
1. En la página **Agregar ruta**, especifique o seleccione la siguiente información.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Instantánea de la página &quot;Agregar una ruta&quot;." lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Configuración | Descripción |
    | --- | --- |
    | Nombre | Especifique un nombre único para la nueva ruta. |   
    | Domain| Seleccione uno o varios dominios validados que no estén asociados a otra ruta. |
    | Patrones de coincidencia  | Configure todos los patrones de rutas URL que se van a aceptar en esta ruta. Por ejemplo, puede establecer este valor en `/images/*` para aceptar todas las solicitudes de la dirección URL `www.contoso.com/images/*`. AFD intentará determinar el tráfico primero en función de la coincidencia exacta. Si ninguna ruta coincide exactamente, buscará una ruta de acceso con caracteres comodín que coincida. Si no hay reglas de enrutamiento con una ruta coincidente, rechace la solicitud y devuelva una respuesta de error HTTP 400: solicitud incorrecta. |
    | Accepted protocols (Protocolos aceptados) | Especifique los protocolos que desea que Azure Front Door acepte cuando el cliente realice la solicitud. |
    | Redirect | Especifique si HTTPS se va a aplicar forzosamente con las solicitudes HTTP entrantes. |
    | Origin group (Grupo de orígenes) | Seleccione el grupo de orígenes al que se debe reenviar la solicitud cuando esta se devuelva al origen. |
    | Ruta de acceso de origen | Esta ruta de acceso se usa para volver a escribir la dirección URL que Azure Front Door usará al construir la solicitud reenviada al origen. De forma predeterminada, no se proporciona esta ruta de acceso. Por lo tanto, Azure Front Door usará la ruta de acceso de la dirección URL entrante en la solicitud al origen. También puede especificar una ruta de acceso con caracteres comodín, que copiará cualquier parte correspondiente de la ruta de acceso entrante a la ruta de acceso de la solicitud al origen. </br></br>Modelo de coincidencia: `/foo/*`</br>Ruta de acceso de origen: `/fwd/`</br></br>Ruta de acceso de dirección URL entrante: `/foo/a/b/c/`</br>Dirección URL de Azure Front Door al origen: `fwd/a/b/c` |
    | Protocolo de reenvío | Seleccione el protocolo utilizado para reenviar la solicitud. |
    | Almacenamiento en memoria caché | Seleccione esta opción para habilitar el almacenamiento en caché de contenido estático con Azure Front Door. |
    | Regla | Seleccione los conjuntos de reglas que se aplicarán a esta ruta. Para más información sobre cómo configurar las reglas, consulte el artículo sobre [configuración de un conjunto de reglas para Azure Front Door](how-to-configure-rule-set.md). | 

1. Seleccione **Agregar** para crear la nueva ruta. La ruta aparecerá en la lista de rutas del punto de conexión.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Instantánea de la lista de rutas.":::  
    
## <a name="clean-up-resources"></a>Limpieza de recursos

Para eliminar una ruta cuando ya no la necesite, selecciónela y haga clic en **Eliminar**. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Instantánea de la página para eliminar una ruta.":::  

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los dominios personalizados, continúe con el tutorial para agregar un dominio personalizado al punto de conexión de Azure Front Door.

> [!div class="nextstepaction"]
> [Agregar un dominio personalizado]()
