---
title: Configuración del equilibrador de carga solo de salida
titleSuffix: Azure Load Balancer
description: En este artículo, aprenderá a crear un equilibrador de carga interno con NAT de salida.
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/21/2021
ms.author: allensu
ms.openlocfilehash: d2f54b8ea7161f297528ac6ea82fde548827033a
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681192"
---
# <a name="outbound-only-load-balancer-configuration"></a>Configuración del equilibrador de carga solo de salida

Use una combinación de equilibradores de carga estándar internos y externos para crear conectividad de salida para las máquinas virtuales situadas detrás de un equilibrador de carga interno. 

Esta configuración proporciona NAT de salida en los escenarios de equilibradores de carga internos, lo que produce una configuración de "solo salida" para el grupo de back-end.

> [!NOTE]
> **Azure Virtual Network NAT** es la configuración recomendada para la conectividad saliente en implementaciones de producción. Para más información sobre **Azure Virtual Network NAT** y el recurso **NAT Gateway**, consulte **[¿Qué es Azure Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md)**
>
> Para implementar una configuración de equilibrador de carga solo de salida con Azure Virtual Network NAT y NAT Gateway, consulte [Tutorial: Integración de NAT Gateway con un equilibrador de carga interno - Azure Portal](../virtual-network/nat-gateway/tutorial-nat-gateway-load-balancer-internal-portal.md).
>
> Para más información sobre las conexiones salientes en Azure y el acceso saliente predeterminado, consulte [Traducción de direcciones de red de origen (SNAT) para conexiones salientes](load-balancer-outbound-connections.md) y [Acceso saliente predeterminado](../virtual-network/default-outbound-access.md).

:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="La figura muestra una configuración de un equilibrador de carga solo de salida" border="true":::

*Ilustración: Configuración de un equilibrador de carga solo de salida*

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network-and-load-balancers"></a>Creación de una red virtual y equilibradores de carga

En esta sección, creará una red virtual y una subred para los equilibradores de carga y la máquina virtual.  A continuación, creará los equilibradores de carga.

### <a name="create-the-virtual-network"></a>Crear la red virtual

En esta sección, creará la red virtual y las subredes para la máquina virtual, el equilibrador de carga y el host bastión.

1. En el cuadro de búsqueda de la parte superior del portal, escriba **Red virtual**. En los resultados de la búsqueda, seleccione **Redes virtuales**.

2. En **Redes virtuales**, seleccione **+ Crear**.

3. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myResourceGroupLB**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **(EE. UU.) Este de EE. UU. 2**. |

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
    | Espacio de direcciones de AzureBastionSubnet | Escriba **10.1.1.0/27**. |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myBastionIP**. </br> Seleccione **Aceptar**. |

11. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear**.

12. Seleccione **Crear**.

### <a name="create-internal-load-balancer"></a>Creación del equilibrador de carga interno

En esta sección va a crear el equilibrador de carga interno.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. En la página **Equilibrador de carga**, seleccione **Crear**.

3. En la pestaña **Conceptos básicos** de la página **Crear equilibrador de carga**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                                              |
    | ---                     | ---                                                |
    | **Detalles del proyecto** |   |
    | Suscripción               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **myResourceGroupLB**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myInternalLoadBalancer**.                                   |
    | Region         | Seleccione **(EE. UU.) Este de EE. UU. 2**.                                        |
    | Tipo          | seleccione **Interno**.                                        |
    | SKU           | Deje el valor predeterminado **Estándar**. |

4. Seleccione **Siguiente: Configuración de IP de front-end** en la parte inferior de la página.

5. En **Configuración de IP de front-end**, seleccione **+Agregar una IP de front-end**.

6. Escriba **LoadBalancerFrontEnd** en **Nombre**.

7. Seleccione **myBackendSubnet** en **Subred**.

8. Seleccione **Dinámica** para **Asignación**.

9. Seleccione **Con redundancia de zona** en **Zona de disponibilidad**.

    > [!NOTE]
    > En las regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), tiene la opción de seleccionar Ninguna zona (opción predeterminada), una zona específica o con redundancia de zona. La elección dependerá de los requisitos de error de dominio específicos. En regiones sin Availability Zones, este campo no aparecerá. </br> Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md).

10. Seleccione **Agregar**.

11. Seleccione **Siguiente: Grupos de back-end** en la parte inferior de la página.

12. En la pestaña **Grupos de back-end**, seleccione **+ Agregar un grupo de back-end**.

13. Escriba **myInternalBackendPool** como **Nombre** en **Agregar un grupo de back-end**.

14. Seleccione **NIC** o **Dirección IP** en **Configuración del grupo de back-end**.

15. Seleccione **IPv4** o **IPv6** para **Versión de IP**.

16. Seleccione **Agregar**.

17. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página.

18. Seleccione **Crear**.

### <a name="create-public-load-balancer"></a>Creación de un equilibrador de carga público

En esta sección va a crear el equilibrador de carga público.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. En la página **Equilibrador de carga**, seleccione **Crear**.

3. En la pestaña **Conceptos básicos** de la página **Crear equilibrador de carga**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                                              |
    | ---                     | ---                                                |
    | **Detalles del proyecto** |   |
    | Suscripción               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **myResourceGroupLB**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myPublicLoadBalancer**.                                   |
    | Region         | Seleccione **(EE. UU.) Este de EE. UU. 2**.                                        |
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

17. Escriba **myPublicBackendPool** como **Nombre** en **Agregar un grupo de back-end**.

18. Seleccione **myVNet** en **Red virtual**.

19. Seleccione **NIC** o **Dirección IP** en **Configuración del grupo de back-end**.

20. Seleccione **IPv4** o **IPv6** para **Versión de IP**.

21. Seleccione **Agregar**.

22. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página.

23. Seleccione **Crear**.

## <a name="create-virtual-machine"></a>Crear máquina virtual

Creará una máquina virtual en esta sección. Durante la creación, la agregará al grupo de back-end del equilibrador de carga interno. Una vez creada la máquina virtual, la agregará al grupo de back-end del equilibrador de carga público.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. En **Máquinas virtuales**, seleccione **+ Crear** > **Máquina virtual**.
   
3. En **Crear una máquina virtual**, escriba o seleccione los valores en la pestaña **Básico**:

    | Configuración | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **myResourceGroupLB**. |
    | **Detalles de instancia** |  |
    | Nombre de la máquina virtual | Escriba **myVM**. |
    | Region | Seleccione **(EE. UU.) Este de EE. UU. 2**. |
    | Opciones de disponibilidad | Seleccione **No se requiere redundancia de la infraestructura** |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen1**. |
    | Instancia de Azure Spot | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
    | Size | Elija el tamaño de la máquina virtual o acepte la configuración predeterminada. |
    | **Cuenta de administrador** |  |
    | Nombre de usuario | Escriba un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmar contraseña | Vuelva a escribir la contraseña. |
    | **Reglas de puerto de entrada** |  |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

4. Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.
  
5. En la pestaña Redes, seleccione o escriba:

    | Parámetro | Value |
    |-|-|
    | **Interfaz de red** |  |
    | Virtual network | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Avanzado**.|
    | Configuración del grupo de seguridad de red | Deje el valor predeterminado de **Básico**. |
    | **Equilibrio de carga**  |
    | ¿Quiere colocar esta máquina virtual detrás de una solución de equilibrio de carga existente? | Active el cuadro. |
    | **Configuración de equilibrio de carga** |
    | Opciones de equilibrio de carga | Seleccione **Equilibrio de carga de Azure**. |
    | Seleccionar un equilibrador de carga | Seleccione **myInternalLoadBalancer**.  |
    | Seleccionar un grupo de back-end | Seleccione **myInternalBackendPool**. |
   
6. Seleccione **Revisar + crear**. 
  
7. Revise la configuración y, a continuación, seleccione **Crear**.

### <a name="add-vm-to-backend-pool-of-public-load-balancer"></a>Adición de una máquina virtual al grupo de back-end del equilibrador de carga público

En esta sección, agregará la máquina virtual que creó anteriormente al grupo de back-end del equilibrador de carga público.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. Seleccione **myPublicLoadBalancer**.

3. Seleccione **Grupos de back-end** en **Configuración**, en **myPublicLoadBalancer**.

4. Seleccione **myPublicBackendPool** en **Grupo de back-end**, en la página **Grupos de back-end**.

5. En **myPublicBackendPool**, seleccione **myVNet** en **Red virtual**.

6. En **Máquinas virtuales**, seleccione el botón **+ Agregar** azul.

7. Active la casilla junto a **myVM** en **Agregar máquinas virtuales al grupo de back-end**.

8. Seleccione **Agregar**.

9. Seleccione **Guardar**.
## <a name="test-connectivity-before-outbound-rule"></a>Prueba de la conectividad antes de la regla de salida

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. Seleccione **myVM**.

3. En la página **Información general**, seleccione **Conectar** y, luego, **Bastion**.

4. Escriba el nombre de usuario y la contraseña especificados durante la creación de la máquina virtual.

5. Seleccione **Conectar**.

6. Abra Internet Explorer.

7. Escriba **https://whatsmyip.org** en la barra de direcciones.

8. Se producirá un error en la conexión. De forma predeterminada, el equilibrador de carga público estándar [no permite el tráfico saliente sin una regla de salida definida](load-balancer-overview.md#securebydefault).
 
## <a name="create-a-public-load-balancer-outbound-rule"></a>Creación de una regla de salida del equilibrador de carga público

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. Seleccione **myPublicLoadBalancer**.

3. Seleccione **Reglas de salida** en **Configuración**, en **myPublicLoadBalancer**.

4. Seleccione **+ Agregar** en **Reglas de salida**.

5. Escriba o seleccione la siguiente información para configurar la regla de salida.

    | Parámetro | Value |
    | ------- | ----- |
    | Nombre | Escriba **myOutboundRule**. |
    | Dirección IP del front-end | Seleccione **LoadBalancerFrontEnd**.|
    | Protocolo | Deje el valor predeterminado de **Todo**. |
    | Tiempo de espera de inactividad (minutos) | Mueva el control deslizante a **15 minutos**.|
    | Restablecimiento de TCP | Seleccione **Habilitado**.|
    | Grupo back-end | Seleccione **myPublicBackendPool**.|
    | **Asignación de puertos** |  |
    | Asignación de puertos | Seleccione **Elegir manualmente el número de puertos de salida**. |
    | **Puertos de salida** |  |
    | Elegir por | Seleccione **Puertos por instancia**. |
    | Puertos por instancia | Escriba **10000**.

6. Seleccione **Agregar**.

## <a name="test-connectivity-after-outbound-rule"></a>Prueba de la conectividad después de la regla de salida

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. Seleccione **myVM**.

3. En la página **Introducción**, seleccione **Conectar** y después **Instancia de Bastion**.

4. Escriba el nombre de usuario y la contraseña especificados durante la creación de la máquina virtual.

5. Seleccione **Conectar**.

6. Abra Internet Explorer.

7. Escriba **https://whatsmyip.org** en la barra de direcciones.

8. La conexión funcionará correctamente.

9. La dirección IP mostrada debe ser la dirección IP de front-end de **myPublicLoadBalancer**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, los equilibradores de carga, la máquina virtual y todos los recursos relacionados. 

Para ello, seleccione el grupo de recursos **myResourceGroupLB** y, luego, elija **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, se ha creado una configuración de "solo salida" con una combinación de equilibradores de carga públicos e internos.  

Esta configuración permite equilibrar la carga del tráfico interno entrante en el grupo de back-end y, al mismo tiempo, evitar las conexiones entrantes públicas.

Para más información sobre Azure Load Balancer y Azure Bastion, consulte [¿Qué e Azure Load Balancer?](load-balancer-overview.md) y [¿Qué es Azure Bastion?](../bastion/bastion-overview.md)

