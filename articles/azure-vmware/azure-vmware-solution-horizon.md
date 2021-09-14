---
title: Implementación de Horizon en Azure VMware Solution
description: Aprenda a implementar VMware Horizon en Azure VMware Solution.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: d99c8dc76dcab1866d0c536be7fc505c2eec0cb6
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323645"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Implementación de Horizon en Azure VMware Solution 

>[!NOTE]
>Este documento se centra en el producto VMware Horizon, anteriormente conocido como Horizon 7. Horizon es una solución distinta a Horizon Cloud en Azure, aunque tienen algunos componentes compartidos. Las principales ventajas de Azure VMware Solution incluyen un método de ajuste de tamaño más directo y la integración de la administración de VMware Cloud Foundation en Azure Portal.

[VMware Horizon](https://www.vmware.com/products/horizon.html)®, una plataforma de escritorio virtual y aplicaciones, se ejecuta en el centro de datos y proporciona una administración sencilla y centralizada. Proporciona escritorios virtuales y aplicaciones en cualquier dispositivo y en cualquier lugar. Horizon permite crear y administrar conexiones a escritorios virtuales Windows y Linux, aplicaciones hospedadas de Servidor de Escritorio remoto (RDS), escritorios y máquinas físicas.

Este artículo se centra específicamente en la implementación de Horizon en Azure VMware Solution. Para obtener información general sobre VMware Horizon, vea la documentación de producción de Horizon:

* [Descripción de VMware Horizon](https://www.vmware.com/products/horizon.html)

* [Más información sobre VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Arquitectura de referencia de Horizon](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Con la incorporación de Horizon a Azure VMware Solution, ya hay dos soluciones de infraestructura de escritorio virtual (VDI) en la plataforma Azure. En el diagrama siguiente se resumen las principales diferencias generales.

:::image type="content" source="media/vmware-horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Diagrama que muestra las diferencias entre Horizon en Azure VMware Solution y Horizon Cloud en Azure." border="false":::

Horizon 2006 y las versiones posteriores de la línea de versiones de Horizon 8 admiten implementación local e implementación de Azure VMware Solution. Hay algunas características de Horizon que se admiten en local pero no en Azure VMware Solution. También se admiten otros productos del ecosistema de Horizon. Para obtener más información, vea la página sobre [interoperabilidad y paridad de características](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Implementación de Horizon en una nube híbrida

Es posible implementar Horizon en un entorno de nube híbrida mediante el uso de la arquitectura de pods en la nube (CPA) de Horizon para interconectar los centros de datos locales y de Azure. CPA escala verticalmente la implementación, crea una nube híbrida y proporciona redundancia para la continuidad empresarial y recuperación ante desastres.  Para más información, consulte [Expansión de los entornos de Horizon 7 existentes](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CPA no es una implementación extendida; cada pod de Horizon es distinto, y todos los servidores de conexión que pertenecen a cada uno de los pods individuales deben encontrarse en una única ubicación y ejecutarse en el mismo dominio de transmisión desde una perspectiva de red.

Al igual que un centro de datos local o privado, puede implementar Horizon en una nube privada de Azure VMware Solution. En las secciones siguientes se describen las diferencias principales entre la implementación de Horizon en el entorno local y en Azure VMware Solution.

La _nube privada de Azure_ es conceptualmente igual que el _SDDC de VMware_, un término que se usa habitualmente en la documentación de Horizon. En el resto de este documento se usan ambos términos indistintamente.

El conector de nube de Horizon es necesario para que Horizon en Azure VMware Solution administre licencias de suscripción. Puede implementar el conector de nube en Azure Virtual Network junto con servidores de conexión de Horizon.

>[!IMPORTANT]
>La compatibilidad del plano de control de Horizon con Horizon en Azure VMware Solution todavía no está disponible. Asegúrese de descargar la versión de VHD del conector de nube de Horizon.

## <a name="vcenter-cloud-admin-role"></a>Rol de Administrador de nube de vCenter

Dado que Azure VMware Solution es un servicio de SDDC y Azure administra el ciclo de vida del SDDC en Azure VMware Solution, el modelo de permisos de vCenter en Azure VMware Solution está limitado por diseño.

Los clientes deben usar el rol de Administrador de nube, que tiene un conjunto limitado de permisos de vCenter. El producto Horizon se ha modificado para funcionar con el rol de Administrador de nube en Azure VMware Solution, en concreto:

* El aprovisionamiento de clones instantáneo se ha modificado para ejecutarse en Azure VMware Solution.

* Se ha creado una directiva específica de vSAN (VMware_Horizon) en Azure VMware Solution para funcionar con Horizon, que debe estar disponible y usarse en los SDDC implementados para Horizon.

* Content-Based Read Cache (CBRC) de vSphere, también conocido como View Storage Accelerator, se deshabilita al ejecutar en Azure VMware Solution.

>[!IMPORTANT]
>CBRC no se debe volver a activar.

>[!NOTE]
>Azure VMware Solution configura automáticamente valores específicos de Horizon siempre que se implemente Horizon 2006 (es decir, Horizon 8) y superior en la rama de Horizon 8 y se seleccione la opción **Azure** en el instalador del servidor de conexión de Horizon.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Arquitectura de implementación de Horizon en Azure VMware Solution

Un diseño de arquitectura típico de Horizon usa una estrategia de pods y bloques. Un bloque es un solo vCenter, mientras que varios bloques combinados forman un pod. Un pod de Horizon es una unidad de organización determinada por los límites de escalabilidad de Horizon. Cada pod de Horizon tiene un portal de administración independiente y, por tanto, un procedimiento de diseño estándar es minimizar el número de pods.

Cada nube tiene su propio esquema de conectividad de red. En combinación con NSX Edge o redes de SDDC de VMware, la conectividad de red de Azure VMware Solution presenta requisitos únicos para implementar Horizon que difieren del entorno local.

Cada nube privada de Azure y SDDC pueden controlar 4000 sesiones de escritorio o de aplicación, suponiendo que:

* El tráfico de las cargas de trabajo se alinea con el perfil de trabajo de la tarea LoginVSI.

* Solo se tiene en cuenta el tráfico de protocolos, sin datos de usuario.

* NSX Edge se configura para un tamaño grande.

>[!NOTE]
>El perfil de las cargas de trabajo y las necesidades pueden diferir, por lo que los resultados pueden variar en función del caso de uso. Los volúmenes de datos de usuario pueden reducir los límites de escala en el contexto de la carga de trabajo. Ajuste el tamaño de la implementación y planéela en consecuencia. Para obtener más información, vea las instrucciones de ajuste de tamaño en la sección [Ajuste del tamaño de los hosts de Azure VMware Solution para implementaciones de Horizon](#size-azure-vmware-solution-hosts-for-horizon-deployments).

Dado el límite máximo de la nube privada de Azure y SDDC, se recomienda una arquitectura de implementación en la que los servidores de conexión de Horizon y las puertas de enlace de acceso unificado (UAG) de VMware se ejecuten dentro de Azure Virtual Network. En la práctica, esto convierte a cada nube privada de Azure y SDDC en un bloque. Como resultado, se maximiza la escalabilidad de Horizon en Azure VMware Solution.

La conexión desde Azure Virtual Network a las nubes privadas de Azure o SDDC debe configurarse con FastPath de ExpressRoute. En el diagrama siguiente se muestra una implementación de pods de Horizon básica.

:::image type="content" source="media/vmware-horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Diagrama que muestra la implementación típica de pods de Horizon mediante FastPath de ExpressRoute." border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Conectividad de red para escalar Horizon en Azure VMware Solution

En esta sección se presenta la arquitectura de red general con algunos ejemplos de implementación comunes para ayudarle a escalar Horizon en Azure VMware Solution. El contenido se centra específicamente en los elementos críticos de las redes. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Pod único de Horizon en Azure VMware Solution

:::image type="content" source="media/vmware-horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Diagrama que muestra un pod único de Horizon en Azure VMware Solution." border="false":::

El escenario de implementación más directo es un único pod de Horizon, porque implementa solo un pod de Horizon en la región Este de EE. UU.  Puesto que se estima que cada nube privada y SDDC pueden controlar 4000 sesiones de escritorio, se implementa el tamaño máximo del pod de Horizon.  Puede planear la implementación de hasta tres nubes privadas o SDDC.

Con las máquinas virtuales de infraestructura de Horizon implementadas en Azure Virtual Network, puede llegar hasta 12 000 sesiones por pod de Horizon. La conexión entre cada nube privada o SDDC y Azure Virtual Network es una ruta rápida de ExpressRoute.  No se necesita tráfico Este-Oeste entre las nubes privadas. 

Las principales suposiciones de este ejemplo de implementación básica son que:

* No se tiene un pod de Horizon local que se quiera conectar a este nuevo pod mediante la arquitectura de pods en la nube (CPA).

* Los usuarios finales se conectan a sus escritorios virtuales mediante Internet (en lugar de hacerlo mediante un centro de datos local).

Puede conectar el controlador de dominio de AD de Azure Virtual Network con la instancia local de AD mediante VPN o un circuito de ExpressRoute.

Una variación del ejemplo básico podría ser admitir la conectividad de los recursos locales. Por ejemplo, los usuarios acceden a los escritorios y generan tráfico de aplicaciones de escritorio virtual o se conectan a un pod de Horizon local mediante CPA.

En el diagrama se muestra cómo admitir la conectividad de los recursos locales. Para conectar la red corporativa a Azure Virtual Network, necesitará un circuito de ExpressRoute.  También necesitará conectar la red corporativa con cada una de las nubes privadas y los SDDC mediante ExpressRoute Global Reach.  Permite la conectividad desde el SDDC al circuito de ExpressRoute y los recursos locales. 

:::image type="content" source="media/vmware-horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Diagrama que muestra la conexión de una red corporativa a una instancia de Azure Virtual Network." border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Varios pods de Horizon en Azure VMware Solution en varias regiones

Otro escenario es el escalado de Horizon en varios pods.  En este escenario, se implementan dos pods de Horizon en dos regiones diferentes y se federan mediante CPA. Es similar a la configuración de red del ejemplo anterior, pero con algunos vínculos cruzados entre regiones adicionales. 

Conectará la instancia de Azure Virtual Network de cada región a las nubes privadas o los SDDC de la otra región. Esto permite que los servidores de conexión de Horizon que forman parte de la federación de CPA se conecten a todos los escritorios que administran. La incorporación de nubes privadas o SDDC adicionales a esta configuración le permitiría escalar hasta 24 000 sesiones en total. 

Se aplican los mismos principios si se implementan dos pods de Horizon en la misma región.  Asegúrese de implementar el segundo pod de Horizon en una instancia independiente de *Azure Virtual Network*. Al igual que en el ejemplo de un único pod, puede conectar la red corporativa y el pod local a este ejemplo de varios pods o regiones mediante ExpressRoute y Global Reach. 

:::image type="content" source="media/vmware-horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Diagrama que muestra varios pods de Horizon en Azure VMware Solution en varias regiones." border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Ajuste del tamaño de los hosts de Azure VMware Solution para implementaciones de Horizon 

La metodología de dimensionamiento de Horizon en un host que se ejecuta en Azure VMware Solution es más sencilla que en Horizon local.  Esto se debe a que el host de Azure VMware Solution está estandarizado.  El dimensionamiento exacto del host ayuda a determinar el número de hosts necesarios para admitir los requisitos de VDI.  Es fundamental para determinar el costo por escritorio.

### <a name="sizing-tables"></a>Tablas de dimensionamiento

Los requisitos específicos de vCPU/vRAM para los escritorios virtuales de Horizon dependen del perfil de carga de trabajo específico del cliente.   Trabaje con el equipo de ventas de MSFT y VMware para ayudar a determinar los requisitos de vCPU/vRAM para los escritorios virtuales. 

| vCPU per VM | vRAM per VM (GB) | Instancia | 100 VMs | 200 VMs | 300 VMs | 400 VMs | 500 VMs | 600 VMs | 700 VMs | 800 VMs | 900 VMs | 1000 VMs | 2000 VMs | 3000 VMs | 4000 VMs | 5000 VMs | 6000 VMs | 6400 VMs |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3,5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3,5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3,5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3,5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Entradas de ajuste de tamaño de Horizon

Esto es lo que necesitará recopilar para la carga de trabajo planeada:

* Número de escritorios simultáneos

* vCPU requerida por escritorio

* vRAM requerida por escritorio

* Almacenamiento requerido por escritorio

En general, las implementaciones de VDI están restringidas por CPU o RAM, lo que determina el tamaño del host. Veamos el siguiente ejemplo de un tipo de trabajador del conocimiento LoginVSI de carga de trabajo validado con pruebas de rendimiento:

* Implementación de 2000 escritorios simultáneos

* 2 vCPU por escritorio

* 4 GB de vRAM por escritorio.

* 50 GB de almacenamiento por escritorio

En este ejemplo, el número total de hosts asciende a 18, lo que resulta en una densidad de VM por host de 111.

>[!IMPORTANT]
>Las cargas de trabajo de cliente varían de este ejemplo de un trabajador del conocimiento LoginVSI. Como parte del plan de la implementación, trabaje con los SE de VMware EUC sobre sus necesidades específicas de tamaño y rendimiento. Asegúrese de ejecutar sus propias pruebas de rendimiento con la carga de trabajo real planeada antes de finalizar el ajuste de tamaño del host y ajuste en consecuencia.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Licencias de Horizon en Azure VMware Solution 

Hay cuatro componentes en los costos generales de ejecución de Horizon en Azure VMware Solution. 

### <a name="azure-vmware-solution-capacity-cost"></a>Costo de capacidad de Azure VMware Solution

Para obtener información sobre los precios, vea la página [Precios de Azure VMware Solution](https://azure.microsoft.com/pricing/details/azure-vmware/)

### <a name="horizon-licensing-cost"></a>Costo de licencias de Horizon

Hay dos licencias disponibles para su uso con Azure VMware Solution, Usuario simultáneo (CCU) o Usuario con nombre (NU):

* Licencia de suscripción de Horizon

* Licencia de suscripción Universal de Horizon

Si solo implementa Horizon en Azure VMware Solution para el futuro inmediato, use la licencia de suscripción de Horizon, ya que su costo es inferior.

Si se ha implementado en Azure VMware Solution y en el entorno local, elija la licencia de suscripción Universal de Horizon como caso de uso de recuperación ante desastres. Aunque esta licencia incluye una licencia de vSphere para la implementación local, por lo que tiene un costo mayor.

Trabaje con el equipo de ventas de VMware EUC para determinar el costo de la licencia de Horizon en función de sus necesidades.

### <a name="azure-instance-types"></a>Tipos de instancia de Azure

Para comprender los tamaños de máquina virtual de Azure necesarios para la infraestructura de Horizon, consulte las instrucciones de [instalación de Horizon en Azure VMware Solution](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution).

## <a name="references"></a>Referencias
[Requisitos del sistema de Horizon Agent para Linux](https://docs.vmware.com/en/VMware-Horizon/2012/linux-desktops-setup/GUID-E268BDBF-1D89-492B-8563-88936FD6607A.html)


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre VMware Horizon en Azure VMware Solution, lea el artículo de [preguntas frecuentes sobre VMware Horizon](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf).
