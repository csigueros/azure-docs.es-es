---
title: 'Tutorial: Configuración del emparejamiento entre Azure Route Server y la aplicación virtual de red de Quagga'
description: En este tutorial se muestra cómo configurar una instancia de Azure Route Server y cómo emparejarla con una aplicación virtual de red de Quagga.
author: duongau
ms.author: duau
ms.service: route-server
ms.topic: tutorial
ms.date: 08/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: cbeee212b8ed7284780083166af34285f2d44a5e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823960"
---
# <a name="tutorial-configure-peering-between-azure-route-server-and-quagga-network-virtual-appliance"></a>Tutorial: Configuración del emparejamiento entre Azure Route Server y la aplicación virtual de red de Quagga

En este tutorial se muestra cómo implementar una instancia de Azure Route Server en una red virtual y cómo establecer una conexión de emparejamiento BGP con una aplicación virtual de red de Quagga. Implementará una red virtual con cinco subredes. Una subred se dedicará a la instancia de Azure Route Server y otra subred a la aplicación virtual de red de Quagga. La aplicación virtual de red de Quagga se configurará para intercambiar rutas con la instancia de Route Server. Por último, realizará una prueba para asegurarse de que las rutas se intercambian correctamente entre la instancia de Azure Route Server y la aplicación virtual de red de Quagga.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual con cinco subredes
> * Implementar una instancia de Azure Route Server
> * Implementar una máquina virtual que ejecuta Quagga
> * Configurar el emparejamiento de Route Server
> * Comprobar las rutas aprendidas

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree su cuenta de forma gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Creación de una red virtual

Necesitará una red virtual en la que implementar la instancia de Azure Route Server y la aplicación virtual de red de Quagga. Cada implementación tendrá su propia subred dedicada.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso** y busque **Red virtual**. Seleccione **Crear**.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-new-virtual-network.png" alt-text="Captura de pantalla de la creación de un nuevo recurso para la red virtual.":::

1. En la pestaña *Aspectos básicos* de la página *Crear una red virtual*, escriba o seleccione la siguiente información y, a continuación, seleccione **Siguiente: Dirección IP>** :

    | Configuración | Value |
    | -------- | ----- | 
    | Suscripción | Seleccione la suscripción para esta implementación. |
    | Resource group | Seleccione un grupo de recursos existente o cree uno nuevo para esta implementación. | 
    | NOMBRE | Escriba un nombre para la red virtual. En este tutorial usaremos *myVirtualNetwork*.
    | Region | Seleccione la región en la que se implementará esta red virtual. En este tutorial se usará *Oeste de EE. UU.* .

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-basics-tab.png" alt-text="Captura de pantalla con la configuración de la pestaña Aspectos básicos para la red virtual.":::

1. En la pestaña **Direcciones IP**, configure el *espacio de direcciones de red virtual* como **10.1.0.0/16**. A continuación, configure las cinco subredes siguientes:

    | Nombre de subred | Intervalo de direcciones de subred |
    | ----------- | -------------------- |
    | RouteServerSubnet | 10.1.1.0/25 |
    | subnet1 | 10.1.2.0/24 |
    | subnet2 | 10.1.3.0/24 |
    | subnet3 | 10.1.4.0/24 |
    | GatewaySubnet | 10.1.5.0/24 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-ip-addresses.png" alt-text="Captura de pantalla de la configuración de direcciones IP de la red virtual.":::

1. Seleccione **Revisar y crear** y, a continuación, seleccione **Crear** una vez que se supere la validación.

## <a name="create-the-azure-route-server"></a>Creación de la instancia de Azure Route Server

La instancia de Route Server se usa para comunicarse con la aplicación virtual de red e intercambiar rutas de red virtual mediante una conexión de emparejamiento BGP.

1. Ir a https://aka.ms/routeserver.

1. Seleccione **+ Create new route server** (+ Crear un nuevo servidor de rutas).

   :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Captura de pantalla de la página de aterrizaje de Route Server.":::

1. En la página **Crear un servidor de ruta**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | -------- | ----- |
    | Suscripción | Seleccione la misma suscripción en la que se creó la red virtual en la sección anterior. | 
    | Resource group | Seleccione el grupo de recursos existente denominado *myRouteServerRG*. |
    | NOMBRE | Escriba el nombre de la instancia de Route Server *myRouteServer*. |
    | Region | Seleccione la región **Oeste de EE. UU.** |
    | Virtual Network | Seleccione la red virtual *myVirtualNetwork*. |
    | Subnet | Seleccione la subred *RouteServerSubnet (10.1.0.0/25)* creada anteriormente. |
    | Dirección IP pública | Cree una nueva dirección IP pública estándar o seleccione una existente para usarla con la instancia de Route Server. Esta dirección IP garantiza la conectividad con el servicio back-end que administra la configuración de la instancia de Route Server. |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/route-server-basics-tab.png" alt-text="Captura de pantalla de la pestaña Aspectos básicos para la creación de la instancia de Route Server.":::

1. Seleccione **Revisar y crear** y, a continuación, seleccione **Crear** una vez que se supere la validación. La instancia de Route Server tarda unos 15 minutos en implementarse.

## <a name="create-quagga-network-virtual-appliance"></a>Creación de una aplicación virtual de red de Quagga

Para configurar la aplicación virtual de red de Quagga, deberá implementar una máquina virtual Linux.

1. En Azure Portal, seleccione **+ Crear un recurso > Proceso > Máquina virtual**. Seleccione **Crear**.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-virtual-machine.png" alt-text="Captura de pantalla de la página para la creación de una nueva máquina virtual.":::

1. En la pestaña *Aspectos básicos*, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | -------- | ----- |
    | Suscripción | Seleccione la misma suscripción que para la red virtual implementada anteriormente. |
    | Resource group | Seleccione el grupo de recursos existente denominado *myRouteServerRG*. |
    | Nombre de la máquina virtual | Escriba el nombre **Quagga**. |
    | Region | Seleccione la región **Oeste de EE. UU.** |
    | Imagen | Seleccione **Ubuntu 18.04 LTS - Gen 1**. |
    | Size | Seleccione **Standard_B2s - 2vcpus, 4GiB memory** (B2s estándar - 2 CPU virtuales, 4 GiB de memoria). |
    | Tipo de autenticación | Seleccione **Contraseña**. |
    | Nombre de usuario | escriba *usuarioazure*. No use *quagga* como nombre de usuario o, de lo contrario, se producirá un error en el script de instalación en un paso posterior. |
    | Contraseña | Escriba y confirme la contraseña que elija. |
    | Puertos de entrada públicos | Seleccione **Permitir los puertos seleccionados**. |
    | Selección de puertos de entrada | Seleccione **SSH (22)** . |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab.png" alt-text="Captura de pantalla de la pestaña Aspectos básicos para crear una nueva máquina virtual." lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab-expanded.png":::
    
1. En la pestaña **Redes**, seleccione los siguientes valores de red:

    | Configuración | Value |
    | -------- | ----- |
    | Virtual Network | Seleccione **myVirtualNetwork**. |
    | Subnet | Seleccione **subnet3 (10.1.4.0/24)** . |
    | Dirección IP pública | Deje el valor predeterminado. |
    | Grupo de seguridad de red de NIC | Deje el valor predeterminado. **Básico**. |
    | Puertos de entrada públicos | Deje el valor predeterminado. **Permitir los puertos seleccionados**. |
    | Selección de puertos de entrada | Deje el valor predeterminado. **SSH (22)** . |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab.png" alt-text="Captura de pantalla de la pestaña Redes para crear una nueva máquina virtual." lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab-expanded.png":::

1. Seleccione **Revisar y crear** y, a continuación, **Crear** una vez que se supere la validación. El proceso de implementación de la máquina virtual tardará unos diez minutos.

1. Una vez implementada la máquina virtual, vaya a la configuración de red de la máquina virtual de Quagga y seleccione la interfaz de red.

     :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-network-settings.png" alt-text="Captura de pantalla de la página de redes de la máquina virtual de Quagga.":::

1. En **Configuración**, seleccione *Configuración de IP* y, después, **ipconfig1**.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-ip-configuration.png" alt-text="Captura de pantalla de la página de configuración de IP de la máquina virtual de Quagga.":::

1. Cambie la asignación de *Dinámica* a **Estática** y, a continuación, cambie la dirección IP de *10.1.4.4* a **10.1.4.10**. Esta dirección IP se usa en este [script](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh), que se ejecutará en un paso posterior. Si desea usar otra dirección IP, asegúrese de actualizarla en el script. Seleccione **Guardar** para actualizar la configuración de IP de la máquina virtual.

1. Con [Putty](https://www.putty.org/), conéctese a la máquina virtual de Quagga usando la dirección IP pública y la credencial usadas para crear la máquina virtual.

1. Una vez que haya iniciado sesión, escriba `sudo su` para cambiar al superusuario y evitar errores al ejecutar el script. Copie este [script](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh) y péguelo en la sesión de Putty. El script configurará la máquina virtual con Quagga, junto con otras opciones de red. Actualice el script para que se adapte a su entorno de red antes de ejecutarlo en la máquina virtual. El script tardará unos minutos en completar la instalación.

## <a name="configure-route-server-peering"></a>Configuración del emparejamiento de Route Server

1. Vaya a la instancia de Route Server que ha creado en el paso anterior.

1. En **Configuración**, seleccione *Nodos del mismo nivel*. Seleccione **+ Add** (+ Agregar) para agregar un nuevo emparejamiento.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peers.png" alt-text="Captura de pantalla de la página de nodos del mismo nivel de la instancia de Route Server.":::

1. En la página *Agregar nodo del mismo nivel*, escriba los siguientes datos y seleccione **Agregar** para guardar la configuración:

    | Parámetro | Value |
    | ------- | ----- |
    | Nombre | Escriba un nombre para identificar el nodo del mismo nivel. **Quagga**. |
    | ASN | Escriba el número de ASN para la aplicación virtual de red de Quagga. **65001** es el ASN definido en el script. |
    | Dirección IPv4 | Escriba la dirección IP privada de la máquina virtual de la aplicación virtual de red de Quagga. |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/add-peer.png" alt-text="Captura de pantalla de la página para agregar un emparejamiento.":::

1. La página Nodos del mismo nivel debe tener este aspecto una vez que agregue un nodo del mismo nivel:

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peer-configured.png" alt-text="Captura de pantalla de un nodo del mismo nivel configurado.":::
    
## <a name="check-learned-routes"></a>Comprobación de las rutas aprendidas
1. Para comprobar las rutas aprendidas por la instancia de Route Server, use este comando:

    ```azurepowershell-interactive
    $routes = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        PeerName = 'Quagga'
    }
    Get-AzRouteServerPeerLearnedRoute @routes | ft
    ```

    La salida debe tener un aspecto similar al siguiente: 

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/routes-learned.png" alt-text="Captura de pantalla de las rutas aprendidas por la instancia de Route Server.":::

1. Para comprobar las rutas aprendidas por la aplicación virtual de red de Quagga, escriba `vtysh` y, después, `show ip bgp`. La salida debe tener un aspecto similar al siguiente:

    ```
    root@Quagga:/home/azureuser# vtysh

    Hello, this is Quagga (version 1.2.4).
    Copyright 1996-2005 Kunihiro Ishiguro, et al.

    Quagga# show ip bgp
    BGP table version is 0, local router ID is 10.1.4.10
    Status codes: s suppressed, d damped, h history, * valid, > best, = multipath,
                  i internal, r RIB-failure, S Stale, R Removed
    Origin codes: i - IGP, e - EGP, ? - incomplete

        Network          Next Hop            Metric LocPrf Weight Path
        10.1.0.0/16      10.1.1.4                               0 65515 i
                         10.1.1.5                               0 65515 i
    *> 10.100.1.0/24    0.0.0.0                  0         32768 i
    *> 10.100.2.0/24    0.0.0.0                  0         32768 i
    *> 10.100.3.0/24    0.0.0.0                  0         32768 i
    ```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita la instancia de Route Server y todos sus recursos asociados, puede eliminar el grupo de recursos **myRouteServerRG**.

:::image type="content" source="./media/tutorial-configure-route-server-with-quagga/delete-resource-group.png" alt-text="Captura de pantalla del botón de eliminación de un grupo de recursos.":::

## <a name="next-steps"></a>Pasos siguientes

Continúe en el siguiente artículo, donde aprenderá a solucionar problemas con Route Server.
> [!div class="nextstepaction"]
> [Solución de problemas de Azure Route Server](troubleshoot-route-server.md)
