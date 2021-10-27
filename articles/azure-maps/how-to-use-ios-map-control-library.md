---
title: Introducción al control de mapa de iOS | Microsoft Azure Maps
description: Familiarícese con el SDK de iOS de Azure Maps. Vea cómo instalar el SDK y cree un mapa interactivo.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/08/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 53d5b70f62d3ccea3d881a4e91a4b16287e1aa49
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043346"
---
# <a name="getting-started-with-azure-maps-ios-sdk-public-preview"></a>Introducción al SDK de iOS de Azure Maps (versión preliminar pública)

El SDK de iOS de Azure Maps es una biblioteca de mapas vectoriales para iOS. En este artículo se ofrece orientación sobre los procesos para instalar el SDK de iOS para Azure Maps y cargar un mapa.

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de completar los pasos descritos en el artículo [Inicio rápido: creación de una aplicación para iOS](quick-ios-app.md).

## <a name="localizing-the-map"></a>Localización del mapa

El SDK de iOS de Azure Maps proporciona tres formas de establecer el idioma y la vista regional del mapa. En el código siguiente se muestra cómo establecer el *idioma* en francés ("fr-FR") y la *vista regional* en "Auto".

1. Pase el idioma y la información de la vista regional en la clase `AzureMaps` mediante las propiedades `language` y `view` estáticas. De esta forma, se establecen las propiedades de idioma predeterminado y vista regional en la aplicación.

    ```swift
    // Alternatively use Azure Active Directory authenticate.
    AzureMaps.configure(aadClient: "<Your aad clientId>", aadAppId: "<Your aad AppId>", aadTenant: "<Your aad Tenant>")
    
    // Set your Azure Maps Key.
    // AzureMaps.configure(subscriptionKey: "<Your Azure Maps Key>")
    
    // Set the language to be used by Azure Maps.
    AzureMaps.language = "fr-FR"
    
    // Set the regional view to be used by Azure Maps.
    AzureMaps.view = "Auto"
    ```

1. También puede pasar la información de idioma y vista regional a la inicialización del control de mapa.

    ```swift
    MapControl(options: [
        StyleOptions.language("fr-FR"),
        StyleOptions.view("Auto")
    ])
    ```

1. La última manera de establecer mediante programación las propiedades de idioma y vista regional usa el método `setStyle` de mapas. Esta opción puede realizarse en cualquier momento para cambiar el idioma y la vista regional del mapa.

    ```swift
    mapControl.getMapAsync { map in
        map.setStyle([
            StyleOptions.language("fr-FR"),
            StyleOptions.view("Auto")
        ])
    }
    ```

Este es un ejemplo de una aplicación de Azure Maps con el idioma establecido en "fr-FR" y la vista regional establecida en "Auto".

:::image type="content" source="media/ios-sdk/how-to-use-ios-map-control-library/fr-borderless.png" alt-text="Imagen de un mapa que muestra las etiquetas en francés.":::

Para obtener una lista completa de los idiomas admitidos y las vistas regionales, consulte [Compatibilidad con la localización en Azure Maps](supported-languages.md).

## <a name="navigating-the-map"></a>Navegación por el mapa

En esta sección se detallan las distintas formas de navegar en un programa de Azure Maps.

### <a name="zoom-the-map"></a>Acercamiento/alejamiento del mapa

* Toque el mapa con dos dedos y acérquelos para alejarse o aléjelos para acercarse.
* Pulse dos veces el mapa para acercarse un nivel.
* Pulse dos veces con dos dedos para alejar el mapa un nivel.
* Pulse dos veces; en la segunda pulsación, mantenga presionado el dedo en el mapa y arrástrelo hacia arriba o hacia abajo para acercar o alejar la imagen, respectivamente.

### <a name="pan-the-map"></a>Desplazamiento lateral del mapa

* Toque el mapa y arrastre en cualquier dirección.

### <a name="rotate-the-map"></a>Giro del mapa

* Toque el mapa con dos dedos y gírelos.

### <a name="pitch-the-map"></a>Inclinación del mapa

* Toque el mapa con dos dedos y arrástrelos hacia arriba o hacia abajo.

## <a name="azure-government-cloud-support"></a>Compatibilidad con la nube de Azure Government

El SDK de iOS de Azure Maps es compatible con la nube de Azure Government. Especifíquelo con el dominio de nube de administración pública de Azure Maps; para ello, agregue la siguiente línea de código donde se especifican los detalles de la autenticación de Azure Maps.

```
AzureMaps.domain = "atlas.azure.us"
```

No olvide usar los detalles de autenticación de Azure Maps de la plataforma en la nube de Azure Government cuando autentique el mapa y los servicios.