---
title: Configuración de Azure Backup Server para Azure VMware Solution
description: Configure el entorno de Azure VMware Solution para realizar copias de seguridad de máquinas virtuales mediante Azure Backup Server.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: fc82e10093402ffd4a026b0c9bef0d8e5a221647
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432147"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Configuración de Azure Backup Server para Azure VMware Solution

Azure Backup Server contribuye a la estrategia de continuidad empresarial y recuperación ante desastres (BCDR). Con Azure VMware Solution, solo puede configurar una copia de seguridad de nivel de máquina virtual (VM) mediante Azure Backup Server. 

Azure Backup Server puede almacenar datos de copia de seguridad en:

- **Disco**: para el almacenamiento a corto plazo, Azure Backup Server realiza copias de seguridad de los datos en grupos de discos.
- **Azure**: para el almacenamiento a corto y a largo plazo fuera de las instalaciones, pueden realizarse copias de seguridad de los datos de Azure Backup Server que estén almacenados en grupos de discos en la nube de Microsoft Azure mediante Azure Backup.

Use Azure Backup Server para restaurar los datos en el origen o en una ubicación alternativa. De este modo, si los datos originales no están disponibles debido a problemas planeados o inesperados, puede restaurarlos en una ubicación alternativa.

Este artículo le ayudas a preparar el entorno de Azure VMware Solution para realizar copias de seguridad de máquinas virtuales mediante Azure Backup Server. Se le guiará a través de los pasos para: 

> [!div class="checklist"]
> * Determinar el tipo de disco de máquina virtual recomendado y el tamaño que se usará.
> * Crear un almacén de Recovery Services que almacene los puntos de recuperación.
> * Establecer la replicación de almacenamiento para un almacén de Recovery Services.
> * Agregar almacenamiento a Azure Backup Server.

## <a name="supported-vmware-features"></a>Características admitidas de VMware

- **Copia de seguridad sin agente:** Azure Backup Server no necesita que se instale un agente en el servidor vCenter o ESXi para crear una copia de seguridad de la máquina virtual. En su lugar, proporcione la dirección IP o el nombre de dominio completo (FQDN) y las credenciales de inicio de sesión que se usan para autenticar el servidor VMware con Azure Backup Server.
- **Copia de seguridad integrada en la nube:** Azure Backup Server protege las cargas de trabajo en el disco y en la nube. El flujo de trabajo de copia de seguridad y recuperación de Azure Backup Server ayuda a administrar la retención a largo plazo y la copia de seguridad fuera del sitio.
- **Detección y protección de máquinas virtuales administradas por vCenter:** Azure Backup Server detecta y protege las máquinas virtuales implementadas en un servidor vCenter o ESXi. Azure Backup Server también detecta las máquinas virtuales administradas por vCenter para que se puedan proteger las implementaciones de gran tamaño.
- **Protección automática en el nivel de carpeta:** vCenter permite organizar las máquinas virtuales en carpetas de máquina virtual. Azure Backup Server detecta estas carpetas. Puede usarlo para proteger las máquinas virtuales en el nivel de carpeta, incluidas todas las subcarpetas. Al proteger las carpetas, Azure Backup Server protege las máquinas virtuales de esa carpeta y las que se agreguen después. Azure Backup Server detecta a diario nuevas máquinas virtuales y las protege de manera automática. Cuando organice las máquinas virtuales en carpetas recursivas, Azure Backup Server detectará y protegerá automáticamente las nuevas máquinas virtuales que se implementen en estas carpetas.
- **Azure Backup Server sigue protegiendo las máquinas virtuales de vMotion dentro del clúster:** Cuando las máquinas virtuales se colocan en vMotion para el equilibrio de carga en el clúster, Azure Backup Server detecta y continúa automáticamente la protección de las máquinas virtual.
- **Recuperación con mayor rapidez de los archivos necesarios:** Azure Backup Server puede recuperar archivos o carpetas de una máquina virtual Windows sin necesidad de recuperar toda la máquina virtual.

## <a name="limitations"></a>Limitaciones

- Hay que instalar el paquete acumulativo de actualizaciones 1 para Azure Backup Server v3.
- No se pueden realizar copias de seguridad de instantáneas de usuario antes de la primera copia de seguridad de Azure Backup Server. Una vez que Azure Backup Server completa la primera copia de seguridad, puede crear copias de seguridad de las instantáneas de usuario.
- Azure Backup Server no puede proteger las máquinas virtuales VMware con discos de acceso directo y asignaciones de dispositivos físicos sin formato (pRDM).
- Azure Backup Server no puede detectar ni proteger VMware vApps.

Para configurar Azure Backup Server para Azure VMware Solution, debe completar los pasos siguientes:

- Configure los requisitos previos y el entorno.
- Cree un almacén de Recovery Services.
- Descargue e instale Azure Backup Server.
- Agregue almacenamiento a Azure Backup Server.

### <a name="deployment-architecture"></a>Arquitectura de implementación

Azure Backup Server se implementa como una máquina virtual de infraestructura como servicio (IaaS) de Azure para proteger las máquinas virtuales de Azure VMware Solution.

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="Diagrama que muestra Azure Backup Server implementado como una máquina virtual de infraestructura como servicio (IaaS) de Azure para proteger las máquinas virtuales de Azure VMware Solution." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Requisitos previos para el entorno de Azure Backup Server

Tenga en cuenta las recomendaciones de esta sección cuando instale Azure Backup Server en el entorno de Azure.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Asegúrese de [configurar la red para la nube privada de VMware en Azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-vm"></a>Determinación del tamaño de la máquina virtual

Siga las instrucciones del tutorial [Creación de la primera máquina virtual Windows en Azure Portal](../virtual-machines/windows/quick-create-portal.md).  Creará la máquina virtual en la red virtual, que ha creado en el paso anterior. Comience con una imagen de la galería de Windows Server 2019 Datacenter para ejecutar Azure Backup Server. 

En la tabla se resume el número máximo de cargas de trabajo protegidas para cada tamaño de máquina virtual de Azure Backup Server. La información se basa en pruebas de escala y rendimiento internas con valores canónicos correspondientes al tamaño y la renovación de la carga de trabajo. El tamaño real de la carga de trabajo puede ser mayor, pero se debe adaptar a los discos conectados a la máquina virtual de Azure Backup Server.

| Número máximo de cargas de trabajo protegidas | Tamaño promedio de la carga de trabajo | Promedio de renovación de la carga de trabajo (diario) | Número máximo de IOPS de almacenamiento | Tipo y tamaño de disco recomendados      | Tamaño de máquina virtual recomendado |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Renovación neta del 5 %                   | 2\.000             | HDD estándar (tamaño de 8 TB o más por disco)  | A4V2       |
| 40                      | 150 GB                | Renovación neta del 10 %                  | 4500             | SSD Premium (tamaño de 1 TB o más por disco) | DS3_V2     |
| 60                      | 200 GB                | Renovación neta del 10 %                  | 10 500            | SSD Premium (tamaño de 8 TB o más por disco) | DS3_V2     |

* Para obtener el número necesario de IOPS, use discos del tamaño mínimo recomendado o superior. Los discos de menor tamaño ofrecen menos IOPS.

> [!NOTE]
> Azure Backup Server está diseñado para ejecutarse en un servidor dedicado de objetivo único. No se puede instalar Azure Backup Server en un equipo que:
> * Se ejecute como controlador de dominio.
> * Tenga instalado el rol Servidor de aplicaciones.
> * Sea un servidor de administración de System Center Operations Manager.
> * Ejecute Exchange Server.
> * Sea nodo de un clúster.

### <a name="disks-and-storage"></a>Discos y almacenamiento

Azure Backup Server necesita discos para la instalación. 

| Requisito                      | Tamaño recomendado  |
|----------------------------------|-------------------------|
| Instalación de Azure Backup Server                | Ubicación de la instalación: 3 GB<br />Unidad de archivos de base de datos: 900 MB<br />Unidad del sistema: 1 GB para la instalación de SQL Server<br /><br />También necesitará espacio para que Azure Backup Server copie el catálogo de archivos en una ubicación de instalación temporal al realizar el archivado.      |
| Disco para el bloque de almacenamiento<br />(Usa volúmenes básicos; no puede estar en un disco dinámico) | De dos a tres veces el tamaño de los datos protegidos.<br />Para más información sobre el cálculo de almacenamiento, consulte el artículo sobre [Azure Site Recovery Capacity Planner para DPM](https://www.microsoft.com/download/details.aspx?id=54301).   |

Para más información sobre cómo conectar un nuevo disco de datos administrado a una máquina virtual de Azure existente, consulte [Conexión de un disco de datos administrado a una máquina virtual Windows en Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> Una sola instancia de Azure Backup Server tiene un límite flexible de 120 TB para el bloque de almacenamiento.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Almacenamiento de datos de copia de seguridad en un disco local y en Azure

El almacenamiento de datos de copia de seguridad de Azure reduce la infraestructura de copia de seguridad en la máquina virtual de Azure Backup Server. En el caso de una recuperación operativa (copia de seguridad), Azure Backup Server almacena los datos de copia de seguridad en discos de Azure conectados a la máquina virtual. Una vez que los discos y el espacio de almacenamiento se conectan a la máquina virtual, Azure Backup Server administra el almacenamiento automáticamente. La cantidad de almacenamiento depende del número y el tamaño de los discos conectados a cada máquina virtual de Azure. Para cada tamaño de la VM de Azure hay un número máximo de discos que se pueden conectar a ella. Por ejemplo, a A2 le corresponden 4 discos, a A3, 8 discos y a A4, 16 discos. De nuevo, el tamaño y número de discos determina la capacidad total del bloque de almacenamiento de copia de seguridad.

> [!IMPORTANT]
> *No* debe conservar datos de recuperación operativa en discos conectados directamente a Azure Backup Server durante más de cinco días. Si los datos tienen más de cinco días de antigüedad, almacénelos en un almacén de Recovery Services.

Para almacenar datos de copia de seguridad en Azure, cree o use un almacén de Recovery Services. Cuando se prepare para hacer una copia de seguridad de la carga de trabajo de Azure Backup Server, [configure el almacén de Recovery Services](#create-a-recovery-services-vault). Una vez configurado, cada vez que se ejecuta un trabajo de copia de seguridad en línea, se crea un punto de recuperación en el almacén. Cada almacén de Recovery Services contiene hasta 9999 puntos de recuperación. En función del número de puntos de recuperación creados y del tiempo que se conserven, puede conservar los datos de copias de seguridad durante muchos años. Por ejemplo, podría crear puntos de recuperación mensuales y conservarlos durante cinco años.

> [!IMPORTANT]
> Tanto si envía datos de copia de seguridad a Azure como si los mantiene localmente, debe registrar Azure Backup Server en un almacén de Recovery Services.

### <a name="scale-deployment"></a>Escalado de la implementación

Si quiere escalar su implementación, tiene las siguientes opciones:

- **Escalado vertical**: aumente el tamaño de la máquina virtual de Azure Backup Server de la serie A a la serie DS3 y aumente el almacenamiento local.
- **Descarga de datos**: envíe los datos antiguos a Azure y conserve solo los más recientes en el almacenamiento conectado a la máquina de Azure Backup Server.
- **Escalado horizontal**: agregue más máquinas de Azure Backup Server para proteger las cargas de trabajo.

### <a name="net-framework"></a>.NET Framework

La máquina virtual necesita tener instalado .NET Framework 3.5 SP1 o una versión posterior.

### <a name="join-a-domain"></a>Unión a un dominio

La máquina virtual de Azure Backup Server debe estar unida a un dominio. El usuario de un dominio con privilegios de administrador en la máquina virtual debe instalar Azure Backup Server.

Azure Backup Server implementado en una máquina virtual de Azure puede realizar copias de seguridad de las cargas de trabajo en las máquinas virtuales de Azure VMware Solution. Las cargas de trabajo deben estar en el mismo dominio para habilitar la operación de copia de seguridad.

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

Un almacén de Recovery Services es una entidad de almacenamiento que almacena los puntos de recuperación creados a lo largo del tiempo. También contiene directivas de copia de seguridad asociadas a elementos protegidos.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y, en el menú izquierdo, elija **Todos los servicios**.

1. En el cuadro de diálogo **Todos los servicios**, escriba **Recovery Services** y, en la lista, seleccione **Almacenes de Recovery Services**.

   Aparece la lista de almacenes de Recovery Services de la suscripción.

1. En el panel **Almacenes de Recovery Services**, seleccione **Agregar**.

   Se abre el cuadro de diálogo **Almacén de Recovery Services**.

1. Especifique los valores y, luego, seleccione **Crear**.

   - **Name**: escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga un mínimo de 2 caracteres y un máximo de 50. El nombre debe comenzar por una letra y consta solo de letras, números y guiones.
   - **Suscripción**: elija la suscripción que desee usar. Si es miembro de una sola suscripción, verá solo ese nombre. Si no está seguro de qué suscripción va a utilizar, use la predeterminada (sugerida). Solo hay varias opciones si la cuenta profesional o educativa está asociada a más de una suscripción de Azure.
   - **Grupo de recursos**: Use un grupo de recursos existente o cree uno. Para ver la lista de grupos de recursos disponibles en una suscripción, seleccione **Usar existente** y, después, seleccione un recurso en la lista desplegable. Para crear un grupo de recursos, seleccione **Crear nuevo** y escriba el nombre.
   - **Ubicación**: seleccione la región geográfica del almacén. Para crear un almacén con el fin de proteger las máquinas virtuales de Azure VMware Solution, dicho almacén *debe* estar en la misma región que la nube privada de Azure VMware Solution.

   La creación del almacén de Recovery Services puede tardar unos minutos. Supervise las notificaciones de estado en el área **Notificaciones** de la parte superior derecha del portal. Después de crear el almacén, se ve en la lista de almacenes de Recovery Services. Si no lo ve, haga clic en **Actualizar**.


## <a name="set-storage-replication"></a>Configuración de la replicación del almacenamiento

La opción de replicación de almacenamiento permite elegir entre almacenamiento con redundancia geográfica (el valor predeterminado) y almacenamiento con redundancia local. El almacenamiento con redundancia geográfica copia los datos de la cuenta de almacenamiento en una región secundaria, lo que hace que los datos sean duraderos. El almacenamiento con redundancia local es una opción más económica que no es tan duradera. Para más información sobre las opciones de almacenamiento con redundancia geográfica y redundancia local, consulte [Redundancia de Azure Storage](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> El cambio del valor de **Tipo de replicación de almacenamiento** (con redundancia local o con redundancia geográfica) para un almacén de Recovery Services se debe realizar antes de configurar las copias de seguridad en el almacén. Tras la configuración de las copias de seguridad, la opción para modificarla está deshabilitada y el tipo de replicación del almacenamiento no se puede cambiar.

1. En **Almacenes de Recovery Services**, seleccione el nuevo almacén. 

1. En **Configuración**, seleccione **Propiedades**. En **Configuración de copia de seguridad**, seleccione **Actualizar**.

1. Seleccione el tipo de replicación almacenamiento y seleccione **Guardar**.

## <a name="download-and-install-the-software-package"></a>Descarga e instalación del paquete de software

Siga los pasos de esta sección para descargar, extraer e instalar el paquete de software.

### <a name="download-the-software-package"></a>Descarga del paquete de software

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Si ya tiene abierto un almacén de Recovery Services, vaya al siguiente paso. 

   >[!TIP]
   >Si no tiene un almacén de Recovery Services abierto y se encuentra en la Azure Portal, en la lista de recursos, escriba **Recovery Services** > **Almacenes de Recovery Services**.

1. En la lista de almacenes de Recovery Services, seleccione un almacén.

   Se abre el panel del almacén seleccionado.

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png" alt-text="Captura de pantalla que muestra el panel de almacenes.":::

   De forma predeterminada, la opción **Configuración** abre el almacén. Si está cerrado, seleccione **Configuración** para abrirlo.

1. Seleccione **Copia de seguridad** para abrir el **Asistente para introducción**.

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png" alt-text="Captura de pantalla que muestra la opción Copia de seguridad seleccionada en el Asistente para introducción.":::

1. En la ventana que se abre:

   1. En el menú **¿Dónde se ejecuta su carga de trabajo?** , seleccione **Local**.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="Captura de pantalla que muestra las opciones de dónde se ejecuta la carga de trabajo y de lo que se hace una copia de seguridad.":::

   1. En el menú **¿De qué desea hacer una copia de seguridad?** , seleccione las cargas de trabajo que quiere proteger mediante Azure Backup Server.

   1. Seleccione **Preparar infraestructura** para descargar e instalar Azure Backup Server y las credenciales del almacén.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Captura de pantalla que muestra el paso para preparar la infraestructura.":::

1. En la ventana **Preparar infraestructura** que se abre:

   1. Seleccione el vínculo **Descargar** para instalar Azure Backup Server.

   1. Seleccione **Ya se ha descargado o se usa la última instalación de Azure Backup Server** y después **Descargar** para descargar las credenciales del almacén. Usará estas credenciales cuando registre Azure Backup Server en el almacén de Recovery Services. Los vínculos redirigen al Centro de descarga, donde se puede descargar el paquete de software.

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure-2.png" alt-text="Captura de pantalla que muestra los pasos para preparar la infraestructura de Azure Backup Server.":::

1. En la página de descarga, seleccione todos los archivos y elija **Siguiente**.

   > [!NOTE]
   > Debe descargar todos los archivos en la misma carpeta. Puesto que el tamaño de descarga de todos los archivos juntos es superior a 3 GB, la descarga podría tardar hasta 60 minutos en completarse. 

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png" alt-text="Captura de pantalla que muestra archivos de Microsoft Azure Backup para descargar.":::

### <a name="extract-the-software-package"></a>Extracción del paquete de software

Si ha descargado el paquete de software en otro servidor, copie los archivos en la máquina virtual que ha creado para implementar Azure Backup Server.

> [!WARNING]
> Se requieren al menos 4 GB de espacio libre para extraer los archivos de instalación.

1. Cuando haya descargado todos los archivos, haga doble clic en **MicrosoftAzureBackupInstaller.exe** para abrir el Asistente para instalación de **Microsoft Azure Backup** y, luego, elija **Siguiente**.

1. Seleccione la ubicación en la que se van a extraer los archivos y elija **Siguiente**.

1. Seleccione **Extraer** para iniciar el proceso de extracción.

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/extract/03.png" alt-text="Captura de pantalla que muestra archivos de Microsoft Azure Backup listos para extraer.":::

1. Al concluir la extracción, seleccione la opción para **ejecutar setup.exe** y, luego, elija **Finalizar**.

> [!TIP]
> También puede buscar el archivo setup.exe en la carpeta donde extrajo el paquete de software.

### <a name="install-the-software-package"></a>Instalación del paquete de software

1. En la ventana de instalación, en **Instalar**, seleccione **Microsoft Azure Backup** para abrir el Asistente para instalación.

1. En la **pantalla de bienvenida**, seleccione **Siguiente** para ir a la página **Comprobaciones de requisitos previos**.

1. Para determinar si se cumplen los requisitos previos de hardware y software para Azure Backup Server, seleccione **Volver a comprobar**. Si se cumplen, seleccione **Siguiente**.

1. En el paquete de instalación de Azure Backup Server se incluyen los archivos binarios de SQL Server que se necesitan. Al iniciar una nueva instalación de Azure Backup Server, seleccione la opción **Instalar una nueva instancia de SQL Server con esta instalación**. Después, seleccione **Comprobar e instalar**.

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/sql/01.png" alt-text="Captura de pantalla que muestra el cuadro de diálogo de configuración de SQL con la opción &quot;Instalar una nueva instancia de SQL Server con esta instalación&quot; seleccionada.":::

   > [!NOTE]
   > Si quiere usar su propia instancia de SQL Server, las versiones de SQL Server admitidas son SQL Server 2014 SP1 o posterior, 2016 y 2017. Todas las versiones de SQL Server deben ser Estándar o Enterprise de 64 bits. La instancia que usa Azure Backup Server solo debe ser local; no puede ser remota. Si utiliza una instancia de SQL Server existente para Azure Backup Server, el programa de instalación solo admite el uso de *instancias con nombre* de SQL Server.

   Si se produce un error que incluye una recomendación para reiniciar el equipo, hágalo y seleccione **Volver a comprobar**. Si hay algún problema de configuración de SQL Server, vuelva a configurarlo según las instrucciones de SQL Server. Después, intente instalar o actualizar Azure Backup Server mediante la instancia existente de SQL Server.

   **Configuración manual**

   Cuando use una instancia propia de SQL Server, asegúrese de agregar builtin\Administrators al rol sysadmin de la base de datos maestra.

   **Configuración del servicio de informes con SQL Server 2017**

   Si usa una instancia propia de SQL Server 2017, debe configurar SQL Server 2017 Reporting Services (SSRS) de forma manual. Después de configurar SSRS, asegúrese de establecer la propiedad **IsInitialized** de SSRS en **True**. Cuando se establece en **True**, Azure Backup Server presupone que SSRS ya está configurado y omite la configuración de SSRS.

   Para comprobar el estado de configuración de SSRS, ejecute lo siguiente:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Se usarán los siguientes valores para la configuración de SSRS:

   * **Cuenta de servicio**: **Usar cuenta integrada** debe estar en **Servicio de red**.
   * **Dirección URL del servicio web**: **Directorio virtual** debe estar en **ReportServer_\<SQLInstanceName>** .
   * **Base de datos**: **DatabaseName** debe estar en **ReportServer$\<SQLInstanceName>** .
   * **Dirección URL del Portal web**: **Directorio virtual** debe estar en **Reports_\<SQLInstanceName>** .

   [Obtenga más información](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) acerca de la configuración de SSRS.

   > [!NOTE]
   > [Términos de Microsoft Online Services](https://www.microsoft.com/licensing/product-licensing/products) (OST) controla las licencias de SQL Server que se usan como base de datos para Azure Backup Server. Según OST, solo se puede usar SQL Server junto con Azure Backup Server como base de datos para Azure Backup Server.

1. Cuando la instalación se haya completado, seleccione **Siguiente**.

1. Proporcione una ubicación para la instalación de los archivos de Microsoft Azure Backup Server y seleccione **Siguiente**.

   > [!NOTE]
   > La ubicación temporal es necesaria para realizar copias de seguridad en Azure. Asegúrese de que la ubicación temporal sea al menos el 5 % de los datos cuya copia de seguridad se planea realizar en la nube. Para la protección del disco, hay que configurar discos independientes una vez que se haya completado la instalación. Para más información sobre los bloques de almacenamiento, consulte [Configuración de bloques de almacenamiento y almacenamiento en disco](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12)).

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/space-screen.png" alt-text="Captura de pantalla que muestra la configuración de SQL Server.":::

1. Proporcione una contraseña segura para las cuentas de usuario locales con permisos restringidos y seleccione **Siguiente**.

1. Seleccione si quiere usar Microsoft Update para buscar actualizaciones y elija **Siguiente**.

   > [!NOTE]
   > Se recomienda que Windows Update se redirija a Microsoft Update, que ofrece actualizaciones importantes y de seguridad para Windows y otros productos como Azure Backup Server.

1. Revise la página **Resumen de la configuración** y seleccione **Instalar**.

   La instalación se realiza en fases. 
   - La primera fase instala el agente de Microsoft Azure Recovery Services.
   - La segunda fase comprueba la conectividad a Internet. Si está disponible, puede continuar con la instalación. En caso contrario, debe proporcionar detalles del proxy para conectarse a Internet. 
   - La fase final comprueba el software necesario. Si no está instalado, el software que falte se instalará junto con el servicio Agente de Microsoft Azure Recovery Services.

1. Seleccione **Examinar** para buscar las credenciales del almacén a fin de registrar la máquina en el almacén de Recovery Services y, luego, elija **Siguiente**.

1. Seleccione una frase de contraseña para cifrar y descifrar los datos enviados entre Azure y el entorno local.

   > [!TIP]
   > Puede generar una frase de contraseña automáticamente o proporcionar una con un mínimo de 16 caracteres.

1. Escriba la ubicación para guardar la frase de contraseña y, luego, seleccione **Siguiente** para registrar el servidor.

   > [!IMPORTANT]
   > Guarde la frase de contraseña en una ubicación segura que no sea el servidor local. Se recomienda encarecidamente usar Azure Key Vault para almacenar la frase de contraseña.

   Una vez finalizada la instalación del servicio Agente de Microsoft Azure Recovery Services, el paso de instalación sigue con la instalación y configuración de SQL Server, y los componentes de Azure Backup Server.

1. Cuando finalice la instalación, seleccione **Cerrar**.

### <a name="install-update-rollup-1"></a>Instalación del paquete acumulativo de actualizaciones 1

La instalación del paquete acumulativo de actualizaciones 1 para Azure Backup Server v3 es obligatoria para proteger las cargas de trabajo.  Puede encontrar las correcciones de errores y las instrucciones de instalación en el [artículo de knowledge base](https://support.microsoft.com/en-us/help/4534062/).

## <a name="add-storage-to-azure-backup-server"></a>Adición de almacenamiento a Azure Backup Server

Azure Backup Server v3 admite Modern Backup Storage, que ofrece:

-  Ahorro de almacenamiento del 50 %.
-  Copias de seguridad tres veces más rápidas.
-  Almacenamiento más eficiente.
-  Almacenamiento con reconocimiento de la carga de trabajo.

### <a name="volumes-in-azure-backup-server"></a>Volúmenes en Azure Backup Server

Agregue los discos de datos con la capacidad de almacenamiento necesaria de la máquina virtual de Azure Backup Server si todavía no se han agregado.

Azure Backup Server v3 solo acepta volúmenes de almacenamiento. Al agregar un volumen, Azure Backup Server le asigna formato en Sistema de archivos resistente (ReFS), que requiere Modern Backup Storage.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Adición de volúmenes al almacenamiento de disco de Azure Backup Server

1. En el panel **Administración**, vuelva a examinar el almacenamiento y seleccione **Agregar**. 

1. Entre los volúmenes disponibles, seleccione los que quiere agregar al bloque de almacenamiento. 

1. Después de agregarlos, asígneles un nombre descriptivo que le ayude a administrarlos. 

1. Seleccione **Aceptar** para dar formato ReFS a estos volúmenes a fin de que Azure Backup Server pueda usar las ventajas de Modern Backup Storage.


## <a name="next-steps"></a>Pasos siguientes

Ahora que se ha explicado cómo configurar Azure Backup Server para Azure VMware Solution, puede que quiera informarse sobre los siguientes temas:

- [Configuración de copias de seguridad en máquinas virtuales de Azure VMware Solution](backup-azure-vmware-solution-virtual-machines.md).
- [Protección de máquinas virtuales de Azure VMware Solution con la integración de Azure Security Center](azure-security-integration.md).