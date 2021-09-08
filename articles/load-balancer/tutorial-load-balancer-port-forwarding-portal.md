---
title: 'Tutorial: Configuración del reenvío de puertos: Azure Portal'
titleSuffix: Azure Load Balancer
description: En este tutorial se explica cómo configurar el reenvío de puertos mediante Azure Load Balancer para crear conexiones a las máquinas virtuales de una red virtual de Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 8/26/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0d36be9f0ba0fc9e1b29e3fdaf0b3f9857db2204
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112136"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-azure-portal"></a>Tutorial: Configuración del reenvío de puertos en Azure Load Balancer mediante Azure Portal

El enrutamiento de puerto le permite conectarse a máquinas virtuales (VM) en una red virtual de Azure con el uso de un número de puerto y una dirección IP pública de Azure Load Balancer. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual y máquinas virtuales.
> * Crear una puerta de enlace NAT para el acceso a Internet de salida para el grupo de back-end.
> * Crear un equilibrador de carga público de SKU estándar con IP de front-end, sondeo de estado, configuración de back-end, regla de equilibrio de carga y reglas NAT de entrada.
> * Instalar y configurar un servidor web en las máquinas virtuales para mostrar las reglas de reenvío y equilibrio de carga de puertos.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network-and-virtual-machines"></a>Creación de la red virtual y las máquinas virtuales

Se requiere una red virtual y una subred para los recursos del tutorial. En esta sección, creará una red virtual y las máquinas virtuales para los pasos posteriores.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

3. En **Máquinas virtuales**, seleccione **+ Crear** > **Máquina virtual**.
   
4. En **Crear una máquina virtual**, escriba o seleccione los valores en la pestaña **Básico**:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **TutorialLBPF-rg**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |    |
    | Nombre de la máquina virtual | Escriba **myVM1**. |
    | Region | Escriba **(US) Oeste de EE. UU. 2**. |
    | Opciones de disponibilidad | Seleccione **Zona de disponibilidad**. |
    | Zona de disponibilidad | Especifique **1**. |
    | Imagen | Seleccione **Ubuntu Server 20.04 LTS: Gen2**. |
    | Instancia de Azure Spot | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
    | Size | Seleccione un tamaño de máquina virtual. |
    | **Cuenta de administrador** |    |
    | Tipo de autenticación | Seleccione **Clave pública SSH**. |
    | Nombre de usuario | escriba **usuarioazure**. |
    | Origen de la clave pública SSH | Seleccione **Generar un par de claves nuevo**. |
    | Nombre del par de claves | Escriba **myKey**. |
    | **Reglas de puerto de entrada** |    |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/create-vm-portal.png" alt-text="Captura de pantalla de la creación de una máquina virtual.":::

5. Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.

6. En la pestaña **Redes**, escriba o seleccione la siguiente información.

    | Configuración | Value |
    | ------- | ----- |
    | **Interfaz de red** |   |
    | Virtual network | Seleccione **Crear nuevo**. </br> Escriba **myVNet** en **Nombre**. </br> En **Espacio de direcciones**, en **Intervalo de direcciones**, escriba **10.1.0.0/16**. </br> En **Subredes**, en **Nombre de subred**, escriba **myBackendSubnet**. </br> En **Intervalo de direcciones**, escriba **10.1.0.0/24**. </br> Seleccione **Aceptar**. |
    | Subnet | Seleccione **myBackendSubnet**. |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Advanced** (Avanzadas). |
    | Configuración del grupo de seguridad de red | Seleccione **Crear nuevo**. </br> Escriba **myNSG** en **Nombre**. </br> Seleccione **+ Agregar una regla de entrada** en **Reglas de entrada**. </br> En **Servicio**, seleccione **HTTP**. </br> Escriba **100** en **Prioridad**. </br> Escriba **myNSGRule** para **Nombre**. </br> Seleccione **Agregar**. </br> Seleccione **Aceptar**. |

7. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear** en la parte inferior de la página.

8. Seleccione **Crear**.

9. En la solicitud **Generar nuevo par de claves**, seleccione **Descargar la clave privada y crear el recurso**. El archivo de clave se descargará como myKey.pem. Asegúrese de saber dónde se descargó el archivo .pem, necesitará la ruta de acceso al archivo de clave en pasos posteriores.

8. Siga los pasos 1 a 8 para crear otra máquina virtual con los siguientes valores y el resto de la configuración igual que la de **myVM1**:

    | Configuración | VM 2 |
    | ------- | ----- |
    | **Conceptos básicos** |    |
    | **Detalles de instancia** |   |
    | Nombre de la máquina virtual | **myVM2** |
    | Zona de disponibilidad | **2** |
    | **Cuenta de administrador** |   |
    | Tipo de autenticación | **Clave pública SSH.** |
    | Origen de la clave pública SSH | Seleccione **Usar la clave existente almacenada en Azure**. |
    | Claves almacenadas | Seleccione **myKey**. |
    | **Redes** |   |
    | **Interfaz de red** |  |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Advanced** (Avanzadas). |
    | Configuración del grupo de seguridad de red | Seleccione el grupo **myNSG** existente. |

## <a name="create-nat-gateway"></a>Creación de una instancia de NAT Gateway

En esta sección, creará una puerta de enlace NAT para el acceso a Internet saliente para los recursos de la red virtual. 

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Puerta de enlace NAT**. Seleccione **Puertas de enlace NAT** en los resultados de búsqueda.

2. En **Puertas de enlace NAT**, seleccione **+ Crear**.

3. En **Crear puerta de enlace de traducción de direcciones de red (NAT)** , escriba o seleccione la información siguiente:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **TutorialLBPF-rg**. |
    | **Detalles de instancia** |    |
    | Nombre de NAT Gateway | Escriba **myNATgateway**. |
    | Zona de disponibilidad | Seleccione **Ninguno**. |
    | Tiempo de espera de inactividad (minutos) | Escriba **15**. |

4. Seleccione la pestaña **Dirección IP de salida** o elija el botón **Siguiente: Dirección IP de salida** situado en la parte inferior de la página.

5. En **Dirección IP de salida**, seleccione **Crear una dirección IP pública** junto a **Direcciones IP públicas**.

6. Escriba **myNATGatewayIP** en **Nombre** en **Agregar una dirección IP pública**.

7. Seleccione **Aceptar**.

8. Seleccione la pestaña **Subred** o seleccione el botón **Siguiente: Subred** situado en la parte inferior de la página.

9. En **Red virtual** de la pestaña **Subred**, seleccione **myVNet**.

10. Seleccione **myBackendSubnet** en **Nombre de subred**.

11. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página, o seleccione la pestaña **Revisar y crear**.

12. Seleccione **Crear**.

## <a name="create-load-balancer"></a>Creación de un equilibrador de carga

En esta sección creará una regla de equilibrador de carga. La dirección IP de front-end, el grupo de back-end, el equilibrio de carga y las reglas NAT de entrada se configuran como parte de la creación.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. En la página **Equilibrador de carga**, seleccione **Crear**.

3. En la pestaña **Conceptos básicos** de la página **Crear equilibrador de carga**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                                              |
    | ---                     | ---                                                |
    | **Detalles del proyecto** |   |
    | Subscription               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **TutorialLBPF-rg**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer**.                                   |
    | Region         | Seleccione **(EE. UU.) Oeste de EE. UU. 2**.                                        |
    | Tipo          | Seleccione **Público**.                                        |
    | SKU           | Deje el valor predeterminado **Estándar**. |
    | Nivel          | En **Regional**, deje el valor predeterminado. |


4. Seleccione **Siguiente: Configuración de IP de front-end** en la parte inferior de la página.

5. En **Configuración de IP de front-end**, seleccione **+Agregar una IP de front-end**.

6. Escriba **LoadBalancerFrontEnd** en **Nombre**.

7. Seleccione **IPv4** o **IPv6** para **Versión de IP**.

    > [!NOTE]
    > IPv6 no se admite actualmente con preferencia de enrutamiento o equilibrio de carga entre regiones (nivel global).

8. Seleccione **Dirección IP** para **Tipo de IP**.

    > [!NOTE]
    > Para más información sobre prefijos de IP, consulte [Prefijo de dirección IP pública de Azure](../virtual-network/public-ip-address-prefix.md).

9. Seleccione **Crear nueva** en **Dirección IP pública**.

10. En **Agregar una dirección IP pública**, escriba **myPublicIP** para **Nombre**.

11. Seleccione **Con redundancia de zona** en **Zona de disponibilidad**.

    > [!NOTE]
    > En las regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), tiene la opción de seleccionar Ninguna zona (opción predeterminada), una zona específica o con redundancia de zona. La elección dependerá de los requisitos de error de dominio específicos. En regiones sin Availability Zones, este campo no aparecerá. </br> Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md).

12. Deje el valor predeterminado **Microsoft Network** para **Preferencia de enrutamiento**.

13. Seleccione **Aceptar**.

14. Seleccione **Agregar**.

15. Seleccione **Siguiente: Grupos de back-end** en la parte inferior de la página.

16. En la pestaña **Grupos de back-end**, seleccione **+ Agregar un grupo de back-end**.

17. Escriba o seleccione la siguiente información en **Agregar grupo back-end**.

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myBackendPool**. |
    | Virtual network | Seleccione **myVNet (TutorialLBPF-rg)** . |
    | Configuración del grupo de back-end | Seleccione **NIC**. |
    | Versión de la dirección IP | Seleccione **IPv4**. |

18. Seleccione **+ Agregar** en **Máquinas virtuales**.

19. Seleccione las casillas situadas junto a **myVM1** y **myVM2** en **Agregar máquinas virtuales al grupo de back-end**.

20. Seleccione **Agregar**.

21. Seleccione **Agregar**.

22. Seleccione el botón **Siguiente: Reglas de entrada** situado en la parte inferior de la página.

23. En **Regla de equilibrio de carga** de la pestaña **Reglas de entrada**, seleccione **+ Agregar regla de equilibrio de carga** para abrir la página de creación.

24. En **Agregar regla de equilibrio de carga**, escriba o seleccione la siguiente información.

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
    | Traducción de direcciones de red de origen (SNAT) de salida | Deje el valor predeterminado, **(Recommended) Use outbound rules to provide backend pool members access to the internet** ([Recomendado] Usar reglas de salida para que los miembros del grupo de servidores de back-end puedan acceder a Internet). |

25. Seleccione **Agregar**.

26. En **Regla NAT de entrada** en **Reglas de entrada**, seleccione **+ Agregar regla NAT de entrada**.

27. En **Agregar regla NAT de entrada**, especifique o seleccione la siguiente información.

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myNATRuleVM1-221**. |
    | Dirección IP del front-end | Seleccione **LoadBalancerFrontend**. |
    | Servicio | seleccione **Personalizada**. |
    | Protocolo | Deje el valor predeterminado de **TCP**. |
    | Tiempo de espera de inactividad (minutos) | Escriba o seleccione **15**. |
    | Restablecimiento de TCP | Seleccione **Habilitado**. |
    | Port | Escriba **221**. |
    | Máquina virtual de destino | Seleccione **myVM1**. |
    | Configuración de IP de red | Seleccione **ipconfig1 (10.1.0.4)** . |
    | Asignación de puertos | seleccione **Personalizada**. |
    | Dirección IP flotante | Deje el valor predeterminado, **Deshabilitado**. |
    | Puerto de destino | Escriba **22**. |

28. Seleccione **Agregar**.

29. Seleccione **+ Agregar regla NAT de entrada**.

30. En **Agregar regla NAT de entrada**, especifique o seleccione la siguiente información.

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myNATRuleVM2-222**. |
    | Dirección IP del front-end | Seleccione **LoadBalancerFrontend**. |
    | Servicio | seleccione **Personalizada**. |
    | Protocolo | Deje el valor predeterminado de **TCP**. |
    | Tiempo de espera de inactividad (minutos) | Escriba o seleccione **15**. |
    | Restablecimiento de TCP | Seleccione **Habilitado**. |
    | Port | Escriba **222**. |
    | Máquina virtual de destino | Seleccione **myVM2**. |
    | Configuración de IP de red | Seleccione **ipconfig1 (10.1.0.5)** . |
    | Asignación de puertos | seleccione **Personalizada**. |
    | Dirección IP flotante | Deje el valor predeterminado, **Deshabilitado**. |
    | Puerto de destino | Escriba **22**. |

31. Seleccione **Agregar**.

32. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página.

33. Seleccione **Crear**.

## <a name="install-web-server"></a>Instalación del servidor web

En esta sección, se conectará mediante SSH a las máquinas virtuales a través de las reglas NAT de entrada e instalará un servidor web.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. Seleccione **myLoadBalancer**.

3. En la página **Información general** de **myLoadBalancer**, anote la **Dirección IP pública**.  En este ejemplo, es **20.190.2.163**.

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/get-public-ip.png" alt-text="Captura de pantalla de la dirección IP pública en Azure Portal.":::

4. Si usa un equipo Mac o Linux, abra un símbolo del sistema de Bash. Si usa un equipo Windows, abra un símbolo del sistema de PowerShell.

5. En el símbolo del sistema, abra una conexión SSH a **myVM1**. Reemplace la dirección IP por la dirección que recuperó en el paso anterior y el puerto **221** que usó para la regla NAT de entrada myVM1. Reemplace la ruta de acceso del archivo .pem por la ruta de acceso de la ubicación en la que se descargó el archivo de clave.

    ```console
    ssh -i .\Downloads\myKey.pem azureuser@20.190.2.163 -p 221
    ```

    > [!TIP]
    > Ahora la clave SSH que creó se puede usar la próxima vez que cree una máquina virtual en Azure. Solo tiene que seleccionar la opción **Usar la clave existente almacenada en Azure** en **Origen de clave pública SSH** la próxima vez que cree una máquina virtual. Ya dispone de la clave privada en el equipo, por lo que no tendrá que descargar nada.

6. En la sesión de SSH, actualice los orígenes de paquete e instale el paquete NGINX más reciente.

    ```bash
    sudo apt-get -y update
    sudo apt-get -y install nginx
    ``` 

7. Escriba `Exit` para salir de la sesión de SSH.

8. En el símbolo del sistema, abra una conexión SSH a **myVM2**. Reemplace la dirección IP por la dirección que recuperó en el paso anterior y el puerto **222** que usó para la regla NAT de entrada myVM2. Reemplace la ruta de acceso del archivo .pem por la ruta de acceso de la ubicación en la que se descargó el archivo de clave.

    ```console
    ssh -i .\Downloads\myKey.pem azureuser@20.190.2.163 -p 222
    ```

9. En la sesión de SSH, actualice los orígenes de paquete e instale el paquete NGINX más reciente.

    ```bash
    sudo apt-get -y update
    sudo apt-get -y install nginx
    ``` 

10. Escriba `Exit` para salir de la sesión de SSH.

## <a name="test-the-web-server"></a>Prueba del servicio web

En esta sección, abrirá el explorador web y escribirá la dirección IP del equilibrador de carga que recuperó en el paso anterior.

1. Abra el explorador web.

2. En la barra de direcciones, escriba la dirección IP del equilibrador de carga. En este ejemplo, es **20.190.2.163**.

3. Se muestra el sitio web predeterminado de NGINX.

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/web-server-test.png" alt-text="Captura de pantalla de la prueba del servidor web NGINX.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine las máquinas virtuales y el equilibrador de carga mediante los pasos siguientes:

1. Escriba **Grupo de recursos** en el cuadro de búsqueda que se encuentra en la parte superior del portal.  Seleccione **Grupos de recursos** en los resultados de la búsqueda.

2. Seleccione **TutorialLBPF-rg** en **Grupos de recursos**.

3. Seleccione **Eliminar grupo de recursos**.

4. Escriba **TutorialLBPF-rg** en **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** . Seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente artículo para aprender a crear un equilibrador de carga entre regiones:

> [!div class="nextstepaction"]
> [Creación de un equilibrador de carga entre regiones mediante Azure Portal](tutorial-cross-region-portal.md)
