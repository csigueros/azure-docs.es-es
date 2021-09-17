---
title: 'Autorización de un grupo de hosts de Azure Virtual Desktop para Azure Files: Azure'
description: Cómo autorizar un grupo de hosts de Azure Virtual Desktop para usar Azure Files.
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 2593552fbad04ada5e903ba1eb6a2613e3ee55a0
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446733"
---
# <a name="authorize-an-account-for-azure-files"></a>Autorización de una cuenta para Azure Files

En este artículo se muestra cómo autorizar un grupo de hosts de Azure Virtual Desktop para usar Azure Files.

## <a name="requirements"></a>Requisitos

Antes de empezar, necesitará lo siguiente:

- Una cuenta de Active Directory Domain Services (AD DS) sincronizada con Azure Active Directory (Azure AD)
- Permisos para crear un grupo en AD DS
- Una cuenta de almacenamiento y los permisos necesarios para crear una nueva cuenta de almacenamiento, si es necesario
- Una máquina virtual (VM) o una máquina física unida a AD DS a la que tiene permiso de acceso
- Un grupo de hosts de Azure Virtual Desktop en el que todos los hosts de sesión se han unido a un dominio

## <a name="create-a-security-group-in-active-directory-domain-services"></a>Creación de un grupo de seguridad de Active Directory Domain Services

En primer lugar, deberá crear un grupo de seguridad en AD DS. Este grupo de seguridad se usará en pasos posteriores para conceder permisos de nivel de recurso compartido y de nuevo sistema de archivos de tecnología (NTFS).

>[!NOTE]
>Si tiene un grupo de seguridad existente que prefiere usar, seleccione el nombre de ese grupo en lugar de crear uno nuevo.

Para crear un grupo de seguridad:

1. Abra una sesión remota con la máquina virtual o la máquina física unida AD DS que quiera agregar al grupo de seguridad.

2. Abra **Usuarios y equipos de Active Directory**.

3. En el nodo de dominio, haga clic con el botón derecho en el nombre de la máquina. En el menú desplegable, seleccione **Nuevo** > **Grupo**.

4. En la ventana **Nuevo objeto: grupo**, escriba el nombre del grupo nuevo y, a continuación, seleccione los valores siguientes:

    - En **Ámbito de grupo**, seleccione **Global**.
    - En **Tipo de grupo**, seleccione **Seguridad**.

5. Haga clic con el botón derecho en el grupo nuevo y seleccione **Propiedades**.

6. En la ventana **Propiedades**, seleccione la pestaña **Miembros**.

7. Seleccione **Agregar…** .

8. En la ventana **Seleccionar usuarios, contactos, equipos, cuentas de servicio o grupos**, seleccione **Tipos de objeto…** > **Equipos**. Cuando haya finalizado, seleccione **Aceptar**.

9. En la ventana **Escriba los nombres de objeto que desea seleccionar**, escriba los nombres de todos los hosts de sesión que desea incluir en el grupo de seguridad.

10. Seleccione **Comprobar nombres** y, a continuación, seleccione el nombre del host de sesión que desea usar en la lista que aparece.

11. Seleccione **Aceptar** y después **Aplicar**.

>[!NOTE]
>Los nuevos grupos de seguridad pueden tardar hasta una hora en sincronizarse con Azure AD.

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Si aún no ha creado una cuenta de almacenamiento, siga primero las instrucciones de [Creación de una cuenta de almacenamiento](../storage/common/storage-account-create.md). Al crear una nueva cuenta de almacenamiento, asegúrese de crear también un nuevo recurso compartido de archivos.

>[!NOTE]
>Si va a crear una cuenta de almacenamiento **Premium**, asegúrese de que **Tipo de cuenta** está establecido en **FileStorage**.

## <a name="get-rbac-permissions"></a>Obtención de permisos de RBAC

Para obtener permisos de RBAC:

1. Seleccione la cuenta de almacenamiento que desea usar.

2. Seleccione **Control de acceso (IAM)** y después **Agregar**. A continuación, seleccione **Agregar asignaciones de roles** en el menú desplegable.

3. En la pantalla **Agregar asignación de roles**, seleccione los siguientes valores:

    - En **Rol**, seleccione **Colaborador de recursos compartidos de SMB de datos de archivos de Storage**.
    - En **Asignar acceso a**, seleccione **Usuario, grupo o entidad de servicio**.
    - En **Suscripción**, seleccione **En función del entorno**.
    - En **Seleccionar**, seleccione el nombre del grupo de Active Directory que contiene los hosts de sesión.

4. Seleccione **Guardar**.

## <a name="join-your-storage-account-to-ad-ds"></a>Unión de la cuenta de almacenamiento a AD DS

A continuación, deberá unir la cuenta de almacenamiento a AD DS. Para unir la cuenta a AD DS:

1. Abra una sesión remota en una máquina virtual o una máquina física unida a AD DS.

      >[!NOTE]
      > Ejecute el script con una credencial de AD DS local que esté sincronizada con su instancia de Azure AD. La credencial de AD DS local debe tener los permisos de la cuenta de propietario o del rol de Azure de colaborador.

2. Descargue y descomprima [la versión más reciente en AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases).

3. Abra **PowerShell** en un modo con privilegios elevados.

4. Ejecute el cmdlet siguiente para configurar la directiva de ejecución:
    
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

5. A continuación, vaya a la carpeta donde descomprimió AzfileHybrid y ejecute este comando:

    ```powershell
    .\\CopyToPSPath.ps1
    ```

6. Después, importe el módulo AzFilesHybrid mediante la ejecución de este cmdlet:
   
   ```powershell
   Import-Module -Name AzFilesHybrid
   ```

7. A continuación, use el siguiente cmdlet para conectarse a Azure AD:
   
   ```powershell
   Connect-AzAccount
   ```

8. Establezca los parámetros siguientes y asegúrese de reemplazar los marcadores de posición por los valores pertinentes para su escenario:

    ```powershell
    $SubscriptionId = "<your-subscription-id-here>"

    $ResourceGroupName = "<resource-group-name-here>"

    $StorageAccountName = "<storage-account-name-here>"
    ```

9.  Por último, ejecute este comando:

    ```powershell
    Join-AzStorageAccountForAuth `

    -ResourceGroupName $ResourceGroupName `

    -StorageAccountName $StorageAccountName `

    -DomainAccountType "ComputerAccount" `

    -OrganizationalUnitDistinguishedName "<ou-here>" `

    -EncryptionType "'RC4','AES256'"
    ```

## <a name="get-ntfs-level-permissions"></a>Obtención de permisos de nivel NTFS

Para autenticarse con cuentas de equipo de AD DS en una cuenta de almacenamiento de Azure Files, también debemos asignar permisos de nivel NTFS además del permiso RBAC que configuramos anteriormente.

Para asignar permisos de nivel NTFS:

1. Abra Azure Portal y vaya a la cuenta de almacenamiento que hemos agregado a AD DS.

2. Seleccione **Claves de acceso** y copie el valor en el campo **Clave1**.

3. Inicie una sesión remota en una máquina virtual o una máquina física unida a AD DS.

4. Abra un símbolo del sistema con privilegios elevados.

5. Ejecute el siguiente comando, con los marcadores de posición reemplazados por los valores pertinentes para la implementación:

    ```cmd
    net use <desired-drive-letter>:
    \\<storage-account-name>.file.core.windows.net\<share-name>
    /user:Azure\<storage-account-name> <storage-account-key>
    ```

    >[!NOTE]
    >Al ejecutar este comando, la salida debe indicar "El comando se completó correctamente". Si no es así, compruebe la entrada e inténtelo de nuevo.

6. Abra **Explorador de archivos** y busque la letra de unidad que usó en el comando en el paso 5.

7. Haga clic con el botón derecho en la letra de unidad y seleccione **Propiedades** > **Seguridad** en el menú desplegable.

8. Seleccione **Editar** y, a continuación, seleccione **Agregar…** .

    >[!NOTE]
    >Asegúrese de que el nombre de dominio coincide con el nombre de dominio de AD DS. Si no es así, significa que la cuenta de almacenamiento no se ha unido al dominio. Tendrá que usar una cuenta unida a un dominio para poder continuar.

9. Escriba sus credenciales de administrador, si se le solicitan.

10. En la ventana **Seleccionar usuarios, equipos, cuentas de servicio o grupos**, escriba el nombre del grupo en [Crear un grupo de seguridad en Active Directory Domain Services](#create-a-security-group-in-active-directory-domain-services).

11. Seleccione **Aceptar**. Después, confirme que el grupo tiene el permiso **Leer y ejecutar**. Si el grupo tiene permisos, se debe seleccionar la casilla "Permitir", como se muestra en la siguiente imagen:

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la ventana Seguridad. En una lista marcada como "Permisos", el permiso "Leer y ejecutar" tiene una marca de verificación verde en la columna "Permitir".](media/read-and-execute.png)

12. Agregue el grupo de Active Directory con las cuentas de equipo con permisos **Leer y ejecutar** al grupo de seguridad.

13. Seleccione **Aplicar**. Si ve un mensaje de Seguridad de Windows, seleccione **Sí** para confirmar los cambios.

## <a name="next-steps"></a>Pasos siguientes

Si tiene algún problema después de la instalación, consulte nuestro [artículo de solución de problemas de Azure Files](troubleshoot-authorization.md).