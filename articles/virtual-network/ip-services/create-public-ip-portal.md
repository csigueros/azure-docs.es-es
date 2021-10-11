---
title: 'Inicio rápido: Creación de una dirección IP pública mediante Azure Portal'
titleSuffix: Azure Virtual Network
description: En este inicio rápido, aprenderá a crear una dirección IP pública de SKU estándar o básica. También obtendrá información sobre la preferencia y el nivel de enrutamiento.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: ea18353a787918cbf0abe491b1a2437b9a7d3d4a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369809"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Inicio rápido: Creación de una dirección IP pública mediante Azure Portal

En este inicio rápido aprenderá a crear una dirección IP pública de Azure. Las direcciones IP públicas de Azure se usan para las conexiones públicas a recursos de Azure. Las direcciones IP públicas están disponibles en dos SKU: básica y estándar. Hay dos niveles de direcciones IP públicas disponibles: regionales y globales.  La preferencia de enrutamiento de una dirección IP pública se establece cuando se crea. El enrutamiento de Internet y el enrutamiento de Microsoft Network son las opciones disponibles.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**SKU Estándar**](#tab/option-1-create-public-ip-standard)

>[!NOTE]
>Se recomienda usar la dirección IP pública de SKU estándar para cargas de trabajo de producción.  Para obtener más información sobre las SKU, consulte **[Direcciones IP públicas](public-ip-addresses.md)** .

## <a name="create-a-standard-sku-public-ip-address"></a>Creación de una dirección IP pública de una SKU estándar

Use los pasos siguientes para crear una dirección IPv4 pública estándar llamada **myStandardPublicIP**. 

> [!NOTE]
>Para crear una dirección IPv6, elija **IPv6** para el parámetro **IP Version**. Si la implementación requiere una configuración de pila doble (dirección IPv4 e IPv6), elija **Ambos**.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

2. En los resultados de la búsqueda, escriba **Direcciones IP públicas**.

3. Seleccione **+ Create** (+ Crear).

4. En **Crear dirección IP pública**, escriba o seleccione la siguiente información:

    | Configuración                 | Value                       |
    | ---                     | ---                         |
    | Versión de la dirección IP              | Seleccione IPv4              |    
    | SKU                     | Seleccione **Estándar**.         |
    | Nivel                   | Seleccione **Regional**.     |
    | Nombre                    | Escriba **myStandardPublicIP**          |
    | Asignación de dirección IP   | Se bloquea como **Estática**                |
    | Preferencia de enrutamiento     | Seleccione **Red de Microsoft**. |
    | Tiempo de espera de inactividad (minutos)  | Deje el valor predeterminado **4**.        |
    | Etiqueta de nombre DNS          | Deje el valor en blanco.    |
    | Subscription            | Seleccione su suscripción.   |
    | Resource group          | Seleccione **Crear nuevo** y escriba **QuickStartCreateIP-rg**. </br> Seleccione **Aceptar**. |
    | Location                | Seleccione **(EE. UU.) Este de EE. UU. 2**.     |
    | Zona de disponibilidad       | Seleccione **Ninguna zona**. |

5. Seleccione **Crear**.

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Captura de pantalla de la creación de una dirección IP estándar en Azure Portal" border="false":::

> [!NOTE]
> En las regiones con [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), tiene la opción de seleccionar Ninguna zona (opción predeterminada), una zona específica o redundancia de zona. La elección dependerá de los requisitos de error de dominio específicos. En regiones sin Availability Zones, este campo no aparecerá. </br> Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../../availability-zones/az-overview.md).

Puede asociar la dirección IP pública creada anteriormente con una máquina virtual de [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Use la sección CLI en la siguiente página del tutorial: [asociación de una dirección IP pública a una máquina virtual](../../virtual-network/associate-public-ip-address-vm.md#azure-cli) para asociar la dirección IP pública a la VM. Asimismo, puede asociar una dirección IP pública que haya creado con anterioridad con una instancia de [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) asignándola a la configuración de **front-end** del equilibrador de carga. La dirección IP pública actúa como dirección IP virtual (VIP) de carga equilibrada.

# <a name="basic-sku"></a>[**SKU básica**](#tab/option-1-create-public-ip-basic)

>[!NOTE]
>Se recomienda usar la dirección IP pública de SKU estándar para cargas de trabajo de producción.  Para obtener más información sobre las SKU, consulte **[Direcciones IP públicas](public-ip-addresses.md)** .

## <a name="create-a-basic-sku-public-ip-address"></a>Creación de una IP pública dinámica para una SKU básica

En esta sección, creará una dirección IPv4 pública básica denominada **myBasicPublicIP**. 

> [!NOTE]
> Las direcciones IP públicas básicas no admiten zonas de disponibilidad.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

2. En los resultados de la búsqueda, escriba **Direcciones IP públicas**.

3. Seleccione **+ Create** (+ Crear).

4. En la página **Crear dirección IP pública**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                       |
    | ---                     | ---                         |
    | Versión de la dirección IP              | Seleccione **IPv4**.                |    
    | SKU                     | Seleccione **Básica**.         |
    | Nombre                    | Escriba **myBasicPublicIP**          |
    | Asignación de dirección IP   | Seleccione **Estática**.            |
    | Tiempo de espera de inactividad (minutos)  | Deje el valor predeterminado **4**.       |
    | Etiqueta de nombre DNS          | Deje el valor en blanco.    |
    | Subscription            | Seleccione su suscripción.   |
    | Resource group          | Seleccione **Crear nuevo** y escriba **QuickStartCreateIP-rg**. </br> Seleccione **Aceptar**. |
    | Location                | Seleccione **(EE. UU.) Este de EE. UU. 2**.      |

5. Seleccione **Crear**.

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Captura de pantalla de la creación de una dirección IP básica en Azure Portal" border="true":::

Si es admisible que la dirección IP cambie con el tiempo, se puede seleccionar la asignación de IP **dinámica** cambiando el valor de AllocationMethod a **Dynamic**. 

# <a name="routing-preference"></a>[**Preferencia de enrutamiento**](#tab/option-1-create-public-ip-routing-preference)

En esta sección se muestra cómo configurar las [preferencias de enrutamiento](routing-preference-overview.md) a través de la red de ISP (opción de **Internet**) para una dirección IP pública. Después de crear la dirección IP pública, se puede asociar a los siguientes recursos de Azure:

* Máquina virtual
* Conjunto de escalado de máquina virtual
* Azure Kubernetes Service (AKS)
* Equilibrador de carga accesible desde Internet
* Application Gateway
* Azure Firewall

De forma predeterminada, la preferencia de enrutamiento de la dirección IP pública se establece en la red global de Microsoft para todos los servicios de Azure y puede asociarse a cualquier servicio de Azure.

> [!NOTE]
>Para crear una dirección IPv6, elija **IPv6** para el parámetro **IP Version**. Si la implementación requiere una configuración de pila doble (dirección IPv4 e IPv6), elija **Ambos**.

## <a name="create-a-public-ip-with-internet-routing"></a>Creación de una dirección IP pública con enrutamiento de Internet

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

2. En los resultados de la búsqueda, escriba **Direcciones IP públicas**.

3. Seleccione **+ Create** (+ Crear).

4. En **Crear dirección IP pública**, escriba o seleccione la siguiente información:

    | Configuración                 | Value                       |
    | ---                     | ---                         |
    | Versión de la dirección IP              | Seleccione IPv4              |    
    | SKU                     | Seleccione **Estándar**.         |
    | Nivel                   | Seleccione **Regional**.     |
    | Nombre                    | Escriba **myStandardPublicIP-RP**.          |
    | Asignación de dirección IP   | Se bloquea como **Estática**                |
    | Preferencia de enrutamiento     | seleccione **Internet**. |
    | Tiempo de espera de inactividad (minutos)  | Deje el valor predeterminado **4**.        |
    | Etiqueta de nombre DNS          | Deje el valor en blanco.    |
    | Subscription            | Seleccione su suscripción.   |
    | Resource group          | Seleccione **Crear nuevo** y escriba **QuickStartCreateIP-rg**. </br> Seleccione **Aceptar**. |
    | Location                | Seleccione **(EE. UU.) Este de EE. UU. 2**.     |
    | Zona de disponibilidad       | Seleccione **Redundancia de zona**. |

5. Seleccione **Crear**.

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="Captura de pantalla de la configuración de la preferencia de enrutamiento en Azure Portal" border="true":::

> [!NOTE]
> Las direcciones IP públicas se crean con una dirección IPv4 o IPv6. Sin embargo, la preferencia de enrutamiento solo admite IPV4 actualmente.

> [!NOTE]
> En las regiones con [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), tiene la opción de seleccionar Ninguna zona (opción predeterminada), una zona específica o redundancia de zona. La elección dependerá de los requisitos de error de dominio específicos. En regiones sin Availability Zones, este campo no aparecerá. </br> Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../../availability-zones/az-overview.md).

Puede asociar la dirección IP pública creada anteriormente con una máquina virtual de [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Use la sección CLI en la siguiente página del tutorial: [asociación de una dirección IP pública a una máquina virtual](../../virtual-network/associate-public-ip-address-vm.md#azure-cli) para asociar la dirección IP pública a la VM. Asimismo, puede asociar una dirección IP pública que haya creado con anterioridad con una instancia de [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) asignándola a la configuración de **front-end** del equilibrador de carga. La dirección IP pública actúa como dirección IP virtual (VIP) de carga equilibrada.

# <a name="tier"></a>[**Nivel**](#tab/option-1-create-public-ip-tier)

Las direcciones IP públicas están asociadas a una sola región. El nivel **Global** abarca una dirección IP entre varias regiones. El nivel **Global** es necesario para los servidores front-end de equilibradores de carga entre regiones.  

Para más información, consulte [Equilibrador de carga entre regiones](../../load-balancer/cross-region-overview.md).

## <a name="create-a-global-tier-public-ip"></a>Creación de una dirección IP pública de nivel global

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

2. En los resultados de la búsqueda, escriba **Direcciones IP públicas**.

3. Seleccione **+ Create** (+ Crear).

4. En **Crear dirección IP pública**, escriba o seleccione la siguiente información:

    | Configuración                 | Value                       |
    | ---                     | ---                         |
    | Versión de la dirección IP              | Seleccione IPv4              |    
    | SKU                     | Seleccione **Estándar**.         |
    | Nivel                   | Seleccione **Global**.     |
    | Name                    | Escriba **myStandardPublicIP-Global**.          |
    | Asignación de dirección IP   | Se bloquea como **Estática**                |
    | Preferencia de enrutamiento     | Seleccione **Microsoft**. |
    | Tiempo de espera de inactividad (minutos)  | Deje el valor predeterminado **4**.        |
    | Etiqueta de nombre DNS          | Deje el valor en blanco.    |
    | Subscription            | Seleccione su suscripción.   |
    | Resource group          | Seleccione **Crear nuevo** y escriba **QuickStartCreateIP-rg**. </br> Seleccione **Aceptar**. |
    | Location                | Seleccione **(EE. UU.) Este de EE. UU. 2**.     |
    | Zona de disponibilidad       | Seleccione **Redundancia de zona**. |

5. Seleccione **Crear**.

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="Captura de pantalla de la configuración del nivel en Azure Portal" border="true":::

Puede asociar la dirección IP creada anteriormente a un equilibrador de carga entre regiones. Para más información, consulte [Tutorial: Creación de un equilibrador de carga entre regiones mediante Azure Portal](../../load-balancer/tutorial-cross-region-portal.md).

---

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine las direcciones IP públicas mediante los siguientes pasos:

1. Escriba **Grupo de recursos** en el cuadro de búsqueda que se encuentra en la parte superior del portal.

2. En los resultados de la búsqueda, seleccione **Grupos de recursos**.

3. Seleccione **QuickStartCreateIP-rg**.

4. Seleccione **Eliminar grupo de recursos**.

5. Escriba **myResourceGroup** para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente artículo para aprender a crear un prefijo de dirección IP pública:
> [!div class="nextstepaction"]
> [Creación de un prefijo de dirección IP pública mediante Azure Portal](create-public-ip-prefix-portal.md)
