---
title: Uso del visualizador de errores de dibujo de Azure Maps
description: En este artículo, aprenderá a visualizar las advertencias y los errores devueltos por la API de conversión de Creator.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/21/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: dcd9ccbcbc2372b5648bf6d5367b2c364a34eb0f
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/03/2021
ms.locfileid: "111352441"
---
# <a name="using-the-azure-maps-drawing-error-visualizer-with-creator"></a>Uso del visualizador de errores de dibujo de Azure Maps con Creator

El visualizador de errores de dibujo es una aplicación web independiente que muestra los [errores y advertencias de paquetes de dibujos](drawing-conversion-error-codes.md) detectados durante el proceso de conversión. La aplicación web del visualizador de errores consiste en una página estática que puede usar sin conectarse a Internet.  Puede usar el visualizador de errores para corregir errores y advertencias de acuerdo con los [requisitos del paquete de dibujos](drawing-requirements.md). La [API de conversión de Azure Maps](/rest/api/maps/v2/conversion) solo devuelve una respuesta con un vínculo al visualizador de errores cuando se detecta un error.

## <a name="prerequisites"></a>Requisitos previos

Antes de poder descargar el visualizador de errores de dibujo, deberá:

1. [Crear una cuenta de Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenga una clave de suscripción principal](quick-demo-map-app.md#get-the-primary-key-for-your-account), también conocida como clave principal o clave de suscripción.
3. [Crear un recurso de Creator](how-to-manage-creator.md)

En este tutorial se usa la aplicación [Postman](https://www.postman.com/), pero puede elegir un entorno de desarrollo de API diferente.

## <a name="download"></a>Descargar

1. Cargue el paquete de dibujos en el servicio Azure Maps Creator para obtener un `udid` para el paquete cargado. Para conocer los pasos para cargar un paquete, vea [Cargar un paquete de dibujos](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Cuando se haya cargado el paquete de dibujos, usaremos el `udid` para el paquete cargado con el fin de convertirlo en datos de mapa. Para conocer los pasos para convertir un paquete, vea [Convertir un paquete de dibujos](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Si el proceso de conversión se realiza correctamente, no recibirá un vínculo a la herramienta Visualizador de errores.

3. En la pestaña de respuesta **Encabezados** de la aplicación Postman, busque la propiedad `diagnosticPackageLocation`, devuelta por la API de conversión. La respuesta debería parecerse al JSON siguiente:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. Descargue el visualizador de errores de paquetes de dibujos; para ello, realice una solicitud `HTTP-GET` en la dirección URL de `diagnosticPackageLocation`.

## <a name="setup"></a>Configurar

En el paquete comprimido descargado del vínculo de `diagnosticPackageLocation`, encontrará dos archivos.

* _VisualizationTool.zip_: Contiene el código fuente, los elementos multimedia y la página web del visualizador de errores de dibujo.
* _ConversionWarningsAndErrors.jsen_: contiene una lista con formato de advertencias, errores y otros detalles que usa el visualizador de errores de dibujo.

Descomprima la carpeta _VisualizationTool.zip_. Contiene los elementos siguientes:

* Carpeta _assets_: contiene imágenes y archivos multimedia
* Carpeta _static_: código fuente
* Archivo _index.html_: aplicación web.

Abra el archivo _index.html_ con cualquiera de los exploradores siguientes, con el número de versión correspondiente. Puede usar una versión diferente si la versión ofrece un comportamiento igualmente compatible con la versión indicada.

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Uso de la herramienta Visualizador de errores de dibujo

Después de iniciar la herramienta Visualizador de errores de dibujo, se mostrará la página de carga. La página cargar contiene un cuadro para arrastrar y colocar. El cuadro para arrastrar y colocar también funciona como botón que inicia un diálogo del explorador de archivos.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="Aplicación Visualizador de errores de dibujos: página de inicio":::

El archivo _ConversionWarningsAndErrors.json_ se ha colocado en la raíz del directorio descargado. Para cargar el archivo _ConversionWarningsAndErrors.jsen_, arrástrelo y colóquelo en el cuadro. O bien, haga clic en el cuadro, busque el archivo en `File Explorer dialogue` y cárguelo.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="Aplicación Visualizador de errores de dibujo: arrastrar y colocar para cargar datos":::

Cuando se cargue el archivo _ConversionWarningsAndErrors.json_, verá una lista de errores y advertencias del paquete de dibujos. Cada error o advertencia se especifica por capa, nivel y un mensaje detallado. Para ver información detallada acerca de un error o una advertencia, haga clic en el vínculo **Detalles**. A continuación, aparecerá una sección debajo de la lista. Ahora puede ir a cada error para obtener más información sobre cómo resolverlo.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="Aplicación Visualizador de errores de dibujo: errores y advertencias":::

## <a name="next-steps"></a>Pasos siguientes

Una vez que el [paquete de dibujos cumpla los requisitos](drawing-requirements.md), puede usar el [servicio de conjuntos de datos de Azure Maps](/rest/api/maps/v2/conversion) para convertir el paquete de dibujos en un conjunto de datos. Después, puede usar el módulo web de planos interiores para desarrollar la aplicación. Para más información, lea los siguientes artículos:

> [!div class="nextstepaction"]
> [Códigos de error de conversión de dibujos](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Guía de paquetes de dibujos](drawing-package-guide.md)

> [!div class="nextstepaction"]
> [Uso de Creator para planos interiores](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Uso del módulo de planos interiores](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementación de estilos dinámicos de planos interiores](indoor-map-dynamic-styling.md)