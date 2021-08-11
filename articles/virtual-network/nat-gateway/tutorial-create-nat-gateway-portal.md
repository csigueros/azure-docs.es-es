---
title: 'Tutorial: Creación de una puerta de enlace de NAT: Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: En este tutorial, aprenderá a crear una puerta de enlace NAT y asegurarse de que sea correcta mediante Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438870"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Tutorial: Creación de una puerta de enlace de NAT mediante Azure Portal

En este tutorial se muestra cómo usar el servicio Azure Virtual Network NAT. Creará una puerta de enlace de NAT para proporcionar conectividad saliente para una máquina virtual en Azure. 

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Cree una red virtual.
> * Cree una máquina virtual.
> * Cree una puerta de enlace NAT y asóciela a la red virtual.
> * Conéctese a la máquina virtual y compruebe la dirección IP de NAT.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="virtual-network"></a>Virtual network

Antes de implementar una máquina virtual y usar la puerta de enlace de NAT, es necesario crear el grupo de recursos y la red virtual.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso > Redes > Red virtual** o busque **Red virtual** en el cuadro de búsqueda.

3. Seleccione **Crear**.

4. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **Crear nuevo**. </br> Especifique **myResourceGroupNAT**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **(Europa) Oeste de Europa**. |

5. Seleccione la pestaña **Direcciones IP** o el botón **Siguiente: Direcciones IP** situado en la parte inferior de la página.

6. En la pestaña **Direcciones IP**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Espacio de direcciones IPv4 | Escriba **10.1.0.0/16**. |

7. Haga clic en **+ Agregar subred**. 

8. En **Editar subred**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre de subred | Escriba **mySubnet**. |
    | Intervalo de direcciones de subred | Escriba **10.1.0.0/24**. |

9. Seleccione **Agregar**.

10. Seleccione la pestaña **Seguridad** .

11. En **BastionHost**, seleccione **Habilitar**. Escriba esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre del bastión | Escriba **myBastionHost**. |
    | Espacio de direcciones de AzureBastionSubnet | Escriba **10.1.1.0/24**. |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myBastionIP**. </br> Seleccione **Aceptar**. |

12. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear**.

13. Seleccione **Crear**.

## <a name="nat-gateway"></a>NAT Gateway

Puede usar uno o varios recursos de dirección IP pública, prefijos de dirección IP pública, o ambos. Se agregará un recurso de IP pública y un recurso de puerta de enlace NAT.

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso > Redes > Puerta de enlace NAT** o busque **Puerta de enlace NAT** en el cuadro de búsqueda.

2. Seleccione **Crear**. 

3. En **Crear puerta de enlace de traducción de direcciones de red (NAT)** , escriba o seleccione esta información en la pestaña **Información básica**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Seleccione su suscripción a Azure.                                  |
    | Grupo de recursos   | Seleccione **myResourceGroupNAT**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Especifique **myNATgateway**.                                    |
    | Region           | Seleccione **(Europa) Oeste de Europa**.  |
    | Zona de disponibilidad | Seleccione **Ninguno**. |
    | Tiempo de espera de inactividad (minutos) | Escriba **10**. |

4. Seleccione la pestaña **Dirección IP de salida** o elija el botón **Next: Outbound IP** (Siguiente: Dirección IP de salida) situado en la parte inferior de la página.

5. En la pestaña **Dirección IP de salida**, escriba o seleccione la siguiente información:

    | **Configuración** | **Valor** |
    | ----------- | --------- |
    | Direcciones IP públicas | Seleccione **Crear una dirección IP pública**. </br> En **Nombre**, escriba **myPublicIP**. </br> Seleccione **Aceptar**. |

6. Seleccione la pestaña **Subred** o elija el botón **Next: Subnet** (Siguiente: Subred) situado en la parte inferior de la página.

7. En la pestaña **Subred**, seleccione **myVNet** en el cuadro desplegable **Red virtual**.

8. Active la casilla junto a **mySubnet**.

9. Seleccione la pestaña **Revisar y crear** o el botón azul **Revisar y crear** en la parte inferior de la página.

10. Seleccione **Crear**.
    
## <a name="virtual-machine"></a>Máquina virtual

En esta sección, creará una máquina virtual para probar la puerta de enlace NAT y comprobar la dirección IP pública de la conexión saliente.

1. En la parte superior izquierda de Azure Portal, seleccione **Crear un recurso** > **Proceso** > **Máquina virtual**. 

2. En la página **Crear una máquina virtual** en la pestaña **Información básica**, escriba o seleccione la siguiente información:

    | **Configuración** | **Valor** |
    | ----------- | --------- |
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **myResourceGroupNAT**. |
    | **Detalles de instancia** |   |
    | Nombre de la máquina virtual | Escriba **myVM**. |
    | Region | Seleccione **(Europa) Oeste de Europa**. |
    | Opciones de disponibilidad | Deje el valor predeterminado de que no es necesario tener redundancia. |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen1**. |
    | Size | Seleccione **Standard_DS1_v2**. |
    | **Cuenta de administrador** |   |
    | Nombre de usuario | Especifique un nombre de usuario de la máquina virtual. |
    | Contraseña | Escriba una contraseña. |
    | Confirmación de la contraseña | Confirme la contraseña. |
    | **Reglas de puerto de entrada** |    |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

3. Seleccione la pestaña **Discos** o elija el botón **Next: Disks** (Siguiente: Discos) situado en la parte inferior de la página.

4. Deje el valor predeterminado en la pestaña **Discos**.

5. Seleccione la pestaña **Redes** o elija el botón **Next: Networking** (Siguiente: Redes) situado en la parte inferior de la página.

6. En la pestaña **Redes**, escriba o seleccione la siguiente información:

    | **Configuración** | **Valor** |
    | ----------- | --------- |
    | **Interfaz de red** |   |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | Seleccione **mySubnet**. |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | Seleccione **Básica**. |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

7. Seleccione la pestaña **Revisar y crear** o el botón azul **Revisar y crear** en la parte inferior de la página.

8. Seleccione **Crear**.

## <a name="test-nat-gateway"></a>Prueba de la puerta de enlace NAT

En esta sección, probaremos la puerta de enlace NAT. En primer lugar, detectaremos la dirección IP pública de la puerta de enlace NAT. A continuación, nos conectaremos a la máquina virtual de prueba y comprobaremos la conexión saliente mediante la puerta de enlace NAT.
    
1. Busque la dirección IP pública de la puerta de enlace NAT en la pantalla **Introducción**. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, **myPublicIP**.

2. Anote la dirección IP pública:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Detección de la dirección IP pública de la puerta de enlace NAT" border="true":::

3. Seleccione **Todos los servicios** en el menú de la izquierda, elija **Todos los recursos** y, en la lista de recursos, seleccione **myVM**, que se encuentra en el grupo de recursos **myResourceGroupNAT**.

4. En la página **Introducción**, seleccione **Conectar** y después **Instancia de Bastion**.

5. Seleccione el botón **Usar bastión** azul.

6. Escriba el nombre de usuario y la contraseña especificados durante la creación de la máquina virtual.

7. Abra **Internet Explorer** en **myTestVM**.

8. Escriba **https://whatsmyip.com** en la barra de direcciones.

9. Compruebe que la dirección IP mostrada coincida con la dirección de puerta de enlace de NAT anotada en el paso anterior:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer que muestra la dirección IP saliente externa" border="true":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine la red virtual, la máquina virtual y la puerta de enlace NAT mediante los pasos siguientes:

1. En el menú izquierdo, seleccione **Grupos de recursos**.

2. Seleccione el grupo de recursos **myResourceGroupNAT**.

3. Seleccione **Eliminar grupo de recursos**.

4. Especifique **myResourceGroupNAT** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Virtual Network NAT, consulte:
> [!div class="nextstepaction"]
> [Información general de Virtual Network NAT](nat-overview.md)
