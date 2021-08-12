---
title: 'Tutorial: Integración de una puerta de enlace NAT con un equilibrador de carga público en Azure Portal'
titleSuffix: Virtual Network NAT
description: En este tutorial, aprenderá a integrar una puerta de enlace NAT de Virtual Network con un equilibrador de carga público mediante Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: 02054d0da914566bc04f56b80a33efc9cdaff4b0
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285809"
---
# <a name="tutorial-integrate-a-nat-gateway-with-a-public-load-balancer-using-the-azure-portal"></a>Tutorial: Integración de una puerta de enlace NAT con un equilibrador de carga público mediante Azure Portal

En este tutorial, aprenderá a integrar una puerta de enlace NAT con un equilibrador de carga público.

De forma predeterminada, una instancia de Azure Standard Load Balancer es segura. La conectividad saliente se define explícitamente al habilitar la SNAT saliente (traducción de direcciones de red de origen). SNAT está habilitado en una regla de equilibrio de carga o en reglas de salida. 

La integración de la puerta de enlace NAT reemplaza la necesidad de reglas de salida para el SNAT de salida del grupo de back-end. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una instancia de Azure Load Balancer
> * Crear dos máquinas virtuales para el grupo de back-end de Azure Load Balancer
> * Crear una puerta de enlace NAT
> * Validar la conectividad saliente de las máquinas virtuales en el grupo de back-end del equilibrador de carga

## <a name="prerequisites"></a>Requisitos previos

Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-load-balancer"></a>Creación de un equilibrador de carga

En esta sección, creará una instancia de Azure Standard Load Balancer. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Crear un recurso**. 
3. En el cuadro de búsqueda, escriba **Equilibrador de carga**. Seleccione **Equilibrador de carga** en los resultados de la búsqueda.
4. En la página **Equilibrador de carga**, seleccione **Crear**.
5. En la página **Crear equilibrador de carga**, especifique o seleccione la siguiente información: 

    | Configuración                 | Value                                              |
    | ---                     | ---                                                |
    | **Detalles del proyecto** |   |
    | Suscripción               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **Crear** y escriba **TutorPubLBNAT-rg** en el cuadro de texto. </br> Seleccione **Aceptar**.|
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer**.                                   |
    | Region         | Seleccione **(EE. UU.) Este de EE. UU.** .                                        |
    | Tipo          | Seleccione **Público**.                                        |
    | SKU           | Deje el valor predeterminado **Estándar**. |
    | Nivel          | En **Regional**, deje el valor predeterminado. |
    | **Dirección IP pública** |   |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> Si tiene una dirección IP pública que le gustaría usar, seleccione **Utilizar existente**. |
    | Nombre de la dirección IP pública | Escriba **myPublicIP-LB** en el cuadro de texto.|
    | Zona de disponibilidad | Seleccione **Con redundancia de zona** para crear un equilibrador de carga resistente. Para crear una instancia de Load Balancer de zona, seleccione una zona específica entre 1, 2 o 3. |
    | Adición de una dirección IPv6 pública | así que seleccione **No**. </br> Para más información sobre las direcciones IPv6 y el equilibrador de carga, consulte [¿Qué es IPv6 para Azure Virtual Network?](../ipv6-overview.md)  |
    | Preferencia de enrutamiento | Deje el valor predeterminado **Microsoft Network**. </br> Para obtener más información sobre la preferencia de enrutamiento, vea [¿Qué es la preferencia de enrutamiento (versión preliminar)?](../routing-preference-overview.md) |

6. Acepte los valores predeterminados en los demás valores y seleccione **Revisar y crear**.

7. En la pestaña **Revisar + crear**, seleccione **Crear**.

## <a name="create-load-balancer-resources"></a>Creación de recursos del equilibrador de carga

En esta sección, va a configurar:

* Las opciones del equilibrador de carga para un grupo de direcciones de back-end.
* Un sondeo de estado.
* Una regla de equilibrador de carga.

### <a name="create-a-backend-pool"></a>Creación de un grupo de back-end

Un grupo de direcciones de back-end contiene las direcciones IP de las tarjetas de interfaz de red virtuales conectadas al equilibrador de carga. 

Cree el grupo de direcciones de back-end **myBackendPool** para incluir máquinas virtuales para el tráfico de Internet de equilibrio de carga.

1. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, en la lista de recursos, **myLoadBalancer**.

2. En **Configuración**, seleccione **Grupos de back-end** y, a continuación, seleccione **Agregar**.

3. En la página **Agregar un grupo back-end**, en nombre, escriba **myBackEndPool**, como el nombre del grupo de back-end y, a continuación, seleccione **Aceptar**.

### <a name="create-a-health-probe"></a>Creación de un sondeo de estado

El equilibrador de carga supervisa el estado de la aplicación con un sondeo de estado. 

El sondeo de estado agrega o quita las máquinas virtuales del equilibrador de carga a partir de su respuesta a las comprobaciones de estado. 

Cree un sondeo de mantenimiento llamado **myHealthProbe** para supervisar el mantenimiento de las máquinas virtuales.

1. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, en la lista de recursos, **myLoadBalancer**.

2. En **Configuración**, seleccione **Sondeos de estado** y, a continuación, seleccione **Agregar**.
    
    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myHealthProbe**. |
    | Protocolo | seleccione **TCP**. |
    | Port | Escriba **80**.|
    | Intervalo | Escriba **15** como número de **Intervalo**, en segundos, entre los intentos de sondeo. |
    | Umbral incorrecto | Seleccione **2** como número de **Umbral incorrecto** o errores de sondeo consecutivos que deben producirse para que una máquina virtual se considere que no funciona de manera correcta.|
   

3. Deje el resto de los valores predeterminados y seleccione **Agregar**.

### <a name="create-a-load-balancer-rule"></a>Creación de una regla de equilibrador de carga

Las reglas de equilibrador de carga se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales. Defina la configuración IP del front-end para el tráfico entrante y el grupo de direcciones IP de back-end para recibir el tráfico. Los puertos de origen y de destino se definen en la regla. 

En esta sección va a crear una regla de equilibrador de carga:

* Llamada **myHTTPRule**.
* En el front-end llamado **LoadBalancerFrontEnd**.
* A la escucha en el **puerto 80**.
* Dirige el tráfico con equilibrio de carga al back-end llamado **myBackendPool** en el **puerto 80**.

1. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, en la lista de recursos, **myLoadBalancer**.

2. En **Configuración**, seleccione **Reglas de equilibrio de carga** y, a continuación, seleccione **Agregar**.

3. Use estos valores para configurar la regla de equilibrio de carga:
    
    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **myHTTPRule**. |
    | Versión de la dirección IP | Seleccione **IPv4**. |
    | Dirección IP del front-end | Seleccione **LoadBalancerFrontEnd**. |
    | Protocolo | seleccione **TCP**. |
    | Port | Escriba **80**.|
    | Puerto back-end | Escriba **80**. |
    | Grupo back-end | Seleccione **MyBackendPool**.|
    | Sondeo de mantenimiento | Seleccione **myHealthProbe**. |
    | Tiempo de espera de inactividad (minutos) | Escriba **15** minutos. |
    | Restablecimiento de TCP | Seleccione **Habilitado**. |
    | Traducción de direcciones de red de origen (SNAT) de salida | Seleccione **(Recomendado) Usar reglas de salida para proporcionar acceso a Internet a los miembros del grupo de back-end**. |

4. Deje el resto de valores predeterminados y después seleccione **Aceptar**.

## <a name="create-the-virtual-network"></a>Crear la red virtual

En esta sección, creará una red virtual y una subred.

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso > Redes > Red virtual** o busque **Red virtual** en el cuadro de búsqueda.

2. Seleccione **Crear**. 

3. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **TutorPubLBNAT-rg**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **Este de EE. UU**. |

4. Seleccione la pestaña **Direcciones IP** o el botón **Siguiente: Direcciones IP** situado en la parte inferior de la página.

5. En la pestaña **Direcciones IP**, especifique esta información:

    | Configuración            | Value                      |
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

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

En esta sección, creará dos máquinas virtuales (**myVM1** y **myVM2**) en dos zonas distintas (**Zona 1** y **Zona 2**).

Estas máquinas virtuales se agregan al grupo de back-end del equilibrador de carga que se creó anteriormente.

1. En la parte superior izquierda de Azure Portal, seleccione **Crear un recurso** > **Proceso** > **Máquina virtual**. 
   
2. En **Crear una máquina virtual**, escriba o seleccione los valores en la pestaña **Básico**:

    | Configuración | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **TutorPubLBNAT-rg**. |
    | **Detalles de instancia** |  |
    | Nombre de la máquina virtual | Escriba **myVM1**. |
    | Region | Seleccione **Este de EE. UU**. |
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

    | Configuración | Value |
    |-|-|
    | **Interfaz de red** |  |
    | Virtual network | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Avanzado**.|
    | Configuración del grupo de seguridad de red | Seleccione **Crear nuevo**. </br> En la página **Crear grupo de seguridad de red**, escriba **myNSG** en **Nombre**. </br> En **Reglas de entrada**, seleccione **+Agregar una regla de entrada**. </br> En **Intervalos de puertos de destino**, escriba **80**. </br> En **Prioridad**, escriba **100**. </br> En **Nombre**, escriba **myHTTPRule**. </br> Seleccione **Agregar**. </br> Seleccione **Aceptar**. |
    | **Equilibrio de carga**  |
    | ¿Quiere colocar esta máquina virtual detrás de una solución de equilibrio de carga existente? | Active la casilla.|
    | **Configuración de equilibrio de carga** |
    | Opciones de equilibrio de carga | Seleccione el **equilibrador de carga de Azure**. |
    | Seleccionar un equilibrador de carga | Seleccione **myLoadBalancer**.  |
    | Seleccionar un grupo de back-end | Seleccione **MyBackendPool**. |
   
5. Seleccione **Revisar + crear**. 
  
6. Revise la configuración y, a continuación, seleccione **Crear**.

7. Siga los pasos 1 a 7 para crear una máquina virtual con los siguientes valores y el resto de la configuración igual que la de **myVM1**:

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
    | Grupo de recursos   | Seleccione **TutorPubLBNAT-rg**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myNATGateway**.                                    |
    | Region           | Seleccione **(EE.UU.) Este de EE. UU.**  |
    | Zona de disponibilidad | Seleccione **Ninguno**. |
    | Tiempo de espera de inactividad (minutos) | Escriba **10**. |

4. Seleccione la pestaña **Dirección IP de salida** o elija el botón **Next: Outbound IP** (Siguiente: Dirección IP de salida) situado en la parte inferior de la página.

5. En la pestaña **Dirección IP de salida**, escriba o seleccione la siguiente información:

    | **Configuración** | **Valor** |
    | ----------- | --------- |
    | Direcciones IP públicas | Seleccione **Crear una dirección IP pública**. </br> En **Nombre**, escriba **myPublicIP-NAT**. </br> Seleccione **Aceptar**. |

6. Seleccione la pestaña **Subred** o elija el botón **Next: Subnet** (Siguiente: Subred) situado en la parte inferior de la página.

7. En la pestaña **Subred**, seleccione **myVNet** en el cuadro desplegable **Red virtual**.

8. Active la casilla junto a **myBackendSubnet**.

9. Seleccione la pestaña **Revisar y crear** o el botón azul **Revisar y crear** en la parte inferior de la página.

10. Seleccione **Crear**.

## <a name="test-nat-gateway"></a>Prueba de la puerta de enlace NAT

En esta sección, probaremos la puerta de enlace NAT. En primer lugar, detectaremos la dirección IP pública de la puerta de enlace NAT. A continuación, nos conectaremos a la máquina virtual de prueba y comprobaremos la conexión saliente mediante la puerta de enlace NAT.
    
1. Busque la dirección IP pública de la puerta de enlace NAT en la pantalla **Introducción**. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, **myPublicIP**.

2. Anote la dirección IP pública:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/find-public-ip.png" alt-text="Captura de pantalla de la detección de la dirección IP pública de la puerta de enlace NAT." border="true":::

3. Seleccione **Todos los servicios** en el menú de la izquierda, seleccione **Todos los recursos** y, después, en la lista de recursos, seleccione **myVM1**, que se encuentra en el grupo de recursos **TutorPubLBNAT-rg**.

4. En la página **Introducción**, seleccione **Conectar** y después **Instancia de Bastion**.

5. Seleccione el botón **Usar bastión** azul.

6. Escriba el nombre de usuario y la contraseña especificados durante la creación de la máquina virtual.

7. Abra **Internet Explorer** en **myVM1**.

8. Escriba **https://whatsmyip.com** en la barra de direcciones.

9. Compruebe que la dirección IP mostrada coincida con la dirección de puerta de enlace de NAT anotada en el paso anterior:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/my-ip.png" alt-text="Captura de pantalla de Internet Explorer que muestra la dirección IP saliente externa." border="true":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine la red virtual, la máquina virtual y la puerta de enlace NAT mediante los pasos siguientes:

1. En el menú izquierdo, seleccione **Grupos de recursos**.

2. Seleccione el grupo de recursos **TutorPubLBNAT-rg**.

3. Seleccione **Eliminar grupo de recursos**.

4. Escriba **TutorPubLBNAT-rg** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Virtual Network NAT, consulte:
> [!div class="nextstepaction"]
> [Información general de Virtual Network NAT](nat-overview.md)