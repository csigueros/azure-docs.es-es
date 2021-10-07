---
title: Solución de problemas con el dispositivo Azure Migrate
description: Obtenga ayuda para solucionar problemas que puedan producirse con el dispositivo Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: d6d87d7503a68091e72f1aa84703966ab28f32b5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654525"
---
# <a name="troubleshoot-the-azure-migrate-appliance"></a>Solución de problemas con el dispositivo Azure Migrate

Este artículo le ayuda a solucionar los problemas que surgen al implementar el dispositivo de [Azure Migrate](migrate-services-overview.md) y el uso de dicho dispositivo para detectar servidores locales.

## <a name="whats-supported"></a>¿Qué se admite?

[Revise](migrate-appliance.md) los requisitos de compatibilidad del dispositivo.

## <a name="invalid-ovf-manifest-entry-error-occurs-during-appliance-setup"></a>Se produce el error "Entrada de manifiesto de OVF no válida" durante la instalación del dispositivo

Aparece el error "El archivo de manifiesto proporcionado no es válido: entrada de manifiesto OVF no válida" al configurar un dispositivo usando una plantilla OVA.

### <a name="remediation"></a>Corrección

1. Compruebe si el archivo OVA del dispositivo de Azure Migrate se descargó correctamente; para ello, compruebe el valor hash. [Más información](./tutorial-discover-vmware.md). Si el valor hash no coincide, vuelva a descargar el archivo OVA e intente de nuevo la implementación.
1. Si la implementación sigue sin funcionar y utiliza el cliente de VMware vSphere para implementar el archivo OVF, intente implementarlo mediante el cliente web de vSphere. Si la implementación sigue sin funcionar, intente usar otro explorador web.
1. Si usa el cliente web de vSphere e intenta implementarlo en vCenter Server 6.5 o 6.7, pruebe a implementar el archivo OVA directamente en el host ESXi:
   - Conéctese directamente al host ESXi (en lugar de vCenter Server) con el cliente web (https://<*dirección IP del host*>/ui).
   - En **Home** > **Inventory** (Inicio > Inventario), seleccione **File** > **Deploy OVF template** (Archivo > Implementar plantilla OVF). Vaya al archivo OVA y complete la implementación.
1. Si la implementación sigue sin funcionar, póngase en contacto con el soporte técnico de Azure Migrate.

## <a name="connectivity-check-fails-during-the-prerequisites-setup"></a>Se produce un error en la comprobación de conectividad durante la configuración de los requisitos previos

Aparece un error de la comprobación de conectividad en el dispositivo.

### <a name="remediation"></a>Corrección

1. Asegúrese de que puede conectarse a las [direcciones URL](./migrate-appliance.md#url-access) necesarias desde el dispositivo.
1. Compruebe si hay algún proxy o firewall que bloquee el acceso a estas direcciones URL. Si tiene que crear una lista de permitidos, asegúrese de incluir todas las direcciones URL.
1. Si hay un servidor proxy configurado localmente, introduzca los detalles del proxy correctamente; para ello, haga clic en **Configurar el proxy** en el mismo paso. Introduzca las credenciales de autorización en caso de que el servidor proxy las necesite.
1. Asegúrese de que el servidor no se haya usado previamente para configurar el [dispositivo de replicación](./migrate-replication-appliance.md) y de tener instalado el agente del servicio Mobility en el servidor.

## <a name="connectivity-check-fails-for-the-akams-url-during-the-prerequisites-setup"></a>Se produce un error en la comprobación de conectividad de la aka.ms URL durante la configuración de los requisitos previos

Aparece un error de la comprobación de conectividad en el dispositivo para la dirección URL aka.ms.

### <a name="remediation"></a>Corrección

1. Asegúrese de tener conectividad a Internet y de haber incluido en la lista de permitidos la dirección URL aka.ms/* para descargar las versiones más recientes de los servicios.
1. Compruebe si hay algún proxy o firewall que bloquee el acceso a esta URL. Asegúrese de haber proporcionado los detalles del proxy correctamente en el paso de requisitos previos del administrador de configuración.
1. Vuelva al administrador de configuración del dispositivo y ejecute de nuevo los requisitos previos para iniciar la actualización automática.
1. Si el reintento no resulta de ayuda, descargue el archivo *latestcomponents.json* en [este sitio web](https://aka.ms/latestapplianceservices) para comprobar las versiones más recientes de los servicios con errores. Actualícelos manualmente mediante los vínculos de descarga de archivo.

 Si ha habilitado el dispositivo para **conectividad de punto de conexión privado** y no desea permitir el acceso a esta dirección URL mediante Internet, [deshabilite la actualización automática](./migrate-appliance.md#turn-off-auto-update), ya que el vínculo de aka.ms es necesario para este servicio.

 >[!Note]
 >Si deshabilita el servicio de actualización automática, los servicios que se ejecutan en el dispositivo no obtendrán automáticamente las actualizaciones más recientes. Para evitar esta situación, [actualice los servicios del dispositivo manualmente](./migrate-appliance.md#manually-update-an-older-version).

## <a name="auto-update-check-fails-during-the-prerequisites-setup"></a>Se produce un error en la comprobación de actualización automática durante la configuración de los requisitos previos

Aparece un error en la comprobación de la actualización automática en el dispositivo.

### <a name="remediation"></a>Corrección

1. Asegúrese de haber creado una lista de permitidos para las [direcciones URL necesarias](./migrate-appliance.md#url-access) y de que no haya ningún proxy ni firewall cuya configuración las bloquee.
1. Si se produce un error en la actualización de cualquier componente del dispositivo, vuelva a ejecutar los requisitos previos o [actualice manualmente los servicios del dispositivo](./migrate-appliance.md#manually-update-an-older-version).

## <a name="time-sync-check-fails-during-the-prerequisites-setup"></a>Se produce un error en la comprobación de sincrónización de tiempo durante la configuración de los requisitos previos

Un error sobre la sincronización de hora indica que el reloj del servidor podría no estar sincronizado con la hora actual en más de cinco minutos.

### <a name="remediation"></a>Corrección

- Asegúrese de que la hora del servidor del dispositivo esté sincronizada con la hora de Internet comprobando la configuración de fecha y hora en el panel de control.
- También puede cambiar la hora del reloj en el servidor del dispositivo para que coincida con la actual. Para ello, siga estos pasos:
     1. Abra un símbolo del sistema de administrador en el servidor.
     1. Para comprobar la zona horaria, ejecute **w32tm /tz**.
     1. Para sincronizar la hora, ejecute **w32tm /resync**.

## <a name="vddk-check-fails-during-the-prerequisites-setup-on-the-vmware-appliance"></a>Se produce un error en la comprobación de VDDK durante la configuración de los requisitos previos en el dispositivo de VMware

Error en la comprobación del kit de desarrollo de discos virtuales (VDDK) porque el dispositivo no pudo encontrar el VDDK necesario instalado en el dispositivo. Esta incidencia puede provocar errores de la replicación en curso.

### <a name="remediation"></a>Corrección

1. Asegúrese de haber descargado VDDK 6.7 y copiado sus archivos en **C:\Archivos de programa\VMware\VMware Virtual Disk Development Kit** en el servidor del dispositivo.
1. Asegúrese de que ningún otro software o aplicación use otra versión del VDDK en el dispositivo.

## <a name="project-key-related-error-occurs-during-appliance-registration"></a>Se produce un error relacionado con la clave de proyecto durante el registro del dispositivo

 Tiene problemas al intentar registrar el dispositivo usando la clave de proyecto de Azure Migrate que se ha copiado del proyecto.

### <a name="remediation"></a>Corrección

1. Asegúrese de haber copiado la clave correcta del proyecto. En la tarjeta **Azure Migrate: detección y valoración** del proyecto, seleccione **Descubrir.** A continuación, seleccione **Administrar dispositivo existente** en el paso 1. Seleccione el nombre del dispositivo (para el que ha generado una clave anteriormente) en la lista desplegable. Copie la clave correspondiente.
1. Asegúrese de pegar la clave en el dispositivo del **tipo de nube** correcto —público/Gov (US)— y el **tipo de aplicación** correcto —VMware/Hyper-V/físico u otro—. Busque en la parte superior del administrador de configuración del dispositivo para confirmar el tipo de escenario y la nube.

## <a name="failed-to-connect-to-the-azure-migrate-project-error-occurs-during-appliance-registration"></a>Se produce el error "No se ha podido establecer conexión con el proyecto de Azure Migrate" durante el registro del dispositivo

Después de iniciar sesión correctamente con la cuenta de usuario de Azure, se produce un error en el paso de registro del dispositivo con el mensaje: "No se pudo conectar al proyecto de Azure Migrate. Consulte el detalle del error y siga los pasos de corrección mediante un clic en Reintentar".

El problema se produce cuando la cuenta de usuario de Azure que se utilizó para iniciar sesión desde el administrador de configuración de aplicaciones es diferente de la cuenta de usuario que se usó para generar la clave de proyecto de Azure Migrate en el portal.

### <a name="remediation"></a>Corrección

Tiene dos opciones:

- Para completar el registro del dispositivo, use la misma cuenta de usuario de Azure que generó la clave de proyecto de Azure Migrate en el portal.
- También puede asignar los roles y [permisos](./tutorial-discover-vmware.md#prepare-an-azure-user-account) necesarios a la otra cuenta de usuario de Azure que se usa para el registro del dispositivo.

## <a name="azure-active-directory-aad-operation-failed-with-status-forbidden-error-occurs-during-appliance-registration"></a>Se produce el error de "Operación de Azure Active Directory (AAD) con el estado 'Prohibida'" durante el registro del dispositivo

No puede completar el registro porque no hay suficientes privilegios de Azure Active Directory y ha obtenido un error de "Operación de Azure Active Directory (AAD) con el estado 'Prohibida'".

### <a name="remediation"></a>Corrección

Asegúrese de que tiene los [permisos necesarios](./tutorial-discover-vmware.md#prepare-an-azure-user-account) para crear y administrar aplicaciones de Azure Active Directory en Azure. Debe tener el rol **Desarrollador de aplicaciones** *o* el rol de usuario con la opción **El usuario puede registrar aplicaciones** permitidas en el nivel de inquilino.

## <a name="forbidden-to-access-key-vault-error-occurs-during-appliance-registration"></a>Se produce el error "Prohibido acceder a Key Vault" durante el registro del dispositivo

Error de la operación de creación o actualización del almacén de claves "{KeyVaultName}" de Azure Key Vault por el siguiente motivo: "{KeyVaultErrorMessage}".

Esta incidencia suele ocurrir cuando la cuenta de usuario de Azure usada para registrar el dispositivo es diferente de la cuenta que se usó para generar la clave de proyecto de Azure Migrate en el portal (es decir, cuando se creó el almacén de claves).

### <a name="remediation"></a>Corrección

1. Asegúrese de que la cuenta de usuario con la sesión iniciada en el dispositivo tenga los permisos necesarios en el almacén de claves (mencionado en el mensaje de error). La cuenta de usuario necesita los permisos que se mencionan en [este sitio web](./tutorial-discover-vmware.md#prepare-an-azure-user-account).
1. Vaya al almacén de claves y asegúrese de que la cuenta de usuario tenga una directiva de acceso con todos los permisos de **clave**, **secreto** y **certificado** asignados en **Directiva de acceso del Key Vault**. [Más información](../key-vault/general/assign-access-policy-portal.md).
1. Si ha habilitado el dispositivo para **conectividad de punto de conexión privado**, asegúrese de que el dispositivo se hospede en la misma red virtual donde se haya creado el almacén de claves o esté conectado a la red virtual de Azure (donde se haya creado el almacén) a través de un vínculo privado. Asegúrese de que el vínculo privado del almacén de claves se pueda resolver desde el dispositivo. Vaya a **Azure Migrate: detección y valoración** > **Propiedades** para encontrar los detalles de puntos de conexión privados para recursos como el almacén de claves creado durante el paso de creación de claves de Azure Migrate. [Más información](./troubleshoot-network-connectivity.md).
1. Si tiene la conectividad y los permisos necesarios, vuelva a intentar el registro en el dispositivo después de un tiempo.

## <a name="unable-to-connect-to-vcenter-server-during-validation"></a>No se puede conectar con vCenter Server durante la validación

Si obtiene este error de conexión, es posible que no pueda conectarse a vCenter Server *NombreDelServidor*.com:9443. Los detalles del error indican que no hay ningún punto de conexión escuchando en `https://\*servername*.com:9443/sdk` que pueda aceptar el mensaje.

### <a name="remediation"></a>Corrección

- Compruebe si tiene en ejecución la versión más reciente del dispositivo. Si no es así, actualice el dispositivo a la [versión más reciente](./migrate-appliance.md).
- Si el problema persiste con la última versión, es posible que el dispositivo no pueda resolver el nombre de vCenter Server especificado o que el puerto especificado sea incorrecto. De forma predeterminada, si no se especifica el puerto, el recopilador intenta conectar con el número de puerto 443.

    1. Haga ping a *NombreDelServidor*.com desde el dispositivo.
    1. Si se produce un error en el paso 1, intente conectarse al Servidor vCenter mediante la dirección IP.
    1. Identifique el número de puerto correcto para conectarse a vCenter Server.
    1. Compruebe que el servidor vCenter está en funcionamiento.

## <a name="server-credentials-domain-fails-validation-on-the-vmware-appliance"></a>Error de validación de credenciales de servidor (dominio) en el dispositivo VMware

Aparece "Error de validación" con relación a las credenciales de dominio agregadas en el dispositivo VMware para realizar el inventario de software y el análisis de dependencias sin agente.

### <a name="remediation"></a>Corrección

1. Compruebe que ha especificado el nombre de dominio y las credenciales correctos.
1. Asegúrese de que se pueda acceder al dominio desde el dispositivo para validar las credenciales. Es posible que el dispositivo tenga problemas de línea de visión o que el nombre de dominio no se pueda resolver desde el servidor del dispositivo.
1. Seleccione **Editar** para actualizar el nombre de dominio o las credenciales. Seleccione **Volver a validar las credenciales** para volver a validar las credenciales después de un tiempo.

## <a name="access-is-denied-error-occurs-when-you-connect-to-hyper-v-hosts-or-clusters-during-validation"></a>Se produce el error "Acceso denegado" al conectarse a hosts o clústeres de Hyper-V durante la validación

No puede validar el host o clúster de Hyper-V agregado debido al error "Acceso denegado".

### <a name="remediation"></a>Corrección

1. Asegúrese de que haber cumplido todos los [requisitos previos para los hosts de Hyper-V](./migrate-support-matrix-hyper-v.md#hyper-v-host-requirements). 
1. Consulte los pasos que se indican en [este sitio web](./tutorial-discover-hyper-v.md#prepare-hyper-v-hosts) sobre cómo preparar los hosts de Hyper-V manualmente o mediante un script de PowerShell de aprovisionamiento.

## <a name="the-server-does-not-support-ws-management-identify-operations-error-occurs-during-validation"></a>Se produce el error "El servidor no admite operaciones de identificación de WS-Management" durante la validación

No puede validar clústeres de Hyper-V en el dispositivo debido a un error con el siguiente mensaje: "El servidor no admite operaciones de identificación de WS-Management. Omita la parte TestConnection de la solicitud e inténtelo de nuevo".

### <a name="remediation"></a>Corrección

Este error suele producirse cuando se ha proporcionado una configuración de proxy en el dispositivo. El dispositivo se conecta a los clústeres con el nombre corto de los nodos del clúster, aunque haya proporcionado el FQDN del nodo. Agregue el nombre corto de los nodos del clúster a la lista de omisión de proxy en el dispositivo, para que el problema se resuelva y la validación del clúster de Hyper-V se realice correctamente.

## <a name="cant-connect-to-host-or-cluster-error-occurs-during-validation-on-a-hyper-v-appliance"></a>Se produce el error "No se puede conectar con el host o clúster" durante la validación en el dispositivo de Hyper-V

Puede producirse el error "No se puede conectar a un host o un clúster porque no se puede resolver el nombre del servidor. Código de error WinRM: 0x803381B9" si el servicio Azure DNS del dispositivo no puede resolver el nombre de host o el clúster que ha proporcionado.

Esta incidencia suele ocurrir cuando se ha agregado la dirección IP de un host que no se puede resolver mediante DNS. También puede ver este error en los hosts de un clúster. Esto indica que el dispositivo puede conectarse al clúster, pero el clúster devuelve nombres de host que no son nombres de dominio completos.

### <a name="remediation"></a>Corrección

Para resolver este error, actualice el archivo de hosts del dispositivo; para ello, agregue una asignación de la dirección IP y los nombres de host.
1. Abra el Bloc de notas como administrador.
1. Abra el archivo C:\Windows\System32\Drivers\etc\hosts.
1. Agregue la dirección IP y el nombre de host en una fila. Repita el procedimiento para cada host o clúster en el que observe este error.
1. Guarde el archivo de hosts y ciérrelo.
1. Compruebe si el dispositivo puede conectarse a los hosts mediante la aplicación de administración del dispositivo. Después de 30 minutos, debería ver la información más reciente sobre estos hosts en Azure Portal.

## <a name="unable-to-connect-to-server-error-occurs-during-validation-of-physical-servers"></a>Se produce el error "No se puede conectar con el servidor" durante la validación de servidores físicos

### <a name="remediation"></a>Corrección

- Asegúrese de que haya conectividad entre el dispositivo y el servidor de destino.
- Si se trata de un servidor Linux, siga estos pasos para asegurarse de que la autenticación basada en contraseña esté habilitada:
    1. Inicie sesión en el servidor y abra el archivo de configuración SSH con el comando **vi/etc/ssh/sshd_config**.
    1. Establezca la opción **PasswordAuthentication** en Sí. Guarde el archivo.
    1. Reinicie el servicio SSH ejecutando **service sshd restart**.
- Si es un servidor Windows, asegúrese de que el puerto 5985 esté abierto para permitir llamadas de WMI remotas.
- Si detecta un servidor Linux GCP y usa un usuario raíz, utilice los siguientes comandos para cambiar la configuración predeterminada del inicio de sesión raíz:
    1. Inicie sesión en el servidor y abra el archivo de configuración SSH con el comando **vi/etc/ssh/sshd_config**.
    1. Establezca la opción **PermitRootLogin** en Sí.
    1. Reinicie el servicio SSH ejecutando **service sshd restart**.

## <a name="failed-to-fetch-bios-guid-error-occurs-for-the-server-during-validation"></a>Se produce el error "Error al capturar el GUID del BIOS" para el servidor durante la validación

Se produce un error de la validación de un servidor físico en el dispositivo con el mensaje "Error al capturar el GUID del BIOS".

### <a name="remediation"></a>Corrección

**Servidores Linux:**

Conéctese al servidor de destino en el que se produce un error de validación. Ejecute los siguientes comandos para ver si devuelve el GUID del BIOS del servidor:

````
cat /sys/class/dmi/id/product_uuid
dmidecode | grep -i uuid | awk '{print $2}'
````
También puede ejecutar los comandos desde el símbolo del sistema en el servidor del dispositivo realizando una conexión SSH con el servidor Linux de destino mediante el siguiente comando:
````
ssh <username>@<servername>
````

**Servidores Windows:**

Ejecute el siguiente código en PowerShell desde el servidor del dispositivo para el de destino con el error de validación, con el fin de comprobar si devuelve el GUID del BIOS del servidor:

````
[CmdletBinding()]
Param(
  [Parameter(Mandatory=$True,Position=1)]
   [string]$Hostname
)
$HostNS = "root\cimv2"
$error.Clear()

$Cred = Get-Credential
$Session = New-CimSession -Credential $Cred -ComputerName $Hostname

if ($Session -eq $null -or $Session.TestConnection() -eq $false)
{
    Write-Host "Connection failed with $Hostname due to $error"
    exit -1
}

Write-Host "Connection established with $Hostname"
#Get-WmiObject -Query "select uuid from Win32_ComputerSystemProduct" 

$HostIntance = $Session.QueryInstances($HostNS, "WQL", "Select UUID from Win32_ComputerSystemProduct")
$HostIntance | fl *
````

Al ejecutar el código anterior, debe proporcionar el nombre de host del servidor de destino. Puede ser dirección IP, FQDN o nombre de host. Después, se le pide que proporcione las credenciales para conectarse al servidor.

## <a name="no-suitable-authentication-method-found-error-occurs-for-the-server-during-validation"></a>Se produce el error "No se ha encontrado ningún método de autenticación adecuado" para el servidor durante la validación

Aparece el error "No se ha encontrado ningún método de autenticación adecuado" al intentar validar un servidor Linux a través del dispositivo físico.

### <a name="remediation"></a>Corrección

Asegúrese de que la autenticación basada en contraseña está habilitada en el servidor Linux mediante los pasos siguientes:

1. Inicie sesión en el servidor de Linux. Abra el archivo de configuración SSH con el comando **vi/etc/ssh/sshd_config**.
1. Establezca la opción **PasswordAuthentication** en **Sí**. Guarde el archivo.
1. Reinicie el servicio SSH ejecutando **service sshd restart**.

## <a name="access-is-denied-error-occurs-when-you-connect-to-physical-servers-during-validation"></a>Se produce el error "Acceso denegado" al conectarse a servidores físicos durante la validación

Aparece el error "El servicio WS-Management no puede procesar la solicitud. El servicio WMI devolvió un error de acceso denegado" al intentar validar un servidor Windows a través del dispositivo físico.

### <a name="remediation"></a>Corrección

- Si recibe este error, asegúrese de que la cuenta de usuario proporcionada (dominio/local) en el administrador de configuración del dispositivo se haya agregado a estos grupos: "Usuarios de administración remota", "Usuarios de Monitor de rendimiento" y "Usuarios del registro de rendimiento".
- Si no aparece el grupo "Usuarios de administración remota", agregue la cuenta de usuario al grupo: WinRMRemoteWMIUsers_.
- También puede comprobar si el protocolo WS-Management está habilitado en el servidor ejecutando el siguiente comando en el símbolo del sistema del servidor de destino: `winrm qc`
- Si persiste el problema, asegúrese de que la cuenta de usuario tenga permisos de acceso al espacio de nombres CIMV2 y a los subespacios de nombres en panel de control de WMI. Siga estos pasos para establecer el acceso:

    1. Vaya al servidor con el error de validación en el dispositivo.
    1. Busque y seleccione **Ejecutar** en el menú **Inicio**. En el cuadro de diálogo **Ejecutar**, escriba **wmimgmt.msc** en el cuadro de texto **Abrir** y seleccione **Entrar**.
    1. Se abre la consola wmimgmt, en la que puede encontrar **Control WMI (local)** en el panel izquierdo. Haga clic con el botón derecho y seleccione **Propiedades** en el menú.
    1. En el cuadro de diálogo **Propiedades: Control WMI (local)** , seleccione la pestaña **Seguridad**.
    1. En la pestaña **Valores**, expanda la carpeta **Raíz** en el árbol de espacios de nombres y seleccione el espacio **cimv2**.
    1. Seleccione **Seguridad** para abrir el diálogo **Seguridad para ROOT\cimv2**.
    1. En la sección **Nombres de grupos o usuarios**, seleccione **Agregar** para abrir el diálogo **Seleccionar usuarios, equipos, cuentas de servicio o grupos**.
    1. Busque la cuenta de usuario, selecciónela y seleccione **Aceptar** para volver al cuadro de diálogo **Seguridad para ROOT\cimv2**.
    1. En la sección **Nombres de grupos o usuarios**, seleccione la cuenta de usuario que acaba de agregar. Compruebe que se admiten los siguientes permisos:<br/>
       - Habilitación de cuenta <br/>
       - Habilitación remota
    1. Seleccione **Aplicar** para habilitar los permisos establecidos en la cuenta de usuario.

- Los mismos pasos también son aplicables a una cuenta de usuario local para servidores que no sean de dominio o grupo de trabajo. En algunos casos el filtrado de [UAC](/windows/win32/wmisdk/user-account-control-and-wmi) puede bloquear ciertas propiedades WMI, ya que los comandos se ejecutan como usuario estándar. Por este motivo, puede usar una cuenta de administrador local o deshabilitar UAC para que la cuenta de usuario local no se filtre y se convierta en un administrador completo.
- Aunque no se recomienda deshabilitar el UAC remoto cambiando la entrada de registro que lo controla, puede que sea necesario en un grupo de trabajo. La entrada de registro es HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system\LocalAccountTokenFilterPolicy. Cuando el valor de esta entrada es cero (0), se habilita el filtrado de tokens de acceso del UAC remoto. Cuando el valor es 1, el UAC remoto está deshabilitado.

## <a name="appliance-is-disconnected"></a>Dispositivo desconectado

Aparece el mensaje de error "Dispositivo desconectado" al intentar habilitar la replicación en varios servidores VMware desde el portal.

Este error puede ocurrir si el dispositivo está en estado apagado o el servicio DRA del dispositivo no se puede comunicar con Azure.

### <a name="remediation"></a>Corrección

 1. Vaya al administrador de configuración del dispositivo y vuelva a ejecutar los requisitos previos para ver el estado del servicio DRA en **View appliance services** (Ver servicios del dispositivo). 
 1. Si el servicio no está ejecución, deténgalo y reinícielo desde el símbolo del sistema con los siguientes comandos:

    ````
    net stop dra
    net start dra
    ````

## <a name="next-steps"></a>Pasos siguientes

Configuración de un dispositivo para [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md), o [servidores físicos](how-to-set-up-appliance-physical.md).
