---
title: Conexión a máquinas virtuales de Azure tras la conmutación por error con Azure Site Recovery
description: Se describe cómo conectarse en Azure a las máquinas virtuales de Azure tras la conmutación por error desde un entorno local con Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: harshacs
ms.openlocfilehash: 2dfb7a185a4fac41059f5f987c4fa602abf4a894
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003682"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Conexión a las máquinas virtuales de Azure tras la conmutación por error desde un entorno local 


En este artículo se describe cómo configurar la conectividad para que pueda conectarse correctamente a las máquinas virtuales de Azure después de la conmutación por error.

Al configurar la recuperación ante desastres de las máquinas virtuales locales y los servidores físicos en Azure, [Azure Site Recovery](site-recovery-overview.md) comienza a replicarlas en Azure. Después, cuando se produzcan interrupciones, puede conmutar por error a Azure desde el sitio local. Cuando se produce la conmutación por error, Site Recovery crea máquinas virtuales de Azure con los datos locales replicados. Como parte del planeamiento de la recuperación ante desastres, debe averiguar cómo conectarse a las aplicaciones que se ejecutan en estas máquinas virtuales de Azure después de la conmutación por error.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Preparar las máquinas locales antes de la conmutación por error.
> * Preparar las máquinas virtuales de Azure después de la conmutación por error. 
> * Conservar las direcciones IP en las máquinas virtuales de Azure después de la conmutación por error.
> * Asignar direcciones IP nuevas a las máquinas virtuales de Azure después de la conmutación por error.

## <a name="prepare-on-premises-machines"></a>Preparación de las máquinas locales

Para garantizar la conectividad a las máquinas virtuales de Azure, prepare las máquinas locales antes de la conmutación por error.

### <a name="prepare-windows-machines"></a>Preparación de las máquinas Windows

En las máquinas Windows locales, realice lo siguiente:

1. Configure Windows. Esto incluye la eliminación de cualquier ruta estática o proxy WinHTTP persistente y la configuración de la directiva SAN de disco en **OnlineAll**. [Siga](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) estas instrucciones.

2. Asegúrese de que se están ejecutando [estos servicios](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services).

3. Habilite el protocolo de escritorio remoto (RDP) para permitir las conexiones remotas a la máquina local. [Aprenda](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) a habilitar el protocolo de escritorio remoto con PowerShell.

4. Para acceder a una máquina virtual de Azure a través de Internet tras la conmutación por error, en el firewall de Windows de la máquina local, permita TCP y UDP en el perfil público y establezca el protocolo de escritorio remoto como aplicación permitida para todos los perfiles.

5. Si quiere acceder a una máquina virtual de Azure mediante una conexión VPN de sitio a sitio tras la conmutación por error, en el firewall de Windows de la máquina local, permita el protocolo de escritorio remoto para los perfiles de dominio y privado. [Aprenda](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) cómo permitir el tráfico RDP.
6. Asegúrese de que no haya actualizaciones de Windows pendientes en la máquina virtual local al desencadenar una conmutación por error. Si es el caso, las actualizaciones podrían empezar a instalarse en la máquina virtual de Azure después de la conmutación por error y no podrá iniciar sesión en ella hasta que finalicen.

### <a name="prepare-linux-machines"></a>Preparación de las máquinas Linux

En las máquinas Linux locales, haga lo siguiente:

1. Asegúrese de que el servicio de Secure Shell está configurado para iniciarse automáticamente al arrancar el sistema.
2. Compruebe que las reglas de firewall permiten una conexión SSH.


## <a name="configure-azure-vms-after-failover"></a>Configuración de las máquinas virtuales de Azure después de la conmutación por error

Después de la conmutación por error, haga lo siguiente en las máquinas virtuales de Azure que se han creado.

1. Para conectarse a la máquina virtual desde Internet, asígnele una dirección IP pública. No puede usar la misma dirección IP pública para la máquina virtual de Azure que usó para la máquina local. [Más información](../virtual-network/ip-services/virtual-network-public-ip-address.md)
2. Compruebe que las reglas del grupo de seguridad de red (NSG) en la máquina virtual permiten las conexiones entrantes al puerto RDP o SSH.
3. Haga clic en [Diagnósticos de arranque](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine) para ver la máquina virtual.


> [!NOTE]
> El servicio Azure Bastion ofrece acceso de RDP y SSH privado a las máquinas virtuales de Azure. [Más información](../bastion/bastion-overview.md) sobre este servicio.

## <a name="set-a-public-ip-address"></a>Establecimiento de una dirección IP pública

Como alternativa a la asignación manual de una dirección IP pública a una máquina virtual de Azure, puede asignar la dirección durante la conmutación por error mediante un script o un runbook de Azure Automation en un [plan de recuperación](site-recovery-create-recovery-plans.md) de Site Recovery, o bien, puede configurar el enrutamiento de nivel de DNS mediante Azure Traffic Manager. [Más información](concepts-public-ip-address-with-site-recovery.md) sobre la configuración de una dirección pública.


## <a name="assign-an-internal-address"></a>Asignación de una dirección interna

Para establecer la dirección IP interna de una máquina virtual de Azure después de la conmutación por error, tiene un par de opciones:

- **Conservar la misma dirección IP**: puede usar la misma dirección IP en la máquina virtual de Azure que la asignada a la máquina local.
- **Usar una dirección IP distinta**: puede usar una dirección IP distinta para la máquina virtual de Azure.


## <a name="retain-ip-addresses"></a>Conservación de las direcciones IP

Site Recovery permite conservar las mismas direcciones IP al conmutar por error a Azure. Conservar la misma dirección IP evita posibles problemas de red después de la conmutación por error, pero presenta cierta complejidad.

- Si la máquina virtual de Azure de destino usa la misma dirección IP o subred que el sitio local, no se puede conectarlas entre sí mediante una conexión VPN de sitio a sitio o ExpressRoute a causa de la superposición de las direcciones. Las subredes deben ser únicas.
- Se necesita una conexión desde el entorno local a Azure después de la conmutación por error, de modo que las aplicaciones estén disponibles en las máquinas virtuales de Azure. Azure no admite las redes VLAN extendidas, por lo que si desea conservar las direcciones IP, debe tomar el espacio de IP en Azure mediante la conmutación por error de toda la subred, además conmutar por error la máquina local.
- La conmutación por error de la subred garantiza que una subred específica no esté disponible simultáneamente en el entorno local y en Azure.

La conservación de las direcciones IP requiere los pasos siguientes:

- En las propiedades de red y proceso del elemento replicado, configure la red y el direccionamiento IP de la máquina virtual de Azure de destino para que reflejen la configuración local.
- Las subredes deben administrarse como parte del proceso de recuperación ante desastres. Necesita una red virtual de Azure que coincida con la red local y, después de la conmutación por error, las rutas de red deben modificarse para que reflejen que la subred se ha migrado a Azure y la nueva ubicación de las direcciones IP.  

### <a name="failover-example"></a>Ejemplo de conmutación por error

Veamos un ejemplo.

- La empresa ficticia Woodgrove Bank hospeda sus aplicaciones empresariales en un entorno local y sus aplicaciones móviles en Azure.
- Se conectan desde el entorno local a Azure mediante VPN de sitio a sitio. 
- Woodgrove usa Site Recovery para replicar las máquinas locales en Azure.
- Sus aplicaciones locales usan direcciones IP codificadas de forma rígida, por lo que quieren conservar las mismas direcciones IP en Azure.
- En el entorno local, las máquinas que ejecutan las aplicaciones se ejecutan en tres subredes:
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- Sus aplicaciones que se ejecutan en Azure se encuentran en la red virtual de Azure **Azure Network** en dos subredes:
    - 172.16.1.0/24
    - 172.16.2.0/24

Para conservar las direcciones, esto es lo que hacen.

1. Cuando habilitan la replicación, especifican que las máquinas se deben replicar en la red **Azure Network**.
2. Crean **Recovery Network** en Azure. Esta red virtual refleja la subred 192.168.1.0/24 en su red local.
3. Woodgrove configura una [conexión de red virtual a red virtual](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) entre las dos redes virtuales. 

    > [!NOTE]
    > En función de los requisitos de la aplicación, se puede configurar una conexión de red virtual a red virtual antes de la conmutación por error, o como paso manual, con scripts o con un runbook de Azure Automation en un [plan de recuperación](site-recovery-create-recovery-plans.md) de Site Recovery, o después de que se complete la conmutación por error.

4. Antes de la conmutación por error, en las propiedades de la máquina en Site Recovery, se establece la dirección IP de destino en la dirección de la máquina local, tal y como se describe en el procedimiento siguiente.
5. Después de la conmutación por error, las máquinas virtuales de Azure se crean con la misma dirección IP. Woodgrove se conecta desde la **red de Azure**  a la red virtual de la **red de recuperación** mediante el emparejamiento de redes virtuales (con la conectividad de tránsito habilitada).
6. En el entorno local, Woodgrove debe realizar cambios en la red, incluida la modificación de las rutas para que reflejen que 192.168.1.0/24 se ha migrado a Azure.  

**Infraestructura antes de la conmutación por error**

![Antes de la conmutación por error de la subred](./media/site-recovery-network-design/network-design7.png)


**Infraestructura después de la conmutación por error**

![Después de la conmutación por error de la subred](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Configuración de la red de destino

Antes de la conmutación por error, especifique la configuración de red y la dirección IP de la máquina virtual de Azure de destino.

1.  En el almacén de Recovery Services ->**Elementos replicados**, seleccione la máquina local.
2. En la página **Proceso y red** de la máquina, haga clic en **Editar** para configurar las opciones de la red y del adaptador de la máquina virtual de Azure de destino.
3. En **Propiedades de red**, seleccione la red de destino en la que se ubicarán las máquinas virtuales de Azure replicadas tras la creación después de la conmutación por error.
4. En **Interfaces de red**, configure los adaptadores de red en la red de destino. De forma predeterminada Site Recovery muestra todas las NIC detectadas en la máquina local.
    - En **Tipo de interfaz de red de destino** puede establecer cada NIC como **Principal**, **Secundaria** o **No crear**, si no necesita esa NIC específica en la red de destino. Un adaptador de red debe establecerse como principal para la conmutación por error. Tenga en cuenta que la modificación de la red de destino afecta a todas las NIC de la máquina virtual de Azure.
    - Haga clic en el nombre de la NIC para especificar la subred en la que se implementará la máquina virtual de Azure.
    - Sobrescriba **Dinámico** con la dirección IP privada que quiere asignar a la máquina virtual de Azure de destino. Si no se especifica una dirección IP, Site Recovery asignará la siguiente dirección IP disponible de la subred a la NIC en la conmutación por error.
    - [Más información](site-recovery-manage-network-interfaces-on-premises-to-azure.md) sobre la administración de las NIC para la conmutación por error de máquinas locales a Azure.


## <a name="get-new-ip-addresses"></a>Obtención de direcciones IP nuevas

En este escenario, la máquina virtual de Azure obtiene una nueva dirección IP después de la conmutación por error. Para configurar una nueva dirección IP para la máquina virtual creada después de la conmutación por error, se puede hacer referencia a los siguientes pasos:

1. Vaya a **Elementos replicados**.
2. Seleccione la máquina virtual de Azure que desee.
3. Seleccione **Proceso y red**  y, después,**Editar**.

     ![Personalización de las configuraciones de red de conmutación por error](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Para actualizar la configuración de red de conmutación por error, seleccione **Editar** para la NIC que desee configurar. En la siguiente página que se abre, proporcione la dirección IP correspondiente creada previamente en la ubicación de conmutación por error de prueba y de conmutación por error.

    ![Edición de la configuración de la NIC](media/azure-to-azure-customize-networking/nic-drilldown.png)

5. Seleccione **Aceptar**.

Site Recovery ahora respetará esta configuración y garantizará que la máquina virtual de la conmutación por error esté conectada al recurso seleccionado a través de la dirección IP correspondiente, si está disponible en el intervalo de direcciones IP de destino. En este escenario, no es necesario realizar una conmutación por error de toda la subred. Se necesitará una actualización de DNS para actualizar los registros de la máquina que conmutó por error para que apunten a la nueva dirección IP de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
[Información](site-recovery-active-directory.md) acerca de la replicación de las instancias locales de Active Directory y DNS en Azure.