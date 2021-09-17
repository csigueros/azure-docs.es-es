---
title: 'Tutorial: Creación de un equilibrador de carga entre regiones mediante Azure Portal'
titleSuffix: Azure Load Balancer
description: Comience a trabajar con este tutorial para la implementación de Azure Load Balancer entre regiones con Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/02/2021
ms.openlocfilehash: f0004845033493dc7546bb3af467918ea77ebcad
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201628"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Tutorial: Creación de una instancia de Azure Load Balancer entre regiones mediante Azure Portal

Un equilibrador de carga entre regiones garantiza que un servicio está disponible globalmente en varias regiones de Azure. Si se produce un error en una región, el tráfico se enruta al siguiente equilibrador de carga regional correcto más cercano.  

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un equilibrador de carga entre regiones.
> * Crear un grupo de back-end que contenga dos equilibradores de carga regionales.
> * Cree una regla de equilibrador de carga.
> * Probar el equilibrador de carga.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

> [!IMPORTANT]
> Azure Load Balancer entre regiones se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Requisitos previos

- Suscripción a Azure.
- Dos instancias de Azure Load Balancer con SKU **Estándar** con grupos de back-end implementados en dos regiones de Azure diferentes.
    - Para obtener información sobre cómo crear un equilibrador de carga estándar regional y máquinas virtuales para los grupos de back-end, consulte [Inicio rápido: Uso de Azure Portal para crear un equilibrador de carga público para equilibrar la carga de máquinas virtuales](quickstart-load-balancer-standard-public-portal.md).
        - Anexe **-R1** y **-R2** al nombre de los equilibradores de carga, las máquinas virtuales y otros recursos de cada región. 

## <a name="sign-in-to-azure-portal"></a>Inicio de sesión en Azure Portal

[Inicie sesión](https://portal.azure.com) en Azure Portal.

## <a name="create-cross-region-load-balancer"></a>Creación de un equilibrador de carga entre regiones

En esta sección, creará un: 

* Equilibrador de carga entre regiones
* Front-end con una dirección IP pública global
* Grupo de back-end con dos equilibradores de carga regionales

> [!IMPORTANT]
> Para completar estos pasos, asegúrese de que se han implementado en la suscripción dos equilibradores de carga regionales con grupos de back-end.  Para más información, consulte **[Inicio rápido: Uso de Azure Portal para crear un equilibrador de carga público para equilibrar la carga de máquinas virtuales](quickstart-load-balancer-standard-public-portal.md)** .

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibrador de carga** en los resultados de la búsqueda.

2. En la página **Equilibrador de carga**, seleccione **Crear**.

3. En la pestaña **Conceptos básicos** de la página **Crear equilibrador de carga**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                                              |
    | ---                     | ---                                                |
    | **Detalles del proyecto** |    |
    | Suscripción               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **Crear nuevo** y escriba **CreateCRLBTutorial-rg** en el cuadro de texto. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer-CR**.                                   |
    | Region         | Seleccione **(EE. UU.) Oeste de EE. UU.** .                                        |
    | Tipo          | Seleccione **Público**.                                        |
    | SKU           | Deje el valor predeterminado **Estándar**. |
    | Nivel           | Seleccione **Global**. |

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Creación de un equilibrador de carga entre regiones" border="true":::
  
4. Seleccione **Siguiente: Configuración de IP de front-end** en la parte inferior de la página.

5. En **Configuración de IP de front-end**, seleccione **+ Agregar una IP de front-end**.

6. Escriba **LoadBalancerFrontend** en **Nombre** en **Agregar dirección IP de front-end**.

7. Seleccione **IPv4** o **IPv6** para **Versión de IP**.

8. En **Dirección IP pública**, seleccione **Crear nueva**. Escriba **myPublicIP-cr** en **Nombre**.  Seleccione **Aceptar**.

9. Seleccione **Agregar**.

10. Seleccione **Siguiente: Grupos de back-end** en la parte inferior de la página.

11. En **Grupos de back-end**, seleccione **+ Agregar un grupo de back-end**.

12. Escriba **myBackendPool-cr** como **Nombre** en **Agregar un grupo de back-end**.

13. En **Equilibradores de carga**, seleccione **myLoadBalancer-r1** o el primer equilibrador de carga regional en el cuadro desplegable **Equilibrador de carga**. Compruebe que la **configuración de IP de front-end** y la **dirección IP** correspondan a **myLoadBalancer-r1**.

14. Seleccione **myLoadBalancer-r2** o el segundo equilibrador de carga regional en el cuadro desplegable **Equilibrador de carga**. Compruebe que la **configuración de IP de front-end** y la **dirección IP** correspondan a **myLoadBalancer-r2**.

15. Seleccione **Agregar**.

16. Seleccione **Siguiente: Reglas de entrada** en la parte inferior de la página.

17. En **Reglas de entrada**, seleccione **+ Agregar una regla de equilibrio de carga**.

18. En **Agregar regla de equilibrio de carga**, escriba o seleccione la siguiente información:

    | Parámetro | Value |
    | ------- | ----- |
    | Nombre | Escriba **myHTTPRule-cr**. |
    | Versión de la dirección IP | Seleccione **IPv4** o **IPv6** para **Versión de IP**. |
    | Dirección IP del front-end | Seleccione **LoadBalancerFrontend**. |
    | Protocolo | seleccione **TCP**. |
    | Port | Escriba **80**. |
    | Grupo back-end | Seleccione **myBackendPool-cr**. |
    | Persistencia de la sesión | Seleccione **Ninguno**. |
    | Tiempo de espera de inactividad (minutos) | Escriba o mueva el control deslizante a **15**. |
    | Restablecimiento de TCP | Seleccione **Habilitado**. |
    | Dirección IP flotante | Deje el valor predeterminado, **Deshabilitado**. |

19. Seleccione **Agregar**.

20. En la parte inferior de la página, seleccione **Revisar y crear**.

21. En la pestaña **Revisar y crear**, seleccione **Crear**.

    > [!NOTE]
    > El equilibrador de carga entre regiones solo se puede implementar en las siguientes regiones de inicio: **Este de EE. UU. 2, Oeste de EE. UU., Oeste de Europa, Sudeste de Asia, Centro de EE. UU., Norte de Europa, Este de Asia**. Para más información, consulte **https://aka.ms/homeregionforglb**.

## <a name="test-the-load-balancer"></a>Prueba del equilibrador de carga

En esta sección va a probar el equilibrador de carga entre regiones. Se conectará a la dirección IP pública en un explorador web.  Detendrá las máquinas virtuales de uno de los grupos de back-end del equilibrador de carga regional y observará la conmutación por error.

1. Busque la dirección IP pública de Load Balancer en la pantalla **Información general**. Seleccione **Todos los servicios** en el menú de la izquierda, seleccione **Todos los recursos** y, después, seleccione **myPublicIP-CR**.

2. Copie la dirección IP pública y péguela en la barra de direcciones del explorador. La página predeterminada del servidor web de IIS se muestra en el explorador.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Prueba del equilibrador de carga" border="true":::

3. Detenga las máquinas virtuales del grupo de back-end de uno de los equilibradores de carga regionales.

4. Actualice el explorador web y observe la conmutación por error de la conexión al otro equilibrador de carga regional.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Prueba del equilibrador de carga después de la conmutación por error" border="true":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, el equilibrador de carga y todos los recursos relacionados. 

Para ello, seleccione el grupo de recursos **CreateCRLBTutorial-rg** que contiene los recursos y, a continuación, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

* Creado un equilibrador de carga entre regiones.
* Agregado equilibradores de carga regionales al grupo de back-end del equilibrador de carga entre regiones.
* Creado una regla de equilibrio de carga.
* Probó el equilibrador de carga.

Para más información sobre el equilibrador de carga entre regiones, consulte:
> [!div class="nextstepaction"]
> [Equilibrador de carga entre regiones (versión preliminar)](cross-region-overview.md)
