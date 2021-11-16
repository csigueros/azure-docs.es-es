---
title: Carga de un archivo VHD mediante el Explorador de Storage
description: Cargue un archivo VHD a una cuenta de almacenamiento de laboratorio de DevTest Labs mediante el Explorador de Microsoft Azure Storage.
ms.topic: how-to
ms.date: 11/05/2021
ms.openlocfilehash: 2531964c056ddbed38da435e16bde3e0f5e1eff9
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054111"
---
# <a name="upload-a-vhd-file-to-a-labs-storage-account-by-using-storage-explorer"></a>Carga de un archivo VHD en una cuenta de almacenamiento de un laboratorio mediante el Explorador de Storage

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

En Azure DevTest Labs, puede usar archivos VHD para crear imágenes personalizadas para aprovisionar máquinas virtuales. En este artículo se indica cómo usar el [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) para cargar un archivo VHD en una cuenta de almacenamiento del laboratorio. Después de cargar el archivo VHD en DevTest Labs, puede [crear una imagen personalizada](devtest-lab-create-custom-image-from-vhd-using-powershell.md) a partir del archivo cargado. Para más información sobre los discos y los discos duros virtuales en Azure, consulte [Introducción a los discos administrados](../virtual-machines/managed-disks-overview.md)

El Explorador de Storage admite varias opciones de conexión. En este artículo se describe la conexión a una cuenta de almacenamiento asociada a la suscripción de Azure. Para más información sobre otras opciones de conexión del Explorador de Storage, consulte [Introducción al Explorador de Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

## <a name="prerequisites"></a>Prerrequisitos

- [Descargue e instale la versión más reciente del Explorador de Microsoft Azure Storage](https://www.storageexplorer.com).

- Obtenga el nombre de la cuenta de almacenamiento del laboratorio mediante Azure Portal:

  1. En [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), busque y seleccione **DevTest Labs** y seleccione el laboratorio en la lista.
  1. En la página del laboratorio, seleccione **Configuración y directivas** en el panel de navegación izquierdo. 
  1. En la página **Configuración y directivas** en **Bases para máquinas virtuales**, seleccione **Imágenes personalizadas**.
  1. En la página **Imágenes personalizadas**, seleccione **Agregar**. 
  1. En la página **Imagen personalizada**, en **VHD**, seleccione el vínculo **Cargar un VHD con PowerShell**.
     ![Captura de pantalla que muestra el vínculo Cargar un VHD con PowerShell.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png)
  1. En la página **Cargar un VHD con PowerShell**, en la llamada al cmdlet **Add-AzureVhd**, el parámetro `Destination` muestra el nombre de la cuenta de almacenamiento de laboratorio con el siguiente formato: `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/`.
  1. Copie el nombre de la cuenta de almacenamiento para usarlo en los pasos siguientes.

## <a name="step-by-step-instructions"></a>Instrucciones paso a paso

1. Al abrir el Explorador de Storage, en el panel izquierdo **Explorador** aparecen todas las suscripciones de Azure en las que ha iniciado sesión.

   Si necesita agregar otra cuenta, seleccione el icono **Administración de cuentas** y, en el panel **Administración de cuentas**, seleccione **Agregar una cuenta**.

   ![Captura de pantalla que muestra Agregar una cuenta en el panel Administración de cuentas.](media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png)

   Siga las indicaciones para iniciar sesión con la cuenta Microsoft asociada a su suscripción de Azure.

1. Después de iniciar sesión, el panel **Explorador** muestra las suscripciones de Azure asociadas a su cuenta. Seleccione la flecha de lista desplegable situada junto a la suscripción de Azure que desea usar. El panel izquierdo muestra ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure seleccionadas.

   ![Captura de pantalla que muestra las cuentas de almacenamiento de una suscripción de Azure seleccionada.](media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png)

1. Seleccione la flecha de lista desplegable situada junto al nombre de la cuenta de almacenamiento de laboratorio que guardó anteriormente.

1. Expanda el nodo **Contenedores de blob** y seleccione **uploads** (cargas).

   ![Captura de pantalla que muestra el nodo Contenedores de blob expandido con el directorio uploads.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png)

1. En el panel derecho del Explorador de Storage, en la barra de herramientas del editor de blobs, seleccione **Cargar** y **Cargar archivos**. 

   ![Captura de pantalla que muestra el botón Cargar y la opción Cargar archivos.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png)

1. En el cuadro de diálogo **Cargar archivos**, seleccione **...** junto al campo **Archivos seleccionados**, vaya al archivo VHD en la máquina, selecciónelo y, finalmente, seleccione **Abrir**.

1. En **Tipo de blob**, cambie **Blob en bloques** a **Blob en páginas**.

1. Seleccione **Cargar**.

   ![Captura de pantalla en la que se muestra el cuadro de diálogo Cargar archivos.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png)

El panel **Actividades** de la parte inferior muestra el estado de carga. La carga del archivo VHD puede tardar mucho tiempo, según el tamaño del archivo y la velocidad de conexión.

![Captura de pantalla que muestra el panel Actividades con el estado de carga.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png)

## <a name="next-steps"></a>Pasos siguientes

- [Creación de una imagen personalizada en Azure DevTest Labs a partir de un archivo VHD mediante el portal de Azure](devtest-lab-create-template.md)
- [Creación de una imagen personalizada en Azure DevTest Labs a partir de un archivo VHD mediante PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

