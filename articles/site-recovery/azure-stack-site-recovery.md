---
title: Replicar máquinas virtuales de Azure Stack en Azure con Azure Site Recovery | Microsoft Docs
description: Aprenda a configurar la recuperación ante desastres en Azure para máquinas virtuales de Azure Stack con el servicio Azure Site Recovery.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 386eae05ea06b59aac290d5f1f51d643d7952b6a
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580429"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replicación de máquinas virtuales de Azure Stack en Azure

En este artículo se muestra cómo configurar la recuperación ante desastres de máquinas virtuales de Azure Stack en Azure, mediante el [servicio Azure Site Recovery](site-recovery-overview.md).

Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR). El servicio garantiza que las cargas de trabajo de máquina virtual sigan estando disponibles cuando se produzcan interrupciones esperadas e inesperadas.

- Site Recovery orquesta y administra la replicación de máquinas virtuales en Azure Storage.
- Cuando se produzca una interrupción en el sitio principal, use Site Recovery para conmutar por error a Azure.
- En la conmutación por error, se crean máquinas virtuales de Azure a partir de los datos almacenados de la máquina virtual y los usuarios pueden seguir teniendo acceso a las cargas de trabajo que se ejecutan en esas máquinas virtuales de Azure.
- Cuando todo vuelve a estar activo y en ejecución, puede conmutar por recuperación las máquinas virtuales de Azure a su sitio principal y empezar a replicar de nuevo Azure Storage.


En este artículo aprenderá a:

> [!div class="checklist"]
> * **Paso 1: Preparación de las máquinas virtuales de Azure Stack para la replicación**. Compruebe que las máquinas virtuales cumplen los requisitos de Site Recovery y prepare la instalación del servicio Site Recovery Mobility. Este servicio se instala en cada máquina virtual que quiere replicar.
> * **Paso 2: Configuración de un almacén de Recovery Services**. Configure un almacén de Site Recovery y especifique lo que quiere replicar. Las acciones y los componentes de Site Recovery se configuran y administran en el almacén.
> * **Paso 3: Configuración del entorno de replicación de origen**. Configure un servidor de configuración de Site Recovery. El servidor de configuración es una única máquina virtual de Azure Stack que ejecuta todos los componentes que Site Recovery necesita. Después de configurar el servidor de configuración, se registra en el almacén.
> * **Paso 4: Configuración del entorno de replicación de destino**. Seleccione su cuenta de Azure, la cuenta de Azure Storage y la red que quiere usar. Durante la replicación, los datos de máquina virtual se copian en Azure Storage. Después de la conmutación por error, las máquinas virtuales de Azure se unen a la red especificada.
> * **Paso 5: Habilitación de la replicación**. Configure las opciones de replicación y habilite la replicación de las máquinas virtuales. Mobility Service se instalará en una máquina virtual cuando se habilite la replicación. Site Recovery realiza una replicación inicial de la máquina virtual y, a continuación, comienza la replicación en curso.
> * **Paso 6: Ejecución de un simulacro de recuperación ante desastres**: después de que la replicación esté en funcionamiento, compruebe que la conmutación por error funcionará según lo esperado; para ello, realice un simulacro. Para iniciar el simulacro, ejecute una conmutación por error de prueba en Site Recovery. La conmutación por error de prueba no afecta al entorno de producción.

Con estos pasos completados, puede ejecutar una conmutación por error completa a Azure como y cuando necesite.

## <a name="architecture"></a>Architecture

![Diagrama en el que se muestran almacenes de Recovery Services para dos inquilinos en nubes asociadas a suscripciones de inquilinos en una infraestructura de Azure Stack común.](./media/azure-stack-site-recovery/architecture.png)

**Ubicación** | **Componente** |**Detalles**
--- | --- | ---
**Servidor de configuración** | Se ejecuta en una única máquina virtual de Azure Stack. | En cada suscripción se configura una máquina virtual de configuración. Esta máquina virtual ejecuta los siguientes componentes de Site Recovery:<br/><br/> - Servidor de configuración: coordina las comunicaciones entre el entorno local y Azure, además de administrar la replicación de datos. - Servidor de procesos: Actúa como puerta de enlace de replicación. Recibe los datos de la replicación, los optimiza mediante el almacenamiento en caché, compresión y cifrado, y los envía a Azure Storage.<br/><br/> Si las máquinas virtuales que va a replicar superan los límites indicados a continuación, puede configurar un servidor de procesos independiente. [Más información](vmware-azure-set-up-process-server-scale.md).
**Servicio de movilidad** | Se instala en cada máquina virtual que quiere replicar. | En los pasos de este artículo, se prepara una cuenta para que Mobility Service se instale automáticamente en una máquina virtual cuando se habilite la replicación. Si no quiere instalar automáticamente el servicio, hay una serie de otros métodos que puede usar. [Más información](vmware-azure-install-mobility-service.md).
**Azure** | En Azure, necesita un almacén de Recovery Services, una cuenta de almacenamiento y una red virtual. |  Los datos replicados se almacenan en la cuenta de almacenamiento. Las máquinas virtuales de Azure se agregan a la red de Azure cuando se produce una conmutación por error.


La replicación funciona de la manera siguiente:

1. En el almacén, se especifica el origen y el destino de la replicación, se configura el servidor de configuración, se crea una directiva de replicación y se habilita la replicación.
2. Mobility Service se instala en la máquina (si ha usado la instalación de inserción) y las máquinas empiezan la replicación de acuerdo con la directiva de replicación.
3. Una copia inicial de los datos del servidor se replica en Azure Storage.
4. Una vez terminada la replicación inicial, comienza la de los cambios incrementales en Azure. Las marcas de revisión de una máquina se conservan en un archivo .hrl.
5. El servidor de configuración orquesta la administración de la replicación con Azure (puerto HTTPS 443 de salida).
6. El servidor de procesos recibe datos de las máquinas de origen, los optimiza y los cifra para enviarlos después a Azure Storage (puerto 443 de salida).
7. Las máquinas replicadas se comunican con el servidor de configuración (puerto HTTPS 443 de entrada) para la administración de la replicación. Las máquinas envían los datos de replicación al servidor de procesos (puerto HTTPS 9443 de entrada) (se puede modificar).
8. El tráfico se replica en los puntos de conexión públicos del almacenamiento de Azure a través de Internet. Como alternativa, puede usar el emparejamiento público de Azure ExpressRoute. No se admite la replicación del tráfico a través de una VPN de sitio a sitio desde un sitio local en Azure.

## <a name="prerequisites"></a>Prerrequisitos

Esto es lo que va a necesitar para configurar este escenario.

**Requisito** | **Detalles**
--- | ---
**Cuenta de suscripción de Azure** | Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Permisos de cuenta de Azure** | La cuenta de Azure que use necesita permisos para:<br/><br/> - Crear un almacén de Recovery Service<br/><br/> - Crear una máquina virtual en el grupo de recursos y la red virtual que se utiliza para el escenario<br/><br/> - Escribir en la cuenta de almacenamiento que especifique<br/><br/> Observe lo siguiente:<br/><br/> \- Si crea una cuenta gratuita, será el administrador de la suscripción y podrá realizar todas las acciones.<br/><br/> - Si usa una suscripción existente y no es el administrador, tendrá que solicitar al administrador que le asigne permisos de propietario o colaborador.<br/><br/> - Si necesita permisos más específicos, consulte [este artículo](site-recovery-role-based-linked-access-control.md).
**Máquina virtual de Azure Stack** | Necesita una máquina virtual de Azure Stack en la suscripción de inquilino, que se implementará como servidor de configuración de Site Recovery.


### <a name="prerequisites-for-the-configuration-server"></a>Requisitos previos del servidor de configuración

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Paso 1: Preparación de las máquinas virtuales de Azure Stack

### <a name="verify-the-operating-system"></a>Comprobar el sistema operativo

Asegúrese de que las máquinas virtuales ejecuten uno de los sistemas operativos que se resumen en la tabla.


**Sistema operativo** | **Detalles**
--- | ---
**Windows de 64 bits** | Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (desde SP1)
**CentOS** | Desde 5.2 hasta 5.11, desde 6.1 hasta 6.9 y desde 7.0 hasta 7.3
**Ubuntu** | Servidor 14.04 LTS, servidor 16.04 LTS. Revise los [kernels compatibles](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions).

### <a name="prepare-for-mobility-service-installation"></a>Preparación para la instalación del Servicio de Movilidad

Todas las máquinas virtuales que desee replicar deben tener instalado Mobility Service. Para que el servidor de procesos instale automáticamente el servicio en la máquina virtual cuando se habilita la replicación, compruebe la configuración de máquina virtual.

#### <a name="windows-machines"></a>Máquinas de Windows

- Se necesita conectividad de red entre la máquina virtual en la que quiere habilitar la replicación y la máquina que ejecuta el servidor de procesos (de forma predeterminada es la máquina virtual del servidor de configuración).
- Se necesita una cuenta con derechos de administrador (local o dominio) en el equipo para el que se habilita la replicación.
    - Esta cuenta se especifica al configurar Site Recovery. Después el servidor de procesos utiliza esta cuenta para instalar Mobility Service cuando se habilita la replicación.
    - Site Recovery solo utilizará esta cuenta para la instalación de inserción y para actualizar Mobility Service.
    - Si no utiliza una cuenta de dominio, deberá deshabilitar el control de acceso de usuario remoto en la máquina virtual:
        - En el Registro, cree el valor DWORD **LocalAccountTokenFilterPolicy** en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Establezca el valor en 1.
        - Para ello, en el símbolo del sistema,escriba el siguiente comando: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- En el Firewall de Windows de la máquina virtual que quiere replicar, permita compartir archivos e impresoras y WMI.
    - Para ello, ejecute **wf.msc** para abrir la consola de Firewall de Windows. Haga clic con el botón derecho en **Reglas de entrada** > **Nueva regla**. Seleccione **Predefinido** y elija **Compartir archivos e impresoras** en la lista. Complete el asistente, seleccione Permitir la conexión > **Finalizar**.
    - Para los equipos del dominio, puede usar un GPO para hacerlo.


#### <a name="linux-machines"></a>Equipos con Linux

- Asegúrese de que hay conectividad de red entre la el equipo con Linux y el servidor de procesos.
- En el equipo para el que se habilita la replicación, necesita una cuenta que sea usuario raíz en el servidor Linux de origen:
    - Esta cuenta se especifica al configurar Site Recovery. Después el servidor de procesos utiliza esta cuenta para instalar Mobility Service cuando se habilita la replicación.
    - Site Recovery solo utilizará esta cuenta para la instalación de inserción y para actualizar Mobility Service.
- Compruebe que el archivo /etc/hosts del servidor Linux de origen tiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.
- Instale los paquetes openssh, openssh-server y openssl más recientes en el equipo que desea replicar.
- Asegúrese de que Secure Shell (SSH) está habilitado y se ejecuta en el puerto 22.
- Habilite la autenticación de la contraseña y el subsistema SFTP en el archivo sshd_config:
    1. Para ello, inicie sesión como raíz.
    2. En el archivo /etc/ssh/sshd_config, encuentre la línea que comienza por **PasswordAuthentication**. Quite la marca de comentario de la línea y cambie el valor **yes**.
    3. Busque la línea que comienza por **Subsystem** y quite la marca de comentario.

        ![Linux Mobility Service](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Reinicie el servicio sshd.


### <a name="note-the-vm-private-ip-address"></a>Anotar la dirección IP privada de la máquina virtual

Para cada equipo que quiera replicar, busque la dirección IP:

1. En el portal de Azure Stack, haga clic en la máquina virtual.
2. En el menú **Recurso**, haga clic en **Interfaces de red**.
3. Anote la dirección IP privada.

    ![Dirección IP privada](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Paso 2: Creación de un almacén y selección de un objetivo de replicación

1. En Azure Portal, seleccione **Crear un recurso** > **Herramientas de administración** > **Backup y Site Recovery**.
2. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.
3. En **Grupo de recursos**, cree o seleccione un grupo de recursos. Estamos usando **contosoRG**.
4. En **Ubicación** especifique la región de Azure. se va a usar **Oeste de Europa**.
5. Para acceder rápidamente al almacén desde el panel, seleccione **Anclar al panel** > **Crear**.

   ![Crear un nuevo almacén](./media/azure-stack-site-recovery/new-vault-settings.png)

   El nuevo almacén aparecerá en **Panel** > **Todos los recursos** y en la página principal de **Almacenes de Recovery Services**.

### <a name="select-a-replication-goal"></a>Selección de un objetivo de replicación

1. En **Almacenes de Recovery Services**, seleccione el nombre de un almacén. Estamos usando **ContosoVMVault**.
2. En **Introducción**, seleccione Site Recovery. Luego, seleccione **Preparar infraestructura**.
3. En **Objetivo de protección** >  **¿Dónde están ubicadas las máquinas?** , seleccione **Local**.
4. En **¿A dónde quiere replicar las máquinas?** , seleccione **En Azure**.
5. En **Are your machines virtualized** (¿Están las máquinas virtualizadas?), seleccione **Not virtualized/Other** (No virtualizadas/Otros). Después, seleccione **Aceptar**.

    ![Objetivo de protección](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Paso 3: Configuración del entorno de origen

Configure el equipo del servidor de configuración, regístrelo en el almacén y detecte las máquinas que quiere replicar.

1. Haga clic en **Preparar infraestructura** > **Origen**.
2. En **Preparar origen**, haga clic en **+Servidor de configuración**.

    ![Captura de pantalla del cuadro de diálogo +Servidor de configuración con el mensaje "Haga clic en +Servidor de configuración en la barra de comandos anterior para configurar uno...".](./media/azure-stack-site-recovery/plus-config-srv.png)

3. En **Agregar servidor**, compruebe que aparezca **Servidor de configuración** en **Tipo de servidor**.
5. Descargue el archivo de instalación unificada de Site Recovery.
6. Descargue la clave de registro del almacén. Necesita la clave de registro cuando ejecuta la instalación unificada. La clave será válida durante cinco días a partir del momento en que se genera.

    ![Captura de pantalla del cuadro de diálogo Agregar servidor con el tipo de servidor establecido en servidor de configuración y el botón Descargar la clave de registro del almacén resaltado.](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Ejecución de la instalación unificada de Azure Site Recovery

Para instalar y registrar el servidor de configuración, realice una conexión RDP a la máquina virtual que quiere usar para el servidor de configuración y ejecute la configuración unificada.

Antes de empezar, asegúrese de que el reloj esté [sincronizado con un servidor horario](/windows-server/networking/windows-time-service/windows-time-service-top) en la máquina virtual antes de empezar. Se produce un error en la instalación si la hora se desvía más de cinco minutos de la hora local.

Ahora instale el servidor de configuración:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> El servidor de configuración también se puede instalar desde la línea de comandos. [Más información](physical-manage-configuration-server.md#install-from-the-command-line).
>
> El nombre de la cuenta podría tardar 15 minutos o más en aparecer en el portal. Para que se actualice inmediatamente, seleccione **Servidores de configuración** > **_nombre del servidor_ *_ > _* Actualizar servidor**.

## <a name="step-4-set-up-the-target-environment"></a>Paso 4: Configuración del entorno de destino

Seleccione y compruebe los recursos de destino.

1. En **Preparar infraestructura** > **Destino** seleccione la suscripción a Azure que quiere usar.
2. Especifique el modelo de implementación de destino.
3. Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles. Si no las encuentra, deberá crear al menos una cuenta de almacenamiento y red virtual, con el fin de completar el asistente.


## <a name="step-5-enable-replication"></a>Paso 5: Habilitar replicación

### <a name="create-a-replication-policy"></a>Creación de una directiva de replicación

1. Haga clic en **Preparar infraestructura** > **Configuración de la replicación**.
2. En **Crear directiva de replicación**, especifique un nombre de directiva.
3. En **Umbral de RPO**, especifique el límite del objetivo de punto de recuperación (RPO).
    - Los puntos de recuperación para los datos replicados se crean según el tiempo establecido.
    - Esta configuración no afecta a la replicación, que es continua. Simplemente emite una alerta si se alcanza el límite de umbral sin crear un punto de recuperación.
4. En **Retención de punto de recuperación**, especifique cuánto tiempo se conserva cada punto de recuperación. Las máquinas virtuales replicadas se pueden recuperar a cualquier momento de una ventana de tiempo especificada.
5. En **Frecuencia de instantánea coherente con la aplicación**, especifique la frecuencia (en minutos) con la que se crearán instantáneas coherentes con la aplicación.

    - Una instantánea coherente con la aplicación es una instantánea de un momento dado de los datos de la aplicación dentro de la máquina virtual.
    - El Servicio de instantáneas de volumen (VSS) garantiza que las aplicaciones en la máquina virtual se encuentre en un estado coherente cuando se toma la instantánea.
6. Seleccione **Aceptar** para crear la directiva.


### <a name="confirm-deployment-planning"></a>Confirmación del planeamiento de la implementación

Puede omitir este paso ahora. En la lista desplegable **Planeamiento de implementación**, haga clic en **Sí, ya lo hice**.



### <a name="enable-replication"></a>Habilitar replicación

Asegúrese de que ha completado todas las tareas del [Paso 1: Preparación de la máquina](#step-1-prepare-azure-stack-vms). A continuación, habilite la replicación como sigue:

1. Seleccione **Replicar la aplicación** > **Origen**.
2. En **Origen**, seleccione el servidor de configuración.
3. En **Tipo de máquina**, seleccione **Máquinas físicas**.
4. Seleccione el servidor de procesos (servidor de configuración). A continuación, haga clic en **Aceptar**.
5. En **Destino**, seleccione la suscripción y el grupo de recursos donde quiere crear las máquinas virtuales de Azure después de la conmutación por error. Elija el modelo de implementación que quiere usar en las máquinas virtuales de conmutación por error.
6. Seleccione la cuenta de Azure Storage en la que quiere almacenar los datos replicados.
7. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error.
8. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** con el fin de aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más adelante** si desea seleccionar la red de Azure por separado para cada máquina.
9. En **Máquinas físicas**, haga clic en **+ Máquina física**. Especifique el nombre, la dirección IP y el tipo de sistema operativo de cada máquina que quiera replicar.

    - Use la dirección IP interna de la máquina.
    - Si se especifica que la dirección IP pública, puede que la replicación no funcione según lo previsto.

10. En **Propiedades** > **Configurar propiedades**, seleccione la cuenta que usará el servidor de procesos para instalar automáticamente Mobility Service en la máquina.
11. En **Configuración de la replicación** > **Establecer configuración de replicación**, compruebe que se haya seleccionado la directiva de replicación correcta.
12. Haga clic en **Enable Replication**.
13. Realice un seguimiento del progreso del trabajo **Habilitar la protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar la protección**.

> [!NOTE]
> Site Recovery instala Mobility Service cuando se habilita la replicación para una máquina virtual.
>
> Los cambios pueden tardar 15 minutos o más en aplicarse y aparecer en el portal.
>
> Para supervisar las máquinas virtuales que agregue, compruebe la última hora de detección de máquinas virtuales en **Servidores de configuración** > **Último contacto a las**. Para agregar máquinas virtuales sin esperar a la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en **Actualizar**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Paso 6: Ejecución de un simulacro de recuperación ante desastres

Ejecute una conmutación por error de prueba en Azure para asegurarse de que todo funciona según lo esperado. Esta conmutación por error no afectará a su entorno de producción.

### <a name="verify-machine-properties"></a>Comprobar las propiedades de la máquina

Antes de ejecutar una conmutación por error de prueba, compruebe las propiedades de la máquina virtual y asegúrese de que la máquina virtual cumpla con los [requisitos de Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements). Puede ver y modificar las propiedades como sigue:

1. En **Elementos protegidos**, haga clic en **Elementos replicados** > VM.
2. En el panel **Elemento replicado**, puede ver un resumen de la información de la máquina virtual, el estado de mantenimiento y los puntos de recuperación disponibles más recientes. Haga clic en **Propiedades** para ver más detalles.
3. En **Compute and Network** (Proceso y red), modifique la configuración según sea necesario.

    - Puede modificar el nombre de máquina virtual de Azure, el grupo de recursos, el tamaño de destino, el [conjunto de disponibilidad](../virtual-machines/windows/tutorial-availability-sets.md) y la configuración de discos administrados.
    - También puede ver y modificar la configuración de red. Esta incluye la red y subred a la que se une la máquina virtual de Azure después de la conmutación por error y la dirección IP que se asignará a la máquina virtual.
1. En **Discos**, puede ver información sobre los discos de datos y el sistema operativo de la máquina virtual.


### <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

Cuando se ejecuta una conmutación por error de prueba, ocurre lo siguiente:

1. Se ejecuta una comprobación de requisitos previos para garantizar que se dan todas las condiciones necesarias para la conmutación por error.
2. La conmutación por error procesa los datos mediante el punto de recuperación especificado:
    - **Procesado más recientemente**: la máquina conmuta por error al último punto de recuperación procesado por Site Recovery. Se muestra la marca de tiempo. Con esta opción, no se empleó tiempo en el procesamiento de datos, por lo que se proporciona un objetivo de tiempo de recuperación (RTO) bajo.
    - **Más reciente coherente con la aplicación**: la máquina conmuta por error al punto de recuperación más reciente coherente con la aplicación.
    - **Personalizado**: Seleccione el punto de recuperación usado para la conmutación por error.

3. Se crea una máquina virtual de Azure con los datos procesados.
4. La conmutación por error de prueba puede limpiar automáticamente las máquinas virtuales de Azure que se crearon durante el simulacro.

Ejecute una conmutación por error de prueba para una máquina virtual de la siguiente manera:

1. En **Configuración** > **Elementos replicados**, haga clic en la VM > **+Probar conmutación por error**.
2. En este tutorial, seleccionaremos el uso del punto de recuperación **Procesado más recientemente**.
3. En **Probar conmutación por error**, seleccione la red Azure de destino.
4. Haga clic en **Aceptar** para iniciar la conmutación por error.
5. Para realizar el seguimiento del progreso, haga clic en la máquina virtual para abrir sus propiedades. También puede hacer clic en el trabajo **Probar conmutación por error** en *nombre del almacén* > **Configuración** > **Trabajos** >**Trabajos de Site Recovery**.
6. Una vez finalizada la conmutación por error, la VM de Azure de réplica aparece en Azure Portal > **Virtual Machines**. Compruebe que la máquina virtual tiene el tamaño adecuado, está conectada a la red correcta y está en ejecución.
7. Ahora debería poder conectarse a la VM replicada en Azure. [Más información](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. Para eliminar máquinas virtuales de Azure que se crearon durante la conmutación por error de prueba, haga clic en **Limpieza de conmutación por error de prueba** en la máquina virtual. En **Notas**, guarde las observaciones asociadas a la conmutación por error de prueba.

## <a name="fail-over-and-fail-back"></a>Conmutación por error y conmutación por recuperación

Una vez haya configurado la replicación y ejecutado un simulacro para asegurarse de que todo funciona, puede conmutar las máquinas a Azure según sea necesario.

Antes de ejecutar una conmutación por error, si quiere conectarse a la máquina en Azure tras la conmutación por error, debe [preparar la conexión](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) antes de empezar.

A continuación, ejecute una conmutación por error de prueba de la manera siguiente:


1. En **Configuración** > **Elementos replicados**, haga clic en la máquina > **Conmutación por error**.
2. Seleccione el punto de recuperación que quiere usar.
3. En **Probar conmutación por error**, seleccione la red Azure de destino.
4. Seleccione **Apague la máquina antes de comenzar con la conmutación por error**. Con esta configuración, Site Recovery intenta apagar la máquina de origen antes de iniciar la conmutación por error. Sin embargo, la conmutación por error continúa aunque se produzca un error de apagado.
5. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.
6. Una vez finalizada la conmutación por error, la VM de Azure de réplica aparece en Azure Portal > **Virtual Machines**. Si ha preparado la conexión para después de la conmutación por error, compruebe que la máquina virtual tiene el tamaño adecuado, está conectada a la red correcta y está en ejecución.
7. Después de comprobar la máquina virtual, haga clic en **Confirmar** para finalizar la conmutación por error. Esta acción elimina todos los puntos de recuperación disponibles.

> [!WARNING]
> No cancele una conmutación por error en curso: Antes de iniciar la conmutación por error, se detiene la replicación de la máquina virtual. Si se cancela una conmutación por error en curso, la conmutación por error se detiene, pero no se replica la máquina virtual de nuevo.


### <a name="fail-back-to-azure-stack"></a>Conmutar por recuperación en Azure Stack

Cuando el sitio principal esté de nuevo activo y en ejecución, puede conmutar por recuperación desde Azure a Azure Stack. Para ello, siga los pasos que se indican [aquí](/azure-stack/operator/site-recovery-failback).

## <a name="conclusion"></a>Conclusión

En este artículo, hemos replicado las máquinas virtuales de Azure Stack en Azure. Con la replicación realizada, hemos ejecutado un simulacro de recuperación ante desastres para asegurarnos de que la conmutación por error a Azure funciona según lo previsto. El artículo también incluye los pasos para ejecutar una conmutación por error completa a Azure y una conmutación por recuperación a Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

Después de conmutar por recuperación, puede volver a proteger la máquina virtual e iniciar su replicación en Azure de nuevo. Para hacer esto, repita los pasos descritos de este artículo.