---
title: 'Administración de recursos de Data Lake Storage Gen1: Explorador de Azure Storage'
description: Más información para acceder a los datos y recursos de Azure Data Lake Storage Gen1 y administrarlos en el Explorador de Azure Storage
author: jejiang
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/04/2021
ms.author: jejiang
ms.openlocfilehash: 9ef06e1b13141e3b5c342842a63e8e520e3e03e9
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2021
ms.locfileid: "111589949"
---
# <a name="manage-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Administración de recursos de Azure Data Lake Storage Gen1 con el Explorador de Storage

[Azure Data Lake Storage Gen1](./data-lake-store-overview.md) es un servicio de almacenamiento de grandes cantidades de datos no estructurados, como texto o datos binarios. Puede acceder a los datos desde cualquier lugar a través de HTTP o HTTPS. Data Lake Storage Gen1 en el Explorador de Azure Storage le permite acceder a los datos y recursos de Data Lake Storage Gen1 y administrarlos junto con otras entidades de Azure, como los blobs o las colas. Ahora puede usar la misma herramienta para administrar las diferentes entidades de Azure en un solo lugar.

Otra ventaja es que, para administrar datos de Data Lake Storage Gen1, no es necesario disponer de permiso de suscripción. En el Explorador de Storage, puede asociar la ruta de acceso de Data Lake Storage Gen1 al nodo **Local and Attached** (Local y acoplado) siempre que un usuario conceda el permiso.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará lo siguiente:

* Suscripción a Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).
* Cuenta de Data Lake Storage Gen1. Para instrucciones sobre cómo crear una, consulte la [introducción a Azure Data Lake Storage Gen1](./data-lake-store-get-started-portal.md).

## <a name="install-storage-explorer"></a>Instalar Explorador de Azure Storage

Instale la última versión del Explorador de Azure Storage desde la [página web del producto](https://azure.microsoft.com/features/storage-explorer/). La instalación admite las versiones de Windows, Linux y Mac.

## <a name="connect-to-an-azure-subscription"></a>Conexión a una suscripción de Azure

1. En el Explorador de Storage, seleccione el icono de complemento.

   ![Captura de pantalla que muestra dónde se encuentra el icono del complemento en la interfaz de usuario](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)

   Se abre el cuadro de diálogo **Conectar a Azure Storage**.
1. En la página **Seleccionar recurso**, elija **Suscripción**.
1. En la página **Select Azure Environment** (Seleccionar entorno de Azure), seleccione el entorno de Azure donde se va a iniciar sesión y, a continuación, elija **Siguiente**.
1. En el cuadro de diálogo **Iniciar sesión**, escriba sus credenciales de Azure y, a continuación, seleccione **Siguiente**.

1. En Explorador de Storage, en el panel **ADMINISTRACIÓN DE CUENTAS**, seleccione la suscripción que contiene la cuenta de Data Lake Storage Gen1 que quiere administrar y, a continuación, seleccione **Abrir explorador**.
1. Expanda la suscripción en el panel **EXPLORADOR**. El panel se actualiza y muestra las cuentas de la suscripción seleccionada. Esto incluye todas las cuentas de Data Lake Storage Gen1, por ejemplo:

     ![Captura de pantalla que muestra una cuenta de ejemplo en el nodo Data Lake Storage Gen1](./media/data-lake-store-in-storage-explorer/account-list.png)

## <a name="connect-to-data-lake-storage-gen1"></a>Conexión a Data Lake Storage Gen 1

Puede tener acceso a recursos que no existen en su suscripción si alguien le proporciona el URI para los recursos. Después, puede conectar a Data Lake Storage Gen1 con el URI una vez iniciada la sesión.

1. Abra el Explorador de Storage.
1. Expanda **Local & Attached** (Local y acoplado).
1. Haga clic con el botón derecho en **Data Lake Storage Gen1 (versión preliminar)** y seleccione **Connect to Data Lake Storage Gen1** (Conectar a Data Lake Storage Gen1).
1. Escriba el URI, por ejemplo:

      ![Captura de pantalla que muestra el cuadro de diálogo "Connect to Data Lake Store" (Conectar a Data Lake Store), con el cuadro de texto para escribir el identificador URI](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

   La herramienta se desplaza a la ubicación de la dirección URL que acaba de escribir.

      ![Muestra la cuenta de Data Lake Storage Gen1 que aparece en el nodo Data Lake Storage Gen1 (versión preliminar) de la interfaz de usuario.](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-the-contents-of-a-data-lake-storage-gen1-account"></a>Visualización del contenido de una cuenta de Data Lake Storage Gen1

Los recursos de una cuenta de Azure Data Lake Storage Gen1 contienen archivos y carpetas. Los pasos siguientes muestran cómo ver el contenido de una cuenta de Data Lake Storage Gen1 en el Explorador de Storage.

1. Abra Explorador de Storage.
1. Expanda la suscripción que contenga la cuenta de Data Lake Storage Gen1 que quiera ver.
1. Expanda **Data Lake Storage Gen1 (versión preliminar)** .
1. Seleccione la cuenta de Data Lake Storage Gen1 que quiere ver.

   El panel principal muestra el contenido de la cuenta de Data Lake Storage Gen1.

   ![Muestra el panel principal con la cuenta de Data Lake Storage Gen1 seleccionada y una lista de las carpetas de la cuenta.](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png)

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Administrar recursos en Data Lake Storage Gen1

A continuación se muestran las operaciones de administración de recursos de Data Lake Storage Gen1:

* Examinar los recursos de Data Lake Storage Gen1 en diferentes cuentas de Data Lake Storage Gen1.  
* Usar una cadena de conexión para conectarse a Data Lake Storage Gen1 y realizar la administración directamente.
* Ver los recursos de Data Lake Storage Gen1 compartidos por otros usuarios mediante la lista de control de acceso de **Local and Attached** (Local y acoplado).
* Realizar operaciones CRUD de archivo y carpeta: admite archivos de selección múltiple y carpetas recursivas.
* Arrastrar, colocar y agregar una carpeta para tener acceso rápidamente a ubicaciones recientes. Esta operación refleja la experiencia del Explorador de archivos del escritorio.
* Copiar y abrir el hipervínculo de Data Lake Storage Gen1 en el Explorador de Storage con un solo clic.
* Mostrar el registro de **Actividades** en el panel inferior para ver el estado de actividad.
* Mostrar las estadísticas de las carpetas y las propiedades de los archivos.

## <a name="manage-resources-in-azure-storage-explorer"></a>Administración de recursos en el Explorador de Azure Storage

Después de crear una cuenta de Data Lake Storage Gen1, puede:

* Cargar archivos y carpetas, descargarlos y abrir recursos en el equipo local.
* Anclar a **Acceso rápido**, crear una nueva carpeta, copiar una dirección URL y seleccionar todo.
* Copiar y pegar, cambiar el nombre, eliminar, obtener las estadísticas de la carpeta y actualizar.

Los elementos siguientes muestran cómo administrar los recursos de una cuenta de Data Lake Storage Gen1. Siga los pasos de la tarea que quiere realizar.

### <a name="upload-files"></a>Carga de archivos

1. En la barra de herramientas del panel principal, seleccione **Cargar** y, a continuación, **Cargar archivos**.
1. En el cuadro de diálogo **Select files to upload** (Seleccionar archivos para cargar), seleccione los archivos que desea cargar.
1. Seleccione **Open** (Abrir) para iniciar la carga.

> [!NOTE]
> También puede arrastrar los archivos directamente a un equipo local para comenzar la carga.

### <a name="upload-a-folder"></a>Carga de una carpeta

1. En la barra de herramientas del panel principal, seleccione **Cargar** y después **Cargar carpeta**.
1. En el cuadro de diálogo **Seleccionar carpeta para cargarla**, seleccione la carpeta que quiere cargar.
1. Elija **Seleccionar carpeta** para empezar a cargar.

> [!NOTE]
> También puede arrastrar una carpeta directamente a un equipo local para comenzar la carga.

### <a name="download-folders-or-files-to-your-local-computer"></a>Descarga de archivos o carpetas en el equipo local

1. Seleccione las carpetas o archivos que desea descargar.
1. En la barra de herramientas del panel principal, seleccione **Descargar**.
1. En el cuadro de diálogo **Select a folder to save the downloaded files into** (Seleccionar una carpeta para guardar los archivos descargados), especifique la ubicación y el nombre.
1. Seleccione **Guardar**.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Apertura de un archivo o una carpeta desde el equipo local

1. Seleccione el archivo o la carpeta que desea abrir.
1. En la barra de herramientas del panel principal, seleccione **Abrir**. También puede hacer clic con el botón derecho en la carpeta o el archivo seleccionado, y, a continuación, seleccione **Abrir** en el menú contextual.

El archivo se descargará y se abrirá mediante la aplicación asociada al tipo de archivo subyacente. O bien, se abrirá la carpeta en el panel principal.

### <a name="copy-folders-or-files-to-the-clipboard"></a>Copia de carpetas o archivos en el Portapapeles

Puede copiar los archivos o carpetas de Data Lake Storage Gen1 y pegarlos en otra cuenta de Data Lake Storage Gen1. No se admiten las operaciones de copiar y pegar entre distintos tipos de almacenamiento. Por ejemplo, no puede copiar archivos o carpetas de Data Lake Storage Gen1 y pegarlos en Azure Blob Storage o viceversa.

1. Seleccione las carpetas o archivos que desea copiar.
1. En la barra de herramientas del panel principal, seleccione **Copiar**. También puede hacer clic con el botón derecho en las carpetas o archivos seleccionados, y, a continuación, seleccione **Copiar** en el menú contextual.
1. En el panel de navegación, vaya a otra cuenta de Data Lake Storage Gen1 y selecciónela para verla en el panel principal.
1. En la barra de herramientas del panel principal, seleccione **Paste** (Pegar) para crear una copia. También puede seleccionar **Pegar** en el menú contextual del destino.

> [!NOTE]
> La operación de copiar y pegar consiste en descargar las carpetas o los archivos en el equipo local y después cargarlos en el destino. La herramienta no realiza la acción en el back-end. La operación de copiar y pegar archivos de gran tamaño es lenta.

### <a name="delete-folders-or-files"></a>Eliminación de archivos o carpetas

1. Seleccione las carpetas o archivos que desee eliminar.
1. En la barra de herramientas del panel principal, seleccione **Eliminar**. También puede hacer clic con el botón derecho en las carpetas o archivos seleccionados, y, a continuación, seleccione **Eliminar** en el menú contextual.
1. Seleccione **Sí** en el cuadro de diálogo de confirmación.

### <a name="pin-to-quick-access"></a>Anclar a acceso rápido

1. Seleccione la carpeta que quiere anclar para poder acceder a los recursos fácilmente.
1. En la barra de herramientas del panel principal, seleccione **Pin to Quick access** (Anclar a Acceso rápido).

   En el panel de navegación, la carpeta seleccionada se agrega al nodo **Acceso rápido**.

   ![Muestra la carpeta que aparece en el nodo Acceso rápido de la interfaz de usuario.](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

### <a name="use-deep-links"></a>Uso de vínculos profundos

Si tiene una dirección URL, puede escribirla en la ruta de acceso de dirección del Explorador de archivos o un explorador. A continuación, el Explorador de Storage se abre automáticamente y va a la ubicación de la dirección URL.

![Muestra la dirección URL de una carpeta en una cuenta de Data Lake Storage Gen1 que se copia en la ventana Explorador de archivos](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)

## <a name="next-steps"></a>Pasos siguientes

* Vea las [notas de la versión y los vídeos más recientes del Explorador de Storage](https://www.storageexplorer.com).
* [Introducción al Explorador de Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Introducción a Azure Data Lake Storage Gen1](./data-lake-store-overview.md)
