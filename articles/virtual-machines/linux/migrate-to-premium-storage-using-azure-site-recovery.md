---
title: Migración de máquinas virtuales Linux a Azure Premium Storage con Azure Site Recovery
description: Migre las máquinas virtuales existentes a Azure Premium Storage mediante Site Recovery. Premium Storage le ofrece compatibilidad con discos de alto rendimiento y baja latencia para cargas de trabajo con un uso intensivo de E/S, que se ejecutan en Azure Virtual Machines.
author: luywang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 426322477ccef3a7e3d8be1e0b024a5abff532d4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695060"
---
# <a name="use-site-recovery-to-migrate-to-premium-storage"></a>Uso de Site Recovery para migrar a Premium Storage

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Conjuntos de escalado flexibles 

Los [SSD de Azure Premium](../disks-types.md) le ofrecen compatibilidad con los discos de alto rendimiento y latencia baja para máquinas virtuales (VM) que ejecutan cargas de trabajo con muchas operaciones de E/S. Esta guía le ayuda a migrar los discos de las máquinas virtuales desde una cuenta de almacenamiento estándar a una cuenta de Premium Storage mediante [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery es un servicio de Azure que contribuye a su estrategia de continuidad empresarial y recuperación ante desastres mediante la organización de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. 

Site Recovery proporciona conmutaciones por error de prueba que admiten maniobras de recuperación ante desastres sin que los entornos de producción se vean afectados. Puede ejecutar conmutaciones por error con la mínima pérdida de datos (según la frecuencia de replicación) para desastres inesperados. En el escenario de migración a Premium Storage, puede utilizar la [conmutación por error en Site Recovery](../../site-recovery/site-recovery-failover.md) para migrar los discos de destino a una cuenta de Premium Storage.

Se recomienda migrar a Premium Storage mediante Site Recovery, ya que con esta opción el tiempo de inactividad es mínimo y se evita tener ejecutar manualmente la copia los discos y la creación de nuevas máquinas virtuales. Site Recovery de forma sistemática copiará los discos y creará nuevas máquinas virtuales durante la conmutación por error. 

Site Recovery admite varios tipos de conmutación por error con un tiempo de inactividad mínimo, o ninguno. Para planear el tiempo de inactividad y calcular la pérdida de datos, consulte los [tipos de conmutación por error de Site Recovery](../../site-recovery/site-recovery-failover.md). Si se [prepara para conectarse a las máquinas virtuales de Azure después de la conmutación por error](../../site-recovery/vmware-azure-tutorial.md), debería poder conectarse a la máquina virtual de Azure mediante RDP después de la conmutación por error.

![Diagrama de la recuperación ante desastres][1]

## <a name="azure-site-recovery-components"></a>Componentes de Azure Site Recovery

Estos componentes de Site Recovery son los relevantes para este escenario de migración:

* El **servidor de configuración** es una máquina virtual de Azure que coordina la comunicación y administra los procesos de replicación y recuperación de datos. En esta máquina virtual se ejecuta un archivo de configuración individual para instalar el servidor de configuración y un componente adicional, denominado servidor de procesos, como puerta de enlace de replicación. Lea sobre los [requisitos previos del servidor de configuración](../../site-recovery/vmware-azure-tutorial.md). El servidor de configuración solo se configura una vez y puede utilizarse para todas las migraciones a la misma región.

* El **servidor de procesos** es una puerta de enlace de replicación que: 

  1. Recibe datos de replicación de las máquinas virtuales de origen.
  2. Optimiza los datos con el almacenamiento en caché, la compresión y cifrado.
  3. Envía los datos a una cuenta de almacenamiento. 

  También controla la instalación de inserción del servicio de movilidad en máquinas virtuales de origen y realiza la detección automática de máquinas virtuales de origen. El servidor de procesos predeterminado está instalado en el servidor de configuración. Puede implementar servidores de procesos independientes adicionales para escalar la implementación. Obtenga información sobre [los procedimientos recomendados para la implementación de servidor de procesos](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) y [la implementación de los servidores de procesos adicionales](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). El servidor de procesos solo se configura una vez y puede utilizarse para todas las migraciones a la misma región.

* El **servicio de movilidad** es un componente que se implementa en cada máquina virtual estándar que desea replicar. Captura las escrituras de datos en la máquina virtual estándar y los reenvía al servidor de procesos. Más información acerca de los [requisitos previos de las máquinas replicadas](../../site-recovery/vmware-azure-tutorial.md).

En este gráfico se muestra la interacción de estos componentes:

![Interacción de los componentes de Site Recovery][15]

> [!NOTE]
> Site Recovery no admite la migración de los discos de espacios de Storage.

Para conocer los componentes adicionales de otros escenarios, consulte [Arquitectura del escenario](../../site-recovery/vmware-azure-tutorial.md).

## <a name="azure-essentials"></a>Información esencial de Azure

Estos son los requisitos de Azure para este escenario de migración:

* Suscripción a Azure.
* Una cuenta de Azure Premium Storage para almacenar los datos replicados.
* Una red virtual de Azure a la que se conectarán las máquinas virtuales cuando se creen en la conmutación por error. La red virtual de Azure debe estar en la misma región en que se ejecuta Site Recovery.
* Una cuenta de almacenamiento estándar de Azure para almacenar los registros de replicación. Puede ser la misma que para los discos de máquina virtual que se migran.

## <a name="prerequisites"></a>Requisitos previos

* Comprender los componentes del escenario de migración relevantes de la sección anterior.
* Planear el tiempo de inactividad, para lo que se debe conocer la [conmutación por error en Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Pasos de configuración y migración

Puede usar Site Recovery para migrar máquinas virtuales de IaaS de Azure entre regiones o dentro de la misma región. Las instrucciones siguientes se han adaptado a este escenario de migración a partir del artículo [Replicación de máquinas virtuales VMware en Azure con Site Recovery](../../site-recovery/vmware-azure-tutorial.md). Siga los vínculos para ver otros pasos, además de las instrucciones de este artículo.

### <a name="step-1-create-a-recovery-services-vault"></a>Paso 1: Creación de un almacén de Recovery Services

1. Abra [Azure Portal](https://portal.azure.com).
2. Seleccione **Crear un recurso** > **Administración** > **Backup** and **Site Recovery (OMS)** . También puede seleccionar **Examinar** > **Almacén de Recovery Services** > **Agregar**. 
3. Especifique la región en la que se van a replicar las máquinas virtuales. A efectos de la migración en la misma región, seleccione la región donde están máquinas virtuales de origen y las cuentas de almacenamiento de origen. 

### <a name="step-2-choose-your-protection-goals"></a>Paso 2: Selección de los objetivos de protección 

1. En la máquina virtual en la que desea instalar el servidor de configuración, abra [Azure Portal](https://portal.azure.com).
2. Vaya a **Almacenes de Recovery Services** > **Configuración** > **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Objetivo de protección**.

   ![Desplazamiento al panel Objetivo de protección][2]

3. En **Objetivo de protección**, en la primera lista desplegable, seleccione **En Azure**. En la segunda lista desplegable, seleccione **No virtualizado/Otro** y, después, haga clic en **Aceptar**.

   ![Panel Objetivo de protección con los cuadros rellenos][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Paso 3: Configuración del entorno de origen (servidor de configuración)

1. Descargue **Instalación unificada de Azure Site Recovery** y la clave de registro del almacén, pero lo que debe ir a los paneles **Preparar infraestructura** > **Preparar origen** > **Agregar servidor**. 
 
   Necesitará la clave de registro del almacén para ejecutar la instalación unificada. La clave será válida durante cinco días a partir del momento en que se genera.

   ![Desplazamiento al panel Agregar servidor][4]

2. En el panel **Agregar servidor**, agregue un servidor de configuración.

   ![Panel Agregar servidor con Servidor de configuración seleccionado][5]

3. En la máquina virtual que usa como servidor de configuración, ejecute la instalación unificada para instalar el servidor de configuración y el servidor de procesos. Para completar la instalación, puede [recorrer las capturas de pantalla](../../site-recovery/vmware-azure-tutorial.md). Puede consultar las capturas de pantalla siguientes para conocer los pasos especificados para este escenario de migración.

   1. En **Antes de comenzar**, seleccione **Install the configuration server and process server** (Instalar el servidor de configuración y el servidor de procesos).

      ![Página Antes de comenzar][6]

   2. En **Registro**, busque y seleccione la clave de registro que descargó del almacén.

      ![Página Registro][7]

   3. En **Detalles del entorno**, seleccione si replicará máquinas virtuales de VMware. En este escenario de migración, elija **No**.

      ![Página Detalles del entorno][8]

4. Una vez que la instalación finalice, realice las siguientes operaciones en la ventana **Servidor de configuración de Microsoft Azure Site Recovery**:
 
   1. Utilice la pestaña **Administrar cuentas** para crear la cuenta que puede usar Site Recovery para la detección automática. (En el escenario sobre cómo proteger las máquinas físicas, no es pertinente configurar la cuenta, pero necesita al menos una cuenta para habilitar uno de los pasos siguientes. En este caso, puede denominar a la cuenta y contraseña como cualquier otra). 
   2. Use la pestaña **Registro del almacén** para cargar el archivo de credenciales del almacén.

      ![Pestaña Registro del almacén][9]

### <a name="step-4-set-up-the-target-environment"></a>Paso 4: Configuración del entorno de destino

Seleccione **Preparar infraestructura** > **Destino** y especifique el modelo de implementación que desea usar para las máquinas virtuales después de la conmutación por error. Puede elegir **Clásico** o **Resource Manager**, en función de su escenario.

![Panel Destino][10]

Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles. 

> [!NOTE]
> Si utiliza una cuenta de Premium Storage para los datos replicados, tendrá que configurar una cuenta de almacenamiento estándar adicional para almacenar los registros de replicación.

### <a name="step-5-set-up-replication-settings"></a>Paso 5: Configuración de las opciones de replicación

Para comprobar que el servidor de configuración se ha asociado correctamente a la directiva de replicación que cree, siga las instrucciones de [Configuración de las opciones de replicación](../../site-recovery/vmware-azure-tutorial.md).

### <a name="step-6-plan-capacity"></a>Paso 6: Planeamiento de la capacidad

1. Utilice el [planeamiento de capacidad](../../site-recovery/site-recovery-capacity-planner.md) para calcular con precisión el ancho de banda de red, almacenamiento y otros requisitos para satisfacer las necesidades de replicación. 
2. Cuando haya terminado, seleccione **Sí, ya lo hice** en **Have you completed capacity planning?** (¿Ha completado el planeamiento de capacidad?).

   ![Cuadro para confirmar que ha completado el planeamiento de capacidad][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Paso 7: Instalación del servicio de movilidad y habilitación de la replicación

1. Puede elegir [insertar la instalación](../../site-recovery/vmware-azure-tutorial.md) en las máquinas virtuales de origen o [instalar manualmente el servicio de movilidad](../../site-recovery/vmware-azure-install-mobility-service.md) en las máquinas virtuales de origen. En el vínculo que se proporciona puede encontrar el requisito de insertar la instalación y la ruta de acceso del instalador manual. Si va a realizar una instalación manual, es posible que tenga que usar una dirección IP interna para buscar el servidor de configuración.

   ![Página Detalles del servidor de configuración][12]

   La máquina virtual conmutada por error tendrá dos discos temporales: uno de la máquina virtual principal y el otro creado durante el aprovisionamiento de la máquina virtual en la región de recuperación. Para excluir el disco temporal antes de la réplica, instale el servicio de movilidad antes de habilitar la replicación. Para más información acerca de cómo excluir el disco temporal, consulte [Exclusión de discos de la replicación](../../site-recovery/vmware-azure-tutorial.md).

2. Habilite la replicación como se indica a continuación:
   1. Seleccione **Replicar la aplicación** > **Origen**. Después de habilitar la replicación por primera vez, seleccione **+Replicar** en el almacén para habilitar la replicación de más máquinas.
   2. En el paso 1, configure el valor de **Origen** como servidor de procesos.
   3. En el paso 2, especifique el modelo de implementación posterior a la conmutación por error, la cuenta de Premium Storage a la que se va a realizar la migración, la cuenta de almacenamiento estándar en la que se guardarán los registros y la red virtual que producirá un error.
   4. En el paso 3, agregue máquinas virtuales protegidas por dirección IP. (para buscarlas, es posible que necesite una dirección IP interna).
   5. En el paso 4, configure las propiedades mediante la selección de las cuentas que configuró anteriormente en el servidor de procesos.
   6. En el paso 5, elija la directiva de replicación que creó anteriormente en "Paso 5: Configuración de las opciones de replicación".
   7. Seleccione **Aceptar**.

   > [!NOTE]
   > Cuando se cancela la asignación de una máquina virtual de Azure y se vuelve a iniciar, no hay ninguna garantía de que obtendrá la misma dirección IP. Si la dirección IP del servidor de procesos o servidor de configuración, o de las máquinas virtuales de Azure protegidas cambia, es posible que la replicación en este escenario no funcione correctamente.

   ![Panel Habilitar la replicación con Origen seleccionado][13]

Cuando se diseña el entorno de Azure Storage, se recomienda utilizar cuentas de almacenamiento separadas para cada máquina virtual en un conjunto de disponibilidad. Es recomendable seguir el procedimiento recomendado en la capa de almacenamiento para [utilizar varias cuentas de almacenamiento para cada conjunto de disponibilidad](../availability.md). La distribución de discos de máquina virtual a varias cuentas de almacenamiento ayuda a mejorar la disponibilidad de almacenamiento y distribuye la entrada/salida en toda la infraestructura de Azure Storage.

Si las máquinas virtuales están en un conjunto de disponibilidad, en lugar de replicar los discos de todas las máquinas virtuales en una cuenta de almacenamiento, se recomienda encarecidamente migrar varias máquinas virtuales repetidas veces. De esa forma, las máquinas virtuales del mismo conjunto de disponibilidad no comparten una única cuenta de almacenamiento. Use el panel **Habilitar la replicación** para configurar una cuenta de almacenamiento de destino para cada máquina virtual, de una en una.
 
Puede elegir un modelo de implementación posterior a la conmutación por error según sus necesidades. Si elige Azure Resource Manager como modelo de implementación posterior a la conmutación por error, puede conmutar por error una máquina virtual (Resource Manager) a una máquina virtual (Resource Manager), o bien puede conmutar por error una máquina virtual (clásico) a una máquina virtual (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Paso 8: Ejecución de una conmutación por error de prueba

Para comprobar si la replicación está completa, seleccione la instancia de Site Recovery y, después, **Configuración** > **Elementos replicados**. Verá el estado y el porcentaje del proceso de replicación. 

Cuando la replicación inicial haya finalizado, ejecute una conmutación por error de prueba para validar la estrategia de replicación. Para ver los pasos detallados de la conmutación por error de prueba, consulte [Ejecución de una conmutación por error de prueba](../../site-recovery/vmware-azure-tutorial.md). 

> [!NOTE]
> Antes de ejecutar cualquier conmutación por error, asegúrese de que las máquinas virtuales y la estrategia de replicación cumplen los requisitos. Para más información acerca de la ejecución de una conmutación por error de prueba, consulte [Conmutación por error de prueba a Azure en Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

El estado de la conmutación por error de prueba se puede ver en **Configuración** > **Trabajos** > *nombreDeSuPlanDeConmutaciónPorError*. En el panel, verá un desglose de los pasos y los resultados correctos o con errores. Si se produce un error en la conmutación por error de prueba en cualquier paso, seleccione el paso para comprobar el mensaje de error. 

### <a name="step-9-run-a-failover"></a>Paso 9: Ejecución de la conmutación por error

Después de realizada la conmutación por error de prueba, ejecute una conmutación por error para migrar los discos a Premium Storage y replicar las instancias de máquinas virtuales. Siga los pasos detallados de [Ejecución de la conmutación por error](../../site-recovery/site-recovery-failover.md#run-a-failover). 

No olvide seleccionar **Shut down VMs and synchronize the latest data** (Apagar máquinas virtuales y sincronizar los datos más recientes). Esta opción especifica que Site Recovery debe intentar apagar las máquinas virtuales protegidas y sincronizar los datos para que se realice la conmutación por error de la versión más reciente de los datos. Si no selecciona esta opción o el intento es infructuoso, la conmutación por error se realizará a partir del último punto de recuperación disponible para la máquina virtual. 

Site Recovery creará una instancia de máquina virtual cuyo tipo sea el mismo, o similar, a una máquina virtual que pueda usar Premium Storage. Puede comprobar el rendimiento y el precio de varias instancias de máquinas virtuales en [Precios de máquinas virtuales Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o [Precios de máquinas virtuales Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Pasos posteriores a la migración

1. **Configure máquinas virtuales replicadas en el conjunto de disponibilidad, si procede**. Site Recovery no admite la migración de máquinas virtuales junto con el conjunto de disponibilidad. Dependiendo de la implementación de la máquina virtual replicada, realice una de las siguientes acciones:
   * Para una VM creada con el modelo de implementación clásica: agregue la VM al conjunto de disponibilidad en Azure Portal. Para ver los pasos detallados, consulte [Adición de una máquina virtual existente a un conjunto de disponibilidad](/previous-versions/azure/virtual-machines/linux/classic/configure-availability-classic).
   * Para una VM creada con el modelo de implementación de Resource Manager: guarde la configuración de la VM y, después, elimine y vuelva a crear las VM en el conjunto de disponibilidad. Para ello, use el script que encontrará en [Set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4) (Establecimiento del conjunto de disponibilidad de máquinas virtuales de Azure Resource Manager). Antes de ejecutar este script, compruebe sus limitaciones y planee el tiempo de inactividad.

2. **Elimine las máquinas virtuales y los discos antiguos**. Asegúrese de que los discos Premium sean coherentes con los discos de origen y que las nuevas máquinas virtuales realizan la misma función que las máquinas virtuales de origen. Elimine la máquina virtual y los discos de las cuentas de almacenamiento de origen en Azure Portal. Si hay un problema en el que el disco no se elimina aunque haya eliminado la máquina virtual, consulte [Troubleshoot storage resource deletion errors](/troubleshoot/azure/virtual-machines/storage-resource-deletion-errors) (Solución de errores de eliminación de recursos de almacenamiento).

3. **Limpie la infraestructura de Azure Site Recovery**. Si Site Recovery deja de necesitarse, puede limpiar su infraestructura. Elimine los elementos duplicados, el servidor de configuración y la directiva de recuperación y, después, elimine el almacén de Azure Site Recovery.

## <a name="troubleshooting"></a>Solución de problemas

* [Protección de supervisión y solución de problemas para las máquinas virtuales y los servidores físicos](../../site-recovery/site-recovery-monitor-and-troubleshoot.md)
* [Página de preguntas y respuestas de Microsoft sobre Microsoft Azure Site Recovery](/answers/topics/azure-site-recovery.html)

## <a name="next-steps"></a>Pasos siguientes

Para conocer los escenarios concretos para migrar máquinas virtuales, consulte los siguientes recursos:

* [Migrar Azure Virtual Machines entre cuentas de almacenamiento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Cargar un disco duro virtual Linux](upload-vhd.md)
* [Migrar máquinas virtuales de Amazon AWS a Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Consulte también los siguientes recursos para más información sobre Azure Storage y Azure Virtual Machines:

* [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Selección de un tipo de disco para máquinas virtuales de IaaS](../disks-types.md)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png