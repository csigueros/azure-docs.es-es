---
title: Tutorial sobre el filtrado del tráfico de red en Azure Portal
titlesuffix: Azure Virtual Network
description: En este tutorial, aprenderá a filtrar el tráfico de red que se dirige a una subred, con un grupo de seguridad de red, mediante Azure Portal.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: 4af40f7f1caab7e4517c0dd621e400f58e3a29ad
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091376"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Tutorial: Filtrado del tráfico de red con un grupo de seguridad de red en Azure Portal

Puede utilizar un grupo de seguridad de red para filtrar el tráfico de entrada y salida de una subred de la red virtual.

Los grupos de seguridad de red contienen reglas de seguridad que filtran el tráfico de red por dirección IP, puerto y protocolo. Las reglas de seguridad se aplican a los recursos implementados en una subred. 

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Crear un grupo de seguridad de red y reglas de seguridad
> * Crear una red virtual y asociar un grupo de seguridad de red a una subred
> * Implementar máquinas virtuales (VM) en una subred
> * Probar los filtros de tráfico

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

- Suscripción a Azure.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Creación de una red virtual

1. Seleccione **Crear un recurso** en la esquina superior izquierda del portal.

2. En el cuadro de búsqueda del portal, escriba **red virtual**. En los resultados de la búsqueda, seleccione **Redes virtuales**.

3. En la página **Red virtual**, seleccione **Crear**.

4. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | Parámetro | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**.  </br> Escriba **myResourceGroup**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |   |
    | Nombre | Escriba **myVNet**. |
    | Region | Seleccione **(EE. UU.) Este de EE. UU.** . |

5. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear** que encontrará en la parte inferior de la pantalla.

6. Seleccione **Crear**.

## <a name="create-application-security-groups"></a>Creación de grupos de seguridad de aplicaciones

Un grupo de seguridad de aplicaciones permite agrupar servidores con funciones similares, como servidores web.

1. Seleccione **Crear un recurso** en la esquina superior izquierda del portal.

2. En el cuadro de búsqueda, escriba **Grupo de seguridad de aplicaciones**. Seleccione **Grupo de seguridad de aplicaciones** en los resultados de la búsqueda.

3. En la página **Grupo de seguridad de aplicaciones**, seleccione **Crear**.

4. En **Crear un grupo de seguridad de aplicación**, escriba o seleccione los datos siguientes en la pestaña **Conceptos básicos**:

    | Configuración | Value |
    | ------- | ----- |
    |**Detalles del proyecto** |  |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **myResourceGroup**. |
    | **Detalles de instancia** |  |
    | Nombre | Escriba **myAsgWebServers**. |
    | Region | Seleccione **(EE. UU.) Este de EE. UU.** . | 

5. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear** que encontrará en la parte inferior de la pantalla.

6. Seleccione **Crear**.

7. Repita el paso 4 especificando los siguientes valores:

    | Configuración | Value |
    | ------- | ----- |
    |**Detalles del proyecto** |  |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **myResourceGroup**. |
    | **Detalles de instancia** |  |
    | Nombre | Escriba **myAsgMgmtServers**. |
    | Region | Seleccione **(EE. UU.) Este de EE. UU.** . |

8. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear** que encontrará en la parte inferior de la pantalla.

9. Seleccione **Crear**.

## <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red

Los grupos de seguridad de red protegen el tráfico de una red virtual.

1. Seleccione **Crear un recurso** en la esquina superior izquierda del portal.

2. En el cuadro de búsqueda, escriba **Grupo de seguridad de red**. En los resultados de la búsqueda, seleccione **Grupo de seguridad de red**.

3. En la página **Grupo de seguridad de red**, seleccione **Crear**.

4. En **Crear un grupo de seguridad de red**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **myResourceGroup**. |
    | **Detalles de instancia** |   |
    | Nombre | Escriba **myNSG**. |
    | Ubicación | Seleccione **(EE. UU.) Este de EE. UU.** . | 

5. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear** que encontrará en la parte inferior de la pantalla.

6. Seleccione **Crear**.

## <a name="associate-network-security-group-to-subnet"></a>Asociación del grupo de seguridad de red a la subred

En esta sección, vamos a asociar el grupo de seguridad de red con la subred de la red virtual que creamos anteriormente.

1. En el cuadro **Search resources, services, and docs** (Buscar en recursos, servicios y documentos) en la parte superior del portal, comience a escribir **myNsg**. Cuando **myNsg** aparezca en los resultados de búsqueda, selecciónelo.

2. En la página de información general de **myNSG**, seleccione **Subredes** en **Configuración**.

3. En la página **Configuración**, seleccione **Asociar**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="Asociación del NSG a la subred." border="true":::

3. En **Asociar subred**, seleccione **Red virtual** y **myVNet**. 

4. Seleccione **Subred**, **predeterminada** y **Aceptar**.

## <a name="create-security-rules"></a>Creación de reglas de seguridad

1. En la opción **Configuración** de **myNSG**, seleccione **Reglas de seguridad de entrada**.

2. En **Reglas de seguridad de entrada**, haga clic en **+Agregar**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Adición de una regla de seguridad de entrada." border="true":::

3. Cree una regla de seguridad que permita a los puertos 80 y 443 para el grupo de seguridad de la aplicación **myAsgWebServers**. En la ventana **Agregar regla de seguridad de entrada**, especifique o seleccione los siguientes datos:

    | Configuración | Value |
    | ------- | ----- |
    | Source | Deje el valor predeterminado, **Cualquiera**. |
    | Source port ranges | Deje el valor predeterminado, **(*)** |
    | Destination | Seleccione **Grupo de seguridad de aplicación**. |
    | Grupo de seguridad de aplicación de destino | Seleccione **myAsgWebServers**. |
    | Servicio | Deje el valor predeterminado, **Personalizado**. |
    | Intervalos de puertos de destino | Escriba **80 443**. |
    | Protocolo | seleccione **TCP**. |
    | Acción | Deje el valor predeterminado, **Permitir**. |
    | Priority | Deje el valor predeterminado, **100**. |
    | Nombre | Escriba **Allow-Web-All**. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Regla de seguridad de entrada." border="true":::

3. Repita el paso 2 de nuevo, utilizando los siguientes valores:

    | Configuración | Value |
    | ------- | ----- |
    | Source | Deje el valor predeterminado, **Cualquiera**. |
    | Source port ranges | Deje el valor predeterminado, **(*)** |
    | Destination | Seleccione **Grupo de seguridad de aplicación**. |
    | Grupo de seguridad de aplicación de destino | Seleccione **myAsgMgmtServers**. |
    | Servicio | Deje el valor predeterminado, **Personalizado**. |
    | Intervalos de puertos de destino | Escriba **3389**. |
    | Protocolo | Seleccione **Cualquiera**. |
    | Acción | Deje el valor predeterminado, **Permitir**. |
    | Priority | Deje el valor predeterminado, **110**. |
    | Nombre | Escriba **Allow-RDP-All**. |

    > [!CAUTION]
    > En este artículo, RDP (puerto 3389) está expuesto a Internet para la máquina virtual asignada al grupo de seguridad de aplicaciones **myAsgMgmtServers**. 
    >
    > En entornos de producción, en lugar de exponer el puerto 3389 a Internet, es conveniente que se conecte a los recursos de Azure que desee administrar utilizando una VPN, una conexión de red privada o Azure Bastion.
    >
    > Para más información, consulte [¿Qué es Azure Bastion?](../bastion/bastion-overview.md)

Una vez completados los pasos 1 a 3, revise las reglas creadas. La lista debería ser similar a la que aparece en el siguiente ejemplo:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Reglas de seguridad." border="true":::

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree dos máquinas virtuales en la red virtual.

### <a name="create-the-first-vm"></a>Creación de la primera máquina virtual

1. Seleccione **Crear un recurso** en la esquina superior izquierda del portal.

2. Seleccione **Proceso** y **Máquina virtual**.

3. En **Crear una máquina virtual**, escriba o seleccione los datos siguientes en la pestaña **Conceptos básicos**:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |  |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **myResourceGroup**. |
    | **Detalles de instancia** |   |
    | Nombre de la máquina virtual | Escriba **myVMWeb**. |
    | Region | Seleccione **(EE. UU.) Este de EE. UU.** . |
    | Opciones de disponibilidad | Deje el valor predeterminado de que no es necesario tener redundancia. |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen1**. |
    | Instancia de Azure Spot | Deje esta casilla desactivada, tal y como está de forma predeterminada. |
    | Size | Seleccione **Standard_D2s_V3**. |
    | **Cuenta de administrador** |   |
    | Nombre de usuario | Especifique un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmación de la contraseña | Vuelva a escribir la contraseña. |
    | **Reglas de puerto de entrada** |   |
    | Puertos de entrada públicos | Seleccione **Ninguno**. |

4. Seleccione la pestaña **Redes**.

5. En la pestaña **Redes**, escriba o seleccione la siguiente información:

    | Parámetro | Value |
    | ------- | ----- |
    | **Interfaz de red** |   |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | Seleccione **valor predeterminado (10.0.0.0/24)** . |
    | Dirección IP pública | Deje el valor predeterminado, que es una nueva dirección IP pública. |
    | Grupo de seguridad de red de NIC | Seleccione **Ninguno**. | 

6. Seleccione la pestaña **Revisar y crear** o el botón azul **Revisar y crear** en la parte inferior de la página.

7. Seleccione **Crear**.

### <a name="create-the-second-vm"></a>Creación de la segunda máquina virtual

Repita los pasos 1 a 7, pero en el paso 3 llame a la máquina virtual **myVMMgmt**. La maquina virtual tarda unos minutos en implementarse. 

No continúe con el paso siguiente hasta que se implemente la máquina virtual.

## <a name="associate-network-interfaces-to-an-asg"></a>Asociación de interfaces de red a un ASG

Cuando el portal creó las máquinas virtuales, se creó también una interfaz de red para cada una y la interfaz de red se asoció a la máquina virtual. 

Agregue la interfaz de red para cada máquina virtual a uno de los grupos de seguridad de la aplicación que creó anteriormente:

1. En el cuadro **Buscar recursos, servicios y documentos** que encontrará en la parte superior del portal, comience a escribir **myVMWeb**. Cuando aparezca la máquina virtual **myVMWeb** en los resultados de la búsqueda, selecciónela.

2. En **Configuración**, seleccione **Redes**.  

3. Seleccione la pestaña **Grupos de seguridad de aplicación** y **Configurar grupos de seguridad de aplicación**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Configuración de grupos de seguridad de aplicación" border="true":::.

4. En **Configurar grupos de seguridad de aplicación** , seleccione **myAsgWebServers**. Seleccione **Guardar**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Selección de los grupos de seguridad de aplicación." border="true":::

5. Vuelva a realizar los pasos 1 y 2 buscando la máquina virtual **myVMMgmt** y seleccionando el ASG **myAsgMgmtServers**.

## <a name="test-traffic-filters"></a>Probar los filtros de tráfico

1. Conéctese a la máquina virtual **myVMMgmt**. Escriba **myVMMgmt** en el cuadro de búsqueda que se encuentra en la parte superior del portal. Cuando **myVMMgmt** aparezca en los resultados de la búsqueda, selecciónela. Seleccione el botón **Conectar**.

2. Seleccione **Descargar archivo RDP**.

3. Abra el archivo RDP descargado y seleccione **Conectar**. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual.

4. Seleccione **Aceptar**.

5. Puede recibir una advertencia sobre el certificado durante el proceso de conexión. Si aparece esta advertencia, seleccione **Sí** o **Continuar** para continuar con la conexión.

    La conexión se realiza correctamente, ya que el puerto 3389 tiene permitida la entrada desde Internet en el grupo de seguridad de aplicaciones **myAsgMgmtServers**. 
    
    La interfaz de red de **myVMMgmt** está asociada con el grupo de seguridad de aplicaciones **myAsgMgmtServers** y permite la conexión.

6. Abra una sesión de PowerShell en **myVMMgmt**. Conéctese a **myVMWeb** utilizando el ejemplo siguiente: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    La conexión RDP entre **myVMMgmt** y **myVMWeb** se realiza correctamente, ya que, de forma predeterminada, las máquinas virtuales de la misma red pueden comunicarse entre sí por cualquier puerto.
    
    No se puede crear una conexión RDP con la máquina virtual **myVMWeb** desde Internet. La regla de seguridad de **myAsgWebServers** impide la conexión con el puerto 3389 de entrada desde Internet. De forma predeterminada, se deniega el tráfico de entrada de Internet en todos los recursos.

7. Para instalar Microsoft IIS en la máquina virtual **myVMWeb**, escriba el siguiente comando desde una sesión de PowerShell de la máquina virtual **myVMWeb**:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Una vez completada la instalación de IIS, desconecte la sesión de la máquina virtual **myVMWeb**, lo que le deja en la conexión de escritorio remoto de la máquina virtual **myVMMgmt**.

9. Desconéctese de la máquina virtual **myVMMgmt**.

10. En el cuadro **Buscar recursos, servicios y documentos** que encontrará en la parte superior de Azure Portal, comience a escribir **myVMWeb** en su equipo. Cuando aparezca **myVMWeb** en los resultados de la búsqueda, selecciónela. Anote la **Dirección IP pública** de la máquina virtual. La dirección que aparece en el ejemplo siguiente es 23.96.39.113, pero su dirección será diferente:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Dirección IP pública." border="true":::
    
11. Para confirmar que tiene acceso al servidor web **myVMWeb** desde Internet, abra un explorador de Internet en el equipo y vaya a `http://<public-ip-address-from-previous-step>`. 

Aparecerá la pantalla de bienvenida de IIS, ya que en el puerto 80 se permite el tráfico de entrada desde Internet al grupo de seguridad de aplicaciones **myAsgWebServers**. 

La interfaz de red conectada para **myVMWeb** está asociada al grupo de seguridad de aplicaciones **myAsgWebServers** y permite la conexión. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene:

1. Escriba **myResourceGroup** en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.
2. Seleccione **Eliminar grupo de recursos**.
3. Escriba **myResourceGroup** para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

* Creado un grupo de seguridad de red y lo ha asociado a una subred de red virtual. 
* Creado grupos de seguridad de aplicaciones para la Web y las tareas de administración.
* Creado dos máquinas virtuales.
* Probado el filtrado de redes de los grupos de seguridad de aplicaciones.

Para más información acerca de los grupos de seguridad de red, consulte [Introducción a los grupos de seguridad de red](./network-security-groups-overview.md) y [Administración de un grupo de seguridad de red](manage-network-security-group.md).

De forma predeterminada, Azure enruta el tráfico entre subredes. En su lugar, puede elegir enrutar el tráfico entre subredes a través de una máquina virtual, que actúa como un firewall, por ejemplo. 

Para aprender a crear una tabla de rutas, avance al siguiente tutorial.
> [!div class="nextstepaction"]
> [Creación de una tabla de rutas](./tutorial-create-route-table-portal.md)
