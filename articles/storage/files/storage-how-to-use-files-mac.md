---
title: Montaje de un recurso compartido de archivos de Azure de SMB con macOS | Microsoft Docs
description: Aprenda a montar un recurso compartido de archivos de Azure sobre SMB con macOS mediante Finder o Terminal. Azure Files es el sencillo sistema de archivos en la nube de Microsoft.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f4bbaed3cf35d25a2441a27755c73dc144a07510
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117776"
---
# <a name="mount-smb-azure-file-share-on-macos"></a>Montaje de un recurso compartido de archivos de Azure de SMB en macOS
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. Los recursos compartidos de archivos de Azure se pueden montar con el protocolo estándar del sector SMB 3 con macOS High Sierra 10.13 y versiones posteriores. En este artículo se muestran dos maneras diferentes de montar un recurso compartido de archivos de Azure en macOS, con la interfaz de usuario de Finder y utilizando el Terminal.

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Requisitos previos para el montaje de un recurso compartido de archivos de Azure en macOS
* **Nombre de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesitará el nombre de la cuenta de almacenamiento.

* **Clave de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesitará la clave principal (o secundaria). Actualmente no se admiten claves SAS para el montaje.

* **Asegúrese de que el puerto 445 está abierto**: SMB se comunica a través del puerto TCP 445. En el equipo cliente (Mac), compruebe que el firewall no bloquea el puerto TCP 445.

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="mount-an-azure-file-share-via-finder"></a>Montaje de un recurso compartido de archivos de Azure con Finder
1. **Abrir Finder**: Finder está abierto en macOS de forma predeterminada, pero puede asegurarse de sea la aplicación seleccionada haciendo clic en el "icono de cara de macOS" en el Dock:  
    ![Icono de cara de macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Seleccione "Connect to Server" (Conectar a servidor) en el menú "Go" (Ir)** : en la ruta de acceso UNC de los requisitos previos, convierta la doble barra diagonal inversa de comienzo (`\\`) en `smb://` y todas las otras barras diagonales inversas (`\`) en barras diagonales (`/`). El vínculo debe ser similar al siguiente: ![Cuadro de diálogo "Conectar a servidor"](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Utilice el nombre y la clave de la cuenta de almacenamiento cuando se le solicite un nombre de usuario y contraseña**: al hacer clic en "Conectar" en el cuadro de diálogo "Conectar a servidor", se le pedirá el nombre de usuario y una contraseña (se rellenará automáticamente con el nombre de usuario de macOS). Puede guardar el nombre y la clave de la cuenta de almacenamiento en la cadena de claves de macOS.

4. **Use el recurso compartido de archivos de Azure de la forma deseada**: después de sustituir el nombre del recurso compartido y la clave de la cuenta de almacenamiento por el nombre de usuario y la contraseña, se montará el recurso compartido. Se puede utilizar como lo haría normalmente con una carpeta local o un recurso compartido de archivos, incluido arrastrar y soltar archivos en el recurso compartido de archivos:

    ![Captura de pantalla de un recurso compartido de archivos de Azure montado](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montaje de un recurso compartido de archivos de Azure con el Terminal
1. Reemplace `<storage-account-name>`, `<storage-account-key>` y `<share-name>` por los valores adecuados para su entorno.

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **Uso del recurso compartido de archivos de Azure**: el recurso compartido de archivos de Azure se montará en el punto de montaje especificado por el comando anterior.  

    ![Captura de pantalla del recurso compartido de archivos de Azure montado](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Pasos siguientes
* [Conectar el Mac a ordenadores y servidores compartidos: soporte técnico de Apple](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)