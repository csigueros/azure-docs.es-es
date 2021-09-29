---
title: 'Inicio rápido: Creación de un servicio Private Link mediante Azure Portal'
titlesuffix: Azure Private Link
description: En este inicio rápido, aprenderá a crear un servicio Private Link desde Azure Portal
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 08/18/2021
ms.author: allensu
ms.openlocfilehash: de7d106ae02e1150d9765e60fbfdf0dc0cb9bc74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810329"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Inicio rápido: Creación de un servicio Private Link mediante Azure Portal

Introducción a la creación de un servicio Private Link que hace referencia a su servicio.  Proporcione acceso de Private Link a su servicio o recurso implementado detrás de una instancia de Standard Load Balancer de Azure.  Los usuarios del servicio tienen acceso privado desde su red virtual.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Creación de un conjunto de equilibrador de carga interno

En esta sección, va a crear una red virtual y una instancia de Azure Load Balancer interna.

### <a name="virtual-network"></a>Virtual network

En esta sección, va a crear una red virtual y una subred para hospedar el equilibrador de carga que accede al servicio Private Link.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso > Redes > Red virtual** o busque **Red virtual** en el cuadro de búsqueda.

3. Seleccione **Crear**. 

4. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Subscription     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **Crear nuevo**. Especifique **CreatePrivLinkService-rg**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **(EE. UU.) Este de EE. UU. 2**. |

5. Seleccione la pestaña **Direcciones IP** o el botón **Siguiente: Direcciones IP** situado en la parte inferior de la página.

6. En la pestaña **Direcciones IP**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Espacio de direcciones IPv4 | Escriba **10.1.0.0/16**. |

7. En **Nombre de subred**, seleccione la palabra **predeterminada**.

8. En **Editar subred**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre de subred | Escriba **myBackendSubnet**. |
    | Intervalo de direcciones de subred | Escriba **10.1.0.0/24**. |

9. Seleccione **Guardar**.

10. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear**.

11. Seleccione **Crear**.

### <a name="create-nat-gateway"></a>Creación de una instancia de NAT Gateway

En esta sección, creará una puerta de enlace NAT y la asignará a la subred de la red virtual que ha creado anteriormente.

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso > Redes > Puerta de enlace NAT** o busque **Puerta de enlace NAT** en el cuadro de búsqueda.

2. Seleccione **Crear**. 

3. En **Crear puerta de enlace de traducción de direcciones de red (NAT)** , escriba o seleccione esta información en la pestaña **Información básica**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Subscription     | Seleccione su suscripción a Azure.                                  |
    | Grupo de recursos   | Seleccione **CreatePrivLinkService-rg**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myNATGateway**.                                    |
    | Region           | Seleccione **(EE. UU.) Este de EE. UU. 2**.  |
    | Zona de disponibilidad | Seleccione **Ninguno**. |
    | Tiempo de espera de inactividad (minutos) | Escriba **10**. |

4. Seleccione la pestaña **Dirección IP de salida** o elija el botón **Next: Outbound IP** (Siguiente: Dirección IP de salida) situado en la parte inferior de la página.

5. En la pestaña **Dirección IP de salida**, escriba o seleccione la siguiente información:

    | **Configuración** | **Valor** |
    | ----------- | --------- |
    | Direcciones IP públicas | Seleccione **Crear una dirección IP pública**. </br> Escriba **myNATgatewayIP** en **Nombre**. </br> Seleccione **Aceptar**. |

6. Seleccione la pestaña **Subred** o elija el botón **Next: Subnet** (Siguiente: Subred) situado en la parte inferior de la página.

7. En la pestaña **Subred**, seleccione **myVNet** en el cuadro desplegable **Red virtual**.

8. Active la casilla junto a **myBackendSubnet**.

9. Seleccione la pestaña **Revisar y crear** o el botón azul **Revisar y crear** en la parte inferior de la página.

10. Seleccione **Crear**.

### <a name="create-load-balancer"></a>Creación de un equilibrador de carga

En esta sección, va a crear un equilibrador de carga que equilibra la carga de las máquinas virtuales.

Durante la creación del equilibrador de carga, configurará:

* Dirección IP del front-end
* Grupo back-end
* Reglas de equilibrio de carga de entrada

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. En la página **Equilibrador de carga**, seleccione **Crear**.

3. En la pestaña **Conceptos básicos** de la página **Crear equilibrador de carga**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                                              |
    | ---                     | ---                                                |
    | **Detalles del proyecto** |   |
    | Subscription               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **CreatePrivLinkService-rg**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer**.                                   |
    | Region         | Seleccione **(EE. UU.) Este de EE. UU. 2**.                                        |
    | Tipo          | seleccione **Interno**.                                        |
    | SKU           | Deje el valor predeterminado **Estándar**. |

4. Seleccione **Siguiente: Configuración de IP de front-end** en la parte inferior de la página.

5. En **Configuración de IP de front-end**, seleccione **+ Agregar una IP de front-end**.

6. Escriba **LoadBalancerFrontEnd** en **Nombre**.

7. Seleccione **myBackendSubnet** en **Subred**.

8. Seleccione **Dinámica** para **Asignación**.

9. Seleccione **Con redundancia de zona** en **Zona de disponibilidad**.

    > [!NOTE]
    > En las regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), tiene la opción de seleccionar Ninguna zona (opción predeterminada), una zona específica o redundancia de zona. La elección dependerá de los requisitos de error de dominio específicos. En regiones sin Availability Zones, este campo no aparecerá. </br> Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md).

10. Seleccione **Agregar**.

11. Seleccione **Siguiente: Grupos de back-end** en la parte inferior de la página.

12. En la pestaña **Grupos de back-end**, seleccione **+ Agregar un grupo de back-end**.

13. Escriba **myBackendPool** como **Nombre** en **Agregar un grupo de back-end**.

14. Seleccione **NIC** o **Dirección IP** en **Configuración del grupo de back-end**.

15. Seleccione **IPv4** o **IPv6** para **Versión de IP**.

16. Seleccione **Agregar**.

17. Seleccione el botón **Siguiente: Reglas de entrada** situado en la parte inferior de la página.

18. En **Regla de equilibrio de carga** de la pestaña **Reglas de entrada**, seleccione **+ Agregar regla de equilibrio de carga**.

19. En **Agregar regla de equilibrio de carga**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myHTTPRule**. |
    | Versión de la dirección IP | Seleccione **IPv4** o **IPv6** en función de sus requisitos. |
    | Dirección IP del front-end | Seleccione **LoadBalancerFrontend**. |
    | Protocolo | seleccione **TCP**. |
    | Port | Escriba **80**. |
    | Puerto back-end | Escriba **80**. |
    | Grupo back-end | Seleccione **MyBackendPool**. |
    | Sondeo de mantenimiento | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myHealthProbe**. </br> Seleccione **HTTP** en **Protocolo**. </br> Deje el resto de los valores predeterminados y seleccione **Aceptar**. |
    | Persistencia de la sesión | Seleccione **Ninguno**. |
    | Tiempo de espera de inactividad (minutos) | Escriba o seleccione **15**. |
    | Restablecimiento de TCP | Seleccione **Habilitado**. |
    | Dirección IP flotante | Seleccione **Deshabilitado**. |

20. Seleccione **Agregar**.

21. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página.

22. Seleccione **Crear**.

## <a name="create-a-private-link-service"></a>Creación de un servicio Private Link

En esta sección, va a crear un servicio Private Link detrás de un equilibrador de carga estándar.

1. En la parte superior izquierda de Azure Portal, seleccione **Crear un recurso**.

2. Busque **Private Link** en el cuadro de **Buscar en Marketplace**.

3. Seleccione **Crear**.

4. En **Información general** en **Private Link Center**, seleccione el botón azul **Create private link service** (Crear servicio de vínculo privado).

5. En la pestaña **Aspectos básicos** en **Create private link service** (Crear servicio de vínculo privado), escriba o seleccione la información siguiente.

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |  |
    | Subscription | Seleccione su suscripción. |
    | Grupo de recursos | Seleccione **CreatePrivLinkService-rg**. |
    | **Detalles de instancia** |  |
    | Nombre | Escriba **myPrivateLinkService**. |
    | Region | Seleccione **(EE. UU.) Este de EE. UU. 2**. |

6. Seleccione la pestaña **Configuración de salida** o seleccione **Siguiente: Configuración de salida** en la parte inferior de la página.

7. En la pestaña **Configuración de salida**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | Equilibrador de carga | Seleccione **myLoadBalancer**. |
    | Dirección IP de front-end del equilibrador de carga | Seleccione **LoadBalancerFrontEnd (10.1.0.4)** . |
    | Subred NAT de origen | Seleccione **mySubnet (10.1.0.0/24)** . |
    | Habilitación del proxy TCP V2 | Deje el valor predeterminado de **No**. </br> Si la aplicación espera un encabezado de proxy TCP V2, seleccione **Sí**. |
    | **Configuración de dirección IP privada** |  |
    | Deje los valores predeterminados. |  |

8. Seleccione la pestaña **Seguridad de acceso** o seleccione **Siguiente: Seguridad de acceso** en la parte inferior de la página.

9. Deje el valor predeterminado de **Solo control de acceso basado en rol** en la pestaña **Seguridad de acceso**.

10. Seleccione la pestaña **Etiquetas** o seleccione **Siguiente: Etiquetas** en la parte inferior de la página.

11. Seleccione la pestaña **Revisar y crear** o seleccione **Siguiente: Revisar y crear** en la parte inferior de la página.

12. En la pestaña **Revisar y crear**, seleccione **Crear**.

Se crea el servicio Private Link, que puede recibir tráfico. Si desea ver los flujos de tráfico, configure la aplicación detrás de la instancia local de Standard Load Balancer.

## <a name="create-private-endpoint"></a>Creación de un punto de conexión privado

En esta sección, asignará el servicio de vínculo privado a un punto de conexión privado. Una red virtual contiene el punto de conexión privado para el servicio de vínculo privado. Esta red virtual contiene los recursos que accederán al servicio de vínculo privado.

### <a name="create-private-endpoint-virtual-network"></a>Creación de una red virtual de punto de conexión privado

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso > Redes > Red virtual** o busque **Red virtual** en el cuadro de búsqueda.

2. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Subscription     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **CreatePrivLinkService-rg**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNetPE**                                    |
    | Region           | Seleccione **(EE. UU.) Este de EE. UU. 2**. |

3. Seleccione la pestaña **Direcciones IP** o el botón **Siguiente: Direcciones IP** situado en la parte inferior de la página.

4. En la pestaña **Direcciones IP**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Espacio de direcciones IPv4 | Escriba **11.1.0.0/16**. |

5. En **Nombre de subred**, seleccione la palabra **predeterminada**.

6. En **Editar subred**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre de subred | Escriba **mySubnetPE** |
    | Intervalo de direcciones de subred | Escriba **11.1.0.0/24**. |

7. Seleccione **Guardar**.

8. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear**.

9. Seleccione **Crear**.

### <a name="create-private-endpoint"></a>Creación de un punto de conexión privado

1. En la parte superior izquierda de la pantalla en el portal, seleccione **Crear un recurso** > **Redes** > **Private Link**, o bien, en el cuadro de búsqueda, escriba **Private Link**.

2. Seleccione **Crear**.

3. En **Private Link Center**, seleccione **Private endpoints** (Puntos de conexión privados) en el menú izquierdo.

4. En **Private endpoints** (Puntos de conexión privados), select **+ Add** (+ Agregar).

5. En la pestaña **Aspectos básicos** de **Crear un punto de conexión privado**, escriba o seleccione esta información:

    | Parámetro | Value |
    | ------- | ----- |
    | **Detalles del proyecto** | |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **CreatePrivLinkService-rg**. Ha creado este grupo de recursos en la sección anterior.|
    | **Detalles de instancia** |  |
    | Nombre  | Escriba **myPrivateEndpoint**. |
    | Region | Seleccione **(EE. UU.) Este de EE. UU. 2**. |

6. Seleccione la pestaña **Recurso** o el botón **Siguiente: Recurso** en la parte inferior de la página.
    
7. En **Recurso**, escriba o seleccione esta información:

    | Parámetro | Value |
    | ------- | ----- |
    | Método de conexión | Seleccione **Conectarse a un recurso de Azure en mi directorio**. |
    | Subscription | Seleccione su suscripción. |
    | Tipo de recurso | Seleccione **Microsoft.Network/privateLinkServices**. |
    | Resource | Seleccione **myPrivateLinkService**. |

8. Seleccione la pestaña **Configuración** o el botón **Siguiente: Configuración** situado en la parte inferior de la pantalla.

9. En **Configuración**, escriba o seleccione esta información:

    | Configuración | Value |
    | ------- | ----- |
    | **Redes** |  |
    | Virtual Network | Seleccione **myVNetPE**. |
    | Subred | Seleccione **mySubnetPE**. |

10. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear** en la parte inferior de la pantalla.

11. Seleccione **Crear**.

### <a name="ip-address-of-private-endpoint"></a>Dirección IP del punto de conexión privado

En esta sección, encontrará la dirección IP del punto de conexión privado que se corresponde con el equilibrador de carga y el servicio Private Link.

1. En la columna izquierda de Azure Portal, seleccione **Grupos de recursos**.

2. Seleccione el grupo de recursos **CreatePrivLinkService-RG**.

3. En el grupo de recursos **CreatePrivLinkService-RG**, seleccione **myPrivateEndpoint**.

4. En la página **Información general** de **myPrivateEndpoint**, seleccione el nombre de la interfaz de red asociada al punto de conexión privado.  El nombre de la interfaz de red comienza por **myPrivateEndpoint.nic**.

5. En la página **Información general** del punto de conexión privado, la dirección IP del punto de conexión se muestra en **Dirección IP privada**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado de usar el servicio Private Link, elimine el grupo de recursos para limpiar los recursos que se han usado en este inicio rápido.

1. Escriba **CreatePrivLinkService-rg** en el cuadro de búsqueda de la parte superior del portal y seleccione **CreatePrivLinkService-rg** en los resultados de la búsqueda.

2. Seleccione **Eliminar grupo de recursos**.

3. En **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**, escriba **CreatePrivLinkService-rg**.

4. Seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido:

* Ha creado una red virtual y una instancia interna de Azure Load Balancer.
* Ha creado un servicio Private Link.
* Ha creado una red virtual y un punto de conexión privado para el servicio Private Link.

Para más información sobre el punto de conexión privado de Azure, continúe a
> [!div class="nextstepaction"]
> [Inicio rápido: Creación de un punto de conexión privado mediante Azure Portal](create-private-endpoint-portal.md)
