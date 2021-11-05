---
title: Archivo de inclusión
description: archivo de inclusión
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/20/2021
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a686ab26c93130d7371bcf2435dccbd7aaa1c985
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070431"
---
Ahora que tiene un destino al que exportar los datos, configure la exportación de datos en su aplicación de IoT Central:

1. Inicie sesión en su aplicación de IoT Central.

1. En el panel izquierdo, seleccione **Exportación de datos**.

    > [!Tip]
    > Si no ve **Exportación de datos** en el panel izquierdo, no tiene permisos para configurar la exportación de datos en la aplicación. Hable con un administrador para configurar la exportación de datos.

1. Seleccione **+ New export** (+ Nueva exportación).

1. Escriba un nombre para mostrar para la nueva exportación y asegúrese de que la exportación de datos está **habilitada**.

1. Elija el tipo de datos para exportar. En la tabla siguiente se enumeran los tipos de exportación de datos compatibles:

    | Tipo de datos | Descripción | Formato de datos |
    | :------------- | :---------- | :----------- |
    |  Telemetría | Exporte los mensajes de telemetría de los dispositivos casi en tiempo real. Cada mensaje exportado contiene todo el contenido del mensaje del dispositivo original, normalizado.   |  [Formato del mensaje de telemetría](#telemetry-format)   |
    | Cambios de la propiedad | Exporte los cambios de las propiedades de dispositivos y la nube casi en tiempo real. En el caso de las propiedades de dispositivo de solo lectura, se exportan los cambios realizados en los valores notificados. En el caso de las propiedades de lectura y escritura, se exportan los valores notificados y deseados. | [Formato de los mensajes de cambio de propiedad](#property-changes-format) |
    | Conectividad de dispositivos | Exporte eventos de dispositivos conectados y desconectados. | [Formato de mensaje de conectividad del dispositivo](#device-connectivity-changes-format) |
    | Ciclo de vida del dispositivo | Exporte los eventos registrados, eliminados, aprovisionados, habilitados, deshabilitados, displayNameChanged y deviceTemplateChanged. | [Formato de los mensajes de cambios del ciclo de vida de dispositivo](#device-lifecycle-changes-format) |
    | Ciclo de vida de plantillas de dispositivo | Exporte los cambios de plantilla de dispositivo publicados, incluidos los creados, actualizados y eliminados. | [Formato de los mensajes de cambios del ciclo de vida de plantilla de dispositivo](#device-template-lifecycle-changes-format) |

1. Opcionalmente, agregue filtros para reducir la cantidad de datos exportados. Hay diferentes tipos de filtros disponibles para cada tipo de exportación de datos: <a name="DataExportFilters"></a>

    | Tipo de datos | Filtros disponibles|
    |--------------|------------------|
    |Telemetría|<ul><li>Filtrar por nombre de dispositivo, id. de dispositivo, plantilla de dispositivo y si el dispositivo está simulado</li><li>Filtrar el flujo para que solo contenga la telemetría que cumpla las condiciones de filtro</li><li>Filtrar el flujo para que solo contenga la telemetría de dispositivos con propiedades que coincidan con las condiciones de filtro</li><li>Filtrar el flujo para que solo contenga la telemetría que tenga *propiedades de mensaje* que cumplan la condición de filtro. Las *propiedades de mensaje* (también denominadas *propiedades de aplicación*) se envían en un contenedor de pares de clave-valor en cada mensaje de telemetría enviado opcionalmente por los dispositivos que usan los SDK de dispositivo. Para crear un filtro de propiedad de mensaje, escriba la clave de la propiedad de mensaje que busca y especifique una condición. Solo se exportarán los mensajes de telemetría con propiedades que coincidan con la condición de filtro especificada. [Obtenga más información sobre las propiedades de aplicaciones en la documentación de IoT Hub](../articles/iot-hub/iot-hub-devguide-messages-construct.md). </li></ul>|
    |Cambios de la propiedad|<ul><li>Filtrar por nombre de dispositivo, id. de dispositivo, plantilla de dispositivo y si el dispositivo está simulado</li><li>Filtrar el flujo para que contenga solo los cambios de propiedad que cumplan las condiciones de filtro</li></ul>|
    |Conectividad de dispositivos|<ul><li>Filtrar por nombre de dispositivo, identificador de dispositivo, plantilla de dispositivo y si el dispositivo es simulado</li><li>Filtrar el flujo para que solo contenga los cambios de dispositivos con propiedades que coincidan con las condiciones de filtro</li></ul>|
    |Ciclo de vida del dispositivo|<ul><li>Filtrar por nombre de dispositivo, id. de dispositivo, plantilla de dispositivo y si el dispositivo está aprovisionado, habilitado o simulado</li><li>Filtrar el flujo para que solo contenga los cambios de dispositivos con propiedades que coincidan con las condiciones de filtro</li></ul>|
    |Ciclo de vida de plantillas de dispositivo|<ul><li>Filtrar por plantilla de dispositivo</li></ul>|

1. Opcionalmente, puede enriquecer los mensajes exportados con metadatos adicionales de pares clave-valor. Están disponibles los enriquecimientos siguientes para los tipos de exportación de datos de telemetría, cambios de propiedad, conectividad de dispositivo y ciclo de vida de dispositivo: <a name="DataExportEnrichmnents"></a>
    - **Cadena personalizada**: agrega una cadena estática personalizada a cada mensaje. Escriba cualquier clave y especifique cualquier valor de cadena.
    - **Propiedad**, que agrega a cada mensaje:
       - Metadatos del dispositivo, como el nombre del dispositivo, el nombre de la plantilla de dispositivo, si está habilitado, las organizaciones, si está aprovisionado y si es simulado.
       - La propiedad notificada de dispositivo actual o el valor de propiedad de la nube a cada mensaje. Si el mensaje exportado procede de un dispositivo que no tiene la propiedad especificada, no obtendrá el enriquecimiento.
