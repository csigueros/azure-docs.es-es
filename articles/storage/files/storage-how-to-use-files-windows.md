---
title: Montaje de un recurso compartido de archivos de Azure de SMB en Windows | Microsoft Docs
description: Aprenda a usar un recurso compartido de archivos de Azure con Windows y Windows Server. Use recursos compartidos de archivos de Azure con SMB 3.x en instalaciones de Windows que se ejecuten de forma local o en máquinas virtuales de Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fecf2ea565343ad2f91471ba1be98df513b55478
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115703"
---
# <a name="mount-smb-azure-file-share-on-windows"></a>Montaje de un recurso compartido de archivos de Azure de SMB en Windows
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. Los recursos compartidos de archivos de Azure se pueden usar sin problemas en Windows y Windows Server. En este artículo se describen los aspectos que se deben tener en cuenta al usar un recurso compartido de archivos de Azure con Windows y Windows Server.

Para usar un recurso compartido de archivos de Azure por medio del punto de conexión público fuera de la región de Azure en la que se hospeda, bien sea en el entorno local o en otra región de Azure, el sistema operativo debe admitir SMB 3.x. Las versiones anteriores de Windows que solo admiten SMB 2.1 no pueden montar recursos compartidos de archivos de Azure por medio del punto de conexión público.

| Versión de Windows | Versión de SMB | Cifrado máximo del canal SMB |
|-|-|-|-|
| Windows 10, versión 21H1 | SMB 3.1.1 | AES-256-GCM |
| Canal semianual de Windows Server, versión 21H1 | SMB 3.1.1 | AES-256-GCM |
| Windows Server 2019 | SMB 3.1.1 | AES-128-GCM |
| Windows 10<br />Versiones: 1607, 1809, 1909, 2004 y 20H2 | SMB 3.1.1 | AES-128-GCM |
| Canal semianual de Windows Server<br />Versiones: 2004 y 20H2 | SMB 3.1.1 | AES-128-GCM |
| Windows Server 2016 | SMB 3.1.1 | AES-128-GCM |
| Windows 10, versión 1507 | SMB 3.0 | AES-128-GCM |
| Windows 8.1 | SMB 3.0 | AES-128-CCM |
| Windows Server 2012 R2 | SMB 3.0 | AES-128-CCM |
| Windows Server 2012 | SMB 3.0 | AES-128-CCM |
| Windows 7<sup>1</sup> | SMB 2.1 | No compatible |
| Windows Server 2008 R2<sup>1</sup> | SMB 2.1 | No compatible |

<sup>1</sup>El soporte técnico habitual de Microsoft para Windows 7 y Windows Server 2008 R2 ha finalizado. Es posible adquirir soporte técnico adicional para las actualizaciones de seguridad solo a través del programa [Actualización de seguridad extendida (ESU)](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates). Se recomienda encarecidamente migrar de estos sistemas operativos.

> [!Note]  
> Siempre se recomienda disponer de la KB más reciente para su versión de Windows.

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>Requisitos previos 
Asegúrese de que el puerto 445 esté abierto: el protocolo SMB requiere que esté abierto el puerto TCP 445; las conexiones producirán errores si el puerto 445 está bloqueado. Puede comprobar si el firewall está bloqueando el puerto 445 con el cmdlet `Test-NetConnection`. Para obtener información sobre las formas de solucionar un puerto 445 bloqueado, vea la sección [Causa 1: el puerto 445 está bloqueado](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) de nuestra guía de solución de problemas de Windows.

## <a name="using-an-azure-file-share-with-windows"></a>Uso de un recurso compartido de archivos de Azure con Windows
Para usar un recurso compartido de archivos de Azure con Windows, debe montarlo, lo que significa asignarle una letra de unidad o una ruta de acceso a un punto de montaje, o acceder a él mediante su [ruta de acceso UNC](/windows/win32/fileio/naming-a-file). 

En este artículo se usa la clave de la cuenta de almacenamiento para tener acceso al recurso compartido de archivos. Una clave de cuenta de almacenamiento es una clave de administrador para una cuenta de almacenamiento, lo que incluye los permisos de administrador de todos los archivos y carpetas dentro de un recurso compartido de archivos al que accede, y de todos los recursos compartidos de archivos y otros recursos de almacenamiento (blobs, colas, tablas, etc.) contenidos en la cuenta de almacenamiento. Si esto no es suficiente para la carga de trabajo, se puede usar [Azure File Sync](../file-sync/file-sync-planning.md), o bien se puede usar la [autenticación basada en identidad a través de SMB](storage-files-active-directory-overview.md).

Un patrón común para elevar y desplazar aplicaciones de línea de negocio (LOB) que esperan un recurso compartido de archivos de SMB es usar un recurso compartido de archivos de Azure como alternativa a ejecutar un servidor de archivos de Windows dedicado en una máquina virtual de Azure. Un aspecto importante que se debe tener en cuenta para migrar correctamente una aplicación de línea de negocio para usar un recurso compartido de archivos de Azure es que muchas de estas aplicaciones se ejecutan en el contexto de una cuenta de servicio dedicada con permisos de sistema limitados y no en la cuenta administrativa de la máquina virtual. Por lo tanto, debe asegurarse de montar o guardar las credenciales del recurso compartido de archivos de Azure desde el contexto de la cuenta de servicio y no de la cuenta administrativa.

### <a name="mount-the-azure-file-share"></a>Montaje del recurso compartido de archivos de Azure

Azure Portal proporciona un script que puede usar para montar el recurso compartido de archivos directamente en un host. Se recomienda usar este script proporcionado.

Para obtener este script:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Vaya a la cuenta de almacenamiento que contiene el recurso compartido de archivos que le gustaría montar.
1. Seleccione **Recursos compartidos de archivos**.
1. Seleccione el recurso compartido de archivos que desea montar.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="Captura de pantalla de la hoja de recursos compartidos de archivos con el recurso compartido de archivos resaltado.":::

1. Seleccione **Conectar**.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Captura de pantalla del icono de conexión para el recurso compartido de archivos.":::

1. Seleccione la letra de unidad en la que montar el recurso compartido.
1. Copie el script proporcionado.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Captura de pantalla de la hoja de conexión con el botón de copia en el script resaltado.":::

1. Pegue el script en un shell del host en el que desea montar el recurso compartido de archivos y ejecútelo.

Ya ha montado el recurso compartido de archivos de Azure.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Montaje del recurso compartido de archivos de Azure con el Explorador de archivos
> [!Note]  
> Tenga en cuenta que las instrucciones siguientes se muestran en Windows 10 y pueden variar ligeramente en las versiones anteriores. 

1. Abra el Explorador de archivos. Para ello, puede abrir el menú Inicio o presionar el método abreviado Win+E.

1. Vaya a **Este PC** en el lado izquierdo de la ventana. Esta operación cambiará los menús disponibles en la barra de herramientas. En el menú Equipo, seleccione **Conectar a unidad de red**.
    
    ![Captura de pantalla del menú desplegable "Conectar a unidad de red"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Seleccione la letra de unidad y escriba la ruta de acceso UNC; el formato de esta ruta es `\\<storageAccountName>.file.core.windows.net\<fileShareName>`. Por ejemplo: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![Captura de pantalla del cuadro de diálogo "Conectar a unidad de red"](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Use el nombre de la cuenta de almacenamiento prefijada con `AZURE\` como el nombre de usuario y la clave de la cuenta de almacenamiento como contraseña.
    
    ![Captura de pantalla del cuadro de diálogo credenciales de red](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Use el recurso compartido de archivos de Azure como prefiera.
    
    ![El recurso compartido de archivos de Azure ahora está montado.](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Cuando esté listo para desmontar el recurso compartido de archivos de Azure, puede hacerlo si hace clic con el botón derecho en la entrada del recurso compartido en **Ubicaciones de red** en el Explorador de archivos y selecciona **Desconectar**.

### <a name="accessing-share-snapshots-from-windows"></a>Acceso a instantáneas de recursos compartido de Windows
Si ha realizado una instantánea de un recurso compartido, ya sea manualmente o automáticamente a través de un script o un servicio como Azure Backup, puede ver las versiones anteriores de un recurso compartido, un directorio o un archivo concreto desde el recurso compartido de archivos en Windows. Las instantáneas de recursos compartidos se pueden realizar desde [Azure Portal](storage-how-to-use-files-powershell.md), [Azure PowerShell](storage-how-to-use-files-cli.md) y la [CLI de Azure](storage-how-to-use-files-portal.md).

#### <a name="list-previous-versions"></a>Enumeración de versiones anteriores
Vaya al elemento o elemento principal que hay que restaurar. Haga doble clic para ir al directorio deseado. Haga clic con el botón derecho y seleccione **Propiedades** en el menú.

![Haga clic con el botón derecho en el menú para ir a un directorio seleccionado](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Seleccione **Versiones anteriores** para ver la lista de instantáneas de recursos compartidos de este directorio. La lista puede tardar unos segundos en cargarse dependiendo de la velocidad de red y del número de instantáneas de recursos compartidos que haya en el directorio.

![Pestaña Versiones anteriores](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Puede seleccionar **Abrir** para abrir una instantánea concreta. 

![Instantánea abierta](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Restaurar desde una versión anterior
Haga clic en **Restaurar** para copiar el contenido de todo un directorio de forma recursiva en el momento de la creación de la instantánea del recurso compartido en la ubicación original.

 ![Botón Restaurar en mensaje de advertencia](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para más información sobre Azure Files:
- [Planeamiento de una implementación de Azure Files](storage-files-planning.md)
- [P+F](./storage-files-faq.md)
- [Solución de problemas en Windows](storage-troubleshoot-windows-file-connection-problems.md)