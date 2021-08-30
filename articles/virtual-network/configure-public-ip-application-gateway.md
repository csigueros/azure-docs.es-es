---
title: Administración de una IP pública con una instancia de Azure Application Gateway
titleSuffix: Azure Virtual Network
description: Obtenga información sobre las formas de usar una IP pública con una instancia de Azure Application Gateway y cómo cambiar y administrar la configuración.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: c333ebec778ad56ac7077863c5829f67d613464f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296975"
---
# <a name="manage-a-public-ip-address-with-an-azure-application-gateway"></a>Administración de una IP pública con una instancia de Azure Application Gateway

Azure Application Gateway es un equilibrador de carga de tráfico web que administra el tráfico a las aplicaciones web. Application Gateway toma decisiones relacionadas con el enrutamiento en función de los atributos de una solicitud HTTP. Ejemplos de atributos como la ruta de acceso del URI o los encabezados host.  El front-end de una instancia de Application Gateway es el punto de conexión para las aplicaciones de su grupo de back-end. 

El front-end de una instancia de Application Gateway puede ser una dirección IP privada, una IP pública o ambas.  La SKU V1 de Application Gateway admite IP públicas básicas, estáticas o dinámicas.  La SKU V2 admite IP públicas de SKU estándar que solo son estáticas. La SKU V2 de Application Gateway no admite una dirección IP interna, ya que solo es front-end.  Para más información, consulte [Configuración de la dirección IP de front-end de Application Gateway](../application-gateway/configuration-front-end-ip.md).  

En este artículo, aprenderá a crear una instancia de Application Gateway con una IP pública existente en su suscripción. 

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Dos IP públicas de SKU estándar en la suscripción. Las direcciones IP no pueden estar asociadas con ningún recurso. Para más información sobre cómo crear una IP pública de SKU estándar, consulte [Creación de una dirección IP pública: Azure Portal](create-public-ip-portal.md).
    - Para fines de los ejemplos de este artículo, asigne los nombres **myStandardPublicIP-1** y **myStandardPublicIP-2** a las IP públicas nuevas.

## <a name="create-application-gateway-existing-public-ip"></a>Creación de una IP pública existente de Application Gateway

En esta sección, creará un recurso de Application Gateway. Seleccionará la dirección IP que creó en los requisitos previos como la IP pública para la instancia de Application Gateway.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Puerta de enlace de aplicación**.

3. En los resultados de la búsqueda, seleccione **Puertas de enlace de aplicación**.

4. Seleccione **+ Create** (+ Crear).

5. En **Crear puerta de enlace de aplicación**, escriba o seleccione esta información.

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**.<ul><li>Escriba **myResourceGroupAppGW**.</li></ul>Seleccione **Aceptar**. |
    | **Detalles de instancia** |   |
    | Nombre de la puerta de enlace de aplicaciones | Escriba **myAppGateway**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |
    | Nivel | Deje el valor predeterminado de **Estándar V2**. |
    | Habilitación del escalado automático | Deje el valor predeterminado de **Sí**. |
    | Recuento mínimo de instancias | Deje el valor predeterminado de **0**. |
    | Recuento máximo de instancias | Deje el valor predeterminado de **10**. |
    | Zona de disponibilidad | Deje el valor predeterminado de **No**. |
    | HTTP2 | Deje el valor predeterminado, **Deshabilitado**. |
    | Red virtual | Seleccione **Crear nuevo**. <ul><li>En **Crear red virtual**, escriba **myVNet** como el nombre.</li><li>Deje el espacio de direcciones predeterminado en **ESPACIO DE DIRECCIONES**.</li><li>En **SUBREDES**, cambie el **valor predeterminado** a **myAGSubnet**.</li><li>En el nombre de la segunda subred, escriba **myBackendSubnet**.</li><li>En **Intervalo de direcciones**, escriba un intervalo dentro del espacio de direcciones predeterminado.</li></ul>Seleccione **Aceptar**.|

6. Seleccione **Siguiente: Front-ends**.

7. Seleccione **myStandardPublicIP-1** para **IP pública** en la pestaña **Front-ends** de la IP pública.

8. Seleccione **Siguiente: Back-end**. 

9. Seleccione **Agregar un grupo de back-end**.

10. Escriba **myBackendPool** como el nombre en **Agregar un grupo de back-end**.

11. Seleccione **Siguiente: Configuration** (Siguiente: Configuración).

12. Seleccione **+ Agregar una regla de enrutamiento**. Escriba o seleccione la siguiente información.

    | Configuración | Valor |
    | ------- | ----- |
    | Nombre de la regla | Escriba **myRoutingRule**. |
    | **Agente de escucha** |    |
    | Nombre del cliente de escucha | Escriba **myListener**. |
    | Dirección IP de front-end | Seleccione **Público**. |
    | Protocolo IP de front-end | Deje el valor predeterminado de **HTTP**. |
    | Port | Deje el valor predeterminado, **80**. |
    | **Configuración adicional** |   |
    | Tipo de cliente de escucha | Deje el valor predeterminado de **Básico**. |
    | Dirección URL de la página de errores | Deje el valor predeterminado de **No**. |
    | **Destinos de back-end** |    |
    | Tipo de destino | Deje el valor predeterminado de **Grupo de back-end**. |
    | Destino de back-end | Seleccione **MyBackendPool**. |
    | Configuración de HTTP | Seleccione **Agregar nuevo**.<ul><li>Escriba **myHTTPsetting** en **Nombre**.</li><li>Deje las otras opciones de configuración con sus valores predeterminados.</li></ul>Seleccione **Agregar**.|

13. Seleccione **Agregar**.

14. Seleccione **Siguiente: Etiquetas** y **Siguiente: Revisar y crear**.

15. Seleccione **Crear**.

> [!NOTE]
> Esta es una implementación sencilla de una instancia de Application Gateway. Para información sobre la configuración avanzada, consulte [Inicio rápido: Dirección del tráfico web con Azure App Gateway: Azure Portal](../application-gateway/quick-create-portal.md).
>
> Para más información sobre Azure Application Gateway, consulte [¿Qué es Azure Application Gateway?](../application-gateway/overview.md)

## <a name="change-or-remove-public-ip-address"></a>Cambio o eliminación de IP pública

Application Gateway no permite cambiar la IP pública después de la creación.

## <a name="more-information"></a>Más información

* Si una dirección IP de SKU básica dinámica está asociada al front-end de Application Gateway, solo cambia cuando se detiene o se inicia la puerta de enlace. El nombre DNS asociado con un front-end de Application Gateway no cambia. 

## <a name="caveats"></a>Advertencias

* En este momento, las direcciones IPv6 públicas no se admiten en Application Gateway.  

## <a name="next-steps"></a>Pasos siguientes

En este artículo, aprendió a crear una instancia de Application Gateway y a utilizar una IP pública existente. 

- Para más información sobre Azure Virtual Network NAT, consulte [¿Qué es Azure Virtual Network NAT?](./nat-gateway/nat-overview.md)
- Para más información sobre las IP públicas en Azure, consulte [Direcciones IP públicas](public-ip-addresses.md).