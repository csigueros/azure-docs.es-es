---
title: 'Inicio rápido: Creación de una instancia pública de Load Balancer: Azure Portal'
titleSuffix: Azure Load Balancer
description: En esta guía de inicio rápido se muestra cómo crear una instancia de Load Balancer mediante Azure Portal.
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.date: 08/09/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: a25c35971d341c9ea6bce716931a76d682027e79
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732828"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Inicio rápido: Uso de Azure Portal para crear un equilibrador de carga público para equilibrar la carga de máquinas virtuales

Comience a usar Azure Load Balancer mediante Azure Portal para crear un equilibrador de carga público y tres máquinas virtuales.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**SKU Estándar**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Se recomienda usar la SKU Estándar de Load Balancer para las cargas de trabajo de producción.  Para más información sobre las SKU, consulte **[SKU de Azure Load Balancer](skus.md)** .

## <a name="create-the-virtual-network"></a>Crear la red virtual

En esta sección, creará una red virtual y una subred.

1. En el cuadro de búsqueda de la parte superior del portal, escriba **Red virtual**. En los resultados de la búsqueda, seleccione **Redes virtuales**.

2. En **Redes virtuales**, seleccione **+ Crear**.

3. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **CreatePubLBQS-rg**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **(Europa) Oeste de Europa**. |

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

## <a name="create-nat-gateway"></a>Creación de una instancia de NAT Gateway

En esta sección, creará una puerta de enlace NAT para el acceso a Internet saliente para los recursos de la red virtual. 

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Puerta de enlace NAT**. Seleccione **Puertas de enlace NAT** en los resultados de búsqueda.

2. En **Puertas de enlace NAT**, seleccione **+ Crear**.

3. En **Crear puerta de enlace de traducción de direcciones de red (NAT)** , escriba o seleccione la información siguiente:

    | Parámetro | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **CreatePubLBQS-rg**. |
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

## <a name="create-load-balancer"></a><a name="create-load-balancer-resources"></a> Creación de un equilibrador de carga

En esta sección, creará un equilibrador de carga con redundancia de zona que equilibra la carga de las máquinas virtuales. Con la redundancia de zona, aunque se produzcan errores en una o varias zonas disponibilidad, la ruta de los datos puede mantenerse a salvo siempre que una zona de la región permanezca en buen estado.

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
    | Resource group         | Seleccione **CreatePubLBQS-rg**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer**.                                   |
    | Region         | Seleccione **(Europa) Oeste de Europa**.                                        |
    | Tipo          | Seleccione **Público**.                                        |
    | SKU           | Deje el valor predeterminado **Estándar**. |
    | Nivel          | En **Regional**, deje el valor predeterminado. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Captura de pantalla de la pestaña de conceptos básicos para crear un equilibrador de carga estándar." border="true":::

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
    > En este ejemplo, hemos creado una puerta de enlace NAT para proporcionar acceso saliente a Internet. La pestaña de reglas de salida de la configuración se omite, ya que no es necesaria con la puerta de enlace NAT. Para más información sobre la puerta de enlace NAT de Azure, consulte [¿Qué es Azure Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md).
    > Para más información sobre las conexiones salientes en Azure, consulte [Traducción de direcciones de red de origen (SNAT) para conexiones salientes](../load-balancer/load-balancer-outbound-connections.md).

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

En esta sección, creará tres máquinas virtuales (**myVM1**, **myVM2** y **myVM3**) en tres zonas diferentes (**Zona 1**, **Zona 2** y **Zona 3**). 

Estas máquinas virtuales se agregan al grupo de back-end del equilibrador de carga que se creó anteriormente.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. En **Máquinas virtuales**, seleccione **+ Crear** > **Máquina virtual**.
   
3. En **Crear una máquina virtual**, escriba o seleccione los valores en la pestaña **Básico**:

    | Parámetro | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **CreatePubLBQS-rg** |
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

4. Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.
  
5. En la pestaña Redes, seleccione o escriba:

    | Parámetro | Value |
    |-|-|
    | **Interfaz de red** |  |
    | Virtual network | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Avanzado**.|
    | Configuración del grupo de seguridad de red | Seleccione **Crear nuevo**. </br> En la página **Crear grupo de seguridad de red**, escriba **myNSG** en **Nombre**. </br> En **Reglas de entrada**, seleccione **+Agregar una regla de entrada**. </br> En **Servicio**, seleccione **HTTP**. </br> En **Prioridad**, escriba **100**. </br> En **Nombre**, escriba **myNSGRule**. </br> Seleccione **Agregar**. </br> Seleccione **Aceptar**. |
    | **Equilibrio de carga**  |
    | ¿Quiere colocar esta máquina virtual detrás de una solución de equilibrio de carga existente? | Active la casilla. |
    | **Configuración de equilibrio de carga** |
    | Opciones de equilibrio de carga | Seleccione **Equilibrio de carga de Azure**. |
    | Seleccionar un equilibrador de carga | Seleccione **myLoadBalancer**.  |
    | Seleccionar un grupo de back-end | Seleccione **MyBackendPool**. |
   
6. Seleccione **Revisar + crear**. 
  
7. Revise la configuración y, a continuación, seleccione **Crear**.

8. Siga los pasos 1 a 7 para crear dos VM más con los siguientes valores y el resto de la configuración debe coincidir con la de **myVM1**:

    | Configuración | VM 2| VM 3|
    | ------- | ----- |---|
    | Nombre |  **myVM2** |**myVM3**|
    | Zona de disponibilidad | **2** |**3**|
    | Grupo de seguridad de red | Seleccione el grupo **myNSG** existente.| Seleccione el grupo **myNSG** existente.|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

# <a name="basic-sku"></a>[**SKU básica**](#tab/option-2-create-load-balancer-basic)

>[!NOTE]
>Se recomienda usar la SKU Estándar de Load Balancer para las cargas de trabajo de producción.  Para más información sobre las SKU, consulte **[SKU de Azure Load Balancer](skus.md)** .

## <a name="create-the-virtual-network"></a>Crear la red virtual

En esta sección, creará una red virtual y una subred.

1. En el cuadro de búsqueda de la parte superior del portal, escriba **Red virtual**. En los resultados de la búsqueda, seleccione **Redes virtuales**.

2. En **Redes virtuales**, seleccione **+ Crear**.

3. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **CreatePubLBQS-rg**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **(Europa) Oeste de Europa**. |

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
    | Intervalo de direcciones de subred | Escriba **10.1.0.0/27** |

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

En esta sección, creará tres máquinas virtuales (**myVM1**, **myVM2** y **myVM3**).

Las tres máquinas virtuales se agregarán a un conjunto de disponibilidad llamado **myAvailabilitySet**.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. En **Máquinas virtuales**, seleccione **+ Crear** > **Máquina virtual**.
   
3. En **Crear una máquina virtual**, escriba o seleccione los valores en la pestaña **Básico**:

    | Parámetro | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **CreatePubLBQS-rg** |
    | **Detalles de instancia** |  |
    | Nombre de la máquina virtual | Escriba **myVM1**. |
    | Region | Seleccione **(Europa) Oeste de Europa**. |
    | Opciones de disponibilidad | Seleccione **Conjunto de disponibilidad**. |
    | Conjunto de disponibilidad | Seleccione **Crear nuevo**. </br> Escriba **myAvailabilitySet** en **Nombre**. </br> Seleccione **Aceptar**. |
    | Imagen | **Windows Server 2019 Datacenter - Gen 1** |
    | Instancia de Azure Spot | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
    | Size | Elija el tamaño de la máquina virtual o acepte la configuración predeterminada. |
    | **Cuenta de administrador** |  |
    | Nombre de usuario | Escriba un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmar contraseña | Vuelva a escribir la contraseña. |
    | **Reglas de puerto de entrada** |   |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

4. Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.
  
5. En la pestaña Redes, seleccione o escriba:

    | Parámetro | Value |
    |-|-|
    | **Interfaz de red** |  |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | Seleccione **myBackendSubnet**. |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Avanzado**.|
    | Configuración del grupo de seguridad de red | Seleccione **Crear nuevo**. </br> En la página **Crear grupo de seguridad de red**, escriba **myNSG** en **Nombre**. </br> En **Reglas de entrada**, seleccione **+Agregar una regla de entrada**. </br> En **Intervalos de puertos de destino**, escriba **80**. </br> En **Prioridad**, escriba **100**. </br> En **Nombre**, escriba **myNSGRule**. </br> Seleccione **Agregar**. </br> Seleccione **Aceptar**. |
    | **Equilibrio de carga**  |
    | ¿Quiere colocar esta máquina virtual detrás de una solución de equilibrio de carga existente? | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
 
6. Seleccione la pestaña **Administración** o seleccione **Siguiente** > **Administración**.

7. En la pestaña **Administración**, seleccione o escriba:
    
    | Configuración | Value |
    |---|---|
    | **Supervisión** | |
    | Diagnósticos de arranque | Seleccione **Desactivado**. |

8. Seleccione **Revisar + crear**. 
  
9. Revise la configuración y, a continuación, seleccione **Crear**.

10. Siga los pasos 1 a 9 para crear dos máquinas virtuales más con los siguientes valores y el resto de la configuración debe coincidir con la de **myVM1**:

    | Configuración | VM 2 | VM 3 |
    | ------- | ----- |---|
    | Nombre |  **myVM2** |**myVM3**|
    | Conjunto de disponibilidad| Seleccione **myAvailabilitySet**. | Seleccione **myAvailabilitySet**.|
    | Grupo de seguridad de red | Seleccione el grupo **myNSG** existente.| Seleccione el grupo **myNSG** existente.|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-load-balancer"></a>Creación de un equilibrador de carga

En esta sección, va a crear un equilibrador de carga que equilibra la carga de las máquinas virtuales.

Durante la creación del equilibrador de carga, configurará:

* Dirección IP del front-end
* Grupo back-end
* Reglas de equilibrio de carga de entrada

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. En la página **Equilibrador de carga**, seleccione **+ Crear**.

3. En la pestaña **Conceptos básicos** de la página **Crear equilibrador de carga**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                                              |
    | ---                     | ---                                                |
    | **Detalles del proyecto** |   |
    | Suscripción               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **CreatePubLBQS-rg**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer**.                                   |
    | Region         | Seleccione **(Europa) Oeste de Europa**.                                        |
    | Tipo          | Seleccione **Público**.                                        |
    | SKU           | Seleccione **Básica**. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Captura de pantalla de la pestaña de conceptos básicos del equilibrador de carga básico." border="true":::
 
4. Seleccione **Siguiente: Configuración de IP de front-end** en la parte inferior de la página.

5. En **Configuración de IP de front-end**, seleccione **+Agregar una IP de front-end**.

6. Escriba **LoadBalancerFrontEnd** en **Nombre**.

7. Seleccione **IPv4** o **IPv6** para **Versión de IP**.

8. Seleccione **Crear nueva** en **Dirección IP pública**.

9. En **Agregar una dirección IP pública**, escriba **myPublicIP** para **Nombre**.

10. En **Asignación**, seleccione **Estática**.

11. Seleccione **Aceptar**.

12. Seleccione **Agregar**.

13. Seleccione **Siguiente: Grupos de back-end** en la parte inferior de la página.

14. En la pestaña **Grupos de back-end**, seleccione **+ Agregar un grupo de back-end**.

15. Escriba **myBackendPool** como **Nombre** en **Agregar un grupo de back-end**.

16. Seleccione **myVNet** en **Red virtual**.

17. En **Asociado a**, seleccione **Máquinas virtuales**.

18. Seleccione **IPv4** o **IPv6** para **Versión de IP**.

19. En **Máquinas virtuales**, seleccione el botón **+ Añadir** azul.

20. En **Agregar máquinas virtuales al grupo de back-end**, seleccione las casillas situadas junto a **myVM1**, **myVM2** y **myVM3**.

21. Seleccione **Agregar**.

22. Seleccione **Agregar** en **Agregar un grupo de back-end**.

23. Seleccione el botón **Siguiente: Reglas de entrada** situado en la parte inferior de la página.

24. En **Regla de equilibrio de carga** de la pestaña **Reglas de entrada**, seleccione **+ Agregar regla de equilibrio de carga**.

25. En **Agregar regla de equilibrio de carga**, escriba o seleccione la siguiente información:

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
    | Dirección IP flotante | Seleccione **Deshabilitado**. |

26. Seleccione **Agregar**.

27. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página.

28. Seleccione **Crear**.

---

## <a name="install-iis"></a>Instalación de IIS

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. Seleccione **myVM1**.

2. En la página **Introducción**, seleccione **Conectar** y después **Instancia de Bastion**.

3. Seleccione **Usar Bastion**.

4. Escriba el nombre de usuario y la contraseña especificados durante la creación de la máquina virtual.

5. Seleccione **Conectar**.

6. En el escritorio del servidor, vaya a **Herramientas administrativas de Windows** > **Windows PowerShell**.

7. Ejecute los siguientes comandos en la ventana de PowerShell para:

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

9. Repita los pasos del 1 al 8 para instalar IIS y el archivo iisstart.htm actualizado en **myVM2** y **myVM3**.

## <a name="test-the-load-balancer"></a>Prueba del equilibrador de carga

1. En el cuadro de búsqueda que aparece en la parte superior de la página, escriba **Load Balancer**.  Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. Busque la dirección IP pública del equilibrador de carga en la página **Introducción**, bajo **Dirección IP pública**.

3. Copie la dirección IP pública y péguela en la barra de direcciones del explorador. La página de VM personalizada del servidor web de IIS se muestra en el explorador.

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/load-balancer-test.png" alt-text="Captura de pantalla de la prueba del equilibrador de carga":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, el equilibrador de carga y todos los recursos relacionados. Para ello, seleccione el grupo de recursos **CreatePubLBQS-rg**, que contiene los recursos y, a continuación, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido:

* Ha creado una instancia Estándar o Básica de Azure Load Balancer.
* Ha conectado 3 máquinas virtuales al equilibrador de carga.
* Probó el equilibrador de carga.

Para más información sobre Azure Load Balancer, vaya a:
> [!div class="nextstepaction"]
> [¿Qué es Azure Load Balancer?](load-balancer-overview.md)