---
title: 'Inicio rápido: Búsqueda interactiva de mapas con Azure Maps'
description: 'Inicio rápido: Aprenda a crear mapas interactivos y en los que se puede realizar búsquedas. Vea cómo crear una cuenta de Azure Maps, obtener una clave principal y usar el SDK web para configurar aplicaciones de mapa.'
author: stevemunk
ms.author: v-munksteve
ms.date: 09/30/2021
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: e4c4cdeeb032b0d6a6691987cec43c8ac1b2d437
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043384"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>Inicio rápido: Creación de un mapa con búsqueda interactiva con Azure Maps

En este inicio rápido, aprenderá a usar Azure Maps para crear un mapa que proporcione a los usuarios una experiencia de búsqueda interactiva. Le guía por estos pasos básicos:

* Cree su propia cuenta de Azure Maps.
* Obtenga la clave principal que se usará en la aplicación web de demostración.
* Descargue y abra la aplicación del mapa de demostración.

En este inicio rápido se usa el SDK web de Azure Maps, pero el servicio Azure Maps se puede usar con cualquier control de mapa, como estos populares [controles de mapa de código abierto](open-source-projects.md#third-part-map-control-plugins) para los que el equipo de Azure Mapas ha creado complementos.

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

* Inicie sesión en [Azure Portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Crear una cuenta de Azure Maps

Cree una nueva cuenta de Azure Maps con los pasos siguientes:

1. En la esquina superior izquierda de [Azure Portal](https://portal.azure.com), haga clic en **Crear un recurso**.
2. En el cuadro *Buscar en el Marketplace*, escriba **Azure Maps**.
3. En los *Resultados*, seleccione **Azure Maps**. Haga clic en el botón **Crear** que aparece debajo del mapa.
4. En la página **Create Maps Account** (Crear una cuenta de Azure Maps), escriba los siguientes valores:
    * La *suscripción* que quiere usar para esta cuenta.
    * El nombre del *grupo de recursos* para esta cuenta. Puede elegir *Crear nuevo* o *Usar existente* para el grupo de recursos.
    * El *nombre* de la nueva cuenta.
    * El *Plan de tarifa* de la cuenta.
    * Lea la *licencia* y la *declaración de privacidad* y active la casilla para aceptar los términos.
    * Haga clic en el botón **Crear**.

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="Creación de una cuenta de Maps en el portal" lightbox="./media/quick-demo-map-app/create-account.png":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obtención de la clave principal de una cuenta

Una vez que se haya creado correctamente la cuenta de Maps, recupere la clave principal que le permite consultar las API de Maps.

1. Abra su cuenta de Maps en el portal.
2. En la sección de configuración, seleccione **Autenticación**.
3. Copie la **clave principal** al Portapapeles. Guárdela localmente para usarla más adelante en este tutorial.

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Obtención de la clave principal de Azure Maps en Azure Portal" lightbox="./media/quick-demo-map-app/get-key.png":::

>[!NOTE]
> En este inicio rápido se usa el enfoque de autenticación de [clave compartida](azure-maps-authentication.md#shared-key-authentication) con fines de demostración, pero el enfoque preferido para cualquier entorno de producción es usar la autenticación de [Azure Active Directory](azure-maps-authentication.md#azure-ad-authentication).

## <a name="download-and-update-the-azure-maps-demo"></a>Descarga y actualización de la demostración de Azure Maps

1. Vaya a [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html). Copie el contenido del archivo.
2. Guarde el contenido de este archivo localmente como **AzureMapDemo.html**. Ábralo en un editor de texto.
3. Agregue el valor de **Clave principal** que obtuvo en la sección anterior.
    1. Comente todo el código de la función `authOptions`; este código se usa para la autenticación de Azure Active Directory.
    1. Quite la marca de comentario de las dos últimas líneas de la función `authOptions`; este código se usa para la autenticación de clave compartida, el enfoque que se usa en este inicio rápido.
    1. Reemplace `<Your Azure Maps Key>` por el valor de **Clave principal** de la sección anterior.

## <a name="open-the-demo-application"></a>Apertura de la aplicación de demostración

1. Abra el archivo **AzureMapDemo.html** en un explorador de su preferencia.
2. Observe el mapa que se muestra de la Ciudad de Los Ángeles. Acerque y aleje para ver de qué manera el mapa se representa automáticamente con más o menos información según el nivel de zoom.
3. Cambie el centro predeterminado del mapa. En el archivo **AzureMapDemo.html**, busque la variable denominada **center**. Reemplace el par de valores de longitud y latitud de esta variable con los nuevos valores **[-74.0060, 40.7128]** . Guarde el archivo y actualice el explorador.
4. Pruebe la experiencia de búsqueda interactiva. En el cuadro de búsqueda de la esquina superior izquierda de la aplicación web de demostración, busque **restaurants**.
5. Mueva el mouse sobre la lista de direcciones y ubicaciones que aparecen debajo del cuadro de búsqueda. Observe cómo el correspondiente alfiler en el mapa muestra información sobre esa ubicación. Para proteger la privacidad de empresas privadas, se muestran nombres y direcciones ficticios.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="Aplicación web de búsqueda interactiva en mapas" lightbox="./media/quick-demo-map-app/interactive-search.png":::

## <a name="clean-up-resources"></a>Limpieza de recursos

>[!WARNING]
>Los tutoriales que se enumeran en la sección [Pasos siguientes](#next-steps) describen cómo usar y configurar Azure Maps con su cuenta. Si tiene pensado seguir con los tutoriales, no elimine los recursos que se crearon con este inicio rápido.

Si no tiene previsto continuar con los tutoriales, siga estos pasos para realizar la limpieza de recursos:

1. Cierre el explorador donde se ejecuta la aplicación web **AzureMapDemo.html**.
2. Vaya a la página de Azure Portal. En la página principal del portal, seleccione **Todos los recursos**. O bien, haga clic en el icono de menú en la esquina superior izquierda. Seleccione **Todos los recursos**.
3. Haga clic en la cuenta de Azure Maps. Haga clic en **Eliminar** en la parte superior de la página.

Para ver más ejemplos de código y obtener una experiencia de codificación interactiva, consulte estas guías:

* [Búsqueda de una dirección mediante el servicio de búsqueda de Azure Maps](how-to-search-for-address.md)
* [Uso del Control de mapa de Azure Maps](how-to-use-map-control.md)

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado la cuenta de Azure Maps y ha creado una aplicación de demostración. Consulte los siguientes tutoriales para más información sobre Azure Maps:

> [!div class="nextstepaction"]
> [Búsqueda de puntos cercanos de interés mediante Azure Maps](tutorial-search-location.md)
