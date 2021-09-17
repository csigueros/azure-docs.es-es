---
title: 'Tutorial: Creación de un equilibrador de carga con más de un conjunto de disponibilidad en el grupo de servidores back-end: Azure Portal'
titleSuffix: Azure Load Balancer
description: En este tutorial implementará una instancia de Azure Load Balancer con más de un conjunto de disponibilidad en el grupo de servidores back-end.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/12/2021
ms.custom: template-tutorial
ms.openlocfilehash: 920cfa4053fac692145f46cc5cff7d53381d900b
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201803"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>Tutorial: Creación de un equilibrador de carga con más de un conjunto de disponibilidad en el grupo de servidores back-end mediante Azure Portal

Como parte de una implementación de alta disponibilidad, las máquinas virtuales a menudo se agrupan en varios conjuntos de disponibilidad. 

Load Balancer admite más de un conjunto de disponibilidad con máquinas virtuales en el grupo de servidores back-end.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una red virtual
> * Crear una puerta de enlace NAT para la conectividad saliente
> * Creación de una instancia de Azure Load Balancer con SKU estándar
> * Creación de cuatro máquinas virtuales y dos conjuntos de disponibilidad
> * Incorporación de máquinas virtuales en conjuntos de disponibilidad al grupo de servidores back-end del equilibrador de carga
> * Prueba del equilibrador de carga

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Creación de una red virtual

En esta sección creará una red virtual para el equilibrador de carga y los demás recursos que se usan en el tutorial.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda de la parte superior del portal, escriba **Red virtual**.

3. En los resultados de la búsqueda, seleccione **Redes virtuales**.

4. Seleccione **+ Create** (+ Crear).

5. En la pestaña **Conceptos básicos** de **Crear una red virtual**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ------|
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **TutorLBmultiAVS-rg** en **Nombre**. |
    | **Detalles de instancia** |   |
    | Nombre | Escriba **myVNet**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |

6. Seleccione la pestaña **Direcciones IP** o el botón **Next: IP Addresses** (Siguiente: direcciones IP) situado en la parte inferior de la página.

7. En la pestaña **Direcciones IP,** en **Nombre de subred**, seleccione **predeterminado**.

8. En el panel **Editar subred**, en **Nombre de subred**, escriba **myBackendSubnet**.

9. Seleccione **Guardar**.

10. Seleccione la pestaña **Seguridad** o el botón **Next: Security** (Siguiente: seguridad) situado en la parte inferior de la página.

11. En la pestaña **Seguridad**, en **BastionHost**, seleccione **Habilitar**.

12. Escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre del bastión | Escriba **MyBastionHost**. |
    | Espacio de direcciones de AzureBastionSubnet | Escriba **10.1.1.0/27**. |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> Escriba **myBastionIP** en **Nombre**. |

13. Seleccione la pestaña **Revisar y crear** o el botón azul **Revisar y crear** de la parte inferior de la página.

14. Seleccione **Crear**.

## <a name="create-nat-gateway"></a>Creación de una instancia de NAT Gateway 

En esta sección creará una puerta de enlace NAT para la conectividad saliente de las máquinas virtuales.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Puerta de enlace NAT**.

2. Seleccione **Puertas de enlace NAT** en los resultados de búsqueda.

3. Seleccione **+ Create** (+ Crear).

4. En la pestaña **Conceptos básicos** de **Crear puerta de enlace de traducción de direcciones de red (NAT)** , escriba o seleccione la información siguiente:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **TutorLBmultiAVS-rg**. |
    | **Detalles de instancia** |   |
    | Nombre de NAT Gateway | Escriba **myNATgateway**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |
    | Zona de disponibilidad | Seleccione **Ninguno**. |
    | Tiempo de espera de inactividad (minutos) | Escriba **15**. |

5. Seleccione la pestaña **Dirección IP de salida** o elija el botón **Next: Outbound IP** (Siguiente: Dirección IP de salida) situado en la parte inferior de la página.

6. Seleccione **Crear una dirección IP pública** junto a **Direcciones IP públicas** en la pestaña **Outbound IP** (Dirección IP de salida).

7. Escriba **myNATgatewayIP** en **Nombre**.

8. Seleccione **Aceptar**.

9. Seleccione la pestaña **Subred** o elija el botón **Next: Subnet** (Siguiente: Subred) situado en la parte inferior de la página.

10. Seleccione **myVNet** en el menú desplegable de **Red virtual**.

11. Active la casilla situada junto a **myBackendSubnet**.

12. Seleccione la pestaña **Revisar y crear** o el botón azul **Revisar y crear** en la parte inferior de la página.

13. Seleccione **Crear**.

## <a name="create-load-balancer"></a>Creación de un equilibrador de carga

En esta sección va a crear un equilibrador de carga para las máquinas virtuales.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. En la página **Equilibrador de carga**, seleccione **Crear**.

3. En la pestaña **Conceptos básicos** de la página **Crear equilibrador de carga**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                                              |
    | ---                     | ---                                                |
    | **Detalles del proyecto** |   |
    | Suscripción               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **TutorLBmultiAVS-rg**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer**.                                   |
    | Region         | Seleccione **(EE. UU.) Oeste de EE. UU. 2**.                                        |
    | Tipo          | Seleccione **Público**.                                        |
    | SKU           | Deje el valor predeterminado **Estándar**. |
    | Nivel          | En **Regional**, deje el valor predeterminado. |

4. Seleccione **Siguiente: Configuración de IP de front-end** en la parte inferior de la página.

5. En **Configuración de IP de front-end**, seleccione **+ Agregar una IP de front-end**.

6. Escriba **LoadBalancerFrontEnd** en **Nombre**.

7. Seleccione **IPv4** o **IPv6** para **Versión de IP**.

    > [!NOTE]
    > IPv6 no se admite actualmente con preferencia de enrutamiento o equilibrio de carga entre regiones (nivel global).

8. Seleccione **Dirección IP** para **Tipo de IP**.

    > [!NOTE]
    > Para más información sobre prefijos de IP, consulte [Prefijo de dirección IP pública de Azure](../virtual-network/public-ip-address-prefix.md).

9. Seleccione **Crear nueva** en **Dirección IP pública**.

10. En **Agregar una dirección IP pública**, escriba **myPublicIP-lb** para **Nombre**.

11. Seleccione **Con redundancia de zona** en **Zona de disponibilidad**.

    > [!NOTE]
    > En las regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), tiene la opción de seleccionar Ninguna zona (opción predeterminada), una zona específica o redundancia de zona. La elección dependerá de los requisitos de error de dominio específicos. En regiones sin Availability Zones, este campo no aparecerá. </br> Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md).

12. Deje el valor predeterminado **Microsoft Network** para **Preferencia de enrutamiento**.

13. Seleccione **Aceptar**.

14. Seleccione **Agregar**.

15. Seleccione **Siguiente: Grupos de back-end** en la parte inferior de la página.

16. En la pestaña **Grupos de back-end**, seleccione **+ Agregar un grupo de back-end**.

17. Escriba **myBackendPool** como **Nombre** en **Agregar un grupo de back-end**.

18. Seleccione **myVNet** en **Red virtual**.

19. Seleccione **NIC** o **Dirección IP** en **Configuración del grupo de back-end**.

20. Seleccione **IPv4** o **IPv6** para **Versión de IP**.

21. Seleccione **Agregar**.

22. Seleccione el botón **Siguiente: Reglas de entrada** situado en la parte inferior de la página.

23. En **Regla de equilibrio de carga** de la pestaña **Reglas de entrada**, seleccione **+ Agregar regla de equilibrio de carga**.

24. En **Agregar regla de equilibrio de carga**, escriba o seleccione la siguiente información:

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
    | Traducción de direcciones de red de origen (SNAT) de salida | Deje el valor predeterminado, **(Recommended) Use outbound rules to provide backend pool members access to the internet** ([Recomendado] Usar reglas de salida para que los miembros del grupo de servidores de back-end puedan acceder a Internet). |

25. Seleccione **Agregar**.

26. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página.

27. Seleccione **Crear**.

    > [!NOTE]
    > En este ejemplo, hemos creado una puerta de enlace NAT para proporcionar acceso saliente a Internet. La pestaña de reglas de salida de la configuración se omite, ya que no es necesaria con la puerta de enlace NAT. Para más información sobre la puerta de enlace NAT de Azure, consulte [¿Qué es Azure Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md)
    > Para más información sobre las conexiones salientes en Azure, consulte [Traducción de direcciones de red de origen (SNAT) para conexiones salientes](../load-balancer/load-balancer-outbound-connections.md).

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

En esta sección creará dos grupos de disponibilidad con dos máquinas virtuales por grupo. Estas máquinas se agregarán al grupo de servidores back-end del equilibrador de carga durante la creación. 

### <a name="create-first-set-of-vms"></a>Creación del primer conjunto de máquinas virtuales

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda del portal.

2. En **Nuevo**, seleccione **Proceso** > **Máquina virtual**.

3. En la pestaña **Conceptos básicos** de **Crear una máquina virtual**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **TutorLBmultiAVS-rg**. |
    | **Detalles de instancia** |   |
    | Nombre de la máquina virtual | Escriba **myVM1**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |
    | Opciones de disponibilidad | Seleccione **Conjunto de disponibilidad**. |
    | Conjunto de disponibilidad | Seleccione **Crear nuevo**. </br> Escriba **myAvailabilitySet1** en **Nombre**. </br> Seleccione **Aceptar**. |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen1**. |
    | Instancia de Azure Spot | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
    | Size | Seleccione un tamaño para la máquina virtual. |
    | **Cuenta de administrador** |   |
    | Nombre de usuario | Especifique un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | **Reglas de puerto de entrada** |   |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

4. Seleccione la pestaña **Redes** o **Next: Disks** (Siguiente: Discos) y, después, **Next: Networking** (Siguiente: Redes) en la parte inferior de la página.

5. En la pestaña **Redes**, escriba o seleccione la siguiente información:

    | Parámetro | Value |
    | ------- | ----- |
    | **Interfaz de red** |   |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | Seleccione **myBackendSubnet**. |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Advanced** (Avanzadas). |
    | Configuración del grupo de seguridad de red | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myNSG**. </br> En **Reglas de entrada**, seleccione **+ Agregar una regla de entrada**. </br> Seleccione **HTTP** en **Servicio**. </br> Escriba **myHTTPRule** en **Nombre**. </br> Seleccione **Agregar**. </br> Seleccione **Aceptar**. | 
    | **Equilibrio de carga** |   |
    | ¿Quiere colocar esta máquina virtual detrás de una solución de equilibrio de carga existente? | Active la casilla. |
    | **Configuración de equilibrio de carga** |   |
    | Opciones de equilibrio de carga | Seleccione **Azure Load Balancer**. |
    | Seleccionar un equilibrador de carga | Seleccione **myLoadBalancer**. |
    | Seleccionar un grupo de back-end | Seleccione **MyBackendPool**. |

6. Seleccione la pestaña **Revisar y crear** o el botón azul **Revisar y crear** en la parte inferior de la página.

7. Seleccione **Crear**.

8. Repita los pasos del 1 al 7 para crear la segunda máquina virtual del conjunto. Reemplace la configuración de la máquina virtual por la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myVM2**. |
    | Conjunto de disponibilidad | Seleccione **myAvailabilitySet1**. |
    | Virtual Network | Seleccione **myVNet**. |
    | Subnet | Seleccione **myBackendSubnet**. |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red | Seleccione **myNSG**. |
    | Opciones de equilibrio de carga | Seleccione **Azure Load Balancer**. |
    | Seleccionar un equilibrador de carga | Seleccione **myLoadBalancer**. |
    | Seleccionar un grupo de back-end | Seleccione **MyBackendPool**. |

### <a name="create-second-set-of-vms"></a>Creación de un segundo conjunto de máquinas virtuales

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda del portal.

2. En **Nuevo**, seleccione **Proceso** > **Máquina virtual**.

3. En la pestaña **Conceptos básicos** de **Crear una máquina virtual**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **TutorLBmultiAVS-rg**. |
    | **Detalles de instancia** |   |
    | Nombre de la máquina virtual | Escriba **myVM3**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |
    | Opciones de disponibilidad | Seleccione **Conjunto de disponibilidad**. |
    | Conjunto de disponibilidad | Seleccione **Crear nuevo**. </br> Escriba **myAvailabilitySet2** en **Nombre**. </br> Seleccione **Aceptar**. |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen1**. |
    | Instancia de Azure Spot | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
    | Size | Seleccione un tamaño para la máquina virtual. |
    | **Cuenta de administrador** |   |
    | Nombre de usuario | Especifique un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | **Reglas de puerto de entrada** |   |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

4. Seleccione la pestaña **Redes** o **Next: Disks** (Siguiente: Discos) y, después, **Next: Networking** (Siguiente: Redes) en la parte inferior de la página.

5. En la pestaña **Redes**, escriba o seleccione la siguiente información:

    | Parámetro | Value |
    | ------- | ----- |
    | **Interfaz de red** |   |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | Seleccione **myBackendSubnet**. |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Advanced** (Avanzadas). |
    | Configuración del grupo de seguridad de red | Seleccione **myNSG**. | 
    | **Equilibrio de carga** |   |
    | ¿Quiere colocar esta máquina virtual detrás de una solución de equilibrio de carga existente? | Active la casilla. |
    | **Configuración de equilibrio de carga** |   |
    | Opciones de equilibrio de carga | Seleccione **Azure Load Balancer**. |
    | Seleccionar un equilibrador de carga | Seleccione **myLoadBalancer**. |
    | Seleccionar un grupo de back-end | Seleccione **MyBackendPool**. |

6. Seleccione la pestaña **Revisar y crear** o el botón azul **Revisar y crear** en la parte inferior de la página.

7. Seleccione **Crear**.

8. Repita los pasos del 1 al 7 para crear la segunda máquina virtual del conjunto. Reemplace la configuración de la máquina virtual por la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myVM4**. |
    | Conjunto de disponibilidad | Seleccione **myAvailabilitySet2**. |
    | Virtual Network | Seleccione **myVNet**. |
    | Grupo de seguridad de red | Seleccione **myNSG**. |
    | Opciones de equilibrio de carga | Seleccione **Azure Load Balancer**. |
    | Seleccionar un equilibrador de carga | Seleccione **myLoadBalancer**. |
    | Seleccionar un grupo de back-end | Seleccione **MyBackendPool**. |

## <a name="install-iis"></a>Instalación de IIS

En esta sección usará el host de Azure Bastion que creó anteriormente para conectarse a las máquinas virtuales e instalar IIS.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**.

2. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

3. Seleccione **myVM1**.

4. En la página **Información general** de myVM1, seleccione **Conectar** > **Bastion**.

5. Seleccione **Usar Bastion**.

6. Escriba el **nombre de usuario** y la **contraseña** que especificó al crear la máquina virtual.

7. Seleccione **Conectar**.

7. En el escritorio del servidor, vaya a **Herramientas administrativas de Windows** > **Windows PowerShell**.

8. Ejecute los siguientes comandos en la ventana de PowerShell para:

    * Instalar el servidor IIS
    * Eliminar el archivo predeterminado iisstart.htm
    * Agregue un nuevo archivo iisstart.htm que muestre el nombre de la máquina virtual:

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Cierre la sesión de Bastion con **myVM1**.

9. Repita los pasos del 1 al 8 para **myVM2**, **myVM3** y **myVM4**.

## <a name="test-the-load-balancer"></a>Prueba del equilibrador de carga

En esta sección detectará la dirección IP pública del equilibrador de carga. Usará la dirección IP para probar el funcionamiento del equilibrador de carga.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

2. Seleccione **Direcciones IP públicas** en los resultados de la búsqueda.

3. Seleccione **myPublicIP-lb**.

4. Anote la dirección IP pública que aparece en **Dirección IP** en la página **Información general** de **myPublicIP-lb**:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="Búsqueda de la dirección IP pública del equilibrador de carga." border="true":::

5. Abra un explorador web y escriba la dirección IP pública en la barra de dirección:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="Prueba del equilibrador de carga con el explorador web." border="true":::

6. Seleccione Actualizar en el explorador para ver el tráfico equilibrado a las otras máquinas virtuales del grupo de servidores back-end.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine el equilibrador de carga y los recursos auxiliares mediante los siguientes pasos:

1. Escriba **Grupo de recursos** en el cuadro de búsqueda que se encuentra en la parte superior del portal.

2. Seleccione **Grupos de recursos** en los resultados de la búsqueda.

3. Seleccione **TutorLBmultiAVS-rg**.

4. En la página de información general de **TutorLBmultiAVS-rg**, seleccione **Eliminar grupo de recursos**.

5. Escriba **TutorLBmultiAVS-rg** en **TYPE THE RESOURCE GROUP NAME** (ESCRIBIR EL NOMBRE DEL GRUPO DE RECURSOS).

6. Seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

* Creado una red virtual y un host de Azure Bastion.
* Creado una instancia de Azure Standard Load Balancer.
* Creado dos conjuntos de disponibilidad con dos máquinas virtuales cada uno.
* Ha instalado IIS y probado el equilibrador de carga.

Pase al siguiente artículo para aprender a crear una instancia de Azure Load Balancer entre regiones:
> [!div class="nextstepaction"]
> [Creación de un equilibrador de carga entre regiones](tutorial-cross-region-portal.md)

