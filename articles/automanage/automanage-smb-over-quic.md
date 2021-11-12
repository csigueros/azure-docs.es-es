---
title: SMB a través de QUIC con procedimientos recomendados para máquinas con Azure Automanage
description: Información general de la administración de SMB a través de QUIC con procedimientos recomendados para máquinas con Azure Automanage
author: daniellee-microsoft
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/1/2021
ms.author: jol
ms.openlocfilehash: 6445f26494394e551795390f29d16b59fca877d7
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511342"
---
# <a name="smb-over-quic-with-automanage-machine-best-practices"></a>SMB a través de QUIC con procedimientos recomendados para máquinas con Automanage

SMB a través de QUIC ofrece una "VPN SMB" para teletrabajadores, usuarios de dispositivos móviles y sucursales, lo que proporciona conectividad segura y confiable a servidores de archivos perimetrales a través de redes que no son de confianza, como Internet. Para obtener más información sobre SMB a través de QUIC y sobre cómo configurar SMB a través de QUIC, consulte [SMB a través de QUIC](https://aka.ms/smboverquic).

Además, SMB a través de QUIC se integra con los procedimientos recomendados para máquinas con Automanage para Windows Server a fin de facilitar la administración de SMB a través de QUIC. QUIC usa certificados para proporcionar su cifrado y las organizaciones suelen tener dificultades para mantener infraestructuras de clave pública complejas. Los procedimientos recomendados para máquinas con Automanage garantizan que los certificados no expiren sin previo aviso y que SMB a través de QUIC permanezca habilitado para una continuidad máxima del servicio.

## <a name="how-to-get-started"></a>Primeros pasos

> [!NOTE]
> Para ver los requisitos previos sobre el uso de los procedimientos recomendados de máquinas con Automanage, consulte [Habilitación para máquinas virtuales en Azure Portal](quick-create-virtual-machines-portal.md). 

> [!NOTE]
> Durante la fase de versión preliminar, puede empezar a trabajar en Azure Portal mediante [este vínculo](https://aka.ms/automanage-ws-portal-preview).

## <a name="enable-automanage-best-practices-when-creating-a-new-vm"></a>Habilitación de los procedimientos recomendados de Automanage al crear una máquina virtual

Para habilitar los procedimientos recomendados para máquinas con Automanage para SMB a través de QUIC en una máquina virtual, siga estos pasos:

1. Inicie sesión en Azure Portal con el anterior vínculo de versión preliminar.
2. Cree una máquina virtual de Azure con la imagen _Windows Server 2022 Datacenter: Azure Edition_ para obtener las funcionalidades de Automanage para Windows Server, incluido SMB a través de QUIC.
3. En la pestaña **Administración** de la configuración del entorno de Azure Automanage, elija **Desarrollo/pruebas** o **Producción** a fin de habilitar los procedimientos recomendados para máquinas con Automanage.

:::image type="content" source="media\automanage-smb-over-quic\create-vm-automanage-setting.png" alt-text="Habilite Automanage al crear una máquina virtual.":::

4. Configure cualquier opción adicional que sea necesaria y cree la máquina virtual.

## <a name="enable-automanage-best-practices-on-existing-vms"></a>Habilitación de los procedimientos recomendados de Automanage en máquinas virtuales existentes

También puede habilitar los procedimientos recomendados para máquinas con Automanage en una máquina virtual que haya creado previamente. Tenga en cuenta que la máquina virtual debe haberse creado con la imagen _Windows Server 2022 Datacenter: Azure Edition_ para obtener las funcionalidades de Automanage para Windows Server, incluido SMB a través de QUIC.

1. Vaya a la máquina virtual que ha creado anteriormente.
2. Seleccione el menú de Automanage, elija el entorno de **Desarrollo/pruebas** o **Producción** y haga clic en **Habilitar**.

:::image type="content" source="media\automanage-smb-over-quic\vm-enable-automanage.png" alt-text="Habilite Automanage en una máquina virtual existente.":::

## <a name="viewing-automanage-best-practice-compliance"></a>Visualización del cumplimiento de los procedimientos recomendados de Automanage

Los procedimientos recomendados de la máquina podrían tardar un par de horas en configurarse, al igual que las directivas de los procedimientos recomendados que luego se asignarán y se evaluarán en la máquina virtual. Una vez finalizada la configuración, verá las directivas de SMB a través de QUIC y su estado, como se muestra a continuación. Estas directivas se evaluarán en todo momento de forma automática para asegurarse de que SMB a través de QUIC está configurado correctamente y de que los certificados usados son válidos y correctos.

:::image type="content" source="media\automanage-smb-over-quic\vm-automanage-configured.png" alt-text="Visualice las directivas de SMB a través de QUIC para una máquina virtual.":::

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre SMB a través de QUIC](https://aka.ms/smboverquic)