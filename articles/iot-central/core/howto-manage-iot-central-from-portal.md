---
title: Administración y supervisión de IoT Central desde Azure Portal | Microsoft Docs
description: En este artículo se describe cómo crear administrar y supervisar las aplicaciones de IoT Central desde Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.openlocfilehash: 57486312b380fc18cfdb399343535e5e12245bff
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091598"
---
# <a name="manage-and-monitor-iot-central-from-the-azure-portal"></a>Administración y supervisión de IoT Central desde Azure Portal

Puede usar [Azure Portal](https://portal.azure.com) para crear, administrar y supervisar aplicaciones de IoT Central.

## <a name="create-iot-central-applications"></a>Creación de aplicaciones de IoT Central

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Para crear una aplicación, vaya a la página [Aplicación IoT Central](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) en Azure Portal:

![Creación de formularios de IoT Central](media/howto-manage-iot-central-from-portal/create-form.png)

* El **nombre del recurso** es un nombre único que puede elegir para la aplicación IoT Central en el grupo de recursos de Azure.

* La **dirección URL de la aplicación** es la dirección URL que puede usar para acceder a la aplicación.

* La **plantilla** es el tipo de aplicación de IoT Central que quiere crear. Puede crear una nueva aplicación desde la lista de plantillas relevantes del sector para empezar a trabajar rápidamente o desde cero con la plantilla **Aplicación personalizada**.

* **Ubicación** es la [zona geográfica](https://azure.microsoft.com/global-infrastructure/geographies/) en la que desea crear la aplicación. Normalmente, se debe elegir la ubicación más cercana físicamente a los dispositivos para un rendimiento óptimo. Azure IoT Central está disponible actualmente en las siguientes ubicaciones:
    
    * Australia
    * Centro-este de EE. UU.
    * Este de EE. UU.
    * Este de EE. UU. 2
    * Japón Oriental
    * Norte de Europa
    * Sudeste de Asia
    * Sur de Reino Unido
    * Oeste de Europa
    * Oeste de EE. UU.

  Una vez que elija una ubicación, no puede mover la aplicación a otra más adelante.

Después de rellenar todos los campos, seleccione **Crear**. Para obtener más información, vea [Creación de una aplicación de Azure IoT Central](howto-create-iot-central-application.md).

## <a name="manage-existing-iot-central-applications"></a>Administrar las aplicaciones existentes de IoT Central

Si ya tiene una aplicación de Azure IoT Central, puede eliminarla o moverla a un grupo de recursos o a una suscripción diferentes en Azure Portal.

> [!NOTE]
> Las aplicaciones creadas con el plan *gratuito* no requieren suscripciones de Azure y, por lo tanto, no aparecerán en la suscripción de Azure en Azure Portal. Solo puede ver y administrar las aplicaciones gratuitas del portal de IoT Central.

Para empezar, busque la aplicación en la barra de búsqueda de la parte superior de Azure Portal. También puede ver todas las aplicaciones buscando _aplicaciones de IoT Central_ y seleccionando el servicio:

![Captura de pantalla que muestra los resultados de la búsqueda de "aplicaciones de IoT Central" con el primer servicio seleccionado.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Al seleccionar una aplicación en los resultados de la búsqueda, Azure Portal muestra su información general. Para ir a la aplicación, puede seleccionar la **URL de la aplicación de IoT Central**:

![Captura de pantalla que muestra la página "Información general" con la opción "Dirección URL de la aplicación de IoT Central" resaltada.](media/howto-manage-iot-central-from-portal/highlight-application.png)

Para mover la aplicación a otro grupo de recursos, seleccione **Cambiar** al lado del grupo de recursos. En la página **Mover recursos**, seleccione el grupo de recursos al que le gustaría mover esta aplicación:

![Captura de pantalla que muestra la página "Información general" con la opción "Grupo de recursos (cambiar)" resaltada.](media/howto-manage-iot-central-from-portal/highlight-resource-group.png)

Para mover la aplicación a otra suscripción, seleccione **Cambiar** al lado de la suscripción. En la página **Mover recursos**, elija la suscripción a la que le gustaría mover esta aplicación:

![Portal de administración: administración de recursos](media/howto-manage-iot-central-from-portal/highlight-subscription.png)

## <a name="monitor-application-health"></a>Supervisión del estado de la aplicación

> [!NOTE]
> Las métricas solo están disponibles para las aplicaciones de IoT Central, versión 3. Para obtener información sobre cómo comprobar la versión de la aplicación, vea [¿Cómo obtengo información sobre mi aplicación?](howto-faq.yml#how-do-i-get-information-about-my-application-).

Puede usar el conjunto de métricas que proporciona IoT Central para evaluar el estado de los dispositivos conectados a la aplicación de IoT Central y el estado de las exportaciones de datos en ejecución.

De forma predeterminada, las métricas están habilitadas para la aplicación de IoT Central y se accede a estas desde [Azure Portal](https://portal.azure.com/). La [plataforma de datos de Azure Monitor expone estas métricas](../../azure-monitor/essentials/data-platform-metrics.md) y proporciona varias formas de interactuar con ellas. Por ejemplo, puede usar los gráficos de Azure Portal, una API de REST o consultas de PowerShell o la CLI de Azure.

> [!TIP]
> Las aplicaciones que usan el plan de evaluación gratuita no tienen una suscripción de Azure asociada, por lo que no son compatibles con las métricas de Azure Monitor. Puede [convertir una aplicación a un plan de precios estándar](./howto-faq.yml#how-do-i-move-from-a-free-to-a-standard-pricing-plan-) y obtener acceso a estas métricas.

### <a name="view-metrics-in-the-azure-portal"></a>Visualización de métricas en Azure Portal

En los pasos siguientes se supone que tiene una [aplicación de IoT Central](./howto-create-iot-central-application.md) con algunos [dispositivos conectados](./tutorial-connect-device.md) o una [exportación de datos](howto-export-data.md) en ejecución.

Para ver las métricas de IoT Central en el portal:

1. Navegue hasta el recurso de la aplicación de IoT Central en el portal. De forma predeterminada, los recursos de IoT Central se encuentran en un grupo de recursos denominado **IOTC**.
1. Para crear un gráfico a partir de las métricas de la aplicación, seleccione **Métricas** en la sección **Supervisión**.

![Métricas de Azure](media/howto-manage-iot-central-from-portal/metrics.png)

### <a name="azure-portal-permissions"></a>Permisos de Azure Portal

El acceso a las métricas en Azure Portal se administra mediante el [control de acceso basado en rol de Azure](../../role-based-access-control/overview.md). Use Azure Portal para agregar usuarios a la aplicación de IoT Central, el grupo de recursos o la suscripción para concederles acceso. Debe agregar un usuario en el portal, aunque ya se haya agregado a la aplicación de IoT Central. Use [roles integrados de Azure](../../role-based-access-control/built-in-roles.md) para lograr un control de acceso más detallado.

### <a name="iot-central-metrics"></a>Métricas de IoT Central

Para obtener una lista de las métricas que están disponibles actualmente en IoT Central, consulte [Métricas compatibles con Azure Monitor](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Métricas y facturas

Las métricas pueden diferir de los números que se muestran en la factura de Azure IoT Central. Esta situación puede producirse por diversas razones, como:

* Los [planes de precios estándar](https://azure.microsoft.com/pricing/details/iot-central/) de IoT Central incluyen dos dispositivos y distintas cuotas de mensajes de forma gratuita. Aunque los elementos gratuitos se excluyen de la facturación, todavía se cuentan en las métricas.

* IoT Central genera automáticamente un identificador de dispositivo de prueba para cada plantilla de dispositivo en la aplicación. Este identificador de dispositivo es visible en la página **Administrar el dispositivo de prueba** para una plantilla de dispositivo. Puede optar por validar las plantillas de dispositivo antes de publicarlas mediante la generación del código que usa estos identificadores de dispositivo de prueba. Aunque estos dispositivos se excluyen de la facturación, todavía se cuentan en las métricas.

* Aunque las métricas pueden mostrar un subconjunto de las comunicaciones del dispositivo a la nube, todas las comunicaciones entre el dispositivo y la nube [cuentan como un mensaje para la facturación](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="monitor-connected-iot-edge-devices"></a>Supervisión de dispositivos IoT Edge conectados

Para obtener información sobre cómo supervisar de forma remota la flota de IoT Edge mediante la integración de métricas integradas de Azure Monitor, vea [Recopilación y transporte de métricas](../../iot-edge/how-to-collect-and-transport-metrics.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar y supervisar aplicaciones de Azure IoT Central desde Azure Portal, este es el paso siguiente sugerido:

> [!div class="nextstepaction"]
> [Administrar su aplicación](howto-administer.md)