---
title: 'Implementación del dispositivo de replicación de Azure Site Recovery: versión preliminar'
description: 'En este artículo se describen el soporte y los requisitos al implementar el dispositivo de replicación para realizar la recuperación ante desastres de VMware en Azure con Azure Site Recovery: versión preliminar'
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: e4021aa0f5572a51ca4d3ddda37f64f4da46a3b4
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446752"
---
# <a name="deploy-azure-site-recovery-replication-appliance---preview"></a>Implementación del dispositivo de replicación de Azure Site Recovery: versión preliminar

>[!NOTE]
> La información de este artículo se aplica a Azure Site Recovery: versión preliminar. Para obtener información sobre los requisitos del servidor de configuración en las versiones clásicas, [consulte este artículo](vmware-azure-configuration-server-requirements.md).

>[!NOTE]
> Asegúrese de crear un nuevo almacén de Recovery Services para configurar el dispositivo de versión preliminar. No use un almacén existente.

Cuando se usa [Azure Site Recovery](site-recovery-overview.md) para realizar la recuperación ante desastres de servidores físicos y máquinas virtuales de VMware en Azure, se implementa un dispositivo de replicación local.

- El dispositivo de replicación coordina la comunicación entre Azure y VMware local. También administra la replicación de datos.
- [Obtenga más información](vmware-azure-architecture-preview.md) sobre los componentes y procesos del dispositivo de replicación de Azure Site Recovery.

## <a name="hardware-requirements"></a>Requisitos de hardware

**Componente** | **Requisito**
--- | ---
Núcleos de CPU | 8
RAM | 32 GB
Número de discos | 3, incluido el disco del sistema operativo (80 GB), el disco de datos 1 (620 GB), el disco de datos 2 (620 GB)

## <a name="software-requirements"></a>Requisitos de software

**Componente** | **Requisito**
--- | ---
Sistema operativo | Windows Server 2016
Configuración regional del sistema operativo | Inglés (en-*)
Roles de Windows Server | No habilite estos roles: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Directivas de grupo | No habilite estas directivas de grupo: <br> - Impedir el acceso al símbolo del sistema. <br> - Impedir el acceso a herramientas de edición del Registro. <br> - Confiar en la lógica de datos adjuntos de archivos. <br> - Activar la ejecución de scripts. <br> [Más información](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Ningún sitio web predeterminado debe existir previamente <br> - Ningún sitio web o aplicación que escuche en el puerto 443 deben existir previamente <br>- Habilitar la [autenticación anónima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Habilitar la configuración de [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
FIPS (Estándar federal de procesamiento de información) | No habilitar el modo FIPS|

## <a name="network-requirements"></a>Requisitos de red

|**Componente** | **Requisito**|
|--- | ---|
|Tipo de dirección IP | estática|
|Puertos | 443 (orquestación del canal de control)<br>9443 (Transporte de datos)|
|Tipo de NIC | VMXNET3 (si el dispositivo es una máquina virtual de VMware)|


### <a name="allow-urls"></a>Permitir direcciones URL

Asegúrese de que se permiten las siguientes direcciones URL y se puede acceder a ellas desde el dispositivo de replicación de Azure Site Recovery para obtener una conectividad continua:

  | **URL**                  | **Detalles**                             |
  | ------------------------- | -------------------------------------------|
  | portal.azure.com          | Vaya a Azure Portal.              |
  | `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | Para iniciar sesión en la suscripción de Azure.  |
  |`*.microsoftonline.com `|Cree aplicaciones de Azure Active Directory (AD) para que el dispositivo se comunique con Azure Site Recovery. |
  |management.azure.com |Cree aplicaciones de Azure AD para que el dispositivo se comunique con el servicio Azure Site Recovery. |
  |`*.services.visualstudio.com `|Cargue los registros de aplicaciones que se usan para la supervisión interna. |
  |`*.vault.azure.net `|Administre secretos en Azure Key Vault. Nota: Asegúrese de que las máquinas que se replican tengan acceso a ella. |
  |aka.ms |Permita el acceso a vínculos también conocido como. Se usa para las actualizaciones del dispositivo de Azure Site Recovery. |
  |download.microsoft.com/download |Permita descargas de Microsoft. |
  |`*.servicebus.windows.net `|Comunicación entre el dispositivo y el servicio Azure Site Recovery. |
  |`*.discoverysrv.windowsazure.com `|Conéctese a la dirección URL del servicio de detección de Azure Site Recovery. |
  |`*.hypervrecoverymanager.windowsazure.com `|Conéctese a las direcciones URL de microservicios de Azure Site Recovery.  |
  |`*.blob.core.windows.net `|Cargue datos en Azure Storage que se usan para crear discos de destino. |
  |`*.backup.windowsazure.com `|Dirección URL del servicio de protección: un microservicio que Azure Site Recovery usa para procesar y crear discos replicados en Azure. |

> [!NOTE]
> Los vínculos privados no se admiten con la versión preliminar.

## <a name="prepare-azure-account"></a>Preparación de la cuenta de Azure

Para crear y registrar un dispositivo de replicación de Azure Site Recovery, necesita una cuenta de Azure con:

- Permisos de Colaborador o Propietario en la suscripción de Azure.
- Permisos para registrar aplicaciones de Azure Active Directory (AAD).
- Permisos de Propietario o Colaborador más Administrador de acceso de usuario en la suscripción de Azure para crear una instancia de Key Vault, que se usa durante el registro del dispositivo de replicación de Azure Site Recovery con Azure.

Si acaba de crear una cuenta de Azure gratuita, es el propietario de la suscripción. Si no es el propietario de la suscripción, colabore con él para obtener los permisos necesarios.

## <a name="prerequisites"></a>Requisitos previos

**Estos son los permisos necesarios del almacén de claves**:

- Microsoft.OffAzure/*
- Microsoft.KeyVault/register/action
- Microsoft.KeyVault/vaults/read
- Microsoft.KeyVault/vaults/keys/read
- Microsoft.KeyVault/vaults/secrets/read
- Microsoft.Recoveryservices/*

**Siga estos pasos para asignar los permisos necesarios**:

1. En Azure Portal, busque **Suscripciones** y, en **Servicios**, seleccione el cuadro de búsqueda **Suscripciones** para buscar la suscripción de Azure.

2. En la página **Suscripciones**, seleccione la suscripción en la que quiere crear el almacén de Recovery Services.

3. En la suscripción, seleccione **Control de acceso** (IAM) > **Comprobar acceso**. En **Comprobar acceso**, busque la cuenta de usuario correspondiente.

4. En **Agregar asignación de roles**, seleccione **Agregar** y, a continuación, seleccione el rol Colaborador o Propietario, y seleccione la cuenta. Después, seleccione **Guardar**.

  Con el fin de registrar el dispositivo, la cuenta de Azure necesita permisos para registrar aplicaciones de AAD.

  **Siga estos pasos para asignar los permisos necesarios**:

  - En Azure Portal, vaya a **Azure Active Directory** > **Usuarios** > **Configuración de usuario**. En **Configuración de usuario**, compruebe que los usuarios de Azure AD puedan registrar aplicaciones (establecido en *Sí* de forma predeterminada).

  - Si el valor **Registros de aplicaciones** se ha establecido en *No*, solicite al administrador global o de inquilinos que asigne el permiso necesario. Como alternativa, el administrador global o de inquilinos puede asignar el rol Desarrollador de aplicaciones a una cuenta para permitir el registro de aplicaciones de AAD.


## <a name="prepare-infrastructure"></a>Preparación de la infraestructura

Debe configurar un dispositivo de replicación de Azure Site Recovery en el entorno local para habilitar la recuperación en la máquina local. Para obtener información detallada sobre las operaciones realizadas por el dispositivo, [consulte esta sección](vmware-azure-architecture-preview.md).

Vaya a **Almacén de Recovery Services** > **Comenzar**. En Máquinas de VMware en Azure, seleccione **Preparar infraestructura** y continúe con las secciones que se detallan a continuación:

![Versión preliminar del almacén de Recovery Services](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

Para configurar un nuevo dispositivo, puede usar una plantilla de OVF (recomendado) o PowerShell. Asegúrese de cumplir todos los [requisitos de hardware ](#hardware-requirements) y [ software](#software-requirements), y cualquier otro requisito previo.

## <a name="create-azure-site-recovery-replication-appliance"></a>Creación del dispositivo de replicación de Azure Site Recovery

Puede crear el dispositivo de replicación de Site Recovery mediante la plantilla de OVF o a través de PowerShell.

>[!NOTE]
> La configuración del dispositivo debe realizarse de forma secuencial. No se puede ejecutar el registro paralelo de varios dispositivos.


### <a name="create-replication-appliance-through-ovf-template"></a>Creación de un dispositivo de replicación mediante una plantilla de OVF

Se recomienda este enfoque, ya que Azure Site Recovery garantiza que la plantilla controle todas las configuraciones de requisitos previos.
La plantilla de OVF pone en marcha una máquina con las especificaciones necesarias.

![Preparación de la infraestructura para la creación del dispositivo](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

**Siga estos pasos:**

1. Descargue la plantilla de OVF para configurar un dispositivo en el entorno local.
2. Una vez completada la implementación, encienda la máquina virtual del dispositivo para aceptar la licencia de evaluación de Microsoft.
3. En la siguiente pantalla, proporcione la contraseña para el usuario administrador.
4. Seleccione **Finalizar**, a continuación, el sistema se reinicia y puede iniciar sesión con la cuenta de usuario de administrador.

### <a name="set-up-the-appliance-through-powershell"></a>Configuración del dispositivo mediante PowerShell

En caso de restricciones organizativas, puede configurar manualmente el dispositivo de replicación de Site Recovery mediante PowerShell. Siga estos pasos:

1. Descargue los instaladores desde [aquí](https://aka.ms/V2ARcmApplianceCreationPowershellZip) y coloque esta carpeta en el dispositivo de replicación de Azure Site Recovery.
2. Después de copiar correctamente la carpeta zip, descomprima y extraiga los componentes de la carpeta.
3. Vaya a la ruta de acceso en la que se extrae la carpeta y ejecute el siguiente script de PowerShell como administrador:

    **DRInstaller.ps1**  

## <a name="register-appliance"></a>Registrar dispositivo
  Una vez creado el dispositivo, el administrador de configuración del dispositivo de Microsoft Azure se inicia automáticamente. Se validan los requisitos previos, como la conectividad a Internet, la sincronización de hora, las configuraciones del sistema y las directivas de grupo (que se enumeran a continuación).

  - CheckRegistryAccessPolicy: impide el acceso a las herramientas de edición del registro.
      - Clave: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
      - El valor de DisableRegistryTools no debe ser igual a 0.

  - CheckCommandPromptPolicy: impide el acceso al símbolo del sistema.

      - Clave: HKLM\SOFTWARE\Policies\Microsoft\Windows\System
      - El valor de DisableCMD no debe ser igual a 0.

  - CheckTrustLogicAttachmentsPolicy: lógica de confianza para los datos adjuntos de los archivos.

      - Clave: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Attachments
      - El valor de UseTrustedHandlers no debe ser igual a 3.

  - CheckPowershellExecutionPolicy: permite activar la ejecución de scripts.

      - La directiva de ejecución de PowerShell no debe ser AllSigned ni Restricted.
      - Asegúrese de que la directiva de grupo "Activar el administrador de datos adjuntos de ejecución de scripts" no esté establecida en Deshabilitado o "Permitir solo scripts firmados".


  **Para registrar el dispositivo, realice los pasos siguientes**:

1. Para configurar el proxy, puede alternar en la opción **Usar proxy para conectarse a Internet**.

    Todos los servicios de Azure Site Recovery usarán esta configuración para conectarse a Internet. Solo se admite un proxy HTTP.

2. Asegúrese de que se permiten las [direcciones URL necesarias](#allow-urls) y se puede acceder a ellas desde el dispositivo de replicación de Azure Site Recovery para obtener una conectividad continua.

3. Una vez comprobados los requisitos previos, en el paso siguiente se capturará información sobre todos los componentes del dispositivo. Revise el estado de todos los componentes y, a continuación, haga clic en **Continuar**. Después de guardar los detalles, continúe con la elección de la conectividad del dispositivo.

4. Después de guardar los detalles de conectividad, seleccione **Continuar** para continuar con el registro con Microsoft Azure.

5. Asegúrese de que se cumplen los [requisitos previos](#prerequisites) y continúe con el registro.

    ![Registrar dispositivo](./media/deploy-vmware-azure-replication-appliance-preview/app-setup-register.png)

  - **Nombre descriptivo del dispositivo**: proporcione un nombre descriptivo con el que desea realizar el seguimiento de este dispositivo en Azure Portal en la infraestructura del almacén de Recovery Services.

  - **Clave del dispositivo de replicación de Azure Site Recovery**: copie la clave desde el portal; para ello, vaya al **almacén de Recovery Services** > **Introducción** > **Infraestructura de preparación de VMware a Azure**.

  - Después de pegar la clave, seleccione **Iniciar sesión**.
    Se le redirigirá a una nueva pestaña de autenticación.

      De manera predeterminada, se generará un código de autenticación como se resalta a continuación, en la página del administrador de autenticación. Use este código en la pestaña autenticación.

  - Escriba las credenciales de Microsoft Azure para completar el registro.

      Después de registrarse correctamente, puede cerrar la pestaña y pasar al administrador de configuración para continuar con la configuración.

      ![código de autenticación](./media/deploy-vmware-azure-replication-appliance-preview/enter-code.png)

      > [!NOTE]
      > Un código de autenticación expira en un plazo de 5 minutos desde la generación. En caso de inactividad durante más de esta duración, se le pedirá que vuelva a iniciar sesión en Azure.


6. Seleccione **Iniciar sesión** para volver a conectarse a la sesión. Para obtener el código de autenticación, consulte la sección *Resumen* o *Registro con el almacén de Azure Recovery Services* en el administrador de configuración.

7. Después de iniciar sesión correctamente, se muestran los detalles de suscripción, grupo de recursos y almacén de Recovery Services. Puede cerrar sesión en caso de que desee cambiar el almacén. Si no, seleccione **Continuar** para seguir.

    ![Dispositivo registrado](./media/deploy-vmware-azure-replication-appliance-preview/app-setup.png)

    Después de registrarse correctamente, continúe con la configuración de los detalles de vCenter.

    ![Configuración de vCenter](./media/deploy-vmware-azure-replication-appliance-preview/vcenter-information.png)

8. Seleccione **Agregar vCenter Server** para agregar información de vCenter. Escriba el nombre del servidor o la dirección IP de la información de vCenter y del puerto. Después, proporcione el nombre de usuario, la contraseña y el nombre descriptivo, que usará para capturar los detalles de la [máquina virtual administrada a través de vCenter](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery). Los detalles de la cuenta de usuario se cifrarán y almacenarán localmente en la máquina.

>[!NOTE]
> Si está intentando agregar la misma instancia de vCenter Server a varios dispositivos, asegúrese de que se usa el mismo nombre descriptivo en ambos dispositivos.

9. Después de guardar correctamente la información de vCenter, seleccione **Agregar credenciales de máquina virtual** para proporcionar detalles de usuario de las máquinas virtuales detectadas a través de vCenter.

   >[!NOTE]
   > - En el caso del sistema operativo Linux, asegúrese de proporcionar credenciales raíz y, en el caso del sistema operativo Windows, se debe agregar una cuenta de usuario con privilegios de administrador. Estas credenciales se usarán para insertar el agente de movilidad en la máquina virtual de origen durante la operación de habilitación de la replicación. Las credenciales se pueden elegir por máquina virtual en Azure Portal durante el flujo de trabajo de habilitación de la replicación.
   > - Visite el configurador del dispositivo para editar o agregar credenciales para acceder a las máquinas.

10. Después de agregar correctamente los detalles, seleccione **Continuar** para instalar todos los componentes del dispositivo de replicación de Azure Site Recovery y registrarse con los servicios de Azure. Esta actividad puede tardar hasta 30 minutos.

    Asegúrese de no cerrar el explorador mientras la configuración está en curso.


## <a name="view-azure-site-recovery-replication-appliance-in-azure-portal"></a>Vista del dispositivo de replicación de Azure Site Recovery en Azure Portal

Después de realizar correctamente la configuración del dispositivo de replicación de Azure Site Recovery, vaya a Azure Portal, **Almacén de Recovery Services**.

Seleccione **Preparar infraestructura (versión preliminar)** en **Introducción**. Puede ver que un dispositivo de replicación de Azure Site Recovery ya está registrado en este almacén. Ya está todo listo. Comience a proteger las máquinas de origen a través de este dispositivo de replicación.

Al hacer clic en *Seleccionar un dispositivo*, se le dirigirá de nuevo a la vista del dispositivo de replicación de Azure Site Recovery, donde se muestra la lista de dispositivos registrados en este almacén.

También podrá ver una pestaña para **Elementos detectados** que muestra todas las instancias de vCenter Server o hosts de vSphere detectados".

![Versión preliminar del dispositivo de replicación](./media/deploy-vmware-azure-replication-appliance-preview/discovered-items.png)

## <a name="sizing-and-capacity"></a>Ajuste de tamaño y capacidad
Un dispositivo en el que se utiliza el servidor de procesos integrado para proteger la carga de trabajo puede administrar hasta 200 máquinas virtuales en función de las siguientes configuraciones:

  |CPU |    Memoria |    Tamaño de disco de caché |    Frecuencia de cambio de datos |    Máquinas protegidas |
  |---|-------|--------|------|-------|
  |16 vCPUs (2 sockets * 8 núcleos @ 2,5 GHz)    | 32 GB |    1 TB |    >1 TB a 2 TB    | Úselo para replicar de 151 a 200 máquinas.|

- Puede realizar la detección de todas las máquinas de una instancia de vCenter Server mediante cualquiera de los dispositivos de replicación del almacén.

- Puede [cambiar una máquina protegida](switch-replication-appliance-preview.md) entre distintos dispositivos del mismo almacén, dado que el dispositivo seleccionado está en buen estado.

Para obtener información detallada sobre cómo usar varios dispositivos y conmutar por error un dispositivo de replicación, consulte [este artículo](switch-replication-appliance-preview.md).

## <a name="next-steps"></a>Pasos siguientes
Configure la recuperación ante desastres en Azure para [máquinas virtuales VMware](vmware-azure-tutorial.md).
