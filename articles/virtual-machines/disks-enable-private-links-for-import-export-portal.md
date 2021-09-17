---
title: 'Azure Portal: Restricción del acceso para importar o exportar discos administrados'
description: Habilite Private Link para los discos administrados con Azure Portal. Esto le permite exportar e importar discos de forma segura dentro de la red virtual.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: b023f7f068d1dc52c073519cc7e7f308e5d86ad0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696461"
---
# <a name="restrict-importexport-access-for-managed-disks-using-azure-private-link"></a>Restricción del acceso para importar o exportar discos administrados mediante Azure Private Link

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Puede usar [puntos de conexión privados](../private-link/private-endpoint-overview.md) para restringir la exportación e importación de discos administrados y un acceso más seguro a los datos mediante una instancia de [Private Link](../private-link/private-link-overview.md) desde los clientes de la red virtual de Azure. El punto de conexión privado usa una dirección IP del espacio de direcciones de la red virtual para los discos administrados. El tráfico de red entre los clientes de su red virtual y los discos administrados solo atraviesa la red virtual y un vínculo privado de la red troncal de Microsoft, lo que elimina la exposición desde la red pública de Internet.

Para usar Private Link para exportar e importar discos administrados, cree primero un recurso de acceso a disco y vincúlelo a una red virtual de la misma suscripción mediante la creación de un punto de conexión privado. A continuación, asocie un disco o una instantánea con una instancia de acceso a disco.

## <a name="limitations"></a>Limitaciones

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="create-a-disk-access-resource"></a>Creación de un recurso de acceso a disco

1. Inicie sesión en Azure Portal y vaya a **Acceso al disco** con [este vínculo](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > Debe usar el [vínculo proporcionado](https://aka.ms/disksprivatelinks) para ir a la hoja Acceso al disco. No está visible actualmente en el portal público sin usar el vínculo.

1. Seleccione **+ Crear** para crear un nuevo recurso de acceso a disco.
1. En el panel **Crear un acceso al disco**, seleccione la suscripción y un grupo de recursos. En **Detalles de la instancia**, escriba un nombre y seleccione una región.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Captura de pantalla del panel de creación del acceso al disco. Rellene el nombre deseado, seleccione una región, seleccione un grupo de recursos y continúe":::

1. Seleccione **Revisar + crear**.
1. Cuando se haya creado el recurso, vaya directamente a él.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Captura de pantalla del botón Ir al recurso en el portal":::

## <a name="create-a-private-endpoint"></a>Creación de un punto de conexión privado

A continuación, tendrá que crear un punto de conexión privado y configurarlo para el acceso a disco.

1. En el recurso de acceso a disco, en **Configuración**, seleccione **Conexiones de punto de conexión privado**.
1. Seleccione **+ Punto de conexión privado**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Captura de pantalla del panel de información general del recurso de acceso al disco. Las conexiones de punto de conexión privado están resaltadas.":::

1. En el panel **Crear un punto de conexión privado**, seleccione un grupo de recursos.
1. Proporcione un nombre y seleccione la misma región en la que se creó el recurso de acceso a disco.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Captura de pantalla del flujo de trabajo de creación del punto de conexión privado, primer panel. Si no selecciona la región adecuada, puede encontrar problemas más adelante.":::

1. Seleccione **Siguiente: Resource** (Siguiente: Recurso).
1. En el panel **Recurso**, seleccione **Conectarse a un recurso de Azure en mi directorio**.
1. En **Tipo de recurso**, seleccione **Microsoft.Compute/diskAccesses**.
1. En **Recurso**, seleccione el recurso de acceso a disco que creó anteriormente.
1. Deje el campo **Subrecurso de destino** como **discos**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Captura de pantalla del flujo de trabajo de creación del punto de conexión privado, segundo panel. Con todos los valores resaltados (tipo de recurso, recurso, subrecurso de destino).":::

1. Seleccione **Siguiente: Configuración**.
1. Seleccione la red virtual a la que limitará la importación y exportación del disco. Esto evita la importación y exportación del disco a otras redes virtuales.

    > [!NOTE]
    > Si tiene un grupo de seguridad de red habilitado para la subred seleccionada, se deshabilitará solo para los puntos de conexión privados de esta subred. Otros recursos de esta subred conservarán la aplicación del grupo de seguridad de red.

1. Seleccione la subred adecuada.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Captura de pantalla del flujo de trabajo de creación del punto de conexión privado, tercer panel. Red virtual y subred resaltada.":::

1. Seleccione **Revisar + crear**.

## <a name="enable-private-endpoint-on-your-disk"></a>Habilitación del punto de conexión privado en el disco

1. Vaya al disco que desea configurar.
1. En **Configuración**, seleccione **Redes**.
1. Seleccione **Punto de conexión privado (mediante acceso al disco)** y seleccione el acceso a disco que creó anteriormente.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Captura de pantalla del panel Redes del disco administrado. Resaltado de la selección del punto de conexión privado, así como el acceso a disco seleccionado. Al guardar, se configura el disco para este acceso.":::

1. Seleccione **Guardar**.

Ahora ha configurado un vínculo privado que puede usar para importar y exportar el disco administrado.

## <a name="next-steps"></a>Pasos siguientes

- Carga de un disco duro virtual en Azure o copia de un disco administrado en otra región: la [CLI de Azure](linux/disks-upload-vhd-to-managed-disk-cli.md) o el [módulo de Azure PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md)
- Descarga de un disco duro virtual [Windows](windows/download-vhd.md) o [Linux](linux/download-vhd.md)
- [Instancias de Private Link para exportar e importar Managed Disks de forma segura](/azure/virtual-machines/faq-for-disks#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportación o copia de instantáneas administradas como VHD a una cuenta de almacenamiento en otra región con PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)