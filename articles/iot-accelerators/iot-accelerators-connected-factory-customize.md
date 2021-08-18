---
title: 'Personalizar la solución de factoría conectada: Azure | Microsoft Docs'
description: En este artículo se describe cómo personalizar el comportamiento del acelerador de la solución de la factoría conectada.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: a95807bc3b209a2b410b205b6d473560b454b2b0
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113758230"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalizar la manera en que la solución de factoría conectada muestra los datos de los servidores de OPC UA

> [!IMPORTANT]
> Mientras actualizamos este artículo, consulte el artículo sobre [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/), donde encontrará el contenido más actualizado.

La solución de factoría conectada agrega y muestra datos de los servidores de OPC UA conectados a la solución. Puede examinar y enviar comandos a los servidores de OPC UA en la solución. Para más información acerca de OPC UA, consulte las [preguntas frecuentes sobre la factoría conectada](iot-accelerators-faq-cf.yml).

Entre los ejemplos de datos agregados en la solución se pueden mencionar Overall Equipment Efficiency (OEE) (Eficacia general de equipos) y Key Performance Indicators (Indicadores clave de rendimiento) (KPI) que se pueden ver en el panel en los ámbitos de factoría, línea y estación. En las capturas de pantalla siguientes se muestran los valores de OEE y KPI para la estación **Assembly**, en **Production line 1**, en la factoría **Munich**:

![Ejemplo de valores de OEE y KPI en la solución][img-oee-kpi]

La solución le permite ver información detallada de elementos de datos específicos de los servidores de OPC UA, llamados *estaciones*. En la captura de pantalla siguiente se muestran los trazados del número de artículos fabricados desde una estación específica:

![Trazados del número de artículos fabricados][img-manufactured-items]

Si hace clic en uno de los gráficos, puede explorar los datos aún más con Time Series Insights (TSI):

![Exploración de datos con Time Series Insights][img-tsi]

En este artículo se describe:

- Cómo los datos se ponen a disposición de las distintas vistas de la solución.
- Cómo puede personalizar la forma en que la solución muestra los datos.

## <a name="data-sources"></a>Orígenes de datos

La solución de factoría conectada muestra datos de los servidores de OPC UA conectados a la solución. La instalación predeterminada incluye varios servidores de OPC UA que ejecutan una simulación de factoría. Puede agregar sus propios servidores OPC UA que se [conectan a través de una puerta de enlace] [lnk-connect-cf] a la solución.

Puede examinar los elementos de datos que un servidor de OPC UA conectado puede enviar a la solución en el panel:

1. Para ir a la vista **Select an OPC UA server** (Seleccionar un servidor de OPC UA), seleccione **Explorador**:

    ![Navegación hasta la vista Select an OPC UA server (Seleccionar un servidor de OPC UA)][img-select-server]

1. Seleccione un servidor y haga clic en **Connect** (Conectar). Haga clic en **Proceed** (Continuar) cuando aparezca la advertencia de seguridad.

    > [!NOTE]
    > Esta advertencia solo aparece una vez para cada servidor y establece una relación de confianza entre el panel de la solución y el servidor.

1. Ahora puede examinar los elementos de datos que el servidor puede enviar a la solución. Los elementos que se envían a la solución tienen la marca de verificación siguiente:

    ![Elementos publicados][img-published]

1. Si es un *administrador* de la solución, puede publicar un elemento de datos para que esté disponible en la solución de factoría conectada. Como administrador, también puede cambiar el valor de los elementos de datos y llamar a métodos en el servidor de OPC UA.

## <a name="map-the-data"></a>Asignación de datos

La solución de factoría conectada asigna y agrega los elementos de datos publicados desde el servidor de OPC UA a las distintas vistas de la solución. La solución de factoría conectada se implementa en su cuenta de Azure al aprovisionar la solución. Un archivo JSON de la solución de factoría conectada de Visual Studio almacena esta información de asignación. Puede ver y modificar este archivo de configuración de JSON en la solución de Visual Studio de factoría conectada. Puede volver a implementar la solución después de realizar un cambio.

Puede usar el archivo de configuración para:

- Editar las líneas de producción, las estaciones y las factorías simuladas existentes.
- Asignar datos de los servidores de OPC UA reales que se conectan a la solución.

Para obtener más información acerca de cómo asignar y agregar los datos para satisfacer necesidades específicas, consulte [Configuración del acelerador de la solución de factoría conectada](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Implementación de los cambios

Cuando haya terminado de realizar cambios en el archivo **ContosoTopologyDescription.json**, debe volver a implementar la solución de factoría conectada en su cuenta de Azure.

El repositorio **-azure-iot-connected-factory** incluye un script de PowerShell **build.ps1** que puede usar para volver a compilar e implementar la solución.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el acelerador de la solución de factoría conectada, lea los siguientes artículos:

* [Permisos en el sitio azureiotsolutions.com][lnk-permissions]
* [Preguntas frecuentes sobre factoría conectada](iot-accelerators-faq-cf.yml)
* [P+F][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.yml