---
title: 'Control de lo que un usuario puede hacer en el nivel de archivo: recursos compartidos de archivos de Azure'
description: Obtenga información sobre cómo configurar permisos de ACL de Windows para la autenticación de AD DS local en recursos compartidos de archivos de Azure. Esto le permitirá sacar partido del control de acceso granular.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: efa2ec8873374604e252677e436e6883867f982a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255493"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Parte 3: Configuración de permisos de nivel de directorio y de archivo en SMB 

Antes de comenzar este artículo, asegúrese de completar el anterior, [Asignación de permisos de nivel de recurso compartido a una identidad](storage-files-identity-ad-ds-assign-permissions.md), para garantizar la vigencia de los permisos de nivel de recurso compartido.

Tras asignar permisos de nivel de recurso compartido con Azure RBAC, debe configurar las ACL de Windows adecuadas a los niveles de raíz, de directorio o de archivo para sacar partido del control de acceso granular. Considere los permisos de nivel de recurso compartido de Azure RBAC como el equipo selector de alto nivel que determina si un usuario puede acceder al recurso compartido. Las ACL de Windows funcionan en un nivel más granular para determinar qué operaciones puede realizar el usuario en el directorio o archivo. Los permisos tanto de nivel de recurso compartido como de nivel de archivo o de directorio se aplican cuando un usuario intenta acceder a un archivo o directorio, de modo que, si existe alguna una diferencia entre alguno de ellos, solo se aplicará el más restrictivo. Por ejemplo, si un usuario tiene acceso de lectura y escritura en el nivel de archivo, pero solo de lectura en un nivel de recurso compartido, solo podrá leer ese archivo. Y lo mismo sucede a la inversa: si un usuario tuviera acceso de lectura y escritura en el nivel de recurso compartido, pero solo de lectura en el nivel de archivo, solo podría leer el archivo.

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="azure-rbac-permissions"></a>Permisos de Azure RBAC

En la siguiente tabla se incluyen los permisos de RBAC de Azure relacionados con esta configuración:


| Rol integrado  | Permiso NTFS  | Acceso resultante  |
|---------|---------|---------|
|Lector de recursos compartidos de SMB de datos de archivos de Storage | Control total, modificación, lectura, escritura y ejecución | Leer y ejecutar  |
|     |   Lectura |     Lectura  |
|Colaborador de recursos compartidos de SMB de datos de archivos de Storage  |  Control total    |  Modificación, lectura, escritura y ejecución |
|     |  Modificar         |  Modificar    |
|     |  Leer y ejecutar |  Leer y ejecutar |
|     |  Lectura           |  Lectura    |
|     |  Escritura          |  Escritura   |
|Colaborador elevado de recursos compartidos de SMB de datos de archivos de Storage | Control total  |  Modificar, leer, escribir, editar (modificar los permisos), ejecutar |
|     |  Modificar          |  Modificar |
|     |  Leer y ejecutar  |  Leer y ejecutar |
|     |  Lectura            |  Lectura   |
|     |  Escritura           |  Escritura  |



## <a name="supported-permissions"></a>Permisos admitidos

Azure Files admite el conjunto completo de ACL de Windows básicas y avanzadas. Las ACL de Windows se pueden ver y configurar en los directorios y archivos de un recurso compartido de archivos de Azure; para ello, hay que montar el recurso compartido y, después, usar el Explorador de archivos de Windows o ejecutar los comandos [icacls](/windows-server/administration/windows-commands/icacls) o [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) de Windows. 

Para configurar ACL con permisos de superusuario, debe montar el recurso compartido con la clave de la cuenta de almacenamiento de la máquina virtual unida al dominio. Siga las instrucciones de la siguiente sección para montar un recurso compartido de archivos de Azure desde el símbolo del sistema y para configurar ACL de Windows.

Los siguientes permisos están incluidos en el directorio raíz de un recurso compartido de archivos:

- BUILTIN\Administrators:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

|Usuarios|Definición|
|---|---|
|BUILTIN\Administrators|Todos los usuarios que son administradores de dominio del entorno de AD DS local.
|BUILTIN\Users|Grupo de seguridad integrado en AD. Incluye NT AUTHORITY\Authenticated Users de forma predeterminada. En el caso de un servidor de archivos tradicional, puede configurar la definición de pertenencia por servidor. Para Azure Files, no hay un servidor de hospedaje, por lo que BUILTIN\Users incluye el mismo conjunto de usuarios que NT AUTHORITY\Authenticated Users.|
|NT AUTHORITY\SYSTEM|La cuenta de servicio del sistema operativo del servidor de archivos. Esta cuenta de servicio no se aplica en el contexto de Azure Files. Se incluye en el directorio raíz para ser coherente con la experiencia del servidor de archivos de Windows para escenarios híbridos.|
|NT AUTHORITY\Authenticated Users|Todos los usuarios de AD que pueden obtener un token de Kerberos válido.|
|CREATOR OWNER|Cada objeto del directorio o archivo tiene un propietario para ese objeto. Si hay ACL asignadas a "CREATOR OWNER" en ese objeto, el usuario que es propietario de este objeto tiene los permisos para el objeto definido por la ACL.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Montar un recurso compartido de archivos de Azure desde el símbolo del sistema

Use el comando `net use` de Windows para montar el recurso compartido de archivos de Azure. No olvide reemplazar los valores del marcador de posición del ejemplo por los suyos propios. Para más información sobre cómo montar los recursos compartidos de archivos, consulte [Uso de un recurso compartido de archivos de Azure con Windows](storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Si tiene problemas para conectarse a Azure Files, vea [la herramienta de solución de problemas publicada para solucionar los errores de montaje de Azure Files en Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). También proporcionamos una [guía](./storage-files-faq.md#on-premises-access) para solucionar aquellos escenarios en los que el puerto 445 está bloqueado. 

## <a name="configure-windows-acls"></a>Configuración de ACL de Windows

Una vez montado el recurso compartido de archivos con la clave de la cuenta de almacenamiento, debe configurar las ACL de Windows (también conocidas como permisos NTFS). Las ACL de Windows se pueden configurar con el Explorador de archivos de Windows o con icacls.

Si tiene directorios o archivos en servidores de archivos locales con ACL de Windows configuradas con identidades de AD DS, puede copiarlos en Azure Files, conservando las ACL con herramientas tradicionales de copia de archivos como Robocopy o [Azure AzCopy versión 10.4+](https://github.com/Azure/azure-storage-azcopy/releases). Si los directorios y archivos están organizados en niveles en Azure Files a través de Azure File Sync, las ACL se trasladan y conservan en su formato nativo.

### <a name="configure-windows-acls-with-icacls"></a>Configuración de ACL de Windows con icacls

Utilice el siguiente comando de Windows para conceder permisos completos para todos los directorios y archivos en el recurso compartido de archivos, incluido el directorio raíz. No olvide reemplazar los valores del marcador de posición en el ejemplo por los suyos propios.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para más información sobre cómo usar icacls para establecer ACL de Windows, así como sobre los distintos tipos de permisos admitidos, vea la [referencia de línea de comandos de icacls](/windows-server/administration/windows-commands/icacls).

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Configuración de ACL de Windows con el Explorador de archivos de Windows

Utilice el Explorador de archivos de Windows para conceder permisos completos para todos los directorios y archivos en el recurso compartido de archivos, incluido el directorio raíz. Si no puede cargar la información de dominio de AD correctamente en el Explorador de archivos de Windows, probablemente se deba a una configuración de confianza en el entorno de AD local. La máquina cliente no pudo comunicarse con el controlador de dominio de AD registrado para la autenticación Azure Files. En este caso, use icacls para la configuración de ACL de Windows.

1. Abra el Explorador de archivos de Windows y haga clic con el botón derecho en el archivo o directorio, y seleccione **Propiedades**.
1. Seleccione la pestaña **Seguridad** .
1. Seleccione **Editar...** para cambiar los permisos.
1. Puede cambiar el permiso de los usuarios existentes o seleccionar **Agregar...** para conceder permisos a los nuevos usuarios.
1. En la ventana del símbolo del sistema para agregar nuevos usuarios, escriba el nombre de usuario de destino al que quiera conceder permisos en el cuadro **Escriba los nombres de objeto que desea seleccionar** y seleccione **Comprobar nombres** para buscar el nombre UPN completo del usuario de destino.
1.    Seleccione **Aceptar**.
1.    En la pestaña **Seguridad**, seleccione todos los permisos que desea conceder al nuevo usuario.
1.    Seleccione **Aplicar**.


## <a name="next-steps"></a>Pasos siguientes

Ahora que la característica está habilitada y configurada, avance al artículo siguiente, donde montará el recurso compartido de archivos de Azure desde una máquina virtual unida a un dominio.

[Parte 4: Montaje de un recurso compartido de archivos desde una máquina virtual unida al dominio](storage-files-identity-ad-ds-mount-file-share.md)
