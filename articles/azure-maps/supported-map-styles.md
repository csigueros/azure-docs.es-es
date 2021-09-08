---
title: Estilos de mapa de Azure Maps integrados admitidos
description: Conozca los estilos de mapa integrados que admite Azure Maps, por ejemplo road, blank_accessible, satellite, satellite_road_labels, road_shaded_relief y night.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 4032dec715fc12b4e8144e6d9eebd51faceaaaaf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735869"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Estilos de mapa integrados admitidos en Azure Maps

Azure Maps es compatible con varios estilos de mapa integrados, tal como se describe a continuación.

>[!important]
>El procedimiento descrito en esta sección requiere una cuenta de Azure Maps en el plan de tarifa Gen 1 o Gen 2. Para más información sobre los planes de tarifa, consulte [Elección del plan de tarifa adecuado de Azure Maps](choose-pricing-tier.md).

## <a name="road"></a>carreteras

Un mapa **de carreteras** es un mapa estándar que muestra las carreteras. También se muestran las características naturales y artificiales, y las etiquetas de esas características.

![estilo de mapa de carreteras](./media/supported-map-styles/road.png)

**API correspondientes:**

* [Imagen de mapa](/rest/api/maps/render/getmapimage)
* [Mosaico de mapa](/rest/api/maps/render/getmaptile)
* Control de mapa de SDK web
* Control de mapa de Android
* Objeto visual de Power BI

## <a name="blank-and-blank_accessible"></a>blank y blank_accessible

Los estilos de mapa **blank** y **blank_accessible** proporcionan un lienzo en blanco para visualizar los datos. El estilo **blank_accessible** seguirá proporcionando actualizaciones del lector de pantalla con detalles de la ubicación del mapa, aunque el mapa base no se muestre.

> [!Note]
> En el SDK web, puede cambiar el color de fondo del mapa estableciendo el estilo CSS `background-color` del elemento DIV del mapa.

**API correspondientes:**

* Control de mapa de SDK web

## <a name="satellite"></a>satélite

El mapa estilo **satélite** es una combinación de imágenes aéreas o por satélite.

![estilo de mapa de mosaico satelital](./media/supported-map-styles/satellite.png)

**API correspondientes:**

* [Mosaico de satélite](/rest/api/maps/render/getmapimagerytilepreview)
* Control de mapa de SDK web
* Control de mapa de Android
* Objeto visual de Power BI

## <a name="satellite_road_labels"></a>satellite_road_labels

Este estilo de mapa es un híbrido de carreteras y etiquetas que se superpone a imágenes aéreas o por satélite.

![estilo de mapa satellite_road_labels](./media/supported-map-styles/satellite-road-labels.png)

**API correspondientes:**

* Control de mapa de SDK web
* Control de mapa de Android
* Objeto visual de Power BI

## <a name="grayscale_dark"></a>grayscale_dark

**escala de grises oscuros** es una versión oscura del estilo de mapa de carreteras.

![estilo de mapa gray_scale](./media/supported-map-styles/grayscale-dark.png)

**API correspondientes:**

* [Imagen de mapa](/rest/api/maps/render/getmapimage)
* [Mosaico de mapa](/rest/api/maps/render/getmaptile)
* Control de mapa de SDK web
* Control de mapa de Android
* Objeto visual de Power BI

## <a name="grayscale_light"></a>grayscale_light

**escala de grises claros** es una versión clara del estilo de mapa de carreteras.

![estilo de mapa de escala de grises claros](./media/supported-map-styles/grayscale-light.jpg)

**API correspondientes:**
* Control de mapa de SDK web
* Control de mapa de Android
* Objeto visual de Power BI

## <a name="night"></a>noche

**noche** es una versión oscura del estilo de mapa de carreteras con símbolos y carreteras en color.

![estilo de mapa de noche](./media/supported-map-styles/night.png)

**API correspondientes:**

* Control de mapa de SDK web
* Control de mapa de Android
* Objeto visual de Power BI

## <a name="road_shaded_relief"></a>road_shaded_relief

**relieve sombreado del camino** es un estilo principal de Azure Maps completado con los contornos de la tierra.

![estilo de mapa de relieve sombreado](./media/supported-map-styles/shaded-relief.png)

**API correspondientes:**

* [Mosaico de mapa](/rest/api/maps/render/getmaptile)
* Control de mapa de SDK web
* Control de mapa de Android
* Objeto visual de Power BI

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** es un estilo de mapa oscuro con un contraste mayor que el de los demás estilos.

![estilo de mapa oscuro de contraste alto](./media/supported-map-styles/high-contrast-dark.png)

**API correspondientes:**

* Control de mapa de SDK web
* Control de mapa de Android
* Objeto visual de Power BI

## <a name="high_contrast_light"></a>high_contrast_light

**high_contrast_light** es un estilo de mapa claro con un contraste mayor que el de los demás estilos.

![estilo de mapa claro de contraste alto](./media/supported-map-styles/high-contrast-light.jpg)

**API correspondientes:**

* Control de mapa de SDK web
* Control de mapa de Android
* Objeto visual de Power BI

## <a name="map-style-accessibility"></a>Accesibilidad de estilo de mapa

Los controles de mapas interactivos de Azure Maps usan mosaicos vectoriales en los estilos de mapa para encender el lector de pantalla y describir el área que muestra el mapa. Hay varios estilos de mapa que también están diseñados para ser totalmente accesibles en lo que respecta al contraste de color. En la tabla siguiente se proporcionan detalles sobre las características de accesibilidad que admite cada estilo de mapa.

| Estilo del mapa  | Contraste de color | Lector de pantalla | Notas |
|------------|----------------|---------------|-------|
| `blank` | N/D | No | Un lienzo en blanco útil para los desarrolladores que quieren usar sus propios mosaicos como mapa base o que quieren ver sus datos sin ningún fondo. Para las descripciones, el lector de pantalla no se basará en los mosaicos vectoriales.  |
| `blank_accessible` | N/D  | Sí | En segundo plano, este estilo de mapa sigue cargando los mosaicos vectoriales usados para representar el mapa, pero hace que los datos sean transparentes. De este modo, los datos se seguirán cargando y se podrán usar para encender el lector de pantalla. |
| `grayscale_dark` | Parcial | Sí | Este estilo de mapa está diseñado principalmente para escenarios de inteligencia empresarial, pero también resulta útil para superponer capas de colores, como imágenes de un radar meteorológico. |
| `grayscale_light` | Parcial | Sí | Este estilo de mapa está diseñado principalmente para escenarios de inteligencia empresarial. |
| `high_contrast_dark` | Sí | Sí | Estilo de mapa totalmente accesible para los usuarios en modo de contraste alto con una configuración oscura. Cuando se cargue el mapa, se detectará automáticamente la configuración de contraste alto. |
| `high_contrast_light` | Sí | Sí | Estilo de mapa totalmente accesible para los usuarios en modo de contraste alto con una configuración clara. Cuando se cargue el mapa, se detectará automáticamente la configuración de contraste alto. |
| `night` | Parcial | Sí | Este estilo está diseñado para cuando el usuario está en condiciones de poca luz y no quiere sobrecargar sus sentidos con un mapa brillante. |
| `road` | Parcial | Sí | Este es el principal estilo de mapa de rutas colorido de Azure Maps. Debido al número de colores diferentes y a las posibles combinaciones de colores superpuestas, es casi imposible que sea 100 % accesible. Dicho esto, este estilo de mapa se somete a pruebas de accesibilidad periódicas y se mejora según sea necesario para que las etiquetas se lean con más claridad. |
| `road_shaded_relief` | Parcial | Sí | Este es casi el mismo estilo que el del mapa de carreteras principales, pero tiene una capa de mosaico incorporada en el fondo que agrega relieve sombreado de las montañas y el color de la cubierta terrestre cuando se aleja a niveles superiores. |
| `satellite` | N/D | Sí | Únicamente imágenes aéreas y por satélite, sin etiquetas ni líneas de carreteras. Los mosaicos vectoriales se cargan en segundo plano para encender el lector de pantalla y realizar una transición más fluida al cambiar a `satellite_with_roads`. |
| `satellite_with_roads` | No | Sí | Imágenes aéreas y por satélite, con etiquetas y líneas de carreteras superpuestas. A escala global, hay un número ilimitado de combinaciones de colores que pueden producirse entre los datos superpuestos y las imágenes. Centrarse en hacer que las etiquetas se puedan leer en la mayoría de los escenarios comunes puede dar como resultado que, en algunos lugares, el contraste de color con las imágenes de fondo dificulte la lectura de estas. |

## <a name="next-steps"></a>Pasos siguientes

Aprenda a establecer un estilo de mapa en Azure Maps:

> [!div class="nextstepaction"]
> [Elección de un estilo de mapa](./choose-map-style.md)
