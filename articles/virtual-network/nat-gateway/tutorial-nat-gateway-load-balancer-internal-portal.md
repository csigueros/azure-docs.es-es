---
title: 'Tutorial: Integración de una puerta de enlace NAT con un equilibrador de carga interno en Azure Portal'
titleSuffix: Virtual Network NAT
description: En este tutorial, aprenderá a integrar una puerta de enlace NAT de Virtual Network con un equilibrador de carga interno mediante Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 08/04/2021
ms.custom: template-tutorial
ms.openlocfilehash: 4f523c41a38fa458d8d29cc6b644cdaaa4e9676f
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201683"
---
# <a name="tutorial-integrate-a-nat-gateway-with-an-internal-load-balancer-using-the-azure-portal"></a>Tutorial: Integración de una puerta de enlace NAT con un equilibrador de carga interno mediante Azure Portal

En este tutorial, aprenderá a integrar una puerta de enlace NAT con un equilibrador de carga interno.

De forma predeterminada, una instancia de Azure Standard Load Balancer es segura. La conectividad saliente se define explícitamente al habilitar la SNAT saliente (traducción de direcciones de red de origen). 

La SNAT se habilita para un grupo de back-end interno mediante otro equilibrador de carga público, un enrutamiento de red o una dirección IP pública definida en una máquina virtual.

Con la integración de una puerta de enlace NAT, ya no es necesaria la implementación de un equilibrador de carga público, un enrutamiento de red o una dirección IP pública definida en una máquina virtual en el grupo de back-end.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una instancia de Azure Load Balancer
> * Crear dos máquinas virtuales para el grupo de back-end de Azure Load Balancer
> * Crear una puerta de enlace NAT
> * Validar la conectividad saliente de las máquinas virtuales en el grupo de back-end del equilibrador de carga

## <a name="prerequisites"></a>Requisitos previos

Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-virtual-network"></a>Crear la red virtual

En esta sección, creará una red virtual y una subred.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso > Redes > Red virtual** o busque **Red virtual** en el cuadro de búsqueda.

2. Seleccione **Crear**. 

3. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **Crear nuevo**. Escriba **TutorIntLBNAT-rg**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **(EE.UU.) Este de EE. UU.** |

4. Seleccione la pestaña **Direcciones IP** o el botón **Siguiente: Direcciones IP** situado en la parte inferior de la página.

5. En la pestaña **Direcciones IP**, especifique esta información:

    | Parámetro            | Value                      |
    |--------------------|----------------------------|
    | Espacio de direcciones IPv4 | Escriba **10.1.0.0/16**. |

6. En **Nombre de subred**, seleccione la palabra **predeterminada**.

7. En **Editar subred**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre de subred | Escriba **myBackendSubnet**. |
    | Intervalo de direcciones de subred | Escriba **10.1.0.0/24**. |

8. Seleccione **Guardar**.

9. Seleccione la pestaña **Seguridad** .

10. En **BastionHost**, seleccione **Habilitar**. Escriba esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre del bastión | Escriba **myBastionHost**. |
    | Espacio de direcciones de AzureBastionSubnet | Escriba **10.1.1.0/24**. |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myBastionIP**. </br> Seleccione **Aceptar**. |


11. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear**.

12. Seleccione **Crear**.

## <a name="create-load-balancer"></a>Creación de un equilibrador de carga

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
    | Suscripción               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **TutorIntLBNAT-rg**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer**.                                   |
    | Region         | Seleccione **(EE. UU.) Este de EE. UU.** .                                        |
    | Tipo          | seleccione **Interno**.                                        |
    | SKU           | Deje el valor predeterminado **Estándar**. |

4. Seleccione **Siguiente: Configuración de IP de front-end** en la parte inferior de la página.

5. En **Configuración de IP de front-end**, seleccione **+ Agregar una IP de front-end**.

6. Escriba **LoadBalancerFrontEnd** en **Nombre**.

7. Seleccione **myBackendSubnet** en **Subred**.

8. Seleccione **Dinámica** para **Asignación**.

9. Seleccione **Con redundancia de zona** en **Zona de disponibilidad**.

    > [!NOTE]
    > En las regiones con [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), tiene la opción de seleccionar Ninguna zona (opción predeterminada), una zona específica o redundancia de zona. La elección dependerá de los requisitos de error de dominio específicos. En regiones sin Availability Zones, este campo no aparecerá. </br> Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../../availability-zones/az-overview.md).

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

    | Parámetro | Value |
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

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

En esta sección, creará dos máquinas virtuales (**myVM1** y **myVM2**) en dos zonas distintas (**Zona 1** y **Zona 2**).

Estas máquinas virtuales se agregan al grupo de back-end del equilibrador de carga que se creó anteriormente.

1. En la parte superior izquierda de Azure Portal, seleccione **Crear un recurso** > **Proceso** > **Máquina virtual**. 
   
2. En **Crear una máquina virtual**, escriba o seleccione los valores en la pestaña **Básico**:

    | Parámetro | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **TutorIntLBNAT-rg**. |
    | **Detalles de instancia** |  |
    | Nombre de la máquina virtual | Escriba **myVM1**. |
    | Region | Seleccione **(EE.UU.) Este de EE. UU.** |
    | Opciones de disponibilidad | Seleccione **Zonas de disponibilidad**. |
    | Zona de disponibilidad | Seleccione **1**. |
    | Imagen | Seleccione **Windows Server 2019 Datacenter**. |
    | Instancia de Azure Spot | Deje el valor predeterminado |
    | Size | Elija el tamaño de la máquina virtual o acepte la configuración predeterminada. |
    | **Cuenta de administrador** |  |
    | Nombre de usuario | Escriba un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmar contraseña | Vuelva a escribir la contraseña. |
    | **Reglas de puerto de entrada** |  |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

3. Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.
  
4. En la pestaña Redes, seleccione o escriba:

    | Parámetro | Value |
    |-|-|
    | **Interfaz de red** |  |
    | Virtual network | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Avanzado**.|
    | Configuración del grupo de seguridad de red | Seleccione **Crear nuevo**. </br> En la página **Crear grupo de seguridad de red**, escriba **myNSG** en **Nombre**. </br> En **Reglas de entrada**, seleccione **+Agregar una regla de entrada**. </br> En **Intervalos de puertos de destino**, escriba **80**. </br> En **Prioridad**, escriba **100**. </br> En **Nombre**, escriba **myNSGRule**. </br> Seleccione **Agregar**. </br> Seleccione **Aceptar**. |
    | **Equilibrio de carga**  |
    | ¿Quiere colocar esta máquina virtual detrás de una solución de equilibrio de carga existente? | Active la casilla. |
    | **Configuración de equilibrio de carga** |
    | Opciones de equilibrio de carga | Seleccione el **equilibrador de carga de Azure**. |
    | Seleccionar un equilibrador de carga | Seleccione **myLoadBalancer**.  |
    | Seleccionar un grupo de back-end | Seleccione **MyBackendPool**. |
   
5. Seleccione **Revisar + crear**. 
  
6. Revise la configuración y, a continuación, seleccione **Crear**.

7. Siga los pasos 1 a 6 para crear una máquina virtual con los siguientes valores y el resto de la configuración igual que la de **myVM1**:

    | Configuración | VM 2 |
    | ------- | ----- |
    | Nombre |  **myVM2** |
    | Zona de disponibilidad | **2** |
    | Grupo de seguridad de red | Seleccione el grupo **myNSG** existente.| 

## <a name="create-nat-gateway"></a>Creación de una instancia de NAT Gateway

En esta sección, creará una puerta de enlace NAT y la asignará a la subred de la red virtual que ha creado anteriormente.

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso > Redes > Puerta de enlace NAT** o busque **Puerta de enlace NAT** en el cuadro de búsqueda.

2. Seleccione **Crear**. 

3. En **Crear puerta de enlace de traducción de direcciones de red (NAT)** , escriba o seleccione esta información en la pestaña **Información básica**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Seleccione su suscripción a Azure.                                  |
    | Grupo de recursos   | Seleccione **TutorIntLBNAT-rg**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myNATGateway**.                                    |
    | Region           | Seleccione **(EE.UU.) Este de EE. UU.**  |
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

## <a name="test-nat-gateway"></a>Prueba de la puerta de enlace NAT

En esta sección, probaremos la puerta de enlace NAT. En primer lugar, detectaremos la dirección IP pública de la puerta de enlace NAT. A continuación, nos conectaremos a la máquina virtual de prueba y comprobaremos la conexión saliente mediante la puerta de enlace NAT.
    
1. Busque la dirección IP pública de la puerta de enlace NAT en la pantalla **Introducción**. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, **myPublicIP**.

2. Anote la dirección IP pública:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-internal-portal/find-public-ip.png" alt-text="Captura de pantalla de la detección de la dirección IP pública de la puerta de enlace NAT." border="true":::

3. Seleccione **Todos los servicios** en el menú de la izquierda, seleccione **Todos los recursos** y, después, en la lista de recursos, seleccione **myVM1**, que se encuentra en el grupo de recursos **TutorIntLBNAT-rg**.

4. En la página **Introducción**, seleccione **Conectar** y después **Instancia de Bastion**.

5. Seleccione el botón **Usar bastión** azul.

6. Escriba el nombre de usuario y la contraseña especificados durante la creación de la máquina virtual.

7. Abra **Internet Explorer** en **myVM1**.

8. Escriba **https://whatsmyip.com** en la barra de direcciones.

9. Compruebe que la dirección IP mostrada coincida con la dirección de puerta de enlace de NAT anotada en el paso anterior:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-internal-portal/my-ip.png" alt-text="Captura de pantalla de Internet Explorer que muestra la dirección IP saliente externa." border="true":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine la red virtual, la máquina virtual y la puerta de enlace NAT mediante los pasos siguientes:

1. En el menú izquierdo, seleccione **Grupos de recursos**.

2. Seleccione el grupo de recursos **TutorIntLBNAT-rg**.

3. Seleccione **Eliminar grupo de recursos**.

4. Escriba **TutorIntLBNAT-RG** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Virtual Network NAT, consulte:
> [!div class="nextstepaction"]
> [Información general de Virtual Network NAT](nat-overview.md)
