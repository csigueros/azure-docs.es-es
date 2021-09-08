---
title: Visualización del tráfico en un mapa | Microsoft Azure Maps
description: Descubra cómo agregar datos de tráfico a los mapas. Obtenga información sobre los datos de flujo y vea cómo usar el SDK web de Azure Maps para agregar datos de incidentes y datos de flujo a los mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9e7f2348c2cbf3ba0ad52f3ab5914235f0cb2e3a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425111"
---
# <a name="show-traffic-on-the-map"></a>Visualización del tráfico en el mapa

Hay dos tipos de datos de tráfico disponibles en Azure Maps:

- Datos de incidentes, que se componen de datos basados en puntos y líneas para aspectos como construcción, cierres de carreteras y accidentes.
- Datos de flujo, que proporcionan métricas sobre el flujo de tráfico en las carreteras. A menudo, los datos de flujo de tráfico se utilizan para colorear las carreteras. Los colores se basan en la cantidad de tráfico que ralentiza el flujo en relación con el límite de velocidad u otra métrica. Hay cuatro valores que se pueden pasar a la opción `flow` de tráfico del mapa.

    |Valor de flujo | Descripción|
    | :-- | :-- |
    | `none` | No muestra los datos de tráfico en el mapa. |
    | `relative` | Muestra los datos de tráfico relativos a la velocidad de flujo libre de la carretera. |
    | `relative-delay` | Muestra las áreas que son más lentas que el retraso promedio esperado. |
    | `absolute` | Muestra la velocidad absoluta de todos los vehículos en la carretera. |

En el código siguiente se muestra cómo mostrar los datos de tráfico en el mapa.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

A continuación se muestra el código de ejemplo de ejecución completo de la funcionalidad anterior.

<br/>

<iframe height='500' scrolling='no' title='Visualización del tráfico en un mapa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>Consulte el fragmento de código (pen) <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Mostrar tráfico en un mapa</a> por Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opciones de superposición de tráfico

La siguiente herramienta le permite cambiar entre las diferentes configuraciones de superposición de tráfico para ver cómo cambia la representación. 

<br/>

<iframe height="700" scrolling="no" title="Opciones de superposición de tráfico" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte el fragmento de código (pen) <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Opciones de superposición de tráfico</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="add-traffic-controls"></a>Incorporación de controles de tráfico

Hay dos controles de tráfico diferentes que se pueden agregar al mapa. El primer control, `TrafficControl`, agrega un botón de alternancia que se puede utilizar para activar y desactivar el tráfico. Las opciones de este control permiten especificar cuándo se va a usar la configuración de tráfico al mostrar el tráfico. De forma predeterminada, este control mostrará el flujo de tráfico relativo y los datos de incidentes, pero podría cambiarlo para que mostrara el flujo de tráfico absoluto y ningún incidente si lo desea. El segundo control, `TrafficLegendControl`, agrega una leyenda de flujo de tráfico al mapa, que ayuda a los usuarios a entender qué significan los aspectos destacados de las carreteras del código de color. Este control solo aparecerá en el mapa cuando se muestren los datos de flujo de tráfico en el mapa y se ocultará en el resto de los casos.

En el código siguiente se muestra cómo agregar controles de tráfico al mapa.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" scrolling="no" title="Controles de tráfico" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte el fragmento de código (pen) <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>Controles de tráfico</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Pasos siguientes

Más información sobre las clases y los métodos utilizados en este artículo:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions)

Mejore sus experiencias de usuario:

> [!div class="nextstepaction"]
> [Interacción del mapa con eventos del mouse](map-events.md)

> [!div class="nextstepaction"]
> [Building an accessible map](map-accessibility.md) (Creación de un mapa accesible)

> [!div class="nextstepaction"]
> [Página de ejemplos de código](https://aka.ms/AzureMapsSamples)