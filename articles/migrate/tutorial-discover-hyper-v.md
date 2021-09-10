---
title: Detección de servidores en Hyper-V con Azure Migrate Discovery and assessment
description: Aprenda a detectar servidores locales en Hyper-V con Azure Migrate Discovery and assessment.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: 7bdc0008baa1ee03c11aeeba03485fd7cd09e6f9
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969823"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Tutorial: Detección de servidores que se ejecutan en Hyper-V con Azure Migrate Discovery and assessment

Como parte del recorrido de la migración a Azure, puede detectar el inventario y las cargas de trabajo locales.

En este tutorial se muestra cómo detectar servidores locales en hosts de Hyper-V con la herramienta Azure Migrate Discovery and assessment mediante un dispositivo ligero de Azure Migrate. El dispositivo se implementa como un servidor en un host de Hyper-V para detectar continuamente metadatos de rendimiento y de la máquina.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configuración de una cuenta de Azure
> * Preparar el entorno de Hyper-V para la detección.
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
**Host de Hyper-V** | Los hosts de Hyper-V en los que se encuentran los servidores pueden ser independientes o estar en un clúster.<br/><br/> El host debe ejecutar Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2.<br/><br/> Compruebe que se permiten conexiones entrantes en el puerto 5985 (HTTP) de WinRM para que el dispositivo pueda conectarse para extraer metadatos del servidor y datos de rendimiento, para lo que se usa una sesión de Modelo de información común (CIM).
**Implementación del dispositivo** | El host de Hyper-V necesita recursos para asignar un servidor al dispositivo:<br/><br/> - 16 GB de RAM, 8 CPU virtuales y alrededor de 80 GB de almacenamiento en disco.<br/><br/> - Un conmutador virtual externo y acceso a Internet en el dispositivo, directamente o a través de un proxy.
**Servidores** | Las servidores pueden ejecutar cualquier sistema operativo Windows o Linux.

## <a name="prepare-an-azure-user-account"></a>Preparación de una cuenta de usuario de Azure

Para crear un proyecto y registrar el dispositivo de Azure Migrate, necesita una cuenta con:
- Permisos de nivel de colaborador o propietario en una suscripción de Azure.
- Permisos para registrar aplicaciones de Azure Active Directory (AAD).

Si acaba de crear una cuenta de Azure gratuita, es el propietario de la suscripción. Si no es el propietario, trabaje con él para asignar los permisos, como se indica a continuación:

1. En Azure Portal, busque "suscripciones" y, en **Servicios**, seleccione **Suscripciones**.

    ![Cuadro para buscar la suscripción de Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. En la página **Suscripciones**, seleccione la suscripción en la que quiere crear un proyecto.
3. En la suscripción, seleccione **Access control (IAM)**  > **Comprobar acceso**.
4. En **Comprobar acceso**, busque la cuenta de usuario correspondiente.
5. En **Agregar una asignación de roles**, haga clic en **Agregar**.

    ![Búsqueda de una cuenta de usuario para comprobar el acceso y asignar un rol](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. En **Agregar asignación de roles**, seleccione el rol Colaborador o Propietario, y seleccione la cuenta (azmigrateuser en nuestro ejemplo). A continuación, haga clic en **Save**(Guardar).

    ![Se abre la página Agregar asignación de roles para asignar un rol a la cuenta.](./media/tutorial-discover-hyper-v/assign-role.png)

1. Con el fin de registrar el dispositivo, la cuenta de Azure necesita **permisos para registrar aplicaciones de AAD**.
1. En Azure Portal, vaya a **Azure Active Directory** > **Usuarios** > **Configuración de usuario**.
1. En **Configuración de usuario**, compruebe que los usuarios de Azure AD puedan registrar aplicaciones (establecido en **Sí** de forma predeterminada).

    ![Comprobación en la configuración de usuario de que los usuarios puedan registrar aplicaciones de Active Directory](./media/tutorial-discover-hyper-v/register-apps.png)

9. Si la configuración de "Registros de aplicaciones" se ha establecido en "No", solicite al administrador global o de inquilinos que asigne el permiso necesario. Como alternativa, el administrador global o de inquilinos puede asignar el rol **Desarrollador de aplicaciones** a una cuenta para permitir el registro de aplicaciones de AAD. [Más información](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Preparar los hosts de Hyper-V

Puede preparar los hosts de Hyper-V manualmente o mediante un script. Los pasos de preparación se resumen en la tabla. El script los prepara automáticamente.

**Step** | **Script** | **Manual**
--- | --- | ---
Comprobación de los requisitos del host | Comprueba que el host está ejecutando una versión compatible de Hyper-V y el rol de Hyper-V.<br/><br/>Habilita el servicio WinRM y abre los puertos 5985 (HTTP) y 5986 (HTTPS) en el host (necesario para la recopilación de metadatos). | El host debe ejecutar Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2.<br/><br/> Compruebe que se permiten conexiones entrantes en el puerto 5985 (HTTP) de WinRM para que el dispositivo pueda conectarse para extraer metadatos del servidor y datos de rendimiento, para lo que se usa una sesión de Modelo de información común (CIM).<br/><br/> El script no es compatible actualmente en hosts con una configuración regional que no esté en inglés.  
Verificación de la versión de PowerShell | Comprueba que se está ejecutando el script en una versión compatible de PowerShell. | Compruebe que ejecuta la versión 4.0 o posterior de PowerShell en el host de Hyper-V.
Crear una cuenta | Comprueba que tiene los permisos correctos en el host de Hyper-V.<br/><br/> Permite crear una cuenta de usuario local con los permisos correctos. | Opción 1: Preparar una cuenta con acceso de administrador a la máquina host de Hyper-V.<br/><br/> Opción 2: Preparar una cuenta de administrador local o una cuenta de administrador de dominio y agregarla a estos grupos: Usuarios de administración remota, Administradores de Hyper-V y Usuarios del Monitor de rendimiento.
Habilitación de la comunicación remota con PowerShell | Habilita la comunicación remota de PowerShell en el host, con el fin de que el dispositivo de Azure Migrate pueda ejecutar comandos de PowerShell en el host mediante a través de una conexión de WinRM. | Para configurarlo, en cada uno de los hosts, abra una consola de PowerShell como administrador y ejecute este comando: ``` powershell Enable-PSRemoting -force ```.
Configuración de los servicios de integración de Hyper-V | Comprueba que Hyper-V Integration Services está habilitado en todos los servidores administrados por el host. | [Habilite Hyper-V Integration Services](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) en todos los servidores.<br/><br/> Si ejecuta Windows Server 2003, [siga estas instrucciones](prepare-windows-server-2003-migration.md).
Delegación de credenciales si los discos de los servidores se encuentran en recursos compartidos de SMB remotos | Delega las credenciales. | Ejecute este comando para permitir que CredSSP delegue credenciales en hosts que ejecutan servidores con discos en recursos compartidos de SMB: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> Puede ejecutar este comando de forma remota en todos los hosts de Hyper-V.<br/><br/> Si agrega nuevos nodos de host en un clúster, se agregan automáticamente para la detección, pero debe habilitar CredSSP manualmente.<br/><br/> Al configurar el dispositivo, termine de configurar CredSSP mediante su [habilitación en el dispositivo](#delegate-credentials-for-smb-vhds). 

### <a name="run-the-script"></a>Ejecución del script

1. Descargue el script del [Centro de descarga de Microsoft](https://aka.ms/migrate/script/hyperv). Microsoft firma criptográficamente el script.
2. Valide la integridad del script mediante archivos hash MD5 o SHA256. A continuación se muestran los valores de hashtag. Ejecute este comando para generar el código hash para el script:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Ejemplo de uso:

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. Después de validar la integridad del script, ejecútelo en cada host de Hyper-V con este comando de PowerShell con permisos elevados:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
Los valores de hash son:

**Hash** |  **Valor**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Configuración de un proyecto

Configure un nuevo proyecto.

1. En Azure Portal > **Todos los servicios**, busque **Azure Migrate**.
2. En **Servicios**, seleccione **Azure Migrate**.
3. En **Información general**, seleccione **Crear proyecto**.
5. En **Crear proyecto**, seleccione su suscripción y grupo de recursos de Azure. Cree un grupo de recursos si no tiene ninguno.
6. En **Detalles del proyecto**, especifique el nombre del proyecto y la región geográfica en la que quiere crearlo. Revise las zonas geográficas admitidas para nubes [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) y [nubes gubernamentales](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Cuadros de nombre de proyecto y región](./media/tutorial-discover-hyper-v/new-project.png)

7. Seleccione **Crear**.
8. Espere unos minutos a que se implemente el proyecto. La herramienta **Azure Migrate Discovery and assessment** se agrega de forma predeterminada al nuevo proyecto.

![Página que muestra la herramienta Azure Migrate Discovery and assessment agregada de forma predeterminada](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Si ya ha creado un proyecto, puede usarlo para registrar dispositivos adicionales con el fin de detectar y evaluar un número mayor de servidores.[Más información](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configuración del dispositivo

Azure Migrate usa un dispositivo de Azure Migrate ligero. El dispositivo de Azure Migrate detecta los servidores y envía los metadatos de configuración y rendimiento a Azure Migrate. El dispositivo se puede configurar mediante la implementación de un archivo de disco duro virtual que se puede descargar del proyecto.

> [!NOTE]
> Si, por algún motivo, no puede configurar el dispositivo mediante la plantilla, puede hacerlo con un script de PowerShell en un servidor de Windows Server 2016 existente. [Más información](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).<br/>
> La opción para implementar un dispositivo mediante una plantilla VHD no se admite en la nube de Azure Government. [Más información](/azure/migrate/deploy-appliance-script-government) sobre cómo implementar un dispositivo para la nube de Azure Government.

En este tutorial se configura el dispositivo en un servidor que se ejecuta en un entorno de Hyper-V, como se indica a continuación:

1. Proporcione un nombre de dispositivo y genere una clave del proyecto en el portal.
1. Descargue un disco duro virtual de Hyper-V comprimido desde Azure Portal.
1. Cree el dispositivo y compruebe que se puede conectar a Azure Migrate Discovery and assessment.
1. Configure el dispositivo por primera vez y regístrelo en el proyecto mediante la clave del proyecto.

### <a name="1-generate-the-project-key"></a>1. Generación de la clave del proyecto

1. En **Migration Goals** > **Servers** >  **Azure Migrate Discovery and assessment** (Objetivos de migración > Servidores > Azure Migrate Discovery and assessment), seleccione **Discover** (Detectar).
2. En **Discover Servers** > **Are your servers virtualized?** (Detectar servidores > ¿Están virtualizados sus servidores?), seleccione **Yes, with Hyper-V** (Sí, con Hyper-V).
3. En **1: Generate project key** (Generar clave de proyecto), especifique un nombre para el dispositivo de Azure Migrate que configurará para la detección de servidores. Este nombre debe ser alfanumérico y no puede tener más de 14 caracteres.
1. Haga clic en **Generar clave** para iniciar la creación de los recursos de Azure necesarios. No cierre la página Discover server (Detectar servidor) durante la creación de recursos.
1. Después de la creación correcta de los recursos de Azure, se genera una **clave de proyecto**.
1. Copie la clave, ya que la necesitará para completar el registro del dispositivo durante su configuración.

### <a name="2-download-the-vhd"></a>2. Descarga del disco duro virtual

En **2: Descargar dispositivo de Azure Migrate**, seleccione el archivo .VHD y haga clic en **Descargar**.

### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo comprimido es seguro, antes de implementarlo.

1. En la máquina en la que descargó el archivo, abra una ventana de comandos de administrador.

2. Ejecute el siguiente comando de PowerShell para generar el código hash para el archivo ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Compruebe las versiones más recientes del dispositivo y los valores hash:

    - Para la nube pública de Azure:

        **Escenario** | **Descargar** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [La versión más reciente](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d6136e02324dc8d8a14f47772da351b46d9127

    - Para Azure Government:

        **Escenario** _ | _ *Descarga** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [La versión más reciente](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="3-create-an-appliance"></a>3. Creación de un dispositivo

Importe el archivo descargado y cree un dispositivo.

1. Extraiga el archivo de disco duro virtual comprimido en una carpeta del host de Hyper-V que hospedará el dispositivo. Se extraen tres carpetas.
2. Abra el administrador de Hyper-V. En **Acciones**, haga clic en **Importar máquina virtual**.
2. En el Asistente para importar máquinas virtuales > **Antes de comenzar**, haga clic en **Siguiente**.
3. En **Buscar carpeta**, especifique la carpeta que contiene el disco duro virtual extraído. A continuación, haga clic en **Siguiente**.
1. En **Seleccionar máquina virtual**, haga clic en **Siguiente**.
2. En **Elegir tipo de importación**, haga clic en **Copiar la máquina virtual (crear un identificador único nuevo)** . A continuación, haga clic en **Siguiente**.
3. En **Elegir destino**, deje la configuración predeterminada. Haga clic en **Next**.
4. En **Carpetas de almacenamiento**, deje la configuración predeterminada. Haga clic en **Next**.
5. En **Elegir red**, especifique el conmutador virtual que el dispositivo va a usar. El conmutador necesita conectividad a Internet para enviar datos a Azure.
6. En **Resumen**, revise los valores de configuración. Haga clic en **Finalizar**.
7. En Hyper-V Manager > **Máquinas virtuales**, inicie el dispositivo.

### <a name="verify-appliance-access-to-azure"></a>Comprobación de que el dispositivo puede acceder a Azure

Asegúrese de que el dispositivo pueda conectarse a las direcciones URL de Azure para las nubes [públicas](migrate-appliance.md#public-cloud-urls) y [gubernamentales](migrate-appliance.md#government-cloud-urls).

### <a name="4-configure-the-appliance"></a>4. Configuración del dispositivo

Configure el dispositivo por primera vez.

> [!NOTE]
> Si configura la aplicación mediante un [script de PowerShell](deploy-appliance-script.md), en lugar del disco duro virtual descargado, los dos primeros pasos de este procedimiento no son pertinentes.

1. En Administrador de Hyper-V > **Máquinas virtuales**, haga clic con el botón derecho en el dispositivo > **Conectar**.
2. Especifique el idioma, la zona horaria y la contraseña del dispositivo.
3. Abra un explorador en cualquier máquina que pueda conectarse al dispositivo y abra la dirección URL de la aplicación web del dispositivo: **https://*nombre o dirección IP del dispositivo*: 44368**.

   Como alternativa, puede abrir la aplicación desde el escritorio del dispositivo, para lo que debe hacer clic en el acceso directo de la aplicación.
1. Acepte los **términos de licencia** y lea la información de terceros.
1. En la aplicación web > **Set up prerequisites** (Configurar los requisitos previos ), realice las siguientes operaciones:
    - **Connectivity** (Conectividad): la aplicación comprueba que el dispositivo tiene acceso a Internet. Si el dispositivo usa un proxy:
      - Haga clic en **Configurar proxy** y especifique la dirección del proxy (con los formatos http://ProxyIPAddress o http://ProxyFQDN) ) y el puerto de escucha.
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
4. Si la cuenta de usuario de Azure que se usa para el registro tiene los permisos adecuados en los recursos de Azure creados durante la generación de la clave, se iniciará el registro del dispositivo.
1. Una vez que el dispositivo se ha registrado correctamente, puede ver los detalles de registro haciendo clic en **Ver detalles**.

### <a name="delegate-credentials-for-smb-vhds"></a>Delegación de credenciales para discos duros virtuales de SMB

Si va a ejecutar discos duros virtuales en SMB, debe habilitar la delegación de credenciales desde el dispositivo a los hosts de Hyper-V. Para ello, desde el dispositivo:

1. En el dispositivo, ejecute este comando. HyperVHost1 y HyperVHost2 son nombres de host de ejemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. También puede hacerlo en el Editor de directivas de grupo local en el dispositivo:
    - En **Directiva de equipo local** > **Configuración del equipo**, haga clic en **Plantillas administrativas** > **Sistema** > **Delegación de credenciales**.
    - Haga doble clic en **Permitir delegación de credenciales nuevas** y seleccione **Habilitado**.
    - En **Opciones**, haga clic en **Mostrar** y agregue cada host de Hyper-V que desee detectar en la lista, con **wsman/** como prefijo.
    - En **Delegación de credenciales**, haga doble clic en **Permitir la delegación de credenciales nuevas con autenticación solo NTLM de servidor**. De nuevo, agregue cada host de Hyper-V que quiera detectar en la lista, con **wsman/** como prefijo.

## <a name="start-continuous-discovery"></a>Inicio de detección continua

Conéctese desde el dispositivo a los hosts o clústeres de Hyper-V e inicie la detección del servidor.

1. En **Paso 1: Proporcionar las credenciales del host de Hyper-V**, haga clic en **Agregar credenciales** para especificar un nombre descriptivo para las credenciales, agregue un **nombre de usuario** y una **contraseña** para el host o el clúster de Hyper-V que utilizará el dispositivo para detectar servidores. Haga clic en **Guardar**.
1. Si quiere agregar varias credenciales a la vez, haga clic en **Agregar más** para guardar y agregar más credenciales. Se admiten varias credenciales para la detección de servidores en el entorno de Hyper-V.
1. En el **Paso 2: Proporcionar los detalles del host o el clúster de Hyper-V**, haga clic en **Agregar origen de detección** para especificar la **dirección IP o el FQDN** del host o el clúster de Hyper-V y el nombre descriptivo de las credenciales para conectarse al host o al clúster.
1. Puede **Agregar un solo elemento** cada vez o **Agregar varios elementos** de una sola vez. También hay una opción para proporcionar los detalles del host o el clúster de Hyper-V a través de **Importar CSV**.

    - Si elige **Agregar un solo elemento**, debe especificar el nombre descriptivo de las credenciales y la **dirección IP o el FQDN** del host o el clúster de Hyper-V y hacer clic en **Guardar**.
    - Si elige **Add multiple items** (Agregar varios elementos) _(seleccionada de manera predeterminada)_ , puede agregar varios registros simultáneamente especificando la **dirección IP o el nombre de dominio completo** del host o clúster de Hyper-V con el nombre descriptivo de las credenciales del cuadro de texto. **Compruebe** los registros agregados y haga clic en **Guardar**.
    - Si elige **Importar CSV**, puede descargar un archivo de plantilla CSV, rellenar el archivo con la **dirección IP o el FQDN** del host o el clúster de Hyper-V y el nombre descriptivo de las credenciales. A continuación, importe el archivo en el dispositivo, **compruebe** los registros del archivo y haga clic en **Guardar**.

1. Al hacer clic en Guardar, el dispositivo intentará validar la conexión a los hosts o clústeres agregados de Hyper-V y mostrar el **estado de validación** en la tabla en cada host o clúster.
    - En el caso de los hosts o clústeres validados correctamente, puede ver más detalles si hace clic en su dirección IP o FQDN.
    - Si se produce un error de validación para un host, haga clic en **Error en la validación** en la columna Estado de la tabla para revisar el error. Corrija el problema y vuelva a validar.
    - Para quitar hosts o clústeres, seleccione **Eliminar**.
    - No se puede quitar un host específico de un clúster. Solo puede quitar todo el clúster.
    - Puede agregar un clúster, incluso si hay problemas con hosts específicos del clúster.
1. Puede **volver a validar** la conectividad con los hosts o clústeres en cualquier momento antes de iniciar la detección.
1. Haga clic en **Iniciar detección** para dar comienzo a la detección de servidores de los hosts o clústeres validados correctamente. Una vez que la detección se ha iniciado correctamente, puede comprobar el estado de detección en cada host o clúster de la tabla.

De esta forma comienza la detección. Los metadatos de los servidores detectados tardan alrededor de 2 minutos por host en aparecer en Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Comprobación de los servidores en el portal

Una vez finalizada la detección, puede verificar que los servidores aparezcan en el portal.

1. Abra el panel de Azure Migrate.
2. En la página **Azure Migrate - Servidores** > **Azure Migrate: Discovery and assessment**, haga clic en el icono que muestra el número de **servidores detectados**.

## <a name="next-steps"></a>Pasos siguientes

- [Evalúe los servidores en el entorno de Hyper-V](tutorial-assess-hyper-v.md) para la migración a máquinas virtuales de Azure.
- [Revise los datos](migrate-appliance.md#collected-data---hyper-v) que el dispositivo recopila durante la detección.