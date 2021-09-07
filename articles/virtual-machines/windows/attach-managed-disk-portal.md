---
title: 'Conexión de un disco de datos administrado a una máquina virtual de Windows: Azure'
description: Cómo conectar un disco de datos administrado a una máquina virtual Windows en Azure Portal.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ba6691e343276445bd881d2cbe2f8fcb5d1d314f
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666533"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Conexión de un disco de datos administrado a una máquina virtual Windows en Azure Portal

En este artículo se muestra cómo conectar un nuevo disco de datos administrado a una máquina virtual Windows en Azure Portal. El tamaño de la máquina virtual determina el número de discos que se puede conectar. Para más información, consulte [Tamaños de las máquinas virtuales Linux en Azure](../sizes.md).


## <a name="add-a-data-disk"></a>Agregar un disco de datos

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque y seleccione **Máquinas virtuales**.
1. Seleccione una máquina virtual de la lista.
1. En el panel **Máquina virtual**, seleccione **Discos**.
1. En el panel **Discos**, seleccione **Crear y adjuntar un nuevo disco**.
1. En las lista desplegables del nuevo disco, realice las selecciones que desee y asigne un nombre al disco.
1. Seleccione **Guardar** para crear y conectar el nuevo disco de datos a la máquina virtual.

## <a name="initialize-a-new-data-disk"></a>Inicio de un nuevo disco de datos

1. Conéctese a la máquina virtual.
1. Seleccione el menú **Inicio** de Windows en la máquina virtual en ejecución y escriba **diskmgmt.msc** en el cuadro de búsqueda. Se abre la consola **Administración de discos**.
1. Administración de discos reconocerá que hay un nuevo disco sin inicializar y se abrirá la ventana **Inicializar disco**.
1. Asegúrese de que el nuevo disco está seleccionado y seleccione **Aceptar** para inicializarlo.
1. El nuevo disco aparecerá como **sin asignar**. Haga clic con el botón derecho en cualquier lugar del disco y seleccione **Nuevo volumen simple**. Se abre la ventana **Asistente para nuevo volumen simple**.
1. Siga los pasos del asistente, conserve todos los valores predeterminados y, cuando haya terminado, seleccione **Finalizar**.
1. Cierre **Administración de discos**.
1. Se abre una ventana emergente que indica que es necesario dar formato al nuevo disco para poder usarlo. Seleccione **Dar formato al disco**.
1. En la ventana **Dar formato al nuevo disco**, active las opciones y seleccione **Iniciar**.
1. Recibirá una advertencia que indica que al dar formato a los discos se borrarán todos los datos. Seleccione **Aceptar**.
1. Cuando se complete el formato, seleccione **Aceptar**.

## <a name="next-steps"></a>Pasos siguientes

- También puede [conectar un disco de datos mediante PowerShell](attach-disk-ps.md).
- Si la aplicación necesita usar la unidad *D:* para almacenar datos, puede [cambiar la letra de la unidad del disco temporal de Windows](change-drive-letter.md).