---
title: Creación y administración de paneles de Azure IoT Central | Microsoft Docs
description: Aprenda a crear y administrar paneles personales y de aplicaciones en Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/19/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: bcd9b0f4aa1b4a7f89479270ebce65fba9968e56
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810781"
---
# <a name="create-and-manage-dashboards"></a>Creación y administración de paneles

El *panel de la organización* predeterminado es la página que se carga cuando va por primera vez a la aplicación. Como administrador, puede crear paneles de la organización adicionales asociados a una organización específica. Un panel de la organización solo es visible para los usuarios que tienen acceso a la organización a la que está asociado el panel. Solo los usuarios con un rol que tenga [permisos de panel de la organización](howto-manage-users-roles.md#customizing-the-app) pueden crear, editar y eliminar paneles de la organización.

> [!TIP]
> Puede ver a qué organización está asociado un panel en la configuración del panel.

Todos los usuarios pueden crear sus propios *paneles personales*. Los usuarios pueden cambiar entre paneles de organizaciones y paneles personales.

## <a name="create-a-dashboard"></a>Creación de un panel

En la captura de pantalla siguiente se muestra el panel en una aplicación creada a partir de la plantilla **Aplicación personalizada**. Si tiene un rol con los permisos adecuados, puede personalizar el panel predeterminado. Para crear un nuevo panel, seleccione **+ Nuevo panel** en la esquina superior izquierda de la página:

:::image type="content" source="media/howto-manage-dashboards/dashboard-custom-app.png" alt-text="Captura de pantalla en la que se muestra el botón Nuevo panel.":::

En el panel **Crear panel**, asigne un nombre al panel y seleccione **Organización** o **Personal** como tipo de panel. Si va a crear un panel de la organización, elija la [organización](howto-create-organizations.md) a la que está asociado el panel. Un panel de la organización y sus iconos solo muestran los dispositivos que son visibles para la organización y cualquiera de sus suborganizaciones.

Después de crear el panel, elija los elementos de la biblioteca que desea agregar al panel. La biblioteca contiene los iconos y paneles primitivos que puede usar para personalizar el panel:

:::image type="content" source="media/howto-manage-dashboards/dashboard-library.png" alt-text="Captura de pantalla que muestra la biblioteca del panel.":::

Si es administrador, puede crear un panel personal o un panel de la organización. Los usuarios pueden ver los paneles de la organización asociados a la organización a la que están asignados. Todos los usuarios pueden crear paneles personales que solo ellos pueden ver.

Especifique un título y seleccione el tipo de panel que desea crear. [Agregue iconos](#add-tiles) para personalizar el panel.

> [!TIP]
> Necesita tener al menos una plantilla de dispositivo en la aplicación para poder agregar iconos que muestren la información del dispositivo.

## <a name="manage-dashboards"></a>Administración de paneles

Puede tener varios paneles personales y cambiar entre ellos o elegir uno de los paneles de la organización:

:::image type="content" source="media/howto-manage-dashboards/switch-dashboards.png" alt-text="Captura de pantalla que muestra cómo cambiar entre paneles.":::

Puede editar los paneles personales y eliminar los que no necesite. Si tiene los [permisos](howto-manage-users-roles.md#customizing-the-app) correctos, también puede editar o eliminar paneles de la organización.

:::image type="content" source="media/howto-manage-dashboards/delete-dashboards.png" alt-text="Captura de pantalla en la que se muestra cómo eliminar un panel.":::

Para cambiar el nombre de un panel o ver la organización a la que está asignado, seleccione **Configuración del panel**:

:::image type="content" source="media/howto-manage-dashboards/rename-dashboards.png" alt-text="Captura de pantalla en la que se muestra cómo cambiar el nombre de un panel.":::

## <a name="add-tiles"></a>Agregar iconos

En la captura de pantalla siguiente se muestra el panel en una aplicación creada a partir de la plantilla **Aplicación personalizada**. Para personalizar el panel actual, seleccione **Editar**. Para agregar un panel personal o de la organización, seleccione **Nuevo panel**:

:::image type="content" source="media/howto-manage-dashboards/dashboard-sample-contoso.png" alt-text="Captura de pantalla en la que se muestra un panel para aplicaciones basadas en la plantilla de aplicación personalizada.":::

Después de seleccionar **Editar** o **Nuevo panel**, el panel se encuentra en modo de *edición*. Puede usar las herramientas del panel **Editar panel** para agregar iconos al panel. Puede personalizar y quitar iconos en el propio panel. Por ejemplo, para agregar un icono de gráfico de líneas a fin realizar el seguimiento de los valores de telemetría notificados por uno o varios dispositivos en el tiempo:

1. Seleccione **Start with a Visual** (Comenzar con un objeto visual), **Gráfico de líneas** y después **Agregar icono**, o simplemente arrastre el icono hasta el lienzo.
 
1. Para configurar el icono, seleccione su botón de **engranaje**. Escriba un **Título** y seleccione un **Grupo de dispositivos**. En la lista **Dispositivos**, seleccione los dispositivos que se van a mostrar en el icono.

   :::image type="content" source="media/howto-manage-dashboards/device-details.png" alt-text="Captura de pantalla en la que se muestra cómo agregar un icono a un panel.":::

1. Después de seleccionar todos los dispositivos que se mostrarán en el icono, seleccione **Actualizar**.

1. Cuando haya terminado de agregar y personalizar los iconos en el panel, seleccione **Guardar**. Al hacerlo, saldrá del modo de edición.

## <a name="customize-tiles"></a>Personalización de iconos

Para editar un icono, debe estar en el modo de edición. Los distintos [tipos de icono](#tile-types) tienen diferentes opciones que se pueden personalizar:

* El botón de **regla** de un icono permite cambiar la visualización. Las visualizaciones incluyen gráficos de líneas, gráficos de barras, gráficos circulares, último valor conocido (LKV), indicadores clave de rendimiento (KPI), mapas térmicos y mapas.

* El botón **cuadrado** permite cambiar el tamaño del icono.

* El botón de **engranaje** permite configurar la visualización. Por ejemplo, para un gráfico de líneas, puede elegir mostrar la leyenda y los ejes, y el intervalo de tiempo que se va a trazar.

## <a name="tile-types"></a>Tipos de iconos

En esta tabla se describen los tipos de icono que puede agregar a un panel:

| Icono             | Descripción |
| ---------------- | ----------- |
| Markdown         | Los iconos de Markdown son iconos interactivos con un título y texto de descripción con formato de Markdown. La dirección URL puede ser un vínculo relativo a otra página de la aplicación, o bien un vínculo absoluto a un sitio externo.|
| Imagen            | Los iconos de imagen muestran una imagen personalizada y se puede hacer clic en ellos. La dirección URL puede ser un vínculo relativo a otra página de la aplicación, o bien un vínculo absoluto a un sitio externo.|
| Etiqueta            | Los iconos de etiqueta muestran texto personalizado en un panel. Puede elegir el tamaño del texto. Use un icono de etiqueta para agregar información relevante al panel como descripciones, detalles de contacto o ayuda.|
| Count            | Los iconos de recuento muestran el número de dispositivos de un grupo de dispositivos.|
| Mapa (telemetría)              | Los iconos de mapa muestran la ubicación de uno o varios dispositivos en un mapa. También puede mostrar hasta cien puntos del historial de ubicación de un dispositivo. Por ejemplo, puede mostrar una ruta muestreada de la ubicación del dispositivo durante la última semana.|
| Mapa (propiedad)              | Los iconos de mapa muestran la ubicación de uno o varios dispositivos en un mapa.|
| KPI              |  Los mosaicos de KPI muestran valores de telemetría agregados para uno o más dispositivos durante un período de tiempo. Por ejemplo, se pueden usar para mostrar la temperatura máxima y la presión alcanzadas para uno o varios dispositivos durante la última hora.|
| Gráfico de líneas       | Los iconos de gráfico de líneas representan uno o varios valores de telemetría agregados para uno o más dispositivos durante un período de tiempo. Por ejemplo, puede mostrar un gráfico de líneas que trace la temperatura y presión medias de uno o varios dispositivos durante la última hora.|
| Gráfico de barras        | Los iconos de gráfico de barras representan uno o varios valores de telemetría agregados para uno o más dispositivos durante un período de tiempo. Por ejemplo, puede mostrar un gráfico de barras que represente la temperatura y presión medias de uno o varios dispositivos durante la última hora.|
| Gráfico circular        | Los iconos de gráfico circular muestran uno o varios valores de telemetría agregados para uno o más dispositivos durante un período de tiempo.|
| Mapa térmico         | Los iconos de mapa térmico muestran información representada en forma de colores sobre uno o varios dispositivos.|
| Último valor conocido | Los iconos de último valor conocido muestran los valores de telemetría más recientes de uno o más dispositivos. Por ejemplo, puede usar este icono para mostrar los valores más recientes de temperatura, presión y humedad de uno o varios dispositivos. |
| Historial de eventos    | Los iconos de historial de eventos muestran los eventos ocurridos en un dispositivo durante un período de tiempo. Por ejemplo, los puede usar para mostrar todos los eventos de apertura y cierre de válvulas de uno o más dispositivos durante la última hora.|
| Propiedad         |  Los iconos de propiedad muestran los valores actuales de las propiedades y las propiedades de la nube de uno o varios dispositivos. Por ejemplo, este icono se puede usar para mostrar las propiedades del dispositivo, como el fabricante o la versión de firmware. |
| Gráfico de estados         |  Los iconos de gráfico de estados muestran los cambios de uno o varios dispositivos durante un período de tiempo. Por ejemplo, puede usar este icono para mostrar propiedades como los cambios de temperatura de un dispositivo. |
| Gráfico de eventos         |  Los iconos de gráfico de eventos muestran eventos de telemetría de uno o varios dispositivos durante un período de tiempo. Por ejemplo, puede usar este icono para mostrar propiedades como los cambios de temperatura de un dispositivo. |
| Historial de estado         |  Los iconos de historial de estado enumeran y muestran los cambios de estado de telemetría.|
| Contenido externo         |  Los iconos de contenido externo permiten cargar contenido desde un origen externo. |

Actualmente, puede agregar hasta 10 dispositivos a los mosaicos que admiten varios dispositivos.

### <a name="customize-visualizations"></a>Personalización de visualizaciones

De forma predeterminada, los gráficos de líneas muestran datos de un intervalo de tiempo. El intervalo de tiempo seleccionado se divide en 50 particiones del mismo tamaño. Después, los datos de dispositivo se agregan por cada partición, a fin de obtener 50 puntos de datos para el intervalo de tiempo seleccionado. Si quiere ver los datos sin procesar, puede cambiar la selección para ver los últimos 100 valores. Para cambiar el intervalo de tiempo o para seleccionar la visualización de datos sin procesar, use la lista desplegable **Mostrar rango** del panel **Configurar gráfico**:

:::image type="content" source="media/howto-manage-dashboards/display-range.png" alt-text="Captura de pantalla en la que se muestra la lista desplegable Mostrar rango.":::

Para los iconos que muestran valores agregados, seleccione el botón de **engranaje** situado junto al tipo de telemetría en el panel **Configurar gráfico** para elegir la agregación. Puede elegir el promedio, la suma, el valor máximo, el valor mínimo o el recuento.

En el caso de los gráficos de líneas, de barras y circulares, puede personalizar el color de los distintos valores de telemetría. Seleccione el botón de **paleta** situado junto a la telemetría que quiera personalizar:

:::image type="content" source="media/howto-manage-dashboards/color-customization.png" alt-text="Captura de pantalla en la que se muestra el botón de paleta.":::

En el caso de los iconos que muestran propiedades de cadena o valores de telemetría, puede elegir cómo mostrar el texto. Por ejemplo, si el dispositivo almacena una dirección URL en una propiedad de cadena, puede mostrarla como un vínculo interactivo. Si la dirección URL hace referencia a una imagen, se puede representar la imagen en un último valor conocido o en el icono de propiedad. Para cambiar cómo se muestra una cadena, seleccione el botón de **engranaje** situado junto a la propiedad o el tipo de telemetría en la configuración del icono.

:::image type="content" source="media/howto-manage-dashboards/string-customization.png" alt-text="Captura de pantalla en la que se muestra cómo cambiar el modo en que se muestra una cadena en un icono.":::

Para los iconos de KPI, último valor conocido y propiedad, puede usar el formato condicional para personalizar el color del icono en función de su valor. Para agregar formato condicional, seleccione **Configurar** en el icono y después el botón **Formato condicional** situado junto al valor que quiera personalizar:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-1.png" alt-text="Captura de pantalla en la que se muestra el botón Formato condicional.":::

Después, agregue las reglas de formato condicional:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-2.png" alt-text="Captura de pantalla en la que se muestran las reglas de formato condicional para la memoria disponible. Hay reglas para menor que, mayor que y mayor o igual que.":::

En la captura de pantalla siguiente se muestra el efecto de estas reglas de formato condicional:

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-3.png" alt-text="Captura de pantalla en la que se muestra un color de fondo púrpura en el icono de memoria disponible.":::

### <a name="tile-formatting"></a>Formato del icono

Esta característica está disponible en los iconos de KPI, último valor conocido y propiedad. Permite ajustar el tamaño de fuente, elegir la precisión decimal, abreviar valores numéricos o encapsular valores de cadena en sus iconos.

:::image type="content" source="media/howto-manage-dashboards/tile-format.png" alt-text="Captura de pantalla en la que se muestra el cuadro de diálogo para el formato del icono.":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a crear y administrar paneles personales, puede [obtener información sobre cómo administrar las preferencias de la aplicación](howto-manage-preferences.md).
