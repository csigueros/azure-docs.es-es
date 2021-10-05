---
title: Uso del Explorador de Azure Storage para administrar discos administrados de Azure
description: Aprenda a cargar, descargar y migrar un disco administrado de Azure entre regiones, y también a crear una instantánea de un disco administrado, mediante el Explorador de Azure Storage.
author: roygara
ms.author: rogarana
ms.date: 09/07/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 84b2ea53ebb0b6102edf5bc501e0e1b9b6f21726
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782054"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Uso del Explorador de Azure Storage para administrar discos administrados de Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

El Explorador de Azure Storage contiene un amplio conjunto de características que le permiten:

- Cargar, descargar y copiar discos administrados.
- Crear instantáneas desde el sistema operativo o el disco duro virtual del disco de datos.
- Migrar datos desde un entorno local a Azure.
- Migrar datos entre regiones de Azure.

## <a name="prerequisites"></a>Requisitos previos

Para completar este artículo, necesitará lo siguiente:

- Suscripción a Azure.
- Al menos un disco administrado de Azure.
- La versión más reciente del [explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="connect-to-an-azure-subscription"></a>Conexión a una suscripción de Azure

Si el Explorador de Storage no está conectado a Azure, no podrá usarlo para administrar los recursos. Siga los pasos de esta sección para conectar el Explorador de Storage a su cuenta de Azure. Después, puede usarlo para administrar los discos.

1. Abra el Explorador de Azure Storage y seleccione el icono **Conectar** en la barra de herramientas.

    [![Captura de pantalla del Explorador de Azure Storage que muestra la ubicación del icono Conectar.](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-lrg.png#lightbox)

1. En el cuadro de diálogo **Conectar a Azure Storage**, seleccione **Suscripción**.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación de la opción Suscripción.](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-lrg.png#lightbox)

1. Seleccione el entorno de adecuado y seleccione **Siguiente**. También puede seleccionar **Administrar los entornos personalizados** para configurar y agregar un entorno personalizado.

    [![Captura pantalla del Explorador de Azure Storage que resalta la ubicación de la opción Entorno de Azure.](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-lrg.png#lightbox)

1. En el cuadro de diálogo **Iniciar sesión**, escriba sus credenciales de Azure.

    ![Captura de pantalla del cuadro de diálogo de inicio de sesión.](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Seleccione la suscripción en la lista y luego seleccione **Open Explorer** (Abrir explorador).

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del botón Open Explorer (Abrir explorador).](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-lrg.png#lightbox)

## <a name="upload-an-on-premises-vhd"></a>Carga de un VHD local

Puede cargar un archivo de disco duro virtual (VHD) local en Azure y usarlo para crear una imagen. Siga los pasos de esta sección para cargar el archivo de origen.

1. En el panel **Explorador**, expanda los **Discos** y seleccione el grupo de recursos en el que desea cargar el disco.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del nodo Discos para cargar un disco.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. En el panel de detalles del grupo de recursos, seleccione **Cargar**.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del botón Cargar.](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-lrg.png#lightbox)

1. En el cuadro de diálogo **Upload VHD** (Cargar disco duro virtual), especifique el archivo de origen del disco duro virtual, el nombre del disco, el tipo de sistema operativo, la región en la que desea cargar el disco y el tipo de cuenta. Si la región admite zonas de disponibilidad, puede seleccionar una zona de su elección. Seleccione **Crear** para empezar a cargar el disco.

    [![Captura de pantalla del cuadro de diálogo Upload VHD (Cargar disco duro virtual) del Explorador de Azure Storage.](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-lrg.png#lightbox)

1. El estado de la carga ahora se mostrará en **Actividades**.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del panel Activities (Actividades) que contiene los mensajes de estado de carga.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-lrg.png#lightbox)

Si la carga ha finalizado y no ve el disco en el panel **Activities** (Actividades), seleccione **Actualizar**.

## <a name="download-a-managed-disk"></a>Descargar un disco administrado

Siga los pasos de esta sección para descargar un disco administrado a un disco duro virtual local. El estado del disco debe estar **Unattached** (Sin conectar) antes de que se pueda descargar.

1. En el panel **Explorador**, expanda los **Discos** y seleccione el grupo de recursos desde el que desea descargar el disco.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del nodo Discos para descargar discos.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. En el panel de detalles del grupo de recursos, seleccione el disco que desea descargar.
1. Seleccione **Descargar** y elija dónde desea guardar el disco.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del botón Descargar.](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-lrg.png#lightbox)

1. Seleccione **Guardar** para comenzar la descarga. El estado de la descarga se mostrará en **Activities** (Actividades).

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del panel Activities (Actividades) que contiene los mensajes de estado de descarga.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-lrg.png#lightbox)

## <a name="copy-a-managed-disk"></a>Copia de un disco administrado

Con Explorador de Storage, puede copiar un disco superpuesto dentro o entre regiones. Para copiar un disco:

1. En el panel **Explorador**, expanda el desplegable **Discos** y seleccione el grupo de recursos que contiene el disco que desea copiar.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del nodo Discos para copiar un disco.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. En el panel de detalles del grupo de recursos, elija el disco que desea copiar y seleccione **Copiar**.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del botón Copiar.](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-lrg.png#lightbox)

1. En el panel **Explorador**, expanda los **Discos** y seleccione el grupo de recursos en el que desea pegar contenido del disco.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del nodo Discos para pegar el contenido de un disco.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-lrg.png#lightbox)

1. En el panel de detalles del grupo de recursos, seleccione **Pegar**.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del botón Pegar.](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-lrg.png#lightbox)

1. En el cuadro de diálogo **Paste Disk** (Pegar disco), rellene los valores. También puede especificar una zona de disponibilidad en las regiones admitidas.

    [![Captura de pantalla del formulario Paste Disk (Pegar disco) del Explorador de Azure Storage.](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-lrg.png#lightbox)

1. Seleccione **Pegar** para iniciar la copia del disco. El estado aparece en **Activities** (Actividades).

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del panel Activities (Actividades) que contiene los mensajes de estado de copia y pegado.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-lrg.png#lightbox)

## <a name="create-a-snapshot"></a>Crear una instantánea

1. En el panel **Explorador**, expanda **Discos** y seleccione el grupo de recursos que contiene el disco del que desea hacer una instantánea.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del nodo Discos para crear instantáneas de discos.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. En el panel de detalles del grupo de recursos, elija el disco del que desea hacer una instantánea y seleccione **Create Snapshot** (Crear instantánea).

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del botón Create Snapshot (Crear instantánea).](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-lrg.png#lightbox)

1. En **Create Snapshot** (Crear instantánea), especifique el nombre de la instantánea, así como el grupo de recursos en el que desea crearla. Seleccione **Crear**.

    [![Captura de pantalla del cuadro de diálogo Create Snapshot (Crear instantánea) del Explorador de Azure Storage.](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-lrg.png#lightbox)

1. Una vez creada la instantánea, puede seleccionar **Abrir en el portal** en **Actividades** para ver la instantánea en el Azure Portal.

    [![Captura de pantalla del Explorador de Azure Storage que resalta la ubicación del vínculo en el panel Activities (Actividades) con mensajes de estado de instantáneas.](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-lrg.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

- [Creación de una máquina virtual a partir de un disco duro virtual mediante Azure Portal](/azure/virtual-machines/windows/create-vm-specialized-portal)
- [Conexión de un disco de datos administrado a una máquina virtual de Windows mediante Azure Portal](/azure/virtual-machines/windows/attach-managed-disk-portal)
