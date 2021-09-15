---
title: Implementación de la recuperación ante desastres usando JetStream
description: Obtenga información sobre cómo implementar JetStream Disaster Recovery (DR) en su nube privada de Azure VMware Solution y sus cargas de trabajo locales de VMware.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: a26cbaa4f8c37f3148a86005a637b006572685dc
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315131"
---
# <a name="deploy-disaster-recovery-using-jetstream"></a>Implementación de la recuperación ante desastres usando JetStream

[JetStream Disaster Recovery (DR)](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) se instala en un entorno de VMware vSphere y se administra a través de un dispositivo de complemento vCenter. Proporciona protección continua de datos (CDP) nativa de la nube, que replica constantemente las operaciones de E/S de máquinas virtuales (VM). En lugar de capturar instantáneas a intervalos regulares, captura y replica continuamente los datos a medida que se escriben en el almacenamiento principal, con un efecto mínimo en las aplicaciones en ejecución.  Como resultado, permite recuperar rápidamente máquinas virtuales y sus datos, y alcanzar un objetivo de punto de recuperación (RPO) inferior.  

Con Azure VMware Solution, puede almacenar datos directamente en un clúster de recuperación en vSAN o en un sistema de archivos conectado, como Azure NetApp Files. Los datos se capturan mediante filtros de E/S que se ejecutan en vSphere. El almacén de datos subyacente puede ser VMFS, vSAN, vVol o cualquier plataforma HCI. 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-disaster-recovery-diagram.png" alt-text="Diagrama en el que se muestra la integración de JetStream DR y Azure VMware Solution" border="false":::

El software JetStream DR consta de tres componentes principales: 

- Management Server Virtual Appliance (MSA) se instala y configura antes de la protección de recuperación ante desastres. 

- DR Virtual Appliance (DRVA) es una imagen ISO que JetStream DR MSA implementa automáticamente.

- Componentes de host (paquetes de filtro de E/S). 

MSA se usa para instalar y configurar componentes de host en el clúster de proceso y, a continuación, para administrar el software JetStream DR. DRVA ejecuta los componentes de recuperación ante desastres de la ruta de acceso de datos. Se pueden ejecutar varias instancias de DRVA simultáneamente para mejorar la escalabilidad. Cada instancia de DRVA tiene una o más particiones dedicadas conectadas como LUN iSCSI o como VDISK de baja latencia. Las particiones se usan para mantener registros de replicación y repositorios de metadatos persistentes.

En este artículo aprenderá a usar JetStream DR y a implementarlo en su nube privada de Azure VMware Solution y sus cargas de trabajo locales de VMware.


## <a name="supported-scenarios"></a>Escenarios admitidos
Según los servicios de protección necesarios y el tipo de nube privada protegida, puede implementar JetStream DR de dos maneras:

- Del entorno local a una nube

- De nube a nube

### <a name="on-premises-to-cloud-deployment"></a>Implementación del entorno local a una nube
Las máquinas virtuales que se ejecutan en un centro de datos basado en VMware de una organización se replican continuamente en Microsoft Azure. Como resultado, las máquinas virtuales pueden reanudar su funcionamiento en Azure VMware Solution si hay un incidente en el centro de datos local. Mientras las máquinas virtuales se ejecutan en el entorno de recuperación, siguen replicando datos para ofrecer una protección continua. Una vez restaurado el centro de datos local, las máquinas virtuales y sus datos (incluidos los nuevos datos generados por las máquinas en el entorno de recuperación) pueden volver al centro de datos original sin interrupción.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="Diagrama en el que se muestra la implementación de JetStream del entorno local a una nube privada de Azure VMware Solution" border="false":::

### <a name="cloud-to-cloud-deployment"></a>Implementación de nube a nube

En esta configuración, Azure VMware Solution hospeda su entorno principal en un solo centro de datos. Protege las máquinas virtuales y los datos replicando continuamente en otra nube privada de otro de sus centros de datos. Si se produce un incidente, las máquinas virtuales y los datos se recuperan en el segundo centro de datos. Esta protección puede ser bidireccional, con el centro de datos "A" protegiendo al "B" y viceversa.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Diagrama en el que se muestra la implementación de JetStream de una nube privada de Azure VMware Solution a otra nube privada" border="false":::


## <a name="prerequisites"></a>Requisitos previos

- Nube privada de Azure VMware Solution implementada como región secundaria. 

- Jumpbox de Ubuntu Linux con una conexión ExpressRoute a la nube privada de Azure VMware Solution. 

- Versión más reciente de PowerShell instalada en el jumpbox de Linux.

- Versión más reciente de un módulo de la galería de PowerShell instalada.

### <a name="protected-site"></a>Sitio protegido

El sitio protegido hospeda un **clúster de servicios** para servicios administrativos, como vCenter, DNS o Active Directory, y **clústeres de proceso** donde se ejecutan aplicaciones de línea de negocio protegidas. El sitio protegido puede encontrarse en el entorno local u hospedarse en Azure VMware Solution.

Se puede usar cualquiera de los siguientes tipos:

- Azure Blob Storage

- vSAN de Azure VMware Solution

- Sistema de archivos conectado a Azure VMware Solution, como Azure NetApp Files


| **Elemento**  | **Descripción**  |  
| --- | --- |  
| **vCenter Server**  | <ul><li>Versión admitida: 6.7.</li><li>Puerto HTTPS: si se usa un firewall, el puerto HTTPS 443 debe estar abierto.</li><li>Conectividad: el FQDN de JetStream DR Management Server Appliance debe estar accesible desde vCenter. De lo contrario, se producirá un error en la instalación del complemento.</li><li>Hora: los relojes de vCenter y de JetStream DR MSA deben estar sincronizados.</li></ul>  |
| **Distributed Resource Scheduler (DRS)**  | Se recomienda en el clúster de proceso para equilibrar los recursos.  |
| **Clúster**  | Hosts de vSphere: las máquinas virtuales que van a protegerse mediante JetStream DR deben formar parte de un clúster.  |
| **Host de vSphere**  | <ul><li>Versión admitida: 6.7U1 (compilación 10302608) o posterior.</li><li>Conectividad: el FQDN de vCenter Server debe estar accesible desde el host. De lo contrario, se producirá un error en la configuración del host.</li><li>Hora: los relojes de los hosts de vSphere y de JetStream DR MSA deben estar sincronizados.</li><li>Servicio CIM: el servidor CIM debe estar habilitado (valor predeterminado).</li></ul>  |
| **JetStream DR MSA**  | <ul><li>CPU: 64 bits y 4 vCPU.</li><li>Memoria: 4 GB</li><li>Espacio en disco: 60 GB.</li><li>Red: se pueden usar direcciones IP estáticas o asignadas dinámicamente (DHCP). El FQDN debe estar registrado en DNS.</li><li>DNS: resolución de nombres DNS para hosts de vSphere y vCenter Server.</li></ul>  |
| **JetStream DRVA**  | <ul><li>CPU: 4 núcleos.</li><li>Memoria: 8 GB</li><li>Red: se pueden usar direcciones IP estáticas o asignadas dinámicamente (DHCP).</li></ul>  |
| **Almacén de registros de replicación**  | El sitio protegido debe exponer un dispositivo de almacenamiento flash de baja latencia que los hosts comparten en el clúster para obtener un rendimiento óptimo. Este dispositivo se puede controlar mediante el software JetStream DR, o bien puede proporcionarlo un tercero. Se usa como repositorio para el registro de replicación. Los hosts de DRVA y ESXi deben tener acceso directo a este almacenamiento a través de iSCSI.  |
| **Puertos**  | Cuando se instala el software JetStream DR, se abre automáticamente un intervalo de puertos en los hosts de ESXi de origen. Por lo tanto, para la mayoría de los usuarios, no es necesario realizar más acciones. Sin embargo, en aquellos casos en los que la configuración local o de origen tenga reglas de firewall especiales que bloqueen estos puertos, deberá abrirlos manualmente.<br /><br />Intervalo de puertos: 32873-32878.  |



### <a name="recovery-site"></a>Sitio de recuperación

Se establece un clúster de _luz piloto_ de Azure VMware Solution para la recuperación de la conmutación por error. Aunque el sitio de recuperación se crea como parte del proceso de instalación, su clúster no se rellena por completo ni se usa durante el funcionamiento normal. Los clústeres de proceso con conmutación por error se agregan al sitio de recuperación a petición, en respuesta a un evento de desastre.

### <a name="network"></a>Red

Se debe establecer una red con las siguientes características entre el sitio protegido y de recuperación:

| **Elementos** | **Descripción** |
| --- | --- |
| **JetStream DR MSA**  | Es necesaria una red de administración para MSA. Esta red se usa para acceder a las API de RESTful de JetStream DR y realizar otras llamadas de ruta de acceso a datos. Si hay una red privada disponible para conectarse al almacén de objetos, esta debe agregarse a la máquina virtual de MSA como red independiente. Si no hay ninguna red privada disponible, asegúrese de que la red de administración se pueda usar para conectarse al almacén de objetos. <br /><br />Se puede usar una red externa dedicada para acceder al almacén de objetos; en los demás casos, el tráfico de datos se envía a través de la red de administración.  | 
| **JetStream DRVA**  | Si la única red usada es la de administración, asegúrese de que tenga acceso al filtro de E/S y al almacén de objetos. Si existen varias redes dentro del clúster, todas deben estar conectadas a las máquinas virtuales de DRVA.  | 
| **Recuperación desde Object Cloud Virtual Appliance (RocVA)**  | Si la única red usada es la de administración, asegúrese de que tenga acceso a los hosts de ESXi y al almacén de objetos. Si existen varias redes dentro del clúster, todas deben estar conectadas a la máquina virtual de RocVA. RocVA es una máquina virtual temporal que se crea automáticamente cuando es necesario para la recuperación de la máquina virtual y se elimina cuando deja de serlo.  | 
| **Almacén de objetos/Blob Storage**  | El almacén de objetos o Blob Storage deben estar accesibles tanto para el sitio protegido como para el de recuperación.  | 
| **Almacén de registros de replicación**  | Los hosts de DRVA y ESXi deben tener acceso directo a este almacenamiento a través de iSCSI.  | 


## <a name="install-jetstream-dr"></a>Instalación de JetStream DR

JetStream DR se puede instalar a través de la funcionalidad de comandos de ejecución en el portal de Azure VMware Solution. La instalación se completa en tres pasos.

:::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="Captura de pantalla en la que se muestra cómo acceder a los comandos de ejecución disponibles de JetStream" lightbox="media/run-command/run-command-overview-jetstream.png":::


>[!NOTE]
>Los comandos Ejecutar se ejecutan de uno en uno en el orden enviado.

### <a name="check-the-current-state-of-the-system"></a>Comprobación del estado actual del sistema

Ejecute el cmdlet `Invoke-PreflightJetDRSystemCheck` para comprobar el estado del sistema y si se cumplen los requisitos del script. También se comprueba la configuración de vCenter necesaria para ejecutar otros cmdlets.  

El cmdlet comprueba lo siguiente:

- PowerShell
- FQDN de vCenter
- Rol CloudAdmin
- Módulos de VMware

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Comando de ejecución** > **Paquetes** > **Invoke-PreflightJetDRSystemCheck**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **Conservar hasta**  | Período de retención de la salida del cmdlet. El valor predeterminado es 60.  |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **changeVMStoragePolicy**.  |
   | **Tiempo de espera**  | Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Compruebe **Notificaciones** para ver el progreso. 



### <a name="check-cluster-configuration"></a>Comprobación de la configuración de clúster

Ejecute el cmdlet `Invoke-PreflightJetDRInstall` para comprobar la siguiente configuración de clúster:

- Los detalles del clúster son correctos. 
- Tiene al menos cuatro hosts.
- Hay una máquina virtual con el mismo nombre proporcionado para instalar MSA.
- Hay un complemento **jetdr** presente en vCenter.


1. Seleccione **Comando de ejecución** > **Paquetes** > **Invoke-PreflightJetDRInstall**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **VMName** | Nombre de la máquina virtual de MSA. Por ejemplo, **jetstreamServer**. |
   | **Clúster** | Nombre del clúster donde se va a implementar MSA. Por ejemplo, **Cluster-1**. |
   | **ProtectedCluster** | Clúster que se va a proteger. Por ejemplo, **Cluster-1**. |
   | **Conservar hasta**  | Período de retención de la salida del cmdlet. El valor predeterminado es 60.  |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **check_jetserverdetails**.  |
   | **Tiempo de espera**  | Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Compruebe **Notificaciones** para ver el progreso.  

1. Si se notifican errores, puede ir al siguiente paso para implementar JetDR MSA.


### <a name="deploy-jetdr-msa"></a>Implementación de JetDR MSA

Ejecute el cmdlet `Install-JetDR` para implementar JetDR MSA, registrar vCenter en JetDR MSA y configurar clústeres. La implementación descarga el conjunto de JetDR de Microsoft Server Media (MMS) y crea un usuario con privilegios elevados asignados. 

1. Seleccione **Comando de ejecución** > **Paquetes** > **Install-JetDR.**

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **Network** | Asignación de red para la instancia de MSA que se va a implementar. Por ejemplo, **Red de VM**.  |
   | **HostName** | Nombre de host (FQDN) de la instancia de MSA que se va a implementar.  |
   | **Credential:** | Credenciales del usuario raíz de la instancia de MSA que se va a implementar.   |
   | **Puerta de enlace** | Puerta de enlace de la instancia de MSA que se va a implementar. Déjelo en blanco para DHCP.  |
   | **Dns** | Dirección IP de DNS que debe usar MSA. Déjelo en blanco para DHCP.  |
   | **MSIp** | Dirección IP de la instancia de MSA que se va a implementar. Déjelo en blanco para DHCP.  |
   | **Máscara de red** | Máscara de red de la instancia de MSA que se va a implementar. Déjelo en blanco para DHCP.  |
   | **Clúster** | Nombre del clúster donde se va a implementar MSA. Por ejemplo, **Cluster-1**. |
   | **VMName** | Nombre de la máquina virtual de MSA. Por ejemplo, **jetstreamServer**. |
   | **Almacén de datos** | Almacén de datos donde se va a implementar MSA.  |
   | **ProtectedCluster** | Clúster que se va a proteger. Por ejemplo, **Cluster-1**. |
   | **RegisterWithIp** | La instancia de MSA se registra con la dirección IP en lugar de con el nombre de host. <ul><li>"True" si el nombre de host de la instancia de MSA no está registrado en DNS.</li><li>"False" si el nombre de host de la instancia de MSA está registrado en DNS. </li></ul> |
   | **Conservar hasta**  | Período de retención de la salida del cmdlet. El valor predeterminado es 60.  |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **check_jetserverdetails**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Compruebe **Notificaciones** para ver el progreso.  


## <a name="uninstall-jetstream-dr"></a>Desinstalación de JetStream DR

JetStream DR se desinstala en dos pasos. 


### <a name="check-current-state-of-the-jetstream-appliance"></a>Comprobación del estado actual del dispositivo JetStream

Ejecute el cmdlet `Invoke-PreflightJetDRUninstall` para diagnosticar la configuración existente de clúster y máquina virtual de MSA. Se comprueba el estado actual del dispositivo JetStream DR y si se cumplen los requisitos mínimos para el script:

- Los detalles del clúster son correctos. 
- Tiene al menos cuatro hosts.
- vCenter está registrado en la instancia de MSA.

1. Seleccione **Comando de ejecución** > **Paquetes** > **Invoke-PreflightJetDRUninstall**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **MSIp** | Dirección IP de la máquina virtual de MSA.  |
   | **Credential:** | Credenciales del usuario raíz de la máquina virtual de MSA. Deben ser las mismas proporcionadas en el momento de la instalación.  |
   | **ProtectedCluster** | Nombre del clúster protegido, por ejemplo, **Cluster-1**.  Debe ser el clúster que se proporcionó en el momento de la instalación. |
   | **Conservar hasta**  | Período de retención de la salida del cmdlet. El valor predeterminado es 60.  |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **uninstallcheck_jetserverdetails**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Compruebe **Notificaciones** para ver el progreso.  




### <a name="uninstall-jetdr"></a>Desinstalación de JetDR

Ejecute el cmdlet `Uninstall-JetDR` para desinstalar JetStream DR y sus componentes.  Anula la configuración del clúster, anula el registro de vCenter de MSA y, a continuación, elimina al usuario.

1. Seleccione **Comando de ejecución** > **Paquetes** > **Uninstall-JetDR.**

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **MSIp** | Dirección IP de la máquina virtual de MSA.  |
   | **Credential:** | Credenciales del usuario raíz de la máquina virtual de MSA. Deben ser las mismas proporcionadas en el momento de la instalación.   |
   | **ProtectedCluster** | Nombre del clúster protegido, por ejemplo, **Cluster-1**.  Debe ser el clúster que se proporcionó en el momento de la instalación. |
   | **Conservar hasta**  | Período de retención de la salida del cmdlet. El valor predeterminado es 60.  |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **uninstallcheck_jetserverdetails**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Compruebe **Notificaciones** para ver el progreso.  


## <a name="next-steps"></a>Pasos siguientes

- [JetStream DR para Azure VMware Solution: demostración completa](https://vimeo.com/475620858/2ce9413248)
   
   - [Introducción a JetStream DR para Azure VMware Solution](https://vimeo.com/491880696/ec509ff8e3)

   - [Configuración y protección de máquinas virtuales](https://vimeo.com/491881616/d887590fb2)

   - [Conmutación por error a Azure VMware Solution](https://vimeo.com/491883564/ca9fc57092)

   - [Conmutación por recuperación al entorno local](https://vimeo.com/491884402/65ee817b60):

- [JetStream DR para Azure VMware Solution: configuración de la infraestructura](https://vimeo.com/480574312/b5386a871c) (*detalles técnicos, sin pista de voz*)
