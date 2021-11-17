---
title: Configuración de la recuperación ante desastres de SAP NetWeaver con Azure Site Recovery
description: Obtenga información sobre cómo configurar la recuperación ante desastres para SAP NetWeaver con Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 4ada2c92b541a4a0d89aad1791d3bb9c97377f83
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441654"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configuración de la recuperación ante desastres para la implementación de una aplicación de SAP NetWeaver de niveles múltiples

La mayor parte de las implementaciones SAP grandes y medianas usan algún tipo de solución de recuperación ante desastres. La importancia de las soluciones de recuperación ante desastres sólidas y verificables ha aumentado a medida que cada vez más procesos empresariales básicos se trasladan a aplicaciones como SAP. Azure Site Recovery se probó e integró con aplicaciones SAP. Site Recovery supera las funcionalidades de la mayoría de las soluciones de recuperación ante desastres locales, con un costo total de propiedad menor que las soluciones de la competencia.

Con Site Recovery, puede:
* Habilitar la protección de aplicaciones de producción de SAP NetWeaver y no NetWeaver que se ejecutan de manera local, mediante la replicación de componentes en Azure.
* Habilitar la protección de aplicaciones de producción de SAP NetWeaver y no NetWeaver que se ejecutan en Azure, mediante la replicación de componentes en otro centro de datos de Azure.
* Simplifique la migración a la nube con Site Recovery para migrar la implementación de SAP en Azure.
* Simplificar las actualizaciones, las pruebas y la creación de prototipos de los proyectos de SAP, mediante la creación de un clon en producción a petición para probar las aplicaciones SAP.

Puede proteger las implementaciones de aplicaciones SAP NetWeaver mediante [Azure Site Recovery](site-recovery-overview.md). En este artículo se describen los procedimientos recomendados para proteger una implementación de SAP NetWeaver de tres niveles en Azure al replicar en otro centro de datos de Azure a través de Site Recovery. En el artículo se describen los escenarios y las configuraciones compatibles y cómo realizar conmutaciones por error de prueba (maniobras de recuperación ante desastres) y conmutaciones ante desastres reales.

## <a name="prerequisites"></a>Prerrequisitos

Antes de comenzar, asegúrese de que sabe cómo realizar las tareas siguientes:

* [Replicar una máquina virtual en Azure](./azure-to-azure-tutorial-enable-replication.md)
* [Diseñar una red de recuperación](./azure-to-azure-about-networking.md)
* [Realizar una conmutación por error de prueba en Azure](./azure-to-azure-tutorial-dr-drill.md)
* [Realizar una conmutación por error en Azure](site-recovery-failover.md)
* [Replicar un controlador de dominio](site-recovery-active-directory.md)
* [Replicar una instancia de SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Escenarios admitidos

Puede usar Site Recovery para implementar una solución de recuperación ante desastres en los escenarios siguientes:
* Dispone de sistemas SAP que se ejecutan en un centro de datos de Azure y los replica en otro centro de datos de Azure (recuperación ante desastres de Azure en Azure). 
   Para más información, consulte [Arquitectura de replicación de Azure en Azure](./azure-to-azure-architecture.md).
* Tiene sistemas SAP que se ejecutan en servidores VMware (o físicos) locales. También replica los sistemas SAP en un sitio de recuperación ante desastres de un centro de datos de Azure (recuperación ante desastres de VMware en Azure). 
   Se requieren componentes adicionales para este escenario. Para más información, consulte el artículo sobre la [arquitectura de replicación de VMware en Azure](./vmware-azure-architecture.md).
* Dispone de sistemas SAP que se ejecutan en Hyper-V de forma local. También replica los sistemas SAP en un sitio de recuperación ante desastres de un centro de datos de Azure (recuperación ante desastres de Hyper-V en Azure).
   Se requieren componentes adicionales para este escenario. Para más información, consulte [Arquitectura de replicación de Hyper-V en Azure](./hyper-v-azure-architecture.md).

En este artículo, usamos un escenario de recuperación ante desastres de **Azure en Azure**. En el escenario se muestran las funcionalidades de Site Recovery de recuperación ante desastres de SAP. Debido a que la replicación de Site Recovery no es específica para la aplicación, el proceso descrito también se puede aplicar a otros escenarios.

### <a name="required-foundation-services"></a>Servicios básicos requeridos
En el escenario que se analiza en este artículo, se implementan los servicios básicos siguientes:
* Azure ExpressRoute o Azure VPN Gateway
* Al menos un servidor DNS y un controlador de dominio de Azure Active Directory que se ejecuten en Azure

Se recomienda establecer esta infraestructura antes de implementar Site Recovery.

## <a name="reference-sap-application-deployment"></a>Implementación de referencia de aplicación SAP

En esta arquitectura de referencia se ejecuta SAP NetWeaver en un entorno Windows en Azure con alta disponibilidad. Esta arquitectura se implementa con tamaños de máquina virtual (VM) específicos que puede cambiar para acomodarlos a las necesidades de la organización.

![Diagrama de un modelo de implementación de SAP típico](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Consideraciones acerca de la recuperación ante desastres

Para la recuperación ante desastres, debe ser capaz de realizar la conmutación por error en una región secundaria. Cada nivel usa una estrategia diferente para proporcionar protección mediante la recuperación ante desastres.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>Máquinas virtuales que ejecutan grupos de SAP Web Dispatcher

El componente Web Dispatcher funciona como equilibrador de carga para el tráfico SAP entre los servidores de aplicaciones de SAP. Para lograr una alta disponibilidad para el componente Web Dispatcher, Azure Load Balancer implementa la configuración paralela de Web Dispatcher. Web Dispatcher usa una configuración round robin para la distribución del tráfico HTTP(S) entre las instancias de Web Dispatcher disponibles en el grupo de equilibradores.

#### <a name="vms-running-application-servers-pools"></a>Máquinas virtuales que ejecutan grupos de servidores de aplicaciones
La transacción SMLG administra grupos de inicio de sesión para servidores de aplicaciones de ABAP. Usa la función de equilibrio de carga en el servidor de mensajes de Central Services para distribuir la carga de trabajo entre los grupos de servidores de aplicaciones de SAP para las SAPGUI y el tráfico de RFC. Puede replicar esta administración mediante Site Recovery.

#### <a name="vms-running-sap-central-services-clusters"></a>Máquinas virtuales que ejecutan clústeres de SAP Central Services
Esta arquitectura de referencia ejecuta Central Services en máquinas virtuales en la capa de aplicación. Central Services es un punto único de error potencial cuando se encuentra en una sola máquina virtual. La implementación típica y la alta disponibilidad no son requisitos.

Para implementar una solución de alta disponibilidad, puede usar un clúster de disco compartido o un clúster de recursos compartidos de archivos. Para configurar las máquinas virtuales para un clúster de disco compartido, use el clúster de conmutación por error de Windows Server. Se recomienda usar el testigo de la nube como testigo de cuórum.

 > [!NOTE]
 > Site Recovery no replica el testigo en la nube, se recomienda implementarlo en la región de recuperación ante desastres.

Para admitir el entorno de clústeres de conmutación por error, [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) realiza la función de volumen compartido de clúster. En la función, SIOS DataKeeper Cluster replica los discos independientes que son propiedad de los nodos de clúster. Puesto que Azure no admite de forma nativa discos compartidos, requiere las soluciones que proporciona SIOS.

También puede controlar la agrupación en clústeres mediante la implementación de un clúster de recursos compartidos de archivos. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ha modificado recientemente el modelo de implementación de Central Services para acceder a los directorios globales de /sapmnt a través de una ruta de acceso UNC. Sigue siendo recomendable asegurarse de que el recurso compartido UNC /sapmnt tenga alta disponibilidad. Puede comprobar la instancia de Central Services. Use el clúster de conmutación por error de Windows Server con las características Servidor de archivos de escalabilidad horizontal (SOFS) y Espacios de almacenamiento directo (S2D) en Windows Server 2016.

 > [!NOTE]
 > En la actualidad, Site Recovery solo admite la replicación de puntos de restauración coherentes con los bloqueos de las máquinas virtuales que usan espacios de almacenamiento directo y el nodo pasivo de SIOS Datakeeper.


## <a name="more-disaster-recovery-considerations"></a>Más consideraciones sobre la recuperación ante desastres

Puede usar Site Recovery para orquestar la conmutación por error de la implementación completa de SAP en regiones de Azure.
A continuación, se muestran los pasos para configurar la recuperación ante desastres:

1. Replicación de máquinas virtuales
1. Diseño de una red de recuperación
1. Replicación de un controlador de dominio
1. Replicación del nivel de base de datos
1. Ejecución de una conmutación por error de prueba
1. Ejecución de una conmutación por error

A continuación, se muestra la recomendación para la recuperación ante desastres de cada nivel usado en este ejemplo.

 **Niveles SAP** | **Recomendación**
 --- | ---
**Grupo de SAP Web Dispatcher** |  Replicación mediante Site Recovery 
**Grupo de SAP Application Server** |  Replicación mediante Site Recovery 
**Clúster de SAP Central Services** |  Replicación mediante Site Recovery 
**Máquinas virtuales de Active Directory** |  Uso de la replicación de Active Directory 
**Servidores de SQL Database** |  Uso de la replicación de SQL Server Always On

## <a name="replicate-virtual-machines"></a>Replicación de máquinas virtuales

Para comenzar a replicar todas las máquinas virtuales de la aplicación SAP en el centro de datos de recuperación ante desastres de Azure, siga las instrucciones que aparecen en el artículo sobre la [replicación de una máquina virtual en Azure](./azure-to-azure-tutorial-enable-replication.md).

* Para obtener instrucciones sobre la protección de Active Directory y DNS, aprenda a [proteger Active Directory y DNS](site-recovery-active-directory.md).

* Para obtener instrucciones sobre la protección de niveles de bases de datos ejecutadas en SQL server, aprenda a [proteger SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Configuración de red

Si usa una dirección IP estática, puede especificar la dirección IP que desea que use la máquina virtual. Para establecer la dirección IP, vaya a **Configuración de red** > **Tarjeta de interfaz de red**.

![Captura de pantalla que muestra cómo establecer una dirección IP privada en el panel del adaptador de red de Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Creación de un plan de recuperación

Un plan de recuperación admite la secuenciación de distintas capas en una aplicación de varios niveles durante una conmutación por error. La secuenciación ayuda a mantener la coherencia de la aplicación. Cuando cree un plan de recuperación para una aplicación web de varios niveles, complete los pasos descritos en [Creación de un plan de recuperación mediante Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Adición de máquinas virtuales a grupos de conmutación por error

1. Cree un plan de recuperación agregando el servidor de aplicaciones, Web Dispatcher y las máquinas virtuales de SAP Central Services.
1. Seleccione **Personalizar** para agrupar las máquinas virtuales. De forma predeterminada, todas las máquinas virtuales forman parte del grupo 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Incorporación de scripts al plan de recuperación
Para que las aplicaciones funcionen correctamente, es posible que tenga que realizar algunas operaciones en las máquinas virtuales de Azure. Realice estas operaciones después de la conmutación por error o durante una conmutación por error de prueba. Algunas de las operaciones posteriores a la conmutación por error también se pueden automatizar. Por ejemplo, actualice la entrada DNS y modifique los enlaces y las conexiones mediante la incorporación de los scripts correspondientes al plan de recuperación.

Puede implementar los scripts de Site Recovery que se usan con más frecuencia en la cuenta de Azure Automation seleccionando **Implementar en Azure**. Si usa un script publicado, siga las instrucciones del script.

[![Implementación en Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Agregue un script anterior a la acción al grupo 1 para conmutar por error el grupo de disponibilidad de SQL Server. Use el script ASR-SQL-FailoverAG publicado en los scripts de ejemplo. Siga las instrucciones del script y realice los cambios necesarios en él, de forma adecuada.
1. Agregue un script posterior a la acción para adjuntar un equilibrador de carga en las máquinas virtuales conmutadas por error del nivel web (grupo 1). Use el script ASR-AddSingleLoadBalancer publicado en los scripts de ejemplo. Siga las instrucciones del script y realice los cambios necesarios en él, según proceda.

![Plan de recuperación de SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

1. En Azure Portal, seleccione el almacén de Recovery Services.
1. Seleccione el plan de recuperación que creó para las aplicaciones SAP.
1. Seleccione **Conmutación por error de prueba**.
1. Para iniciar el proceso de conmutación por error de prueba, seleccione el punto de recuperación y la red virtual de Azure.
1. Cuando el entorno secundario esté activo, realice las validaciones.
1. Cuando se completen las validaciones, seleccione **Limpiar conmutación por error de prueba** para limpiar el entorno de conmutación por error.

Para más información, consulte [Conmutación por error de prueba a Azure en Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Ejecución de la conmutación por error

1. En Azure Portal, seleccione el almacén de Recovery Services.
1. Seleccione el plan de recuperación que creó para las aplicaciones SAP.
1. Seleccione **Failover** (Conmutación por error).
1. Para iniciar el proceso de conmutación por error, seleccione el punto de recuperación.

Para más información, consulte [Conmutación por error en Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre cómo crear una solución de recuperación ante desastres para las implementaciones de SAP NetWeaver con Site Recovery. Consulte las notas del producto descargables [SAP NetWeaver: Creación de una solución de recuperación ante desastres con Site Recovery](/samples/browse/?redirectedfrom=TechNet-Gallery). En las notas del producto se describen las recomendaciones para distintas arquitecturas de SAP. Puede ver las aplicaciones y los tipos de máquinas virtuales compatibles para SAP en Azure. También hay opciones de plan para probar la solución de recuperación ante desastres.
* Obtenga más información sobre cómo [replicar otras cargas de trabajo](site-recovery-workload.md) con Site Recovery.