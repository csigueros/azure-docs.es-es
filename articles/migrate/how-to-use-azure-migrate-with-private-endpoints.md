---
title: Uso de Azure Migrate con puntos de conexión privados
description: Use el soporte de vínculo privado de Azure Migrate para detectar, evaluar y migrar mediante Azure Private Link.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: fad5a625de4b39163c893d1fb35efc68261d0145
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179426"
---
# <a name="use-azure-migrate-with-private-endpoints"></a>Uso de Azure Migrate con puntos de conexión privados

En este artículo se describe cómo usar Azure Migrate para detectar, evaluar y migrar servidores a través de una red privada mediante [Azure Private Link](../private-link/private-endpoint-overview.md).

Puede usar las herramientas [Azure Migrate: Discovery and Assessment](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) y [Azure Migrate: Server Migration](./migrate-services-overview.md#azure-migrate-server-migration-tool) para conectarse de forma privada y segura a Azure Migrate a través de un emparejamiento privado de Azure ExpressRoute o una conexión VPN de sitio a sitio mediante Private Link.

Se recomienda el método de conectividad de punto de conexión privado cuando hay un requisito organizativo para acceder a Azure Migrate y otros recursos de Azure sin atravesar redes públicas. Mediante la utilización de Private Link puede usar los circuitos de emparejamiento privado de ExpressRoute existentes para mejorar los requisitos de ancho de banda o latencia.

## <a name="support-requirements"></a>Requisitos de admisión

Revise los siguientes permisos necesarios y los escenarios y herramientas admitidos.

### <a name="supported-geographies"></a>Ubicaciones geográficas admitidas

La funcionalidad está ahora en versión preliminar en todas las [regiones de la nube pública.](./migrate-support-matrix.md#supported-geographies-public-cloud)

### <a name="required-permissions"></a>Permisos necesarios

Debe disponer de permisos de colaborador + administrador de acceso de usuario o de propietario en la suscripción.

### <a name="supported-scenarios-and-tools"></a>Herramientas y escenarios admitidos

**Implementación** | **Detalles** | **Herramientas**
--- | --- | ---
**Detección y evaluación** | Realice una detección y evaluación a escala sin agente de los servidores que se ejecutan en todas las plataformas. Entre los ejemplos se incluyen plataformas de hipervisor como [VMware vSphere](./tutorial-discover-vmware.md) o [Microsoft Hyper-V](./tutorial-discover-hyper-v.md), nubes públicas como [AWS](./tutorial-discover-aws.md) o [GCP](./tutorial-discover-gcp.md), o incluso [servidores sin sistema operativo](./tutorial-discover-physical.md). | Azure Migrate: Detección y evaluación <br/>
**Inventario de software** | Puede detectar aplicaciones, roles y características que se ejecutan en máquinas virtuales de VMware. | Azure Migrate: Detección y evaluación
**Visualización de dependencias** | Use la funcionalidad de análisis de dependencias para identificar y comprender las dependencias entre servidores. <br/> [La visualización de dependencias sin agente](./how-to-create-group-machine-dependencies-agentless.md) se admite de forma nativa con soporte de vínculo privado de Azure Migrate. <br/>[La visualización de dependencias basada en agente](./how-to-create-group-machine-dependencies.md) requiere conectividad a Internet. Aprenda a usar [puntos de conexión privados para la visualización de dependencias basada en agente](../azure-monitor/logs/private-link-security.md). | Azure Migrate: Detección y evaluación |
**Migración** | Realice [migraciones de Hyper-V sin agente](./tutorial-migrate-physical-virtual-machines.md) o use el enfoque basado en agente para migrar máquinas virtuales de [VMware](./tutorial-migrate-vmware-agent.md), máquinas virtuales de [Hyper-V](./tutorial-migrate-physical-virtual-machines.md), servidores [físicos](./tutorial-migrate-hyper-v.md), máquinas virtuales que se ejecutan en [AWS](./tutorial-migrate-aws-virtual-machines.md), máquinas virtuales que se ejecutan en [GCP](./tutorial-migrate-gcp-virtual-machines.md) o máquinas virtuales que se ejecutan en un proveedor de virtualización diferente. | Azure Migrate: Server Migration

>[!Note]
> [Las migraciones de VMware sin agente](./tutorial-migrate-vmware.md) requieren acceso a Internet o conectividad a través del emparejamiento de Microsoft ExpressRoute. Aprenda a usar [puntos de conexión privados para realizar replicaciones a través del emparejamiento privado de ExpressRoute o una conexión VPN de sitio a sitio (S2S)](./replicate-using-expressroute.md).

#### <a name="other-integrated-tools"></a>Otras herramientas integradas

Es posible que otras herramientas de migración no puedan cargar datos de uso en proyectos de Azure Migrate si el acceso a la red pública está deshabilitado. El proyecto de Azure Migrate debe configurarse para permitir que el tráfico de todas las redes reciba datos de otras ofertas de Microsoft o de [proveedores de software independientes (ISV)](./migrate-services-overview.md#isv-integration) externos.

Para habilitar el acceso a la red pública para el proyecto de Azure Migrate, inicie sesión en Azure Portal, vaya a la página de **propiedades de Azure Migrate** del portal y seleccione **No** > **Guardar**.

![Captura de pantalla que muestra cómo cambiar el modo de acceso a la red.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Otras consideraciones

**Consideraciones** | **Detalles**
--- | ---
**Precios** | Para más información sobre los precios, consulte [Precios de blobs en páginas de Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) y [Precios de Private Link](https://azure.microsoft.com/pricing/details/private-link/).
**Requisitos de red virtual** | El punto de conexión de ExpressRoute/VPN Gateway debe residir en la red virtual seleccionada o en una red virtual conectada a ella. Es posible que necesite alrededor de 15 direcciones IP en la red virtual.

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Creación de un proyecto con conectividad de punto de conexión privado

Para configurar un nuevo proyecto de Azure Migrate, consulte [Creación y administración de proyectos](./create-manage-projects.md#create-a-project-for-the-first-time).

> [!Note]
> No se puede cambiar el método de conectividad a una conectividad de punto de conexión privado para proyectos de Azure Migrate existentes.

En la sección **Configuración avanzada**, proporcione los detalles siguientes para crear un punto de conexión privado para el proyecto de Azure Migrate.
1. Elija **Punto de conexión privado** como **método de conectividad**.
1. En **Deshabilitar el acceso al punto de conexión público**, mantenga el valor predeterminado **No**. Es posible que algunas herramientas de migración no puedan cargar datos de uso en proyectos de Azure Migrate si el acceso a la red pública está deshabilitado. Obtenga más información sobre [otras herramientas integradas](#other-integrated-tools).
1. En **Suscripción de red virtual**, seleccione la suscripción de la red virtual del punto de conexión privado.
1. En **Red virtual**, seleccione la red virtual para el punto de conexión privado. El dispositivo de Azure Migrate y otros componentes de software que deben conectarse al proyecto de Azure Migrate deben estar en esta red virtual o en una conectada.
1. En **Subred**, seleccione la subred para el punto de conexión privado.

   ![Captura de pantalla que muestra la sección Configuración avanzada en la página Crear proyecto.](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

1. Seleccione **Crear** para crear un proyecto de migración y asociarle un punto de conexión privado. Espere unos minutos para que se implemente el proyecto de Azure Migrate. No cierre esta página mientras se esté creando el proyecto.

## <a name="discover-and-assess-servers-for-migration-by-using-private-link"></a>Detección y evaluación de servidores para la migración mediante Private Link

En esta sección se describe cómo configurar el dispositivo de Azure Migrate. A continuación, lo usará para detectar y evaluar servidores para la migración.

### <a name="set-up-the-azure-migrate-appliance"></a>Configuración del dispositivo de Azure Migrate

1. En **Detectar máquinas** >  **¿Las máquinas están virtualizadas?** , seleccione el tipo de servidor.
1. En **Generación de la clave del proyecto de Azure Migrate**, especifique un nombre para el dispositivo de Azure Migrate.
1. Seleccione **Generar clave** para crear los recursos de Azure necesarios.

    > [!Important]
    > No cierre la página **Detectar máquinas** durante la creación de recursos.
    - En este paso, Azure Migrate crea un almacén de claves, una cuenta de almacenamiento, un almacén de Recovery Services (solo para migraciones de VMware sin agente) y algunos recursos internos. Azure Migrate asocia un punto de conexión privado a cada recurso. Los puntos de conexión privados se crean en la red virtual seleccionada durante la creación del proyecto.
    - Una vez creados los puntos de conexión privados, los registros de recursos CNAME de DNS para los recursos de Azure Migrate se actualizan a un alias de un subdominio con el prefijo *privatelink*. De forma predeterminada, Azure Migrate también crea una zona DNS privada correspondiente al subdominio *privatelink* para cada tipo de recurso e inserta registros DNS A para los puntos de conexión privados asociados. Esta acción permite que el dispositivo de Azure Migrate y otros componentes de software que residen en la red de origen lleguen a los puntos de conexión de recursos de Azure Migrate en direcciones IP privadas.
    - Azure Migrate también habilita una [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md) para el proyecto de migración y concede permisos a la identidad administrada para acceder de forma segura a la cuenta de almacenamiento.

1. Una vez generada correctamente la clave, copie los detalles de la clave para configurar y registrar el dispositivo.

#### <a name="download-the-appliance-installer-file"></a>Descarga del archivo instalador del dispositivo

La herramienta Azure Migrate: Discovery and assessment usa un dispositivo de Azure Migrate ligero. El dispositivo de Azure Migrate detecta los servidores y envía los metadatos de configuración y rendimiento a Azure Migrate.

> [!Note]
> La opción de implementar un dispositivo mediante una plantilla (OVA para servidores en el entorno de VMware y el entorno de Hyper-V VHD) no se admite para los proyectos de Azure Migrate con conectividad de punto de conexión privado.

Para configurar el dispositivo:
  1. Descargue el archivo comprimido que contiene el script del instalador desde el portal.
  1. Extraiga el archivo comprimido en la carpeta del servidor que hospedará el dispositivo.
  1. Después de descargar el archivo ZIP, compruebe la seguridad del archivo.
  1. Ejecute el script del instalador para implementar el dispositivo.

#### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo comprimido es seguro, antes de implementarlo.

1. En el servidor en el que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo ZIP:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Compruebe la versión más reciente del dispositivo y el valor hash:

    **Descargar** | **Valor del código hash**
    --- | ---
    [La versión más reciente](https://go.microsoft.com/fwlink/?linkid=2160648) | b4668be44c05836bf0f2ac1c8b1f48b7a9538afcf416c5212c7190629e3683b2

> [!NOTE]
> El mismo script se puede usar para configurar un dispositivo con conectividad de punto de conexión privado para cualquiera de los escenarios elegidos, como VMware, Hyper-V, físico u otro para implementar un dispositivo con la configuración deseada.

Asegúrese de que el servidor cumple los [requisitos de hardware](./migrate-appliance.md) para el escenario elegido (VMware,Hyper-V/Físico u otro) y que puede conectarse a las [direcciones URL requeridas](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity).

#### <a name="run-the-azure-migrate-installer-script"></a>Ejecución del script del instalador de Azure Migrate

1. Extraiga el archivo comprimido en la carpeta del servidor que hospedará el dispositivo.  No ejecute el script en un servidor con un dispositivo de Azure Migrate existente.
2. Inicie PowerShell en el servidor anterior con privilegios administrativos (elevados).
3. Cambie el directorio de PowerShell a la carpeta en la que se ha extraído el contenido del archivo comprimido descargado.
4. Ejecute el script denominado **AzureMigrateInstaller.ps1** ejecutando el comando siguiente:

    
    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. Seleccione entre las opciones de escenario, nube y conectividad para implementar un dispositivo con la configuración deseada. Por ejemplo, la selección que se muestra a continuación configura un dispositivo para detectar y evaluar **servidores que se ejecutan en el entorno de VMware** en un proyecto de Azure Migrate con **conectividad de punto de conexión privado** en la **nube pública de Azure**.

    :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/script-vmware-private-inline.png" alt-text="Captura de pantalla que muestra cómo configurar un dispositivo con la configuración deseada para un punto de conexión privado." lightbox="./media/how-to-use-azure-migrate-with-private-endpoints/script-vmware-private-expanded.png":::

Una vez que el script se haya ejecutado correctamente, el administrador de configuración del dispositivo se iniciará automáticamente.

> [!NOTE]
> En caso de que surja algún problema, puede acceder a los registros de script en C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log para solucionarlo.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Configuración del dispositivo e inicio de la detección continua

Abra un explorador en cualquier máquina que pueda conectarse al servidor del dispositivo. Abra la dirección URL del administrador de configuración del dispositivo: `https://appliance name or IP address: 44368`. También puede abrir el administrador de configuración desde el escritorio del servidor del dispositivo; para ello, seleccione el acceso directo del administrador de configuración.

#### <a name="set-up-prerequisites"></a>Configuración de requisitos previos

1. Lea la información de terceros y acepte los **términos de licencia**.

1. En administrador de configuración, en **Configurar los requisitos previos**, realice las siguientes operaciones:
   - **Conectividad:** el dispositivo comprueba el acceso a las direcciones URL necesarias. Si el servidor usa un proxy:
     - Haga clic en **Configurar el proxy** y especifique la dirección del proxy `http://ProxyIPAddress` o `http://ProxyFQDN`, y el puerto de escucha.
     - Especifique las credenciales si el proxy requiere autenticación. Solo se admite un proxy HTTP.
     - Puede agregar una lista de direcciones URL o direcciones IP que deben omitir el servidor proxy.
        ![Agregar para omitir la lista de servidores proxy](./media/how-to-use-azure-migrate-with-private-endpoints/bypass-proxy-list.png)
     - Seleccione **Guardar** para registrar la configuración si ha actualizado los detalles del servidor proxy, o bien si ha agregado direcciones URL o IP para omitir el proxy.

        > [!Note]
        > Si recibe un error con el vínculo aka.ms/* durante la comprobación de conectividad y no desea que el dispositivo acceda a esta dirección URL a través de Internet, deshabilite el servicio de actualización automática en el dispositivo. Realice los pasos que se indican en [Desactivación de la actualización automática](./migrate-appliance.md#turn-off-auto-update). Una vez deshabilitada la actualización automática, se omitirá la comprobación de conectividad de la dirección URL aka.ms/*.

   - **Time sync** (Sincronización de hora): Para que la detección funcione correctamente, la hora del dispositivo debe estar sincronizada con la hora de Internet.
   - **Instalación de actualizaciones**: el dispositivo garantiza que se instalan las actualizaciones más recientes. Una vez finalizada la comprobación, seleccione **Ver servicios del dispositivo** para ver el estado y las versiones de los servicios que se ejecutan en el servidor del dispositivo.
        > [!Note]
        > Si ha deshabilitado el servicio de actualización automática en el dispositivo, puede actualizar los servicios del dispositivo manualmente para obtener las versiones más recientes de los servicios. Siga los pasos que se indican en [Actualización manual de una versión anterior](./migrate-appliance.md#manually-update-an-older-version).
   - **Install VDDK** (Instalar VDDK): _(solo necesario para dispositivos VMware)._ El dispositivo comprueba si VMware vSphere Virtual Disk Development Kit (VDDK) está instalado. Si no está instalado, descargue VDDK 6.7 de VMware. Extraiga el contenido del archivo ZIP descargado en la ubicación especificada del dispositivo, tal como se indica en las instrucciones de instalación.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Registro del dispositivo e inicio de la detección continua

Una vez completada la comprobación de requisitos previos, siga los pasos para registrar el dispositivo e iniciar la detección continua para los escenarios correspondientes:
- [Máquinas virtuales de VMware](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)
- [Máquinas virtuales de Hyper-V](./tutorial-discover-hyper-v.md#register-the-appliance-with-azure-migrate)
- [Servidores físicos](./tutorial-discover-physical.md#register-the-appliance-with-azure-migrate)
- [Máquinas virtuales de AWS](./tutorial-discover-aws.md#register-the-appliance-with-azure-migrate)
- [Máquinas virtuales de GCP](./tutorial-discover-gcp.md#register-the-appliance-with-azure-migrate)

>[!Note]
> Si experimenta problemas de resolución DNS durante el registro del dispositivo o en el momento de iniciar la detección, asegúrese de que los recursos de Azure Migrate creados durante el paso **Generar clave** en el portal son accesibles desde el servidor local que hospeda el dispositivo de Azure Migrate. Obtenga más información sobre cómo comprobar la [conectividad de red](./troubleshoot-network-connectivity.md).

### <a name="assess-your-servers-for-migration-to-azure"></a>Evaluación de servidores para migrar a máquinas virtuales de Azure
Una vez completada la detección, evalúe los servidores (como, por ejemplo, las máquinas virtuales de [VMware](./tutorial-assess-vmware-azure-vm.md), [máquinas virtuales de Hyper-V](./tutorial-assess-hyper-v.md), [servidores físicos](./tutorial-assess-vmware-azure-vm.md), [máquinas virtuales de AWS](./tutorial-assess-aws.md) y [máquinas virtuales de GCP)](./tutorial-assess-gcp.md) para la migración a máquinas virtuales de Azure o Azure VMware Solution (AVS), mediante la herramienta Azure Migrate: Discovery and Assessment.

También puede [evaluar las máquinas locales](./tutorial-discover-import.md#prepare-the-csv) con la herramienta Azure Migrate: Discovery and Assessment mediante un archivo .csv importado.

## <a name="migrate-servers-to-azure-by-using-private-link"></a>Migración de servidores a Azure mediante Private Link

En las secciones siguientes se describen los pasos necesarios para usar Azure Migrate con [puntos de conexión privados](../private-link/private-endpoint-overview.md) para migraciones mediante el emparejamiento privado de ExpressRoute o conexiones VPN.

En este artículo se muestra una ruta de implementación de prueba de concepto para que las replicaciones basadas en agente migren [las máquinas virtuales de VMware](./tutorial-migrate-vmware-agent.md), [las máquinas virtuales de Hyper-V](./tutorial-migrate-physical-virtual-machines.md), [los servidores físicos](./tutorial-migrate-physical-virtual-machines.md), [las máquinas virtuales que se ejecutan en AWS](./tutorial-migrate-aws-virtual-machines.md), [máquinas virtuales que se ejecutan en GCP](./tutorial-migrate-gcp-virtual-machines.md) o las máquinas virtuales que se ejecutan en un proveedor de virtualización diferente mediante puntos de conexión privados de Azure. Puede usar un enfoque similar para realizar [migraciones de Hyper-V sin agente](./tutorial-migrate-hyper-v.md) mediante Private Link.

>[!Note]
>[Las migraciones de VMware sin agente](./tutorial-assess-physical.md) requieren acceso a Internet o conectividad a través del emparejamiento de Microsoft ExpressRoute.

### <a name="set-up-a-replication-appliance-for-migration"></a>Configuración de un dispositivo de replicación para la migración

En el diagrama siguiente se muestra el flujo de trabajo de replicación basado en agente con puntos de conexión privados mediante la herramienta Azure Migrate: Server Migration.

![Diagrama que muestra la arquitectura de replicación.](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

La herramienta usa un dispositivo de replicación para replicar los servidores en Azure. Obtenga más información sobre cómo [preparar y configurar una máquina para el dispositivo de replicación](./tutorial-migrate-physical-virtual-machines.md#prepare-a-machine-for-the-replication-appliance).

Después de configurar el dispositivo de replicación, realice estos pasos para crear los recursos necesarios para la migración.

1. En **Detectar máquinas** >  **¿Las máquinas están virtualizadas?** , seleccione **No virtualizado/Otro**.
1. En **Región de destino**, seleccione la región de Azure a la que desea migrar las máquinas.
1. Seleccione **Crear recursos** para crear los recursos de Azure necesarios. No cierre la página durante la creación de recursos.
    - Este paso crea un almacén de Recovery Services en segundo plano y habilita una identidad administrada para el almacén. Un almacén de Recovery Services es una entidad que contiene la información de replicación de las máquinas y se usa para desencadenar operaciones de Site Recovery.
    - Si el proyecto Azure Migrate tiene conectividad de punto de conexión privado, se crea un punto de conexión privado para el almacén de Recovery Services. Este paso agrega cinco nombres de dominio completos (FQDN) al punto de conexión privado, uno para cada microservicio vinculado al almacén de Recovery Services.
    - Para dar formato a los cinco nombres de dominio se usa este patrón: <br/> _{Vault-ID}-asr-pod01-{type}-.{target-geo-code}_ .privatelink.siterecovery.windowsazure.com
    - De forma predeterminada, Azure Migrate crea automáticamente una zona DNS privada y agrega registros DNS A para los microservicios del almacén de Recovery Services. A continuación, la zona DNS privada se vincula a la red virtual del punto de conexión privado.

1. Antes de registrar el dispositivo de replicación, asegúrese de que los FQDN del vínculo privado del almacén son accesibles desde la máquina que hospeda el dispositivo de replicación. Es posible que se requiera una configuración de DNS adicional para que el dispositivo de replicación local resuelva los FQDN de vínculo privado en sus direcciones IP privadas. Obtenga más información sobre [cómo comprobar la conectividad de red](./troubleshoot-network-connectivity.md#verify-dns-resolution).

1. Una vez que compruebe la conectividad, descargue la configuración del dispositivo y el archivo de clave, ejecute el proceso de instalación y registre el dispositivo en Azure Migrate. Obtenga información sobre cómo [configurar el dispositivo de replicación](./tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). Después de configurar el dispositivo de replicación, siga estas instrucciones para instalar el [servicio Mobility](./tutorial-migrate-physical-virtual-machines.md#install-the-mobility-service) en las máquinas que desea migrar.

### <a name="replicate-servers-to-azure-by-using-private-link"></a>Replicación de servidores en Azure mediante Private Link

Siga [estos pasos](./tutorial-migrate-physical-virtual-machines.md#replicate-machines) para seleccionar servidores para la replicación.

En **Replicar** > **Configuración de destino** > **Cache/Replication storage account** (Cuenta de almacenamiento de caché o replicación), use la lista desplegable para seleccionar una cuenta de almacenamiento para replicar a través de un vínculo privado.

Si el proyecto de Azure Migrate tiene conectividad de punto de conexión privado, debe [conceder permisos a la identidad administrada del almacén de Recovery Services](#grant-access-permissions-to-the-recovery-services-vault) para acceder a la cuenta de almacenamiento requerida por Azure Migrate.

Para habilitar las replicaciones a través de un vínculo privado, [cree un punto de conexión privado para la cuenta de almacenamiento](#create-a-private-endpoint-for-the-storage-account-optional).

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Concesión de permisos de acceso al almacén de Recovery Services

Debe conceder permisos al almacén de Recovery Services para el acceso autenticado a la caché o a la cuenta de almacenamiento de replicación.

Para identificar el almacén de Recovery Services creado por Azure Migrate y conceder los permisos necesarios, realice los pasos siguientes:

**Identificación del almacén de Recovery Services y el identificador de objeto de identidad administrada**

Puede encontrar los detalles del almacén de Recovery Services en la página de **propiedades** de Azure Migrate: Server Migration.

1. Vaya al centro de conectividad de **Azure Migrate**, y en el icono de **Azure Migrate: Server Migration**, seleccione **Información general**.

    ![Captura de pantalla que muestra la página Información general del centro de conectividad de Azure Migrate.](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

1. Seleccione **Propiedades** en el panel izquierdo. Anote el nombre del almacén de Recovery Services y el identificador de identidad administrada. El almacén tendrá **Punto de conexión privado** como **Tipo de conectividad** y **Otro** como **Tipo de replicación**. Necesitará esta información cuando proporcione acceso al almacén.

    ![Captura de pantalla que muestra la página Propiedades de la herramienta Azure Migrate: Server Migration](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**Permisos para acceder a la cuenta de almacenamiento**

 A la identidad administrada del almacén se le deben conceder los siguientes permisos de rol en la cuenta de almacenamiento necesaria para la replicación. En este caso, debe crear de antemano la cuenta de almacenamiento.

>[!Note]
> Cuando migre máquinas virtuales de Hyper-V a Azure mediante Private Link, debe conceder acceso tanto a la cuenta de almacenamiento de replicación como a la cuenta de almacenamiento de caché.

Los permisos de rol de Azure Resource Manager varían en función del tipo de cuenta de almacenamiento.

|**Tipo de cuenta de almacenamiento** | **Permisos de rol**|
|--- | ---|
|Tipo estándar | [Colaborador](../role-based-access-control/built-in-roles.md#contributor)<br>[Colaborador de datos de blobs de almacenamiento](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|
|Tipo Premium | [Colaborador](../role-based-access-control/built-in-roles.md#contributor)<br>[Propietario de datos de blobs de almacenamiento](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Vaya a la cuenta de almacenamiento de replicación/caché seleccionada para la replicación. En el panel izquierdo, seleccione **Control de acceso (IAM)** .

1. Seleccione **+ Agregar** y seleccione **Agregar asignación de roles**.

   ![Captura de pantalla que muestra el cuadro Agregar asignaciones de roles](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)

1. En la página **Agregar asignación de roles**, en el campo **Rol**, seleccione el rol adecuado en la lista de permisos mencionada anteriormente. Escriba el nombre del almacén y seleccione **Guardar**.

    ![Captura de pantalla que muestra la página Agregar una asignación de roles.](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

1. Además de estos permisos, también debe permitir el acceso a los servicios de confianza de Microsoft. Si el acceso a la red está restringido a redes seleccionadas, en la pestaña **Redes** de la sección **Excepciones**, seleccione **Permitir que los servicios Microsoft de confianza accedan a esta cuenta de almacenamiento**.

   ![Captura de pantalla de la opción "Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento".](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)

### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Creación de un punto de conexión privado para la cuenta de almacenamiento

Para la replicación mediante ExpressRoute con emparejamiento privado, [cree un punto de conexión privado](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) para las cuentas de almacenamiento de caché o replicación (subrecurso de destino: _blob_).

>[!Note]
> Solo se pueden crear puntos de conexión privados en una cuenta de almacenamiento de uso general v2. Para más información sobre los precios, consulte [Precios de blobs en páginas de Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) y [Precios de Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

Cree el punto de conexión privado de la cuenta de almacenamiento en la misma red virtual que el punto de conexión privado del proyecto de Azure Migrate u otra red virtual conectada a esta red.

Seleccione **Sí** y realice la integración con una zona DNS privada. La zona DNS privada ayuda a enrutar las conexiones de la red virtual a la cuenta de almacenamiento a través de un vínculo privado. Si selecciona **Sí**, la zona DNS se vincula automáticamente a la red virtual. También agrega los registros DNS para la resolución de las nuevas direcciones IP y los FQDN que se creen. Más información sobre las [zonas DNS privadas.](../dns/private-dns-overview.md)

Si el usuario que creó el punto de conexión privado también es el propietario de la cuenta de almacenamiento, la creación del punto de conexión privado se aprobará automáticamente. De lo contrario, el propietario de la cuenta de almacenamiento deberá aprobar el punto de conexión privado para su uso. Para aprobar o rechazar una conexión de punto de conexión privado solicitada, vaya a **Conexiones de punto de conexión privado** en **Redes** en la página de la cuenta de almacenamiento.

Revise el estado del estado de conexión del punto de conexión privado antes de continuar.

![Captura de pantalla que muestra el estado de aprobación del punto de conexión privado.](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Después de crear el punto de conexión privado, use la lista desplegable de **Replicar** > **Configuración de destino** > **Cuenta de almacenamiento en caché** para seleccionar la cuenta de almacenamiento para replicar a través de un vínculo privado.

Asegúrese de que el dispositivo de replicación local tiene conectividad de red a la cuenta de almacenamiento en su punto de conexión privado. Obtenga más información sobre cómo comprobar la [conectividad de red](./troubleshoot-network-connectivity.md).

>[!Note]
> En el caso de las migraciones de máquinas virtuales de Hyper-V a Azure, si la cuenta de almacenamiento de replicación es de tipo _Premium_, debe seleccionar otra cuenta de almacenamiento de tipo _Estándar_ para la cuenta de almacenamiento en caché. En este caso, debe crear puntos de conexión privados para la replicación y la cuenta de almacenamiento en caché.

A continuación, siga estas instrucciones para [revisar e iniciar la replicación](./tutorial-migrate-physical-virtual-machines.md#replicate-machines) y [realizar migraciones](./tutorial-migrate-physical-virtual-machines.md#run-a-test-migration).


## <a name="next-steps"></a>Pasos siguientes
- Complete el [proceso de migración](./tutorial-migrate-physical-virtual-machines.md#complete-the-migration).
- Consulte los [procedimientos recomendados después de la migración](./tutorial-migrate-physical-virtual-machines.md#post-migration-best-practices).