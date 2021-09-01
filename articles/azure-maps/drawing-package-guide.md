---
title: Guía del paquete de dibujo de Microsoft Azure Maps Creator (versión preliminar)
description: Aprenda a preparar un paquete de dibujo para el servicio Azure Maps Conversion
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: eb74ffb55536b18e48a4b5dcea83fac4db20abe0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727987"
---
# <a name="conversion-drawing-package-guide"></a>Guía del paquete de dibujo de Conversion

En esta guía se muestra cómo preparar un paquete de dibujo para el [servicio Azure Maps Conversion](/rest/api/maps/v2/conversion) mediante comandos CAD específicos para preparar correctamente los archivos DWG y el archivo de manifiesto para el servicio Conversion.

Para empezar, asegúrese de que el paquete de dibujo está en formato .zip y contiene los siguientes archivos:

* Uno o varios archivos de dibujo en formato DWG.
* Un archivo de manifiesto que describa los archivos DWG y los metadatos de las instalaciones.

Si no tiene su propio paquete al que hacer referencia en esta guía, puede descargar el [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

Puede elegir cualquier software CAD para abrir y preparar los archivos de dibujo de la instalación. Sin embargo, esta guía se crea mediante el software AutoCAD® de Autodesk. Los comandos a los que se hace referencia en esta guía están previstos para ejecutarse mediante el software AutoCAD® de Autodesk.  

>[!TIP]
>Para más información sobre los requisitos del paquete de dibujo que no se tratan en esta guía, consulte [Requisitos de paquetes de dibujo](drawing-requirements.md).

## <a name="glossary-of-terms"></a>Glosario de términos

Para facilitar la referencia, aquí encontrará son varios términos y definiciones que son importantes a la hora de leer este artículo.

| Término  | Definición |
|:-------|:------------|
| Nivel | Capa DWG de AutoCAD del archivo de dibujo.|
| Entidad | Entidad DWG de AutoCAD del archivo de dibujo. |
| Nivel  |Área de un edificio en una elevación determinada. Por ejemplo, la planta de un edificio.    |
| Característica | Objeto que combina una geometría con más información de metadatos. |
| Clases de características | Proyecto común para las características. Por ejemplo, una *unidad* es una clase de característica y una *oficina* es una característica. |

## <a name="step-1-dwg-file-requirements"></a>Paso 1: requisitos del archivo DWG

Al preparar los archivos de dibujo de las instalaciones para el servicio Conversion, asegúrese de seguir estos requisitos y recomendaciones preliminares:

* Los archivos de dibujo de las instalaciones se deben guardar en formato DWG, que es el formato de archivo nativo del software AutoCAD® de Autodesk.

* El servicio de conversión funciona con el formato de archivo DWG de AutoCAD.AC1032 es la versión interna del formato para los archivos DWG y se recomienda seleccionar AC1032 para la versión interna del formato de archivo DWG.

* Un archivo DWG solo puede contener una sola planta. Las plantas de una instalación deben proporcionarse en sus propios archivo DWG independientes.  Por consiguiente, si tiene cinco plantas en una instalación, debe crear cinco archivos DWG independientes.

## <a name="step-2-prepare-the-dwg-files"></a>Paso 2: Preparación de los archivos DWG

En esta parte de la guía se muestra cómo usar comandos de CAD para asegurarse de que los archivos DWG cumplen los requisitos del servicio Conversion.

Puede elegir cualquier software CAD para abrir y preparar los archivos de dibujo de la instalación. Sin embargo, esta guía se crea mediante el software AutoCAD® de Autodesk. Los comandos a los que se hace referencia en esta guía están previstos para ejecutarse mediante el software AutoCAD® de Autodesk.  

### <a name="bind-external-references"></a>Enlace de referencias externas

Cada planta de una instalación debe proporcionarse en forma de archivo DWG. Si no hay referencias externas, no es preciso hacer nada más. Sin embargo, si hay referencias externas, deben enlazarse a un único dibujo. Para enlazar una referencia externa, puede usar el comando `XREF`. Después del enlace, cada dibujo de una referencia externa se agregará como una referencia de bloque. Si necesita realizar cambios en cualquiera de estas capas, no olvide expandir las referencias de bloque mediante el comando `XPLODE`.

### <a name="unit-of-measurement"></a>Unidad de medida

Los dibujos se pueden crear con cualquier unidad de medida. Sin embargo, todos ellos deben usar la misma unidad de medida. Por consiguiente, si una planta de la instalación usa milímetros, las restantes plantas (dibujos) también deben estar en milímetros. Para comprobar o modificar la unidad de medida, utilice el comando `UNITS`.

En la imagen siguiente se muestra la ventana Drawing Units (Unidades de dibujo) del software AutoCAD® de Autodesk, que se puede usar para comprobar la unidad de medida.  

:::image border="true" type="content" source="./media/drawing-package-guide/units.png" alt-text="Ventana Drawing Units (Unidades de dibujo) en el software AutoCAD® de Autodesk":::

### <a name="alignment"></a>Alineación

Cada planta de una instalación se proporciona como un archivo DWG individual. Como resultado, es posible que las plantas no estén perfectamente alineados cuando se apilan unas sobre otras. El servicio Azure Maps Conversion requiere que todos los dibujos se alineen con el espacio físico. Para comprobar la alineación, use un punto de referencia que pueda abarcar varias plantas, como un ascensor o una columna que abarca varias plantas. Para ver todas las plantas, abra un dibujo nuevo y use el comando `XATTACH` para cargar los dibujos de todas las plantas. Si necesita corregir cualquier problema de alineación, puede usar los puntos de referencia y el comando `MOVE` para volver a alinear las plantas que lo requieran.

### <a name="layers"></a>Capas

Asegúrese de que cada capa de un dibujo contiene entidades de una clase de característica. Si una capa contiene entidades para las paredes, no puede tener otras características, como unidades o puertas.  Sin embargo, una clase de característica se puede dividir en varias capas. Por ejemplo, un dibujo puede tener tres capas que contengan entidades de pared.

Además, cada capa tiene una lista de tipos de entidad admitidos y se ignoran los restantes. Por ejemplo, si la capa Etiqueta de unidad solo admite texto de una sola línea, se ignora el texto que contenga varias líneas o la polilínea en la misma capa.

Para conocer mejor las capas y las clases de características, consulte el artículo [Requisitos de los paquetes de dibujos](drawing-requirements.md).

### <a name="exterior-layer"></a>Capa exterior

Se crea una característica de un solo nivel a partir de cada capa o capas exteriores. Esta característica de nivel define el perímetro del nivel. Es importante asegurarse de que las entidades de la capa exterior cumplen los requisitos de la capa. Por ejemplo, se admite una polilínea cerrada, pero no una polilínea abierta. Si la capa exterior está compuesta de varios segmentos de línea, se deben proporcionar como una polilínea cerrada. Para unir varios segmentos de línea, seleccione todos ellos y use el comando `JOIN`.

La siguiente imagen se ha tomado del paquete de ejemplo y muestra la capa exterior de la instalación en rojo. Para facilitar la visualización, la capa de unidad está desactivada.

:::image border="true" type="content" source="./media/drawing-package-guide/exterior.png" alt-text="Capa exterior de una instalación.":::

### <a name="unit-layer"></a>Capa de unidades

Las unidades son espacios transitables en el edificio, como oficinas, vestíbulos, escaleras y ascensores.Se requiere un tipo de entidad cerrada como Polígono, Polilínea cerrada, Círculo o Elipse cerrada para representar cada unidad. Por lo tanto, las puertas y las paredes por sí solas no crearán una unidad porque no hay ninguna entidad que represente la unidad.  

La siguiente imagen se toma del [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples) y muestra la capa de etiqueta de unidad y la capa de unidad en rojo. Para facilitar la visualización, las restantes capas están desactivadas. Además, se selecciona una unidad para ayudar a mostrar que cada unidad es una polilínea cerrada.  

:::image border="true" type="content" source="./media/drawing-package-guide/unit.png" alt-text="Capa de unidad de una instalación.":::

### <a name="unit-label-layer"></a>Capa de etiqueta de unidad

Si desea agregar una propiedad name a una unidad, deberá agregar una capa independiente para las etiquetas de la unidad. Las etiquetas se deben proporcionar como entidades de texto de una sola línea que se encuentran dentro de los límites de una unidad. Se debe agregar al archivo de manifiesto una propiedad unit correspondiente donde `unitName` coincide con el contenido del texto.  Para obtener información sobre todas las propiedades de unidad admitidas, consulte [`unitProperties`](#unitproperties).

### <a name="door-layer"></a>Capa de puertas

Las puertas son opcionales. Sin embargo, las pueden usar si desea especificar los puntos de entrada de una unidad. Las puertas se pueden dibujar de cualquier manera si es un tipo de entidad admitido por la capa de puerta. La puerta debe superponerse al límite de una unidad y el borde superpuesto de la unidad se tratará como una apertura a la unidad.  

La siguiente imagen se toma del [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples) y muestra una unidad con una puerta (en rojo) dibujada en el límite de la unidad.

:::image border="true" type="content" source="./media/drawing-package-guide/door.png" alt-text="Capa de puertas de una instalación.":::

### <a name="wall-layer"></a>Capa de paredes

La capa de paredes está pensada para representar las extensiones físicas de una instalación, como las paredes y las columnas. El servicio Azure Maps Conversion percibe las paredes como estructuras físicas que son un obstáculo para deambular. Teniendo eso en cuenta, una pared debe considerarse como una estructura física que se puede ver, pero no recorrer. En esta capa no se capturará nada que no se pueda ver. Si una pared tiene paredes internas o columnas dentro, solo se debe capturar el exterior.  

## <a name="step-3-prepare-the-manifest"></a>Paso 3: preparación del manifiesto

El manifiesto del paquete de dibujo es un archivo JSON. El manifiesto indica al servicio Azure Maps Conversion cómo leer los archivos DWG y los metadatos de la instalación. Algunos ejemplos de esta información podrían ser la información específica que contiene cada capa de DWG o la ubicación geográfica de la instalación.

Para lograr una conversión correcta, se deben definir todas las propiedades "requeridas". En el [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples) se puede encontrar un archivo de manifiesto de ejemplo. En esta guía no se cubren las propiedades admitidas por el manifiesto. Para más información sobre las propiedades del manifiesto, consulte la sección sobre las [propiedades del archivo de manifiesto](drawing-requirements.md#manifest-file-requirements).

### <a name="building-levels"></a>Niveles de creación

El nivel de creación especifica el archivo DWG que se va a usar para cada nivel. Un nivel debe tener un nombre de nivel y un ordinal que describa el orden vertical de cada nivel. Cada instalación debe tener un ordinal 0, que es la planta baja. Se debe proporcionar un ordinal 0 aunque los dibujos ocupen pocas plantas de una instalación. Por ejemplo, las plantas 15-17 se pueden definir como ordinal 0-2, respectivamente.

El siguiente ejemplo está tomado del [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples). La instalación tiene tres niveles: sótano, planta baja y nivel 2. El nombre de archivo contiene el nombre de archivo completo y la ruta de acceso del archivo, en relación al archivo de manifiesto dentro del paquete de dibujo .zip.  

```json
    "buildingLevels": { 
      "levels": [ 
       { 
           "levelName": "Basement", 
           "ordinal": -1, 
           "filename": "./Basement.dwg" 
            }, { 

            "levelName": "Ground", 
            "ordinal": 0, 
            "filename": "./Ground.dwg" 
            }, { 

            "levelName": "Level 2", 
            "ordinal": 1, 
             "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
```

### <a name="georeference"></a>georeference

El objeto `georeference` se usa para especificar el lugar en que se encuentra geográficamente la instalación y cuánto se va a girar la instalación. El punto de origen del dibujo debe coincidir con la latitud y longitud proporcionadas con el objeto `georeference`. Ángulo en el sentido de las agujas del reloj, en grados, entre el norte verdadero y el eje vertical (Y) del dibujo.  

### <a name="dwglayers"></a>dwgLayers

El objeto `dwgLayers` se usa para especificar los nombres de capa DWG donde se pueden encontrar clases de características. Para recibir una instalación convertida adecuadamente, es importante proporcionar los nombres de capa correctos. Por ejemplo, las capas de paredes de DWG se deben proporcionar como capas de paredes, no como capas de unidades. El dibujo puede tener otras capas, como mobiliario o tuberías. pero si no se especifican en el manifiesto, el servicio Azure Maps Conversion las ignorará.  

El ejemplo siguiente del objeto `dwgLayers` en el manifiesto.  

```json
"dwgLayers": { 
        "exterior": [ 
            "OUTLINE" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    } 
```

En la imagen siguiente se muestran las capas del dibujo DWG correspondiente visto en el software AutoCAD® de Autodesk.

:::image border="true" type="content" source="./media/drawing-package-guide/layer.png" alt-text="Capas de Dwg en el software AutoCAD® de Autodesk":::

### <a name="unitproperties"></a>unitProperties

El objeto `unitProperties` permite definir otras propiedades para una unidad, lo que no se puede hacer en el archivo DWG. Algunos ejemplos podrían ser la información de directorios de una unidad o el tipo de categoría de una unidad. Para que una propiedad unit se asocie a una unidad, es preciso hacer que el objeto `unitName` coincida con la etiqueta de la capa `unitLabel`.  

El siguiente ejemplo se ha tomado del [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples). Muestra la etiqueta de unidad asociada a la propiedad unit en el manifiesto.

:::image border="true" type="content" source="./media/drawing-package-guide/unit-property.png" alt-text="Etiqueta de unidad que se asociará a la propiedad unit en el manifiesto":::

El siguiente fragmento de código muestra el objeto propiedad unit asociado a la unidad.  

```json
 "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
```

## <a name="step-4-prepare-the-drawing-package"></a>Paso 4: preparación del paquete de dibujo

Ya debería tener todos los dibujos DWG preparados para cumplir los requisitos del servicio Azure Maps Conversion. También se ha creado un archivo de manifiesto que ayude a describir la instalación. Todos los archivos tendrán que comprimirse en un único archivo con la extensión `.zip`. Es importante que el archivo de manifiesto se llame `manifest.json` y que se coloque en el directorio raíz del paquete comprimido. Los restantes archivos pueden estar en cualquier directorio del paquete comprimido, siempre que el nombre de archivo incluya la ruta de acceso relativa al manifiesto. Para obtener un ejemplo de un paquete de dibujo, vea el [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: creación de un plano interior de Creator](tutorial-creator-indoor-maps.md)
