---
title: Planificación de la implementación de Azure VMware Solution
description: Aprenda a planear la implementación de Azure VMware Solution.
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 07/07/2021
ms.openlocfilehash: 4f0f59a01ff53646a23809efa129a154bfba81ee
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444218"
---
# <a name="plan-the-azure-vmware-solution-deployment"></a>Planificación de la implementación de Azure VMware Solution

El planeamiento de la implementación de Azure VMware Solution es fundamental para contar con un entorno listo para producción adecuado para la creación de máquinas virtuales y la migración. Durante el proceso de planeamiento, identificará y recopilará los elementos necesarios para la implementación. A medida que avance en el planteamiento de la implementación, asegúrese de documentar la información que recopila para facilitar la referencia durante la implementación. Una buena implementación consigue un entorno listo para la producción donde se pueden crear máquinas virtuales y realizar migraciones.

En estos procedimientos, hará lo siguiente:

> [!div class="checklist"]
> * Identificar la suscripción, el grupo de recursos, la región y el nombre del recurso de Azure
> * Identificar los hosts de tamaño y determinar el número de clústeres y hosts
> * Solicitar una cuota de host para un plan de Azure apto
> * Identificar el segmento IP del CIDR /22 para la administración de la nube privada
> * Identificar un único segmento de red
> * Definición de la puerta de enlace de red virtual
> * Definición de segmentos de red de VMware HCX

Cuando haya terminado, siga los pasos recomendados al final para continuar con esta guía de introducción.


## <a name="identify-the-subscription"></a>Identificación de la suscripción

Identifique la suscripción que piensa usar para implementar Azure VMware Solution.  Puede crear una nueva suscripción o usar una existente.

>[!NOTE]
>La suscripción debe estar asociada a un Contrato Enterprise de Microsoft o a un plan de Azure de Proveedor de soluciones en la nube. Para más información, consulte [Criterios de idoneidad](request-host-quota-azure-vmware-solution.md#eligibility-criteria).

## <a name="identify-the-resource-group"></a>Identificación del grupo de recursos

Identifique el grupo de recursos que quiere usar para Azure VMware Solution.  Por lo general, se crea un grupo de recursos específicamente para Azure VMware Solution, pero puede usar un grupo de recursos existente.

## <a name="identify-the-region-or-location"></a>Identificación de la región o ubicación

Identifique la [región](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) en la que desea implementar Azure VMware Solution. 

## <a name="define-the-resource-name"></a>Definición del nombre del recurso

El nombre del recurso es un nombre descriptivo en el que se le da un título a la nube privada de Azure VMware Solution; por ejemplo, **MyPrivateCloud**.

>[!IMPORTANT]
>El nombre no puede tener más de 40 caracteres. Si el nombre supera este límite, no podrá crear direcciones IP públicas para utilizarlas con la nube privada. 

## <a name="identify-the-size-hosts"></a>Identificación de los hosts de tamaño

Identifique los hosts de tamaño que quiere usar al implementar Azure VMware Solution.  

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="determine-the-number-of-clusters-and-hosts"></a>Determinación del número de clústeres y hosts

La primera implementación de Azure VMware Solution que realice consta de una nube privada que contendrá un único clúster. Deberá definir el número de hosts que desea implementar en el primer clúster de su implementación.

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]


>[!NOTE]
>Para obtener información sobre los límites del número de hosts por clúster, el número de clústeres por nube privada y el número de hosts por nube privada, consulte [Límites, cuotas y restricciones de suscripción y servicio de Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-vmware-solution-limits).

## <a name="request-a-host-quota"></a>Solicitud de una cuota de host 

Es fundamental solicitar una cuota de host pronto; de este modo, una vez que haya terminado el proceso de planeamiento, estará listo para implementar la nube privada de Azure VMware Solution.
Antes de solicitar una cuota de host, asegúrese de que ha identificado la suscripción, el grupo de recursos y la región de Azure. Además, asegúrese de que ha identificado los hosts de tamaño y determine el número de clústeres y hosts que necesitará.

Cuando el equipo de soporte técnico reciba su solicitud, tardará un máximo de cinco días laborables en confirmarla y asignar los hosts.

- [Clientes de EA](request-host-quota-azure-vmware-solution.md#request-host-quota-for-ea-customers)
- [Clientes de CSP](request-host-quota-azure-vmware-solution.md#request-host-quota-for-csp-customers)


## <a name="define-the-ip-address-segment-for-private-cloud-management"></a>Definición del segmento de dirección IP para la administración de la nube privada

Azure VMware Solution requiere una red CIDR /22; por ejemplo, `10.0.0.0/22`. Este espacio de direcciones se divide en segmentos de red más pequeños (subredes) y se usa para los segmentos de administración de Azure VMware Solution, incluida la funcionalidad de vCenter, VMware HCX, NSX-T y vMotion. En el diagrama se resaltan los segmentos de direcciones IP de administración de Azure VMware Solution.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Diagrama que muestra los segmentos de direcciones IP de administración de Azure VMware Solution." border="false":::  

>[!IMPORTANT]
>Este bloque de direcciones de red CIDR /22 no debe superponerse con ningún segmento de red existente que ya tenga en el entorno local o en Azure. Para obtener más información sobre cómo se desglosa la red CIDR /22 por nube privada, consulte [Consideraciones sobre el enrutamiento y la subred](tutorial-network-checklist.md#routing-and-subnet-considerations).



## <a name="define-the-ip-address-segment-for-vm-workloads"></a>Definición del segmento de dirección IP para cargas de trabajo de máquina virtual

Al igual que en cualquier entorno de VMware, las máquinas virtuales deben conectarse a un segmento de red.  Cuando la implementación de producción de Azure VMware Solution se expande, a menudo hay una combinación de segmentos extendidos de nivel 2 de segmentos de red locales y de NSX-T. 

Para la implementación inicial, identifique un único segmento de red (red IP); por ejemplo, `10.0.4.0/24`. Este segmento de red se usa principalmente con para realizar pruebas durante la implementación inicial.  El bloque de direcciones no debe superponerse con ningún segmento de red en el entorno local ni dentro de Azure y no debe estar dentro del segmento de red /22 ya definido. 
  
:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identificación: segmento de dirección IP para cargas de trabajo de máquina virtual" border="false":::     


## <a name="define-the-virtual-network-gateway"></a>Definición de la puerta de enlace de red virtual

Azure VMware Solution requiere una instancia de Azure Virtual Network y un circuito ExpressRoute. Defina si quiere usar una puerta de enlace de red virtual de ExpressRoute *existente* o *nueva*. Si decide usar una puerta de enlace de red virtual *nueva*, podrá crearla después de crear la nube privada. Es posible usar una puerta de enlace de red virtual de ExpressRoute que ya existe; para el planeamiento, tome nota de la puerta de enlace de red virtual de ExpressRoute que va a usar. 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Diagrama en el que se muestra el Azure Virtual Network asociado a Azure VMware Solution" border="false":::

>[!IMPORTANT]
>Puede conectarse a una puerta de enlace de red virtual en un Azure Virtual WAN, pero está fuera del ámbito de este inicio rápido.

## <a name="define-vmware-hcx-network-segments"></a>Definición de segmentos de red de VMware HCX

VMware HCX es una plataforma de movilidad de aplicaciones que simplifica la migración de aplicaciones, el reequilibrio de cargas de trabajo y la continuidad empresarial entre centros de datos y nubes. Puede migrar las cargas de trabajo de VMware a Azure VMware Solution y a otros sitios conectados mediante varios tipos de migración. 

El conector de VMware HCX implementa un subconjunto de aplicaciones virtuales (automatizadas) que requieren varios segmentos IP. Al crear los perfiles de red, se usan los segmentos IP. Identifique lo siguiente para la implementación de VMware HCX, que admite un caso de uso piloto o de producto pequeño.  Modifique lo que sea necesario en función de las necesidades de la migración. 

- **Red de administración:** al implementar VMware HCX en un entorno local, deberá identificar una red de administración para VMware HCX.  Normalmente, es la misma red de administración que usa el clúster de VMware local.  Como mínimo, identifique **dos** direcciones IP en este segmento de red para VMware HCX. Es posible que necesite números mayores, en función de la escala de la implementación más allá del caso de uso pequeño o piloto.

  >[!NOTE]
  >La preparación de entornos grandes, en lugar de usar la red de administración usada para el clúster de VMware local, cree una red /26 y presente dicha red como un grupo de puertos a su clúster de VMware local.  Luego puede crear hasta 10 mallas de servicio y 60 controles extensores de red (-1 por malla de servicio). Se pueden expandir **ocho** redes por cada extensor de red mediante nubes privadas de soluciones de Azure VMware Solution.

- **Red de vínculo superior:** al implementar VMware HCX en un entorno local, deberá identificar una red de vínculo superior para VMware HCX. Use la misma red que va a usar para la red de administración. 

- **Red de vMotion:** al implementar VMware HCX en un entorno local, deberá identificar una red de vMotion para VMware HCX.  Normalmente, es la misma red que usa el clúster de VMware local para vMotion.  Como mínimo, identifique **dos** direcciones IP en este segmento de red para VMware HCX. Es posible que necesite números mayores, en función de la escala de la implementación más allá del caso de uso pequeño o piloto.

  Debe exponer la red de vMotion en un conmutador virtual distribuido o vSwitch0. Si no es así, modifique el entorno para adaptarlo.

  >[!NOTE]
  >Muchos entornos de VMware utilizan segmentos de red no enrutados para vMotion, que no plantea ningún problema.
  
- **Red de replicación:** al implementar VMware HCX local, deberá definir una red de replicación. Utilice la misma red que usa para las redes de administración y de vínculo superior.  Si los hosts del clúster local usan una red VMkernel de replicación dedicada, reserve **dos** direcciones IP en este segmento de red y use la red VMkernel de replicación para la red de replicación.


## <a name="determine-whether-to-extend-your-networks"></a>Determine si hay que extender las redes

De forma opcional, los segmentos de red se pueden extender del entorno local a Azure VMware Solution. Si extiende segmentos de red, identifique las redes ahora siguiendo estas pautas:

- Las redes deben conectarse a [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) en el entorno local de VMware.  
- Las redes que se encuentran [en vSphere Standard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) no se pueden extender.

>[!IMPORTANT]
>Estas redes se extienden como paso final de la configuración, no durante la implementación.


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha recopilado y documentado la información necesaria, continúe con el siguiente tutorial para crear la nube privada de Azure VMware Solution.

> [!div class="nextstepaction"]
> [Implementación de Azure VMware Solution](deploy-azure-vmware-solution.md)
