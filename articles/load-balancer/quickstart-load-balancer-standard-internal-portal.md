---
title: 'Inicio rápido: Creación de un equilibrador de carga interno: Azure Portal'
titleSuffix: Azure Load Balancer
description: En este inicio rápido se muestra cómo crear un equilibrador de carga interno mediante Azure Portal.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.date: 08/09/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 9c8f46bf615b0d0c9fe8289057a8244a1d57185d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732808"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Inicio rápido: Creación de un equilibrador de carga interno para equilibrar la carga de las máquinas virtuales mediante Azure Portal

Para empezar a usar Azure Load Balancer diríjase a Azure Portal para crear un equilibrador de carga interno y tres máquinas virtuales.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**SKU Estándar**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>Se recomienda usar la SKU Estándar de Load Balancer para las cargas de trabajo de producción.  Para más información sobre las SKU, consulte **[SKU de Azure Load Balancer](skus.md)** .

Cuando se crea un equilibrador de carga interno, se configura una red virtual como red para él. 

Una dirección IP privada de la red virtual se configura como front-end (denominado **LoadBalancerFrontend** de manera predeterminada) para el equilibrador de carga. 

La dirección IP de front-end puede ser **estática** o **dinámica**.

## <a name="create-the-virtual-network"></a>Crear la red virtual

En esta sección, creará una red virtual y una subred.

1. En el cuadro de búsqueda de la parte superior del portal, escriba **Red virtual**. En los resultados de la búsqueda, seleccione **Redes virtuales**.

2. En **Redes virtuales**, seleccione **+ Crear**.

3. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **CreateIntLBQS-rg**. </br> Seleccione **Aceptar**. |
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
    | Resource group | Seleccione **CreateIntLBQS-rg**. |
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

## <a name="create-load-balancer"></a><a name="create-load-balancer-resources"></a> Creación de un equilibrador de carga

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
    | Resource group         | Seleccione **CreateIntLBQS-rg**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer**.                                   |
    | Region         | Seleccione **(Europa) Oeste de Europa**.                                        |
    | Tipo          | seleccione **Interno**.                                        |
    | SKU           | Deje el valor predeterminado **Estándar**. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Captura de pantalla de la pestaña de conceptos básicos del equilibrador de carga estándar." border="true":::

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

    > [!NOTE]
    > En este ejemplo, ha creado una puerta de enlace NAT para proporcionar acceso saliente a Internet. La pestaña de reglas de salida de la configuración se omite y no es necesaria con la puerta de enlace NAT. Para obtener más información sobre la puerta de enlace NAT de Azure, consulte [¿Qué es Azure Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md).
    > Para obtener más información sobre las conexiones salientes en Azure, consulte [Traducción de direcciones de red de origen (SNAT) para conexiones salientes](../load-balancer/load-balancer-outbound-connections.md).

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

En esta sección, creará tres máquinas virtuales (**myVM1**, **myVM2** y **myVM3**) en tres zonas diferentes (**Zona 1**, **Zona 2** y **Zona 3**). 

Estas máquinas virtuales se agregan al grupo de back-end del equilibrador de carga que se creó anteriormente.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. En **Máquinas virtuales**, seleccione **+ Crear** > **Máquina virtual**.
   
3. En **Crear una máquina virtual**, escriba o seleccione los valores en la pestaña **Básico**:

    | Configuración | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **CreateIntLBQS-rg** |
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
    | Configuración del grupo de seguridad de red | Seleccione **Crear nuevo**. </br> En la página **Crear grupo de seguridad de red**, escriba **myNSG** en **Nombre**. </br> En **Reglas de entrada**, seleccione **+Agregar una regla de entrada**. </br> En **Intervalos de puertos de destino**, escriba **80**. </br> En **Prioridad**, escriba **100**. </br> En **Nombre**, escriba **myNSGRule**. </br> Seleccione **Agregar**. </br> Seleccione **Aceptar**. |
    | **Equilibrio de carga**  |
    | ¿Quiere colocar esta máquina virtual detrás de una solución de equilibrio de carga existente? | Seleccione la caja. |
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
    | Grupo de recursos   | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **CreateIntLBQS-rg**. </br> Seleccione **Aceptar**. |
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
    | Resource group         | Seleccione **CreateIntLBQS-rg**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer**.                                   |
    | Region         | Seleccione **(Europa) Oeste de Europa**.                                        |
    | Tipo          | seleccione **Interno**.                                        |
    | SKU           | Seleccione **Básica**. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Captura de pantalla de la pestaña de conceptos básicos del equilibrador de carga estándar." border="true":::

4. Seleccione **Siguiente: Configuración de IP de front-end** en la parte inferior de la página.

5. En **Configuración de IP de front-end**, seleccione **+ Agregar una IP de front-end**.

6. Escriba **LoadBalancerFrontEnd** en **Nombre**.

7. Seleccione **myBackendSubnet** en **Subred**.

8. Seleccione **Dinámica** para **Asignación**.

9. Seleccione **Agregar**.

10. Seleccione **Siguiente: Grupos de back-end** en la parte inferior de la página.

11. En la pestaña **Grupos de back-end**, seleccione **+ Agregar un grupo de back-end**.

12. Escriba **myBackendPool** como **Nombre** en **Agregar un grupo de back-end**.

13. Seleccione **Máquinas virtuales** en **Asociado a**.

14. Seleccione **IPv4** o **IPv6** para **Versión de IP**.

15. Seleccione **Agregar**.

16. Seleccione el botón **Siguiente: Reglas de entrada** situado en la parte inferior de la página.

17. En **Regla de equilibrio de carga** de la pestaña **Reglas de entrada**, seleccione **+ Agregar regla de equilibrio de carga**.

18. En **Agregar regla de equilibrio de carga**, escriba o seleccione la siguiente información:

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

19. Seleccione **Agregar**.

20. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página.

21. Seleccione **Crear**.

    > [!NOTE]
    > En este ejemplo, hemos creado una puerta de enlace NAT para proporcionar acceso saliente a Internet. La pestaña de reglas de salida de la configuración se omite y no es necesaria con la puerta de enlace NAT. Para obtener más información sobre la puerta de enlace NAT de Azure, consulte [¿Qué es Azure Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md).
    > Para obtener más información sobre las conexiones salientes en Azure, consulte [Traducción de direcciones de red de origen (SNAT) para conexiones salientes](../load-balancer/load-balancer-outbound-connections.md).

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
    | Grupo de recursos | Seleccione **CreateIntLBQS-rg** |
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
    | ¿Quiere colocar esta máquina virtual detrás de una solución de equilibrio de carga existente? | Seleccione la caja. |
    | **Configuración de equilibrio de carga** |   |
    | Opciones de equilibrio de carga | Seleccione **Azure Load Balancer**. |
    | Seleccionar un equilibrador de carga | Seleccione **myLoadBalancer**. |
    | Seleccionar un grupo de back-end | Seleccione **MyBackendPool**. |
 
6. Seleccione **Revisar + crear**. 
  
7. Revise la configuración y, a continuación, seleccione **Crear**.

8. Siga los pasos 1 a 7 para crear dos VM más con los siguientes valores y el resto de la configuración debe coincidir con la de **myVM1**:

    | Configuración | VM 2 | VM 3 |
    | ------- | ----- |---|
    | Nombre |  **myVM2** |**myVM3**|
    | Conjunto de disponibilidad| Seleccione **myAvailabilitySet**. | Seleccione **myAvailabilitySet**.|
    | Grupo de seguridad de red | Seleccione el grupo **myNSG** existente.| Seleccione el grupo **myNSG** existente.|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]
---

## <a name="create-test-virtual-machine"></a>Creación de una máquina virtual de prueba

En esta sección va a crear una máquina virtual denominada **myTestVM**.  Esta máquina virtual se usará para probar la configuración del equilibrador de carga.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. En **Máquinas virtuales**, seleccione **+ Crear** > **Máquina virtual**.
   
2. En **Crear una máquina virtual**, escriba o seleccione los valores en la pestaña **Básico**:

    | Parámetro | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **CreateIntLBQS-rg** |
    | **Detalles de instancia** |  |
    | Nombre de la máquina virtual | Escriba **myTestVM** |
    | Region | Seleccione **(Europa) Oeste de Europa**. |
    | Opciones de disponibilidad | Seleccione **No se requiere redundancia de la infraestructura** |
    | Imagen | Seleccione **Windows Server 2019 Datacenter**. |
    | Instancia de Azure Spot | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
    | Size | Elija el tamaño de la máquina virtual o acepte la configuración predeterminada. |
    | **Cuenta de administrador** |  |
    | Nombre de usuario | Escriba un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmar contraseña | Vuelva a escribir la contraseña. |
    | **Reglas de puerto de entrada** |   |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

3. Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.
  
4. En la pestaña **Redes**, seleccione o escriba:

    | Parámetro | Value |
    |-|-|
    | **Interfaz de red** |  |
    | Virtual network | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Avanzado**. |
    | Configuración del grupo de seguridad de red | Seleccione **MyNSG**, que creó en el paso anterior.|
       
5. Seleccione **Revisar + crear**. 
  
6. Revise la configuración y, a continuación, seleccione **Crear**.

## <a name="install-iis"></a>Instalación de IIS

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. Seleccione **myVM1**.

3. En la página **Información general**, seleccione **Conectar** y, luego, **Bastion**.

4. Seleccione **Usar Bastion**. 

5. Escriba el nombre de usuario y la contraseña especificados durante la creación de la máquina virtual.

6. Seleccione **Conectar**.

7. En el escritorio del servidor, vaya a **Herramientas administrativas de Windows** > **Windows PowerShell** > **Windows PowerShell**.

8. En la nueva ventana de PowerShell, ejecute los siguientes comandos para hacer lo siguiente:

    * Instalar el servidor IIS.
    * Eliminar el archivo predeterminado iisstart.htm.
    * Agregar un nuevo archivo iisstart.htm que muestre el nombre de la VM.

   ```powershell
    
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```

9. Cierre la sesión de Bastion con **myVM1**.

10. Repita los pasos del 1 al 9 para instalar IIS y el archivo iisstart.htm actualizado en **myVM2** y **myVM3**.

## <a name="test-the-load-balancer"></a>Prueba del equilibrador de carga

En esta sección, probará el equilibrador de carga. Para hacerlo, se conectará a **myTestVM** y comprobará la página web.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. Seleccione **myLoadBalancer**.

3. Tome nota o copie la dirección que encontrará junto a **Dirección IP privada**, en la pestaña **Información general** de **myLoadBalancer**.

4. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Máquina virtual**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

5. Seleccione **myTestVM**.

6. En la página **Información general**, seleccione **Conectar** y, luego, **Bastion**.

7. Seleccione **Usar Bastion**.

8. Escriba el nombre de usuario y la contraseña especificados durante la creación de la máquina virtual.

9. Abra **Internet Explorer** en **myTestVM**.

10. Escriba la dirección IP del paso anterior en la barra de direcciones del explorador. La página personalizada que muestra uno de los nombres de servidor back-end se muestra en el explorador.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="La captura de pantalla muestra una ventana del explorador que presenta la página personalizada, según lo esperado" border="true":::.
   
Para ver cómo Load Balancer distribuye el tráfico entre ambas VM, puede forzar la actualización del explorador web desde la máquina cliente.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, el equilibrador de carga y todos los recursos relacionados. Para ello, seleccione el grupo de recursos **CreateIntLBQS-rg**, que contiene los recursos y, a continuación, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido:

* Ha creado un equilibrador de carga Básico o Estándar en Azure.
* Ha conectado 3 máquinas virtuales al equilibrador de carga.
* Ha configurado la regla de tráfico del equilibrador de carga, el sondeo de estado y, a continuación, ha probado el equilibrador de carga. 

Para más información sobre Azure Load Balancer, vaya a:
> [!div class="nextstepaction"]
> [¿Qué es Azure Load Balancer?](load-balancer-overview.md)