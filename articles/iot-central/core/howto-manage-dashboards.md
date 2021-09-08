---
title: Creación y administración de paneles de Azure IoT Central | Microsoft Docs
description: Aprenda a crear y administrar paneles personales y de aplicaciones.
author: dominicbetts
ms.author: dobett
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6c14cc00843a998990c89e7892a793d10cfdbbdf
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113650787"
---
# <a name="create-and-manage-dashboards"></a>Creación y administración de paneles

El *panel de la aplicación* predeterminado es la página que se carga cuando va por primera vez a la aplicación. Como administrador, puede crear hasta 10 paneles que son visibles para todos los usuarios de la aplicación. Solo los administradores pueden crear, editar y eliminar paneles en el nivel de la aplicación.

Todos los usuarios pueden crear sus propios *paneles personales*. Los usuarios pueden cambiar entre paneles de aplicaciones y paneles personales.

## <a name="create-dashboard"></a>Crear panel

En la captura de pantalla siguiente se muestra el panel en una aplicación creada a partir de la plantilla **Aplicación personalizada**. Puede reemplazar el panel predeterminado de la aplicación por un panel personal, o bien, si es administrador, por otro panel en el nivel de aplicación. Para ello, seleccione **+ Nuevo panel** en la parte superior izquierda de la página.

:::image type="content" source="media/howto-manage-dashboards/dashboard-custom-app.png" alt-text="Captura de pantalla del panel para aplicaciones basadas en la plantilla de aplicación personalizada.":::

Seleccione **+ Nuevo panel** para que se abra el editor de paneles. En el editor, puede asignarle un nombre al panel y elegir los elementos de la biblioteca. La biblioteca contiene iconos y paneles primitivos que puede usar para personalizar el panel:

:::image type="content" source="media/howto-manage-dashboards/dashboard-library.png" alt-text="Captura de pantalla que muestra la biblioteca del panel.":::

Si es administrador, puede crear un panel personal o un panel de aplicación. Todos los usuarios de la aplicación pueden ver los paneles que crea el administrador. Todos los usuarios pueden crear paneles personales que solo ellos pueden ver.

Especifique un título y seleccione el tipo de panel que desea crear. [Agregue iconos](#add-tiles) para personalizar el panel.

> [!TIP]
> Necesita al menos una plantilla de dispositivo en la aplicación para poder agregar iconos que muestren la información del dispositivo.

## <a name="manage-dashboards"></a>Administración de paneles

Puede tener varios paneles personales y cambiar entre ellos o elegir uno de los paneles de la aplicación:

:::image type="content" source="media/howto-manage-dashboards/switch-dashboards.png" alt-text="Captura de pantalla que muestra cómo cambiar entre paneles.":::

Puede editar los paneles personales y eliminar los que ya no necesite. Si es **administrador**, también puede editar o eliminar los paneles de nivel de aplicación.

:::image type="content" source="media/howto-manage-dashboards/delete-dashboards.png" alt-text="Captura de pantalla que muestra cómo eliminar paneles.":::

## <a name="add-tiles"></a>Agregar iconos

En la captura de pantalla siguiente se muestra el panel en una aplicación creada a partir de la plantilla **Aplicación personalizada**. Para personalizar el panel actual, seleccione **Editar**. Para agregar un panel personal o de aplicación, seleccione **Nuevo**:

:::image type="content" source="media/howto-manage-dashboards/dashboard-sample-contoso.png" alt-text="Panel para aplicaciones basadas en la plantilla Aplicación personalizada":::

Después de seleccionar **Editar** o **Nuevo**, el panel se encuentra en modo de *edición*. Puede usar las herramientas del panel **Editar panel** para agregar, personalizar y quitar iconos del panel. Por ejemplo, para agregar un icono de **gráfico de líneas** para realizar un seguimiento de los valores de telemetría a lo largo del tiempo notificados por uno o varios dispositivos:

1. Seleccione **Start with a Visual** (Empezar con un objeto visual), después, elija **Gráfico de líneas** y, a continuación, seleccione **Agregar icono** o simplemente arrástrelo y suéltelo en el lienzo.
 
1. Para configurar el icono, seleccione el icono de engranaje. Especifique un **título** y seleccione un **grupo de dispositivos** y, a continuación, elija los dispositivos del menú desplegable **Dispositivos** que se deben mostrar en el icono.

:::image type="content" source="media/howto-manage-dashboards/device-details.png" alt-text="Adición de un icono de telemetría de temperatura al panel":::

Cuando haya seleccionado todos los valores que se van a mostrar en el icono, haga clic en **Actualizar**.

Cuando haya terminado de agregar y personalizar iconos en el panel, seleccione **Guardar** para guardar los cambios en el panel, con lo que saldrá del modo de edición.

## <a name="customize-tiles"></a>Personalización de iconos

Para editar un icono, debe estar en el modo de edición. Las opciones de personalización disponibles dependen del [tipo de icono](#tile-types):

* El icono de regla de un icono permite cambiar la visualización. Las visualizaciones incluyen gráficos de líneas, gráficos de barras, gráficos circulares, último valor conocido (LKV), indicadores clave de rendimiento (KPI), mapas térmicos y mapas.

* El icono cuadrado permite cambiar el tamaño del icono.

* El icono de engranaje permite configurar la visualización. Por ejemplo, para un gráfico de líneas puede elegir mostrar la leyenda y los ejes, y el intervalo de tiempo que se va a trazar.

## <a name="tile-types"></a>Tipos de iconos

En la tabla siguiente se describen los distintos tipos de icono que puede agregar a un panel:

| Icono             | Descripción |
| ---------------- | ----------- |
| Markdown         | Los iconos de Markdown son iconos interactivos con un encabezado y texto de descripción a los que se les aplica formato mediante Markdown. La dirección URL puede ser un vínculo relativo a otra página de la aplicación, o bien un vínculo absoluto a un sitio externo.|
| Imagen            | Los iconos de imagen muestran una imagen personalizada y se puede hacer clic en ellos. La dirección URL puede ser un vínculo relativo a otra página de la aplicación, o bien un vínculo absoluto a un sitio externo.|
| Etiqueta            | Los iconos de etiqueta muestran texto personalizado en un panel. Puede elegir el tamaño del texto. Use un icono de etiqueta para agregar información relevante al panel como, por ejemplo, descripciones, detalles de contacto o ayuda.|
| Count            | Los iconos de recuento muestran el número de dispositivos de un grupo de dispositivos.|
| Map(telemetry)              | Los iconos de mapa muestran la ubicación de uno o varios dispositivos en un mapa. También puede mostrar hasta cien puntos del historial de ubicación de un dispositivo. Por ejemplo, puede mostrar una ruta muestreada de la ubicación del dispositivo durante la última semana.|
| Map(property)              | Los iconos de mapa muestran la ubicación de uno o varios dispositivos en un mapa.|
| KPI              |  Los mosaicos de KPI muestran valores de telemetría agregados para uno o más dispositivos durante un período de tiempo. Por ejemplo, se pueden usar para mostrar la temperatura máxima y la presión alcanzadas para uno o varios dispositivos durante la última hora.|
| Gráfico de líneas       | Los iconos de gráfico de líneas representan uno o varios valores de telemetría agregados para uno o más dispositivos durante un período de tiempo. Por ejemplo, puede mostrar un gráfico de líneas que trace la temperatura y presión medias de uno o varios dispositivos durante la última hora.|
| Gráfico de barras        | Los iconos de gráfico de barras representan uno o varios valores de telemetría agregados para uno o más dispositivos durante un período de tiempo. Por ejemplo, puede mostrar un gráfico de barras que muestre la temperatura y presión medias de uno o varios dispositivos durante la última hora.|
| Gráfico circular        | Los iconos de gráfico circular muestran uno o varios valores de telemetría agregados para uno o más dispositivos durante un período de tiempo.|
| Mapa térmico         | Los iconos de mapa térmico muestran información sobre uno o varios dispositivos, representada en forma de colores.|
| Último valor conocido | Los iconos de último valor conocido muestran los valores de telemetría más recientes de uno o más dispositivos. Por ejemplo, puede usar este icono para mostrar los valores más recientes de temperatura, presión y humedad de uno o varios dispositivos. |
| Historial de eventos    | Los iconos del Historial de eventos muestran los eventos ocurridos en un dispositivo durante un período de tiempo. Por ejemplo, los puede usar para mostrar todos los eventos de apertura y cierre de válvulas de uno o más dispositivos durante la última hora.|
| Propiedad         |  Los iconos de propiedades muestran el valor actual de las propiedades y las propiedades de la nube de uno o varios dispositivos. Por ejemplo, puede usar este icono para mostrar las propiedades del dispositivo, como el fabricante o la versión de firmware. |
| Gráfico de estados         |  El gráfico de estados muestra los cambios de uno o varios dispositivos en un intervalo de tiempo establecido. Por ejemplo, puede usar este icono para mostrar las propiedades del dispositivo, como los cambios de temperatura de un dispositivo. |
| Gráfico de eventos         |  El gráfico de eventos muestra los eventos de telemetría de uno o varios dispositivos a lo largo de un intervalo de tiempo establecido. Por ejemplo, puede usar este icono para mostrar las propiedades, como los cambios de temperatura de un dispositivo. |
| Historial de los estados         |  El historial de estado enumera y muestra los cambios de estado de telemetría de estado.|
| Contenido externo         |  El icono de contenido externo permite cargar contenido externo desde un origen externo. |

Actualmente, puede agregar hasta 10 dispositivos a los mosaicos que admiten varios dispositivos.

### <a name="customizing-visualizations"></a>Personalización de visualizaciones

De forma predeterminada, los gráficos de líneas muestran datos de un intervalo de tiempo. El intervalo de tiempo seleccionado se divide en 50 cubos del mismo tamaño y, a continuación, los datos del dispositivo se agregan por cubo para proporcionar 50 puntos de datos durante el intervalo de tiempo seleccionado. Si desea ver los datos sin procesar, puede cambiar la selección para ver los últimos 100 valores. Para cambiar el intervalo de tiempo o para seleccionar la visualización de datos sin procesar, use le menú desplegable Mostrar intervalo en el panel **Configurar gráfico**.

:::image type="content" source="media/howto-manage-dashboards/display-range.png" alt-text="Cambiar el intervalo de visualización de un gráfico de líneas":::

En el caso de los iconos que muestran valores agregados, seleccione el icono de engranaje situado junto al tipo de telemetría en el panel **Configurar gráfico** para elegir la agregación. Puede elegir entre el promedio, la suma, el valor máximo, el valor mínimo y el recuento.

En el caso de los gráficos de líneas, de barras y circulares, puede personalizar el color de los distintos valores de telemetría. Seleccione el icono de paleta situado junto a la telemetría que quiere personalizar:

:::image type="content" source="media/howto-manage-dashboards/color-customization.png" alt-text="Cambio del color de un valor de telemetría":::

En el caso de los iconos que muestran propiedades de cadena o valores de telemetría, puede elegir cómo mostrar el texto. Por ejemplo, si el dispositivo almacena una dirección URL en una propiedad de cadena, puede mostrarla como un vínculo interactivo. Si la dirección URL hace referencia a una imagen, se puede representar la imagen en un último valor conocido o en el icono de propiedad. Para cambiar cómo se muestra una cadena, en la configuración del icono, seleccione el icono de engranaje situado junto a la propiedad o el tipo de telemetría:

:::image type="content" source="media/howto-manage-dashboards/string-customization.png" alt-text="Cambio de la representación de una cadena en un icono":::

Para los iconos de KPI, último valor conocido y propiedad, puede usar el formato condicional para personalizar el color del icono en función de su valor actual. Para agregar formato condicional, seleccione **Configurar** en el icono y, a continuación, seleccione el icono de **Formato condicional** situado junto al valor que se va a personalizar:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-1.png" alt-text="Captura de pantalla que muestra cómo buscar la opción de configuración de un icono y, después, el icono de formato condicional":::

Agregue las reglas de formato condicional:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-2.png" alt-text="Captura de pantalla que muestra las reglas de formato condicional para un flujo medio. Hay tres reglas: menos de 20 es verde, menos de 50 es amarillo y todo lo que está por encima de 50 es rojo":::

En la captura de pantalla siguiente se muestra el efecto de la regla de formato condicional:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-3.png" alt-text="Captura de pantalla que muestra el fondo de color rojo en el icono de caudal de agua promedio. El número del icono es 50.54":::

### <a name="tile-formatting"></a>Formato del icono

Esta característica, disponible en los iconos de KPI, LKV y Propiedad, permite a los usuarios ajustar el tamaño de fuente, elegir la precisión decimal, abreviar valores numéricos o ajustar valores de encapsulado en sus iconos.

:::image type="content" source="media/howto-manage-dashboards/tile-format.png" alt-text="Formato de icono":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a crear y administrar paneles personales, puede [obtener información sobre cómo administrar las preferencias de la aplicación](howto-manage-preferences.md).
