---
title: 'Tutorial: Configuración manual de Azure Front Door para Azure Static Web Apps'
description: Aprenda a configurar Azure Front Door para Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cshoe
ms.openlocfilehash: aa377c63f0282bb43e8f69088f7925d9ac5ffa0d
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400693"
---
# <a name="tutorial-manually-configure-azure-front-door-for-azure-static-web-apps"></a>Tutorial: Configuración manual de Azure Front Door para Azure Static Web Apps

Aprenda a agregar [Azure Front Door](../frontdoor/front-door-overview.md) como la CDN para la aplicación web estática.  Azure Front Door es un punto de entrada escalable y seguro para la entrega rápida de aplicaciones web.

En este tutorial, aprenderá a:

> [!div class="checklist"]
>
> - Creación de una instancia de Azure Front Door
> - Asociación de Azure Font Door con el sitio de Azure Static Web Apps

> [!NOTE]
> Este tutorial requiere el plan Azure Static Web Apps Standard.

## <a name="copy-url"></a>Copiar la dirección URL

1. Acceda a Azure Portal.

1. Abra la aplicación web estática en la que desea aplicar Azure Front Door.

1. Vaya a la sección de *información general*.

1. Copie la *Dirección URL* en el Portapapeles para usarla más adelante.

## <a name="add-azure-front-door"></a>Adición de Azure Front Door

1. Acceda a Azure Portal.

1. Seleccione **Crear un recurso**.

1. Busque **Azure Front Door**.

1. Seleccione **Front Door**.

    Asegúrese de no seleccionar el servicio etiquetado como *Front Door Standard/Premium*, ya que los pasos para el servicio Standard/Premium difieren de los que se presentan en este tutorial.

1. Seleccione **Crear**.

1. En la pestaña *Aspectos básicos*, escriba los valores siguientes:

    | Configuración | Valor |
    |---|---|
    | Subscription | Seleccione su suscripción a Azure. |
    | Resource group | Escriba el nombre de un grupo de recursos. Este nombre suele ser el mismo nombre de grupo que usa la aplicación web estática. |
    | Ubicación del grupo de recursos | Si crea un grupo de recursos, escriba la ubicación más cercana. |

    Seleccione **Siguiente: Configuración >** .

1. En la pestaña *Configuración*, seleccione el **signo más** junto a *Front-ends/dominios* y escriba el siguiente valor:

    | Configuración | Valor |
    |---|---|
    | Nombre de host | Escriba un nombre único para el host de Front Door. |

    Acepte los valores predeterminados para el resto del formulario y seleccione **Agregar**.

1. Seleccione el **signo más** junto a *Grupos de back-end*, y escriba el siguiente valor:

    | Configuración | Valor |
    |---|---|
    | Nombre | Escriba **my-static-web-app-pool**. |

1. Seleccione **+ Agregar un back-end** y escriba los siguientes valores:

    | Configuración | Valor |
    |---|---|
    | Tipo de host de back-end | Seleccione **Host personalizado**. |
    | Nombre de host de back-end | Escriba el nombre de host de la aplicación web estática. Asegúrese de que el valor no incluye un protocolo o una barra diagonal final. (Por ejemplo, `desert-rain-04056.azurestaticapps.net`)  |
    | Encabezado host de back-end. | Escriba el nombre de host de la aplicación web estática. Asegúrese de que el valor no incluye un protocolo de barra diagonal final. (Por ejemplo, `desert-rain-04056.azurestaticapps.net`) |

    Acepte los valores predeterminados para el resto del formulario y seleccione **Agregar**.

1. Seleccione **Agregar**.

1. Seleccione el **signo más** junto a *Regla de enrutamiento*, y escriba el siguiente valor:

    | Configuración | Valor |
    |---|---|
    | Nombre | Escriba **my-routing-rule**. |

    Acepte los valores predeterminados para el resto del formulario y seleccione **Agregar**.

1. Seleccione **Revisar + crear**.

1. Seleccione **Crear**.

    El proceso de creación puede tardar unos minutos en completarse.

1. Haga clic en **Go to resource** (Ir al recurso).

1. Seleccione **Información general**.

1. Seleccione el vínculo con la etiqueta *Host de front-end*.

    Al seleccionar este vínculo, es posible que vea un error 404 si el sitio no se propaga por completo. En lugar de actualizar la página, espere unos minutos y vuelva a la ventana *Información general* y seleccione el vínculo con la etiqueta *Host de front-end*.

1. En la ventana *Información general*, copie el valor etiquetado como **Id. de Front Door** y péguelo en un archivo para usarlo más adelante.

> [!IMPORTANT]
> De forma predeterminada, Azure Front Door configura [sondeos de estado](../frontdoor/front-door-health-probes.md) que pueden afectar a las estadísticas de tráfico. Es posible que desee editar los valores predeterminados para los [sondeos de estado](../frontdoor/front-door-health-probes.md).

## <a name="update-static-web-app-configuration"></a>Actualización de la configuración de la aplicación web estática

Para completar la integración con Front Door, debe actualizar el archivo de configuración de la aplicación para:

- Restringir el tráfico al sitio solo a través de Front Door
- Restringir el tráfico al sitio solo desde la instancia de Front Door
- Definir qué dominios pueden acceder a tu sitio

Abra el archivo [staticwebapp.config.json](configuration.md) para el sitio y realice los cambios siguientes.

1. Restrinja el tráfico para que se use solo Front Door mediante la adición de la siguiente sección al archivo de configuración:

    ```json
    "networking": {
      "allowedIpRanges": ["AzureFrontDoor.Backend"]
    }
    ```

1. Para definir qué dominios e instancias de Azure Front Door pueden acceder al sitio, agregue la sección `forwardingGateway`.

    ```json
    "forwardingGateway": {
      "requiredHeaders": {
        "X-Azure-FDID" : "<YOUR-FRONT-DOOR-ID>"
      },
      "allowedForwardedHosts": [
        "my-sitename.azurefd.net"
      ]
    }
    ```

    En primer lugar, configure la aplicación para que solo permita el tráfico desde la instancia de Front Door. En cada solicitud de back-end, Front Door agrega automáticamente un encabezado `X-Azure-FDID` que contiene el identificador de la instancia de Front Door. Al configurar la aplicación web estática para que requiera este encabezado, restringirá el tráfico exclusivamente a la instancia de Front Door. En la sección `forwardingGateway` del archivo de configuración, agregue la sección `requiredHeaders` y defina el encabezado `X-Azure-FDID`. Reemplace `<YOUR-FRONT-DOOR-ID>` por el *Id. de Front Door* que reservó anteriormente.

    A continuación, agregue el nombre de host de Azure Front Door (no el nombre de host de Azure Static Web Apps) a la matriz `allowedForwardedHosts`. Si tiene dominios personalizados configurados en la instancia de Front Door, inclúyalos también en esta lista.

    En este ejemplo, reemplace `my-sitename.azurefd.net` por el nombre de host de Azure Front Door para el sitio.

Con esta configuración, el sitio ya no estará disponible a través del nombre de host `*.azurestaticapps.net` generado, sino exclusivamente a través de los nombres de host configurados en la instancia de Front Door.

> [!NOTE]
> Al implementar actualizaciones en archivos existentes en la aplicación web estática, Azure Front Door puede continuar operativo para versiones anteriores de los archivos hasta que expire su [período de vida](https://wikipedia.org/wiki/Time_to_live). Purgue la [caché de Azure Front Door](../frontdoor/front-door-caching.md#cache-purge) para las rutas de acceso afectadas, para asegurarse de que se proporcionan los archivos más recientes.

Ahora que Front Door está administrando el sitio, ya no usará la característica de dominios personalizados de Azure Static Web Apps. Azure Front Door tiene un proceso independiente para agregar un dominio personalizado. Consulte [Incorporación de un dominio personalizado a Front Door](../frontdoor/front-door-custom-domain.md). Al agregar un dominio personalizado a Front Door, deberá actualizar el archivo de configuración de la aplicación web estática para incluirlo en la lista `allowedForwardedHosts`.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Adición de una API](apis.md)
