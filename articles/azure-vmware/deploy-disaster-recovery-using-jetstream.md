---
title: Implementación de la recuperación ante desastres usando JetStream DR (versión preliminar)
description: Obtenga información sobre cómo implementar JetStream DR en su nube privada de Azure VMware Solution y sus cargas de trabajo locales de VMware.
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: references_regions
ms.openlocfilehash: f43d2a8db4ef29588ad5260c583faab31bd01228
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279077"
---
# <a name="deploy-disaster-recovery-using-jetstream-dr-preview"></a>Implementación de la recuperación ante desastres usando JetStream DR (versión preliminar)

>[!IMPORTANT]
>JetStream DR de Azure VMware Solution (versión preliminar) se encuentran actualmente en versión preliminar pública.
>Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
>Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[JetStream DR](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) es una solución de recuperación ante desastres nativa de la nube diseñada para minimizar el tiempo de inactividad de las máquinas virtuales (VM) en caso de desastre. Las instancias de JetStream DR se implementan tanto en el sitio protegido como en el de recuperación. 

JetStream se basa en la protección continua de datos (CDP), mediante el [marco VMware VAIO](https://core.vmware.com/resource/vmware-vsphere-apis-io-filtering-vaio), que permite una pérdida de datos mínima o cercana a cero. JetStream DR proporciona el nivel de protección deseado para aplicaciones empresariales críticas. También permite la recuperación ante desastres rentable mediante el uso de recursos mínimos en el sitio de recuperación ante desastres y el uso de almacenamiento en la nube rentable, como [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/).

En este artículo, implementará JetStream DR en su nube privada de Azure VMware Solution y sus cargas de trabajo locales de VMware.

Para más información sobre JetStream DR, consulte:

- [Resumen de la solución JetStream](https://www.jetstreamsoft.com/2020/09/28/solution-brief-disaster-recovery-for-avs/)

- [JetStream DR en Azure Marketplace](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020)

- [Artículos de Knowledge Base de JetStream](https://www.jetstreamsoft.com/resources/knowledge-base/)


## <a name="core-components-of-the-jetstream-dr-solution"></a>Componentes principales de la solución JetStream DR

| Elementos | Descripción |
| --- | --- |
| **Aplicación virtual del servidor de administración (MSA) de JetStream**  | MSA habilita la configuración del día 0 y del día 2, como los sitios primarios, los dominios de protección y la recuperación de máquinas virtuales.  El administrador de la nube instala MSA en un nodo de vSphere. MSA implementa un complemento de vCenter que le permite administrar JetStream DR de forma nativa desde vCenter. MSA no controla los datos de replicación de máquinas virtuales protegidas.  | 
| **Aplicación virtual de JetStream DR (DRVA)**  | El dispositivo de máquina virtual basado en Linux recibe datos de replicación de máquinas virtuales protegidas del host ESXi de origen. Es responsable de almacenar los datos de replicación en el sitio de recuperación ante desastres, normalmente en un almacén de objetos como Azure Blob Storage. Según el número de máquinas virtuales protegidas y la cantidad de almacenamiento que se replicará, el administrador de nube privada puede crear una o varias instancias de DRVA.  | 
| **Componentes de host ESXi de JetStream (paquetes de filtro de E/S)**  | Software JetStream instalado en cada host ESXi configurado para JetStream DR. El controlador host intercepta la E/S de una VM de vSphere y envía los datos de replicación al DRVA.   | 
| **Dominio de protección de JetStream**  | Grupo lógico de máquinas virtuales que se protegerán conjuntamente con las mismas directivas y el mismo runbook. Los datos de todas las máquinas virtuales de un dominio de protección se almacenan en la misma instancia de contenedor de blobs de Azure. La misma instancia de DRVA controla la replicación en el almacenamiento remoto de recuperación ante desastres para todas las máquinas virtuales de un dominio de protección.   | 
| **Contenedores de Azure Blob Storage**  | Los datos replicados de las máquinas virtuales protegidas se almacenan en blobs de Azure. El software de JetStream crea una instancia de contenedor de blobs de Azure para cada dominio de protección de JetStream.    | 



## <a name="jetstream-scenarios-on-azure-vmware-solution"></a>Escenarios de JetStream en Azure VMware Solution
Puede usar JetStream DR con Azure VMware Solution para los dos escenarios siguientes:  

- VMware local para DR de Azure VMware Solution

- Azure VMware Solution a DR de Azure VMware Solution

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>Escenario 1: VMware local a DR de Azure VMware Solution

En este escenario, el sitio primario es el entorno de VMware local y el sitio de recuperación ante desastres es una nube privada de Azure VMware Solution.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="Diagrama en el que se muestra la implementación de JetStream del entorno local a una nube privada de Azure VMware Solution" border="false" lightbox="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png":::

### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>Escenario 2: Azure VMware Solution a DR de Azure VMware Solution

En este escenario, el sitio primario es una nube privada de Azure VMware Solution en una región de Azure. El sitio de recuperación ante desastres es una nube privada de Azure VMware Solution en otra región de Azure. 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Diagrama en el que se muestra la implementación de JetStream de una nube privada de Azure VMware Solution a otra nube privada" border="false" lightbox="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png":::


## <a name="prerequisites"></a>Requisitos previos


### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>Escenario 1: VMware local a DR de Azure VMware Solution 

- Nube privada de Azure VMware Solution implementada con un mínimo de tres nodos en la región de recuperación ante desastres de destino.  


   :::image type="content" source="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png" alt-text="Diagrama que muestra el primer requisito previo para la solución de recuperación ante desastres en Azure VMware Solution." lightbox="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png":::


- Conectividad de red configurada entre los dispositivos de JetStream del sitio primario y la instancia de Azure Storage Blob. 

- [Configure JetStream DR y suscríbase](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) desde Azure Marketplace descargar el software de JetStream DR.

- Una [cuenta de Azure Blob Storage](../storage/common/storage-account-create.md) creada mediante el nivel de rendimiento Estándar o Premium. En [Nivel de acceso, seleccione **Acceso rápido**](../storage/blobs/access-tiers-overview.md). 

   >[!NOTE]
   >No se admite la opción **Habilitar espacio de nombres jerárquico** en el blob.   

- Un segmento de red NSX-T configurado en la nube privada de Azure VMware Solution y, opcionalmente, la habilitación de DHCP en el segmento para las aplicaciones virtuales de JetStream.  

- Un servidor DNS configurado para resolver las direcciones IP de Azure VMware Solution vCenter, los hosts ESXi de Azure VMware Solution, la cuenta de Azure Storage y el servicio Marketplace de JetStream para las aplicaciones virtuales de JetStream. 



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>Escenario 2: Azure VMware Solution a DR de Azure VMware Solution

- La nube privada de Azure VMware Solution implementada con un mínimo de tres nodos en las regiones primaria y secundaria.  
 
- Conectividad de red configurada entre los dispositivos de JetStream del sitio primario y la instancia de Azure Storage Blob. 

- [Configure JetStream DR y suscríbase](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) desde Azure Marketplace descargar el software de JetStream DR.

- Una [cuenta de Azure Blob Storage](../storage/common/storage-account-create.md) creada mediante el nivel de rendimiento Estándar o Premium. En [Nivel de acceso, seleccione **Acceso rápido**](../storage/blobs/access-tiers-overview.md). 

   >[!NOTE]
   >No se admite la opción **Habilitar espacio de nombres jerárquico** en el blob.   

- Un segmento de red NSX-T configurado en la nube privada de Azure VMware Solution y, opcionalmente, la habilitación de DHCP en el segmento para las aplicaciones virtuales de JetStream.  

- Un servidor DNS configurado tanto en el sitio primario como en el sitio de recuperación ante desastres para resolver las direcciones IP de Azure VMware Solution vCenter, los hosts ESXi de Azure VMware Solution, la cuenta de Azure Storage y el servicio Marketplace de JetStream para las aplicaciones virtuales de JetStream. 


Para obtener más requisitos previos de JetStream DR local, consulte la [Guía de preinstalación de JetStream](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/pre-installation-guidelines/).





## <a name="install-jetstream-dr-on-azure-vmware-solution"></a>Instalación de JetStream DR en Azure VMware Solution  
 
Puede seguir estos pasos para ambos escenarios admitidos. 
 
1. En el centro de datos local, siga la [documentación de JetStream](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/installing-jetstream-dr-software/) para instalar JetStream DR.  

1. En la nube privada de Azure VMware Solution, instale JetStream DR mediante un comando Ejecutar. En [Azure Portal](https://portal.azure.com), seleccione **Comando Ejecutar** > **Paquetes** > **JSDR.Configuration**.  

   :::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="Captura de pantalla en la que se muestra cómo acceder a los comandos de ejecución disponibles de JetStream" lightbox="media/run-command/run-command-overview-jetstream.png":::
 
   >[!NOTE]
   >El usuario CloudAdmin predeterminado de Azure VMware Solution no tiene privilegios suficientes para instalar JetStream DR.  Azure VMware Solution permite una instalación simplificada y automatizada de JetStream DR invocando el comando Ejecutar de Azure VMware Solution para JetStream DR.  
 

1. Ejecute el cmdlet **Invoke-PreflightJetDRInstall**, que comprueba si se han cumplido los requisitos previos para instalar JetStream DR. Por ejemplo, valida el número necesario de hosts, nombres de clúster y nombres de máquina virtual únicos. 

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **Network**  | Nombre del segmento de red NSX-T donde debe implementar la MSA de JetStream.  |
   | **Almacén de datos**  | Nombre del almacén de datos donde implementará la MSA.  |
   | **ProtectedCluster** | Nombre del clúster de nube privada de Azure VMware Solution que se va a proteger, por ejemplo, **Cluster-1**.  Solo puede proporcionar un nombre de clúster. |
   | **Clúster** | Nombre del clúster privado de Azure VMware Solution donde se implementa la MSA de JetStream, por ejemplo, **Cluster-1**. |
   | **VMName** | Nombre de la máquina virtual de MSA de JetStream, por ejemplo, **jetstreamServer**. |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico de la ejecución, por ejemplo, **Invoke-PreflightJetDRInstall-Exec1**. Se usa para comprobar si el cmdlet se ejecutó correctamente. |
   | **Tiempo de espera**  | Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. [Vea el estado de la ejecución](concepts-run-command.md#view-the-status-of-an-execution).
   

## <a name="install-the-jetstream-dr-msa"></a>Instalación de la MSA de JetStream DR

Azure VMware Solution admite la instalación de JetStream mediante direcciones IP estáticas o mediante direcciones IP basadas en DHCP.  

### <a name="static-ip-address"></a>Dirección IP estática

1. Seleccione **Comando Ejecutar** > **Paquetes** > **Install-JetDRWithStaticIP**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.


   | **Campo** | **Valor** |
   | --- | --- |
   | **ProtectedCluster** | Nombre del clúster de nube privada de Azure VMware Solution que se va a proteger, por ejemplo, **Cluster-1**.  Solo puede proporcionar un nombre de clúster durante la instalación. |
   | **Almacén de datos**  | Nombre del almacén de datos donde implementará la MSA de JetStream.  |
   | **VMName** | Nombre de la máquina virtual de MSA de JetStream, por ejemplo, **jetstreamServer**. |
   | **Clúster** | Nombre del clúster privado de Azure VMware Solution donde se implementa la MSA de JetStream, por ejemplo, **Cluster-1**. |
   | **Máscara de red** | Máscara de red de la MSA que se va a implementar, por ejemplo, **22** o **24**. |
   | **MSIp** | Dirección IP de la máquina virtual de MSA de JetStream.   |
   | **Dns** | Dirección IP de DNS que debe usar la máquina virtual de MSA de JetStream.   |
   | **Puerta de enlace** | Dirección IP de la puerta de enlace de red para la máquina virtual de MSA de JetStream.  |
   | **Credential:**  |  Credenciales del usuario raíz de la máquina virtual de MSA de JetStream.   |
   | **HostName** | Nombre de host (FQDN) de la máquina virtual de MSA de JetStream.  |
   | **Network**  | Nombre del segmento de red NSX-T donde debe implementar la MSA de JetStream. |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico de la ejecución, por ejemplo, **Install-JetDRWithStaticIP-Exec1**.   Se usa para comprobar si el cmdlet se ejecutó correctamente.  |


1. [Vea el estado de la ejecución](concepts-run-command.md#view-the-status-of-an-execution).


### <a name="dhcp-based-ip-address"></a>Dirección IP basada en DHCP

Este paso también instala el conjunto de instalación de vSphere (VIB) de JetStream en los clústeres que necesitan protección de recuperación ante desastres. 

1. Seleccione **Comando Ejecutar** > **Paquetes** > **Install-JetDRWithDHCP**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **ProtectedCluster** | Nombre del clúster de nube privada de Azure VMware Solution que se va a proteger, por ejemplo, **Cluster-1**.  Solo puede proporcionar un nombre de clúster durante la instalación. |
   | **Almacén de datos**  | Nombre del almacén de datos donde implementará la MSA de JetStream.  |
   | **VMName** | Nombre de la máquina virtual de MSA de JetStream, por ejemplo, **jetstreamServer**. |
   | **Clúster** | Nombre del clúster privado de Azure VMware Solution donde se implementa la MSA de JetStream, por ejemplo, **Cluster-1**. |
   | **Credential:**  |  Credenciales del usuario raíz de la máquina virtual de MSA de JetStream.   |
   | **HostName** | Nombre de host (FQDN) de la máquina virtual de MSA de JetStream.  |
   | **Network**  | Nombre del segmento de red NSX-T donde debe implementar la MSA de JetStream. |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico de la ejecución, por ejemplo, **Install-JetDRWithDHCP-Exec1**.   Se usa para comprobar si el cmdlet se ejecutó correctamente.  |
 
 
1. [Vea el estado de la ejecución](concepts-run-command.md#view-the-status-of-an-execution).
 
 
## <a name="add-jetstream-dr-to-new-azure-vmware-solution-clusters"></a>Adición de JetStream DR a nuevos clústeres de Azure VMware Solution  


1. Seleccione **Comando Ejecutar** > **Paquetes** > **Enable-JetDRForCluster**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **ProtectedCluster** | Nombre del clúster de nube privada de Azure VMware Solution que se va a proteger, por ejemplo, **Cluster-1**.  Solo puede proporcionar un nombre de clúster durante la instalación. |
   | **Credential:**  |  Credenciales del usuario raíz de la máquina virtual de MSA de JetStream.   |
   | **MSIp** | Dirección IP de la máquina virtual de MSA de JetStream.   |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico de la ejecución, por ejemplo, **Enable-JetDRForCluster-Exec1**.   Se usa para comprobar si el cmdlet se ejecutó correctamente.  |
  
1. [Vea el estado de la ejecución](concepts-run-command.md#view-the-status-of-an-execution).
 
 
 

## <a name="configure-jetstream-dr"></a>Configuración de JetStream DR 
 
En esta sección solo se describe una introducción a los pasos necesarios para configurar JetStream DR.  Para obtener descripciones detalladas y pasos, consulte la documentación de [Configuración de JetStream DR](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/configuring-jetstream-dr/). 
 
Una vez que MSA de JetStream DR y VIB de JetStream están instalados en los clústeres de Azure VMware Solution, use el portal de JetStream para completar los pasos de configuración restantes. 



1. Acceda al portal de JetStream desde el dispositivo vCenter.

1. [Agregue un sitio de almacenamiento externo](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/add-a-storage-site/).  

1. [Implemente un dispositivo DRVA de JetStream](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/deploy-a-dr-virtual-appliance/). 

1. [Cree un volumen de almacén de registro de replicación de JetStream](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-replication-log-store-volume/) mediante uno de los almacenes de datos disponibles para el clúster de Azure VMware Solution. 

   >[!TIP]
   >El almacenamiento local rápido, como el almacén de datos vSAN, es preferible para el registro de replicación. 
 
1. [Cree un dominio protegido de JetStream](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-protected-domain/). Proporcionará el sitio de Azure Blob Storage, la instancia de DRVA de JetStream y el registro de replicación creados en los pasos anteriores. 

1. [Seleccione las máquinas virtuales](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/select-vms-for-protection/) que desea proteger y, a continuación, [inicie la protección de máquinas virtuales](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/start-vm-protection/).

 
Para ver los pasos de configuración restantes de JetStream DR, como la creación de un runbook de conmutación por error, la invocación de la conmutación por error al sitio de recuperación ante desastres y la invocación de la conmutación por recuperación al sitio primario, consulte la [documentación de la Guía de administración de JetStream](https://www.jetstreamsoft.com/portal/jetstream-article-categories/product-manual/).  
 
## <a name="disable-jetstream-dr-on-an-azure-vmware-solution-cluster"></a>Deshabilitación de JetStream DR en un clúster de Azure VMware Solution  
 
Este cmdlet deshabilita JetStream DR solo en uno de los clústeres y no desinstala completamente JetStream DR. 

1. Seleccione **Comando Ejecutar** > **Paquetes** > **Disable-JetDRForCluster**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **ProtectedCluster** | Nombre del clúster de nube privada de Azure VMware Solution que se va a proteger, por ejemplo, **Cluster-1**.  Solo puede proporcionar un nombre de clúster durante la instalación. |
   | **Credential:**  |  Credenciales del usuario raíz de la máquina virtual de MSA de JetStream.   |
   | **MSIp** | Dirección IP de la máquina virtual de MSA de JetStream.   |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico de la ejecución, por ejemplo, **Disable-JetDRForCluster-Exec1**.   Se usa para comprobar si el cmdlet se ejecutó correctamente.  |

1. [Vea el estado de la ejecución](concepts-run-command.md#view-the-status-of-an-execution).


 
## <a name="uninstall-jetstream-dr"></a>Desinstalación de JetStream DR  

1. Seleccione **Comando de ejecución** > **Paquetes** > **Invoke-PreflightJetDRUninstall**. Este cmdlet comprueba si el clúster tiene al menos cuatro hosts (mínimo necesario). 

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **ProtectedCluster** | Nombre del clúster de nube privada de Azure VMware Solution que se va a proteger, por ejemplo, **Cluster-1**.  Solo puede proporcionar un nombre de clúster durante la instalación. |
   | **Credential:**  |  Credenciales del usuario raíz de la máquina virtual de MSA de JetStream.   |
   | **MSIp** | Dirección IP de la máquina virtual de MSA de JetStream.   |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico de la ejecución, por ejemplo, **Invoke-PreflightJetDRUninstall-Exec1**.   Se usa para comprobar si el cmdlet se ejecutó correctamente.  |

1. [Vea el estado de la ejecución](concepts-run-command.md#view-the-status-of-an-execution).

1. Una vez que el cmdlet de nivel previo se complete correctamente, seleccione **Uninstall-JetDR**, proporcione los valores necesarios o cambie los valores predeterminados y seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **ProtectedCluster** | Nombre del clúster de nube privada de Azure VMware Solution que se va a proteger, por ejemplo, **Cluster-1**.  Solo puede proporcionar un nombre de clúster durante la instalación. |
   | **Credential:**  |  Credenciales del usuario raíz de la máquina virtual de MSA de JetStream.   |
   | **MSIp** | Dirección IP de la máquina virtual de MSA de JetStream.   |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico de la ejecución, por ejemplo, **Uninstall-JetDR-Exec1**.   Se usa para comprobar si el cmdlet se ejecutó correctamente.  |

 1. [Vea el estado de la ejecución](concepts-run-command.md#view-the-status-of-an-execution).


## <a name="region-availability"></a>Disponibilidad en regiones

JetStream DR para Azure VMware Solution está disponible en las siguientes regiones: 

- Este de EE. UU. 

- Norte de Europa 

- Sur de EE. UU. 

- Oeste de Europa 

- Centro de EE. UU. 

- Oeste de EE. UU. 

- Este de Asia 

- Japón Oriental 

- Sur de Brasil 

- Este de Canadá 

- Oeste de Reino Unido 


 
## <a name="support"></a>Soporte técnico  
 
JetStream DR es una solución compatible con el [software de JetStream](https://www.jetstreamsoft.com/). Para cualquier problema de producto o soporte técnico con JetStream, póngase en contacto con support-avs@jetstreamsoft.com.  
 
Azure VMware Solution usa el comando Ejecutar (versión preliminar) para automatizar la instalación y desinstalación de JetStream DR. Póngase en contacto con el soporte técnico de Microsoft para cualquier problema con los comandos de ejecución. Si tiene problemas con los cmdlets de instalación y desinstalación de JetStream, póngase en contacto con JetStream para obtener soporte técnico. 



## <a name="next-steps"></a>Pasos siguientes

- [Configuración de la infraestructura: JetStream DR para Azure VMware Solution](https://vimeo.com/480574312/b5386a871c) 

- [JetStream DR para Azure VMware Solution (demostración completa)](https://vimeo.com/475620858/2ce9413248)
   
   - [Introducción a JetStream DR para Azure VMware Solution](https://vimeo.com/491880696/ec509ff8e3)

   - [Configuración y protección de máquinas virtuales](https://vimeo.com/491881616/d887590fb2)

   - [Conmutación por error a Azure VMware Solution](https://vimeo.com/491883564/ca9fc57092)

   - [Conmutación por recuperación al entorno local](https://vimeo.com/491884402/65ee817b60)

