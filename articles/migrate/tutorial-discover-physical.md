---
title: 'Detección de servidores físicos con Azure Migrate: Discovery and assessment'
description: 'Aprenda a detectar servidores físicos locales con Azure Migrate: Discovery and assessment.'
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/11/2021
ms.custom: mvc
ms.openlocfilehash: 0878911bdd3caa2202ef993142aa89e4eabfe33c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464884"
---
# <a name="tutorial-discover-physical-servers-with-azure-migrate-discovery-and-assessment"></a>Tutorial: Detección de servidores físicos con Azure Migrate: Discovery and assessment

Como parte del recorrido de la migración a Azure, detectará los servidores para la valoración y la migración.

En este tutorial se muestra cómo detectar servidores físicos locales con la herramienta Azure Migrate: Discovery and assessment, con un dispositivo ligero de Azure Migrate. Implemente el dispositivo como servidor físico para detectar continuamente servidores y metadatos de rendimiento.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar una cuenta de Azure.
> * Preparar los servidores físicos para la detección.
> * Crear un proyecto.
> * Configurar el dispositivo de Azure Migrate.
> * Iniciar la detección continua.

> [!NOTE]
> Los tutoriales muestran la manera más rápida de probar un escenario y utilizar las opciones predeterminadas.  

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar este tutorial, compruebe que dispone de estos requisitos previos.

**Requisito** | **Detalles**
--- | ---
**Dispositivo** | Necesita un servidor en el que se ejecute el dispositivo de Azure Migrate. El servidor debe tener:<br/><br/> - Windows Server 2016 instalado.<br/> _(Actualmente, la implementación del dispositivo solo se admite en Windows Server 2016)._<br/><br/> - 16 GB de RAM, 8 vCPU, alrededor de 80 GB de almacenamiento en disco<br/><br/> - Una dirección IP estática o dinámica, con acceso a Internet, ya sea directamente o mediante un proxy.
**Servidores Windows** | Permita las conexiones entrantes en el puerto WinRM 5985 (HTTP), para que el dispositivo pueda extraer los metadatos de configuración y rendimiento.
**Servidores Linux** | Permita las conexiones entrantes en el puerto 22 (TCP).

## <a name="prepare-an-azure-user-account"></a>Preparación de una cuenta de usuario de Azure

Para crear un proyecto y registrar el dispositivo de Azure Migrate, necesita una cuenta con:
- Permisos de nivel de colaborador o propietario en una suscripción de Azure.
- Permisos para registrar aplicaciones de Azure Active Directory (AAD).

Si acaba de crear una cuenta de Azure gratuita, es el propietario de la suscripción. Si no es el propietario, trabaje con él para asignar los permisos, como se indica a continuación:

1. En Azure Portal, busque "suscripciones" y, en **Servicios**, seleccione **Suscripciones**.

    ![Cuadro para buscar la suscripción de Azure](./media/tutorial-discover-physical/search-subscription.png)

2. En la página **Suscripciones**, seleccione la suscripción en la que desee crear el proyecto.
3. En la suscripción, seleccione **Access control (IAM)**  > **Comprobar acceso**.
4. En **Comprobar acceso**, busque la cuenta de usuario correspondiente.
5. En **Agregar una asignación de roles**, haga clic en **Agregar**.

    ![Búsqueda de una cuenta de usuario para comprobar el acceso y asignar un rol](./media/tutorial-discover-physical/azure-account-access.png)

6. En **Agregar asignación de roles**, seleccione el rol Colaborador o Propietario, y seleccione la cuenta (azmigrateuser en nuestro ejemplo). A continuación, haga clic en **Save**(Guardar).

    ![Se abre la página Agregar asignación de roles para asignar un rol a la cuenta.](./media/tutorial-discover-physical/assign-role.png)

1. Con el fin de registrar el dispositivo, la cuenta de Azure necesita **permisos para registrar aplicaciones de AAD**.
1. En Azure Portal, vaya a **Azure Active Directory** > **Usuarios** > **Configuración de usuario**.
1. En **Configuración de usuario**, compruebe que los usuarios de Azure AD puedan registrar aplicaciones (establecido en **Sí** de forma predeterminada).

    ![Comprobación en la configuración de usuario de que los usuarios puedan registrar aplicaciones de Active Directory](./media/tutorial-discover-physical/register-apps.png)

9. Si la configuración de "Registros de aplicaciones" se ha establecido en "No", solicite al administrador global o de inquilinos que asigne el permiso necesario. Como alternativa, el administrador global o de inquilinos puede asignar el rol **Desarrollador de aplicaciones** a una cuenta para permitir el registro de aplicaciones de AAD. [Más información](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-physical-servers"></a>Preparación de los servidores físicos

Configure una cuenta que el dispositivo pueda usar para acceder a los servidores físicos.

**Servidores Windows**

- En el caso de servidores Windows, use una cuenta de dominio para los servidores que se hayan unido a un dominio y una cuenta local para los que no. 
- Debe agregar la cuenta de usuario a estos grupos: Usuarios de administración remota, Usuarios de Monitor de rendimiento y Usuarios del registro de rendimiento. 
- Si el grupo Usuarios de administración remota no está presente, agregue la cuenta de usuario al grupo: **WinRMRemoteWMIUsers_** .
- La cuenta necesita estos permisos para que el dispositivo cree una conexión CIM con el servidor y extraiga los metadatos de configuración y rendimiento necesarios de las clases WMI enumeradas aquí.
- En algunos casos, es posible que agregar la cuenta a estos grupos no devuelva los datos necesarios de las clases WMI, ya que la cuenta podría estar filtrada por [UAC](/windows/win32/wmisdk/user-account-control-and-wmi). Para superar el filtrado de UAC, la cuenta de usuario debe tener los permisos necesarios en el espacio de nombres CIMV2 y en los subespacios de nombres en el servidor de destino. Puede seguir [estos](troubleshoot-appliance.md) pasos para habilitar los permisos obligatorios.

    > [!Note]
    > En el caso de Windows Server 2008 y 2008 R2, asegúrese de que WMF 3.0 esté instalado en los servidores.

**Servidores Linux**

- Necesita una cuenta raíz en los servidores que desee detectar. Como alternativa, puede proporcionar permisos sudo a una cuenta de usuario.
- La compatibilidad para agregar una cuenta de usuario con acceso sudo se proporciona de manera predeterminada con el nuevo script del instalador del dispositivo descargado del portal después del 20 de julio de 2021.
- En el caso de dispositivos anteriores, puede seguir estos pasos para habilitar la funcionalidad:
    1. En el servidor que ejecuta el dispositivo, abra el Editor del Registro.
    1. Vaya a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
    1. Cree una clave del Registro "isSudo" con un valor de DWORD de 1.

    :::image type="content" source="./media/tutorial-discover-physical/issudo-reg-key.png" alt-text="Captura de pantalla que muestra cómo habilitar la compatibilidad con sudo.":::

- Para detectar los metadatos de configuración y rendimiento del servidor de destino, debe habilitar el acceso sudo para los comandos enumerados [aquí](migrate-appliance.md#linux-server-metadata). Asegúrese de que ha habilitado "NOPASSWD" para que la cuenta ejecute los comandos necesarios sin solicitar una contraseña cada vez que se invoque el comando sudo.
- Las siguientes distribuciones del sistema operativo Linux son compatibles con la detección de Azure Migrate mediante una cuenta con acceso sudo:

    Sistema operativo | Versiones 
    --- | ---
    Red Hat Enterprise Linux | 6,7,8
    Cent OS | 6.6, 8.2
    Ubuntu | 14.04,16.04,18.04
    SUSE Linux | 11.4, 12.4
    Debian | 7, 10
    Amazon Linux | 2.0.2021
    CoreOS Container | 2345.3.0

- Si no puede proporcionar acceso sudo a la cuenta raíz o a la cuenta de usuario, puede establecer la clave del Registro "isSudo" en el valor "0" en el Registro HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance y proporcionar una cuenta no raíz con las funcionalidades necesarias mediante los siguientes comandos:

**Comando** | **Propósito**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(si /usr/sbin/fdisk no está presente)_ | Recopilar datos de configuración del disco
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Recopilar datos de rendimiento del disco
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Recopilar el número de serie del BIOS
chmod a+r /sys/class/dmi/id/product_uuid | Recopilar el GUID del BIOS

## <a name="set-up-a-project"></a>Configuración de un proyecto

Configure un nuevo proyecto.

1. En Azure Portal > **Todos los servicios**, busque **Azure Migrate**.
2. En **Servicios**, seleccione **Azure Migrate**.
3. En **Información general**, seleccione **Crear proyecto**.
5. En **Crear proyecto**, seleccione su suscripción y grupo de recursos de Azure. Cree un grupo de recursos si no tiene ninguno.
6. En **Detalles del proyecto**, especifique el nombre del proyecto y la región geográfica en la que quiere crearlo. Revise las zonas geográficas admitidas para nubes [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) y [nubes gubernamentales](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Cuadros de nombre de proyecto y región](./media/tutorial-discover-physical/new-project.png)

7. Seleccione **Crear**.
8. Espere unos minutos a que se implemente el proyecto. La herramienta **Azure Migrate: Discovery and assessment** se agrega de forma predeterminada al nuevo proyecto.

![Página que muestra la herramienta Server Assessment agregada de forma predeterminada](./media/tutorial-discover-physical/added-tool.png)

> [!NOTE]
> Si ya ha creado un proyecto, puede usarlo para registrar dispositivos adicionales con el fin de detectar y evaluar un número mayor de servidores.[Más información](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configuración del dispositivo

El dispositivo de Azure Migrate realiza la detección del servidor y envía los metadatos de configuración y rendimiento del servidor a Azure Migrate. El dispositivo se puede configurar ejecutando un script de PowerShell que se puede descargar del proyecto.

Para configurar el dispositivo:

1. Proporcione un nombre de dispositivo y genere una clave de proyecto en el portal.
2. Descargue un archivo comprimido con el script del instalador de Azure Migrate desde Azure Portal.
3. Extraiga el contenido del archivo comprimido. Inicie la consola de PowerShell con privilegios administrativos.
4. Ejecute el script de PowerShell para iniciar el administrador de configuración del dispositivo.
5. Configure el dispositivo por primera vez y regístrelo en el proyecto mediante la clave del proyecto.

### <a name="1-generate-the-project-key"></a>1. Generación de la clave del proyecto

1. En **Migration Goals** > **Servers** >  **Azure Migrate: Discovery and assessment** (Objetivos de migración > Servidores > Azure Migrate: Discovery and assessment), seleccione **Discover** (Detectar).
2. En **Discover Servers** > **Are your servers virtualized?** (Detectar servidores > ¿Están virtualizados sus servidores?), seleccione **Physical or other (AWS, GCP, Xen, etc.)** [Físicos u otros (AWS, GCP, Xen, etc.)].
3. En **1: Generar la clave del proyecto**, proporcione un nombre para el dispositivo de Azure Migrate que configurará para la detección de los servidores virtuales o físicos de GCP. Este nombre debe ser alfanumérico y no puede tener más de 14 caracteres.
1. Haga clic en **Generar clave** para iniciar la creación de los recursos de Azure necesarios. No cierre la página Detectar servidores durante la creación de los recursos.
1. Después de la creación correcta de los recursos de Azure, se genera una **clave de proyecto**.
1. Copie la clave, ya que la necesitará para completar el registro del dispositivo durante su configuración.

  [ ![Selecciones para Generar clave.](./media/tutorial-assess-physical/generate-key-physical-inline-1.png)](./media/tutorial-assess-physical/generate-key-physical-expanded-1.png#lightbox)

### <a name="2-download-the-installer-script"></a>2. Descarga del script del instalador

En **2: Descargar el dispositivo de Azure Migrate**, haga clic en **Descargar**.

### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo comprimido es seguro, antes de implementarlo.

1. En el servidor en el que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo ZIP:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Compruebe la versión más reciente del dispositivo y el valor hash:

    **Descargar** | **Valor del código hash**
    --- | ---
    [La versión más reciente](https://go.microsoft.com/fwlink/?linkid=2140334) | 15a94b637a39c53ac91a2d8b21cc3cca8905187e4d9fb4d895f4fa6fd2f30b9f

> [!NOTE]
> El mismo script se puede usar para configurar el dispositivo físico para la nube de Azure Government o pública de Azure con conectividad de punto de conexión privado o público.


### <a name="3-run-the-azure-migrate-installer-script"></a>3. Ejecución del script del instalador de Azure Migrate

1. Extraiga el archivo comprimido en la carpeta del servidor que hospedará el dispositivo.  No ejecute el script en un servidor con un dispositivo de Azure Migrate existente.
2. Inicie PowerShell en el servidor anterior con privilegios administrativos (elevados).
3. Cambie el directorio de PowerShell a la carpeta en la que se ha extraído el contenido del archivo comprimido descargado.
4. Ejecute el script denominado **AzureMigrateInstaller.ps1** ejecutando el comando siguiente:

    
    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. Seleccione entre las opciones de escenario, nube y conectividad para implementar un dispositivo con la configuración deseada. Por ejemplo, la selección que se muestra a continuación configura un dispositivo y evalúa **servidores físicos** _(o servidores que se ejecutan en otras nubes, como AWS, GCP, Xen, etc.)_ en un proyecto de Azure Migrate con la conectividad **predeterminada _(punto de conexión público)_**  en la **nube pública de Azure**.

    :::image type="content" source="./media/tutorial-discover-physical/script-physical-default-inline.png" alt-text="Captura de pantalla que muestra cómo configurar un dispositivo con la configuración deseada" lightbox="./media/tutorial-discover-physical/script-physical-default-expanded.png":::

6. El script del instalador hace lo siguiente:

 - Instala agentes y una aplicación web.
 - Instala los roles de Windows, incluido el servicio de activación de Windows, IIS y PowerShell ISE.
 - Descarga e instala un módulo de reescritura de IIS.
 - Actualiza una clave del registro (HKLM) con detalles de configuración persistentes para Azure Migrate.
 - Crea los siguientes archivos en la ruta de acceso:
    - **Archivos de configuración**:%Programdata%\Microsoft Azure\Config
    - **Archivos de registro**:%Programdata%\Microsoft Azure\Logs

Una vez que el script se haya ejecutado correctamente, el administrador de configuración del dispositivo se iniciará automáticamente.

> [!NOTE]
> En caso de que surja algún problema, puede acceder a los registros de script en C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log para solucionarlo.

### <a name="verify-appliance-access-to-azure"></a>Comprobación de que el dispositivo puede acceder a Azure

Asegúrese de que el dispositivo pueda conectarse a las direcciones URL de Azure para las nubes [públicas](migrate-appliance.md#public-cloud-urls) y [gubernamentales](migrate-appliance.md#government-cloud-urls).

### <a name="4-configure-the-appliance"></a>4. Configuración del dispositivo

Configure el dispositivo por primera vez.

1. Abra un explorador en cualquier servidor que pueda conectarse al dispositivo y abra la dirección URL de la aplicación web del dispositivo: **https://*nombre o dirección IP del dispositivo*: 44368**.

   Como alternativa, puede abrir la aplicación desde el escritorio, para lo que debe hacer clic en el acceso directo de la aplicación.
2. Acepte los **términos de licencia** y lea la información de terceros.
1. En la aplicación web > **Set up prerequisites** (Configurar los requisitos previos ), realice las siguientes operaciones:
    - **Connectivity** (Conectividad): la aplicación comprueba que el servidor tenga acceso a Internet. Si el servidor usa un proxy:
        - Haga clic en **Configurar el proxy** y especifique la dirección del proxy (con los formatos http://ProxyIPAddress o http://ProxyFQDN) ) y el puerto de escucha.
        - Especifique las credenciales si el proxy requiere autenticación.
        - Solo se admite un proxy HTTP.
        - Si ha agregado detalles del proxy o ha deshabilitado el proxy o la autenticación, haga clic en **Guardar** para desencadenar la comprobación de conectividad.
    - **Time sync** (Sincronización de hora): Se comprueba la hora. Para que la detección del servidor funcione correctamente, la hora del dispositivo debe estar sincronizada con la hora de Internet.
    - **Instalación de actualizaciones**: la herramienta Azure Migrate: Discovery and assessment comprueba que el dispositivo tenga instaladas las actualizaciones más recientes. Una vez finalizada la comprobación, puede hacer clic en **Ver servicios del dispositivo** para ver el estado y las versiones de los componentes que se ejecutan en el dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registro del dispositivo en Azure Migrate

1. Pegue la **clave de proyecto** copiada desde el portal. Si no tiene la clave, vaya a **Azure Migrate: Discovery and assessment> Discover> Manage existing appliances** (Azure Migrate: Discovery and assessment > Detectar > Administrar los dispositivos existentes), seleccione el nombre del dispositivo que proporcionó al generar la clave y copie la clave correspondiente.
1. Necesitará un código de dispositivo para autenticarse con Azure. Al hacer clic en **Iniciar sesión** se abrirá un modal con el código del dispositivo, tal como se muestra a continuación.

    ![Modal que muestra el código del dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Haga clic en **Copiar código e Iniciar sesión** para copiar el código del dispositivo y abrir un símbolo del sistema de inicio de sesión de Azure en una nueva pestaña del explorador. Si no aparece, asegúrese de que ha deshabilitado el bloqueador de elementos emergentes en el explorador.
1. En la nueva pestaña, pegue el código del dispositivo e inicie sesión con su nombre de usuario y contraseña de Azure.
   
   No se admite el inicio de sesión con un PIN.
3. En caso de que cierre accidentalmente la pestaña de inicio de sesión sin iniciar sesión, deberá actualizar la pestaña explorador del administrador de configuración del dispositivo para volver a habilitar el botón Iniciar sesión.
1. Una vez que haya iniciado sesión correctamente, vuelva a la pestaña anterior con el administrador de configuración del dispositivo.
4. Si la cuenta de usuario de Azure que se usa para el registro tiene los [permisos]() adecuados en los recursos de Azure creados durante la generación de la clave, se iniciará el registro del dispositivo.
1. Una vez que el dispositivo se ha registrado correctamente, puede ver los detalles de registro haciendo clic en **Ver detalles**.


## <a name="start-continuous-discovery"></a>Inicio de detección continua

Ahora, conecte desde el dispositivo a los servidores físicos que se van a detectar e inicie la detección.

1. En **Paso 1: proporcionar credenciales para la detección de servidores físicos o virtuales de Windows y Linux**, haga clic en **Agregar credenciales**.
1. En el caso de Windows Server, seleccione el tipo de origen **Windows Server**, especifique un nombre descriptivo para las credenciales, agregue el nombre de usuario y la contraseña. Haga clic en **Guardar**.
1. Si usa la autenticación basada en contraseña para el servidor Linux, seleccione el tipo de origen **Servidor Linux (basado en contraseña)** , especifique un nombre descriptivo para las credenciales y agregue el nombre de usuario y la contraseña. Haga clic en **Guardar**.
1. Si usa la autenticación basada en clave SSH para el servidor Linux, puede seleccionar el tipo de origen como **Servidor Linux (basado en clave SSH)** , especifique un nombre descriptivo para las credenciales, agregue el nombre de usuario. busque el archivo de clave privada SSH y selecciónela. Haga clic en **Guardar**.

    - Azure Migrate admite la clave privada SSH generada por el comando ssh-keygen mediante los algoritmos RSA, DSA, ECDSA y ed25519.
    - Actualmente, Azure Migrate no admite la clave SSH basada en frase de contraseña. Use una clave SSH sin frase de contraseña.
    - Actualmente, Azure Migrate no admite el archivo de clave privada SSH generado por PuTTy.
    - Azure Migrate admite el formato OpenSSH del archivo de clave privada SSH, como se muestra a continuación:
    
    ![Formato compatible con clave privada SSH](./media/tutorial-discover-physical/key-format.png)

1. Si quiere agregar varias credenciales a la vez, haga clic en **Agregar más** para guardar y agregar más credenciales. Se admiten varias credenciales para la detección de servidores físicos.
1. En **Paso 2: Proporcionar los detalles del servidor virtual o físico**, haga clic en **Agregar origen de detección** para especificar la **dirección IP o el FQDN** del servidor y el nombre descriptivo de las credenciales para conectarse al servidor.
1. Puede **Agregar un solo elemento** cada vez o **Agregar varios elementos** de una sola vez. También hay una opción para proporcionar los detalles del servidor a través de **Importar CSV**.


    - Si elige **Agregar un solo elemento**, puede elegir el tipo de sistema operativo, especificar el nombre descriptivo de las credenciales, agregar la **dirección IP o el FQDN** del servidor y hacer clic en **Guardar**.
    - Si elige **Agregar varios elementos**, puede agregar varios registros a la vez mediante la especificación de la **dirección IP o el FQDN** del servidor con el nombre descriptivo de las credenciales en el cuadro de texto. Compruebe los registros agregados y haga clic en **Guardar**.
    - Si elige **importar CSV** _(opción seleccionada de manera predeterminada)_ , puede descargar un archivo de plantilla CSV, rellenar el archivo con la **dirección IP o el FQDN** del servidor y el nombre descriptivo de las credenciales. A continuación, importe el archivo en el dispositivo, **compruebe** los registros del archivo y haga clic en **Guardar**.

1. Al hacer clic en Guardar, el dispositivo intentará validar la conexión a los servidores agregados y mostrar el **estado de validación** en la tabla en cada servidor.
    - Si se produce un error de validación para un servidor, haga clic en **Error en la validación** en la columna Estado de la tabla para revisar el error. Corrija el problema y vuelva a validar.
    - Para quitar un servidor, haga clic en **Eliminar**.
1. Puede **volver a validar** la conectividad a los servidores en cualquier momento antes de iniciar la detección.
1. Haga clic en **Iniciar detección** para dar comienzo a la detección de los servidores validados correctamente. Una vez que la detección se ha iniciado correctamente, puede comprobar el estado de detección en cada servidor de la tabla.


De esta forma comienza la detección. Los metadatos de los servidores detectados tardan alrededor de 2 minutos por servidor en aparecer en Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Comprobación de los servidores en el portal

Una vez finalizada la detección, puede verificar que los servidores aparezcan en el portal.

1. Abra el panel de Azure Migrate.
2. En la página **Azure Migrate - Servidores** > **Azure Migrate: Discovery and assessment**, haga clic en el icono que muestra el número de **servidores detectados**.
## <a name="next-steps"></a>Pasos siguientes

- [Valoración de los servidores físicos](tutorial-assess-physical.md) para la migración a máquinas virtuales de Azure.
- [Revise los datos](migrate-appliance.md#collected-data---physical) que el dispositivo recopila durante la detección.
