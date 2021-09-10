---
title: 'Tutorial: equilibrio de carga en varias configuraciones de IP en Azure Portal'
titleSuffix: Azure Load Balancer
description: En este artículo, obtendrá información sobre el equilibrio de carga en las configuraciones de NIC principales y secundarias mediante Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/08/2021
ms.custom: template-tutorial
ms.openlocfilehash: 1d84de13820b23b52c09d3333799f45fd342763f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260338"
---
# <a name="tutorial-load-balance-multiple-ip-configurations-using-the-azure-portal"></a>Tutorial: equilibrio de carga en varias configuraciones de IP mediante Azure Portal 

Para hospedar varios sitios web, puede usar otra interfaz de red asociada a una máquina virtual. Azure Load Balancer admite la implementación de equilibrio de carga para admitir la alta disponibilidad de los sitios web.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear y configurar una red virtual, una subred y una puerta de enlace NAT
> * Crear dos máquinas virtuales con Windows Server
> * Crear una NIC secundaria y configuraciones de red para cada máquina virtual
> * Crear dos sitios web de Internet Information Server (IIS) en cada máquina virtual
> * Enlazar los sitios web a las configuraciones de red
> * Crear y configurar una instancia de Azure Load Balancer
> * Prueba del equilibrador de carga

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network"></a>Creación de una red virtual

En esta sección creará una red virtual para el equilibrador de carga y las máquinas virtuales.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda de la parte superior del portal, escriba **Red virtual**. En los resultados de la búsqueda, seleccione **Redes virtuales**.

3. En **Redes virtuales**, seleccione **+ Crear**.

4. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Subscription     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **TutorialLBIP-rg**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **(Europa) Oeste de Europa**. |

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

10. Seleccione la pestaña **Seguridad** .

11. En **BastionHost**, seleccione **Habilitar**. Escriba esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre del bastión | Escriba **myBastionHost**. |
    | Espacio de direcciones de AzureBastionSubnet | Escriba **10.1.1.0/27**. |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myBastionIP**. </br> Seleccione **Aceptar**. |


12. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear**.

13. Seleccione **Crear**.

## <a name="create-nat-gateway"></a>Creación de una instancia de NAT Gateway

En esta sección, creará una puerta de enlace NAT para el acceso a Internet saliente para los recursos de la red virtual. 

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Puerta de enlace NAT**. Seleccione **Puertas de enlace NAT** en los resultados de búsqueda.

2. En **Puertas de enlace NAT**, seleccione **+ Crear**.

3. En **Crear puerta de enlace de traducción de direcciones de red (NAT)** , escriba o seleccione la información siguiente:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **TutorialLBIP-rg**. |
    | **Detalles de instancia** |    |
    | Nombre de NAT Gateway | Escriba **myNATgateway**. |
    | Zona de disponibilidad | Seleccione **Ninguno**. |
    | Tiempo de espera de inactividad (minutos) | Escriba **15**. |

4. Seleccione la pestaña **Dirección IP de salida** o elija el botón **Siguiente: Dirección IP de salida** situado en la parte inferior de la página.

5. En **Dirección IP de salida**, seleccione **Crear una dirección IP pública** junto a **Direcciones IP públicas**.

6. Escriba **myNATgatewayIP** en **Nombre** en **Agregar una dirección IP pública**.

7. Seleccione **Aceptar**.

8. Seleccione la pestaña **Subred** o seleccione el botón **Siguiente: Subred** situado en la parte inferior de la página.

9. En **Red virtual** de la pestaña **Subred**, seleccione **myVNet**.

10. Seleccione **myBackendSubnet** en **Nombre de subred**.

11. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página, o seleccione la pestaña **Revisar y crear**.

12. Seleccione **Crear**.

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

En esta sección, creará dos máquinas virtuales para hospedar los sitios web de IIS.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. En **Máquinas virtuales**, seleccione **+ Crear** y, a continuación, **Máquina virtual**.

3. En **Crear máquina virtual**, escriba o seleccione esta información:

    | Configuración | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Subscription | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **TutorialLBIP-rg**. |
    | **Detalles de instancia** |  |
    | Nombre de la máquina virtual | Escriba **myVM1**. |
    | Region | Seleccione **(Europa) Oeste de Europa**. |
    | Opciones de disponibilidad | Seleccione **Zonas de disponibilidad**. |
    | Zona de disponibilidad | Seleccione **1**. |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen1**. |
    | Instancia de Azure Spot | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
    | Size | Elija el tamaño de la máquina virtual o acepte la configuración predeterminada. |
    | **Cuenta de administrador** |  |
    | Nombre de usuario | Escriba un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmar contraseña | Vuelva a escribir la contraseña. |
    | **Reglas de puerto de entrada** |  |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

3. Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.
  
4. En la pestaña Redes, seleccione o escriba:

    | Configuración | Value |
    |-|-|
    | **Interfaz de red** |  |
    | Virtual network | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Avanzado**.|
    | Configuración del grupo de seguridad de red | Seleccione **Crear nuevo**. </br> En la página **Crear grupo de seguridad de red**, escriba **myNSG** en **Nombre**. </br> En **Reglas de entrada**, seleccione **+Agregar una regla de entrada**. </br> En **Servicio**, seleccione **HTTP**. </br> En **Prioridad**, escriba **100**. </br> En **Nombre**, escriba **myNSGrule**. </br> Seleccione **Agregar**. </br> Seleccione **Aceptar**. |
   
7. Seleccione **Revisar + crear**. 
  
8. Revise la configuración y, a continuación, seleccione **Crear**.

9. Siga los pasos 1 a 8 para crear otra máquina virtual con los siguientes valores y el resto de la configuración igual que la de **myVM1**:

    | Configuración | VM 2 |
    | ------- | ---- |
    | Nombre |  **myVM2** |
    | Zona de disponibilidad | **2** |
    | Grupo de seguridad de red | Seleccione el grupo **myNSG** existente. |

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-secondary-network-configurations"></a>Creación de configuraciones de red secundarias

En esta sección, cambiará la dirección IP privada de la NIC existente de cada máquina virtual a **Estática**. A continuación, agregará un nuevo recurso de NIC a cada máquina virtual con una configuración de dirección IP privada **estática**.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. Seleccione **myVM1**.

3. Si la máquina virtual se está ejecutando, deténgala. 

4. En **Configuración**, seleccione **Redes**.

5. En **Redes**, seleccione el nombre de la interfaz de red junto a **Interfaz de red**. La interfaz de red comenzará con el nombre de la máquina virtual y tendrá asignado un número aleatorio. En este ejemplo, es **myVM1266**.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-nic.png" alt-text="Captura de pantalla de la configuración de red myVM1 en Azure Portal.":::

6. En la página de interfaz de red, seleccione **Configuraciones de IP** en **Configuración**.

7. En **Configuraciones IP**, seleccione **ipconfig1**.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-ipconfig1.png" alt-text="Captura de pantalla de la configuración de la interfaz de red myVM1.":::

8. Seleccione **Estática** en **Asignación** en la configuración **ipconfig1**.

9. Seleccione **Guardar**.

10. Vuelva a la página de **información general** de **myVM1**.

11. En **Configuración**, seleccione **Redes**.

12. En la página **Redes**, seleccione **Asociar interfaz de red**.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-attach-nic.png" alt-text="Captura de pantalla de la interfaz de red de conexión myVM1.":::

13. En **Asociar interfaz de red**, seleccione **Crear y asociar interfaz de red**.

14. En la hoja **Crear interfaz de red**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Grupo de recursos | Seleccione **TutorialLBIP-rg**. |
    | **Interfaz de red** |  |
    | Nombre | Escriba **myVM1NIC2**. |
    | Subnet | Seleccione **myBackendSubnet (10.1.0.0/24)** . |
    | Grupo de seguridad de red de NIC | Seleccione **Advanced** (Avanzadas). |
    | Configuración del grupo de seguridad de red | Seleccione **myNSG**. |
    | Asignación de la dirección IP privada | Seleccione **Estática**. |
    | Dirección IP privada | Escriba **10.1.0.6**. |

15. Seleccione **Crear**. 

16. Inicie la máquina virtual.

17. Repita los pasos 1 al 16 para **myVM2** y reemplace la siguiente información:

    | Configuración | myVM2 |
    | ------  | ----- |
    | Nombre | **myVM2NIC2** |
    | Dirección IP privada | **10.1.0.7** |

## <a name="configure-virtual-machines"></a>Configuración de máquinas virtuales

Se conectará a **myVM1** y **myVM2** con Azure Bastion y configurará la red secundaria en esta sección. Agregará una ruta para la puerta de enlace para la configuración de red secundaria. A continuación, instalará IIS en cada máquina virtual y personalizará los sitios web para mostrar el nombre de host de la máquina virtual.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. Seleccione **myVM1**.

3. Inicie **myVM1**.

4. En **Información general**, seleccione **Conectar** y, luego, **Bastion**.

5. Seleccione **Usar Bastion**.

6. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual.

7. Seleccione **Permitir** para que Bastion use el Portapapeles.

8. En el escritorio del servidor, vaya a Inicio > Herramientas administrativas de Windows > Windows PowerShell > Windows PowerShell.

9. En la ventana de PowerShell, ejecute el comando `route print`. Este comando devuelve una salida similar a la siguiente para una máquina virtual con dos interfaces de red conectadas:

    ```console
    ===========================================================================
    Interface List
      6...00 22 48 86 00 53 ......Microsoft Hyper-V Network Adapter #2
     13...00 22 48 83 0b da ......Microsoft Hyper-V Network Adapter #3
      1...........................Software Loopback Interface 1
    ===========================================================================
    ```
    En este ejemplo, **Microsoft Hyper-V Network Adapter #3** (interfaz 13) es la interfaz de red secundaria que no tiene una puerta de enlace predeterminada asignada.

10. En la ventana de PowerShell, ejecute el comando `ipconfig /all` para ver la dirección IP asignada a la interfaz de red secundaria. En este ejemplo, la dirección IP 10.1.0.6 se asigna a la interfaz 13. No se devuelve ninguna dirección de puerta de enlace predeterminada para la interfaz de red secundaria.

11. Para redirigir todo el tráfico para direcciones fuera de la subred a la puerta de enlace, ejecute el comando siguiente:

    ```console
    route -p add 0.0.0.0 MASK 0.0.0.0 10.1.0.1 METRIC 5015 IF 13
    ```

    En este ejemplo, **10.1.0.1** es la puerta de enlace predeterminada para la red virtual que creó anteriormente.

12. Ejecute los siguientes comandos en las ventanas de PowerShell para instalar y configurar IIS y los sitios web de prueba:

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.4:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.4:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2 -Force

    ## Remove the default htm file. ##
    Remove-Item c:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

     ## Create a new website and site binding for the second IP address 10.1.0.6. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.6:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)

    }
    Add-Content @para6

    ```
13. Cierre la conexión de Bastion a **myVM1**.

14. Repita los pasos 1 al 13 para **myVM2**. Use el código de PowerShell siguiente para **myVM2** para la instalación de IIS.

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.5:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.5:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2

    ## Remove the default htm file. ##
    Remove-Item C:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

    ## Create a new website and site binding for the second IP address 10.1.0.7. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.7:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)
    }
    Add-Content @para6

    ```

## <a name="create-load-balancer"></a>Creación de un equilibrador de carga

En esta sección, creará un equilibrador de carga con redundancia de zona que equilibra la carga de las máquinas virtuales. 

Con la redundancia de zona, aunque se produzcan errores en una o varias zonas disponibilidad, la ruta de los datos puede mantenerse a salvo siempre que una zona de la región permanezca en buen estado.

Durante la creación del equilibrador de carga, configurará:

* Dos direcciones IP de front-end, una para cada sitio web.
* Grupo back-end
* Reglas de equilibrio de carga de entrada

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. En la página **Equilibrador de carga**, seleccione **Crear**.

3. En la pestaña **Conceptos básicos** de la página **Crear equilibrador de carga**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                                              |
    | ---                     | ---                                                |
    | **Detalles del proyecto** |   |
    | Subscription               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **TutorialLBIP-rg**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer**.                                   |
    | Region         | Seleccione **(Europa) Oeste de Europa**.                                        |
    | Tipo          | Seleccione **Público**.                                        |
    | SKU           | Deje el valor predeterminado **Estándar**. |
    | Nivel          | En **Regional**, deje el valor predeterminado. |

4. Seleccione **Siguiente: Configuración de IP de front-end** en la parte inferior de la página.

5. En **Configuración de IP de front-end**, seleccione **+Agregar una IP de front-end**.

6. Escriba **Frontend-contoso** en **Nombre**.

7. Seleccione **IPv4** para **Versión de IP**.

    > [!NOTE]
    > IPv6 no se admite actualmente con preferencia de enrutamiento o equilibrio de carga entre regiones (nivel global).

8. Seleccione **Dirección IP** para **Tipo de IP**.

    > [!NOTE]
    > Para más información sobre prefijos de IP, consulte [Prefijo de dirección IP pública de Azure](../virtual-network/public-ip-address-prefix.md).

9. Seleccione **Crear nueva** en **Dirección IP pública**.

10. En **Agregar una dirección IP pública**, escriba **myPublicIP-contoso** para **Nombre**.

11. Seleccione **Con redundancia de zona** en **Zona de disponibilidad**.

    > [!NOTE]
    > En las regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), tiene la opción de seleccionar Ninguna zona (opción predeterminada), una zona específica o con redundancia de zona. La elección dependerá de los requisitos de error de dominio específicos. En regiones sin Availability Zones, este campo no aparecerá. </br> Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md).

12. Deje el valor predeterminado **Microsoft Network** para **Preferencia de enrutamiento**.

13. Seleccione **Aceptar**.

14. Seleccione **Agregar**.

14. Seleccione **+ Agregar una IP de front-end**.

15. Escriba **Frontend-fabrikam** en **Nombre**.

7. Seleccione **IPv4** para **Versión de IP**.

8. Seleccione **Dirección IP** para **Tipo de IP**.

9. Seleccione **Crear nueva** en **Dirección IP pública**.

10. En **Agregar una dirección IP pública**, escriba **myPublicIP-fabrikam** para **Nombre**.

11. Seleccione **Con redundancia de zona** en **Zona de disponibilidad**.

14. Seleccione **Agregar**.

15. Seleccione **Siguiente: Grupos de back-end** en la parte inferior de la página.

16. En la pestaña **Grupos de back-end**, seleccione **+ Agregar un grupo de back-end**.

17. Escriba **myBackendPool-contoso** en **Nombre** en **Agregar un grupo de back-end**.

18. Seleccione **myVNet** en **Red virtual**.

19. Seleccione **NIC** para **Configuración del grupo de back-end**.

20. Seleccione **IPv4** para **Versión de IP**.

21. En **Máquinas virtuales**, seleccione **+ Agregar**.

22. Seleccione **myVM1** y **myVM2**, que se corresponden con **ipconfig1 (10.1.0.4)** y **ipconfig1 (10.1.0.5)** .

23. Seleccione **Agregar**.

21. Seleccione **Agregar**.

22. Seleccione **+ Agregar un grupo de back-end**.

23. Escriba **myBackendPool-fabrikam** en **Nombre** en **Agregar un grupo de back-end**.

24. Seleccione **myVNet** en **Red virtual**.

19. Seleccione **NIC** para **Configuración del grupo de back-end**.

20. Seleccione **IPv4** para **Versión de IP**.

21. En **Máquinas virtuales**, seleccione **+ Agregar**.

22. Seleccione **myVM1** y **myVM2**, que se corresponden con **ipconfig1 (10.1.0.6)** y **ipconfig1 (10.1.0.7)** .

23. Seleccione **Agregar**.

21. Seleccione **Agregar**.

22. Seleccione el botón **Siguiente: Reglas de entrada** situado en la parte inferior de la página.

23. En **Regla de equilibrio de carga** de la pestaña **Reglas de entrada**, seleccione **+ Agregar regla de equilibrio de carga**.

24. En **Agregar regla de equilibrio de carga**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myHTTPRule-contoso**. |
    | Versión de la dirección IP | Seleccione **IPv4**. |
    | Dirección IP del front-end | Seleccione **Frontend-contoso**. |
    | Protocolo | seleccione **TCP**. |
    | Port | Escriba **80**. |
    | Puerto back-end | Escriba **80**. |
    | Grupo back-end | Seleccione **myBackendPool-contoso**. |
    | Sondeo de mantenimiento | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myHealthProbe-contoso**. </br> Seleccione **HTTP** en **Protocolo**. </br> Deje el resto de valores predeterminados y seleccione **Aceptar**. |
    | Persistencia de la sesión | Seleccione **Ninguno**. |
    | Tiempo de espera de inactividad (minutos) | Escriba o seleccione **15**. |
    | Restablecimiento de TCP | Seleccione **Habilitado**. |
    | Dirección IP flotante | Seleccione **Deshabilitado**. |
    | Traducción de direcciones de red de origen (SNAT) de salida | Deje el valor predeterminado, **(Recommended) Use outbound rules to provide backend pool members access to the internet** ([Recomendado] Usar reglas de salida para que los miembros del grupo de servidores de back-end puedan acceder a Internet). |

25. Seleccione **Agregar**.

26. Seleccione **Agregar una regla de equilibrio de carga**.

27. En **Agregar regla de equilibrio de carga**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myHTTPRule-fabrikam** |
    | Versión de la dirección IP | Seleccione **IPv4**. |
    | Dirección IP del front-end | Seleccione **Frontend-fabrikam**. |
    | Protocolo | seleccione **TCP**. |
    | Port | Escriba **80**. |
    | Puerto back-end | Escriba **80**. |
    | Grupo back-end | Seleccione **myBackendPool-fabrikam**. |
    | Sondeo de mantenimiento | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myHealthProbe-fabrikam**. </br> Seleccione **HTTP** en **Protocolo**. </br> Deje el resto de valores predeterminados y seleccione **Aceptar**. |
    | Persistencia de la sesión | Seleccione **Ninguno**. |
    | Tiempo de espera de inactividad (minutos) | Escriba o seleccione **15**. |
    | Restablecimiento de TCP | Seleccione **Habilitado**. |
    | Dirección IP flotante | Seleccione **Deshabilitado**. |
    | Traducción de direcciones de red de origen (SNAT) de salida | Deje el valor predeterminado, **(Recommended) Use outbound rules to provide backend pool members access to the internet** ([Recomendado] Usar reglas de salida para que los miembros del grupo de servidores de back-end puedan acceder a Internet). |

25. Seleccione **Agregar**.

26. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página.

27. Seleccione **Crear**.

    > [!NOTE]
    > En este ejemplo, hemos creado una puerta de enlace NAT para proporcionar acceso saliente a Internet. La pestaña de reglas de salida de la configuración se omite, ya que no es necesaria con la puerta de enlace NAT. Para más información sobre la puerta de enlace NAT de Azure, consulte [¿Qué es Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md).
    > Para más información sobre las conexiones salientes en Azure, consulte [Traducción de direcciones de red de origen (SNAT) para conexiones salientes](../load-balancer/load-balancer-outbound-connections.md).

## <a name="test-load-balancer"></a>Prueba del equilibrador de carga

En esta sección detectará la dirección IP pública de cada sitio web. Escribirá la dirección IP en un explorador para probar los sitios web que creó anteriormente.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**. Seleccione **Direcciones IP públicas** en los resultados de la búsqueda.

2. Seleccione **myPublicIP-contoso**.

3. Copie la **dirección IP** en la página de información general de **myPublicIP-contoso**.

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-contoso.png" alt-text="Captura de pantalla de la dirección IP pública myPublicIP-fabrikam.":::

4. Abra un explorador web y copie la dirección IP pública en la barra de direcciones.

    :::image type="content" source="./media/load-balancer-multiple-ip/test-contoso.png" alt-text="Captura de pantalla del sitio web de Contoso en el explorador web.":::

5. Vuelva a **Direcciones IP públicas**. Seleccione **myPublicIP-fabrikam**.

6. Copie la **dirección IP** en la página de información general de **myPublicIP-fabrikam**.

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-fabrikam.png" alt-text="Captura de pantalla de la dirección IP pública myPublicIP-contoso.":::

7. Abra un explorador web y copie la dirección IP pública en la barra de direcciones.

    :::image type="content" source="./media/load-balancer-multiple-ip/test-fabrikam.png" alt-text="Captura de pantalla del sitio web de Fabrikam en el explorador web.":::

8. Para probar el equilibrador de carga, actualice el explorador o apague una de las máquinas virtuales.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine las máquinas virtuales y el equilibrador de carga mediante los pasos siguientes:

1. Escriba **Grupo de recursos** en el cuadro de búsqueda que se encuentra en la parte superior del portal.  Seleccione **Grupos de recursos** en los resultados de la búsqueda.

2. Seleccione **TutorialLBIP-rg** en **Grupos de recursos**.

3. Seleccione **Eliminar grupo de recursos**.

4. Escriba **TutorialLBIP-rg** en **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** . Seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente artículo para aprender a crear un equilibrador de carga entre regiones:

> [!div class="nextstepaction"]
> [Creación de un equilibrador de carga entre regiones mediante Azure Portal](tutorial-cross-region-portal.md)
