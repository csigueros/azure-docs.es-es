---
title: Automanage para Windows Server
description: Introducción a Azure Automanage para funcionalidades de Windows Server con Windows Server Azure Edition
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: efd5b643608b8d3de5cacbc1f810a9b1e9efb612
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501597"
---
# <a name="azure-automanage-for-windows-server"></a>Azure Automanage para Windows Server

Azure Automanage para Windows Server aporta nuevas funcionalidades específicamente a _Windows Server Azure Edition_.  Estas son algunas de ellas:
- Revisión en caliente (versión preliminar)
- SMB a través de QUIC
- Red extendida para Azure

> [!IMPORTANT]
> Actualmente, la revisión en caliente está en versión preliminar pública. No es necesario ningún procedimiento de participación para usar la funcionalidad de revisión en caliente que se describe a continuación.
> Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Las funcionalidades de Automanage para Windows Server se pueden encontrar en una o varias de estas imágenes de _Windows Server Azure Edition_: 

- Windows Server 2022 Datacenter: Azure Edition (Desktop Experience)
- Windows Server 2022 Datacenter: Azure Edition (Core)

Las funcionalidades varían según la imagen; para más detalles, consulte la sección en la que se indican los [primeros pasos con Windows Server Azure Edition](#getting-started-with-windows-server-azure-edition).

## <a name="automanage-for-windows-server-capabilities"></a>Funcionalidades de Automanage para Windows Server

### <a name="hotpatch-preview"></a>Revisión en caliente (versión preliminar)

La revisión en caliente está disponible en versión preliminar pública en las siguientes imágenes:

- Windows Server 2022 Datacenter: Azure Edition (Core)

La revisión en caliente le ofrece la posibilidad de aplicar actualizaciones de seguridad a una máquina virtual sin necesidad de reiniciarla.  Además, Automanage para Windows Server automatiza la incorporación, configuración y orquestación de las revisiones en caliente.  Para más información, consulte el artículo sobre [revisión en caliente](automanage-hotpatch.md).  

### <a name="smb-over-quic"></a>SMB a través de QUIC

SMB a través de QUIC está disponible en versión preliminar pública en las siguientes imágenes:

- Windows Server 2022 Datacenter: Azure Edition (Desktop Experience)
- Windows Server 2022 Datacenter: Azure Edition (Core)

SMB a través de QUIC ofrece una "VPN SMB" para teletrabajadores, usuarios de dispositivos móviles y sucursales, lo que proporciona conectividad segura y confiable a servidores de archivos perimetrales a través de redes que no son de confianza, como Internet. [QUIC](https://datatracker.ietf.org/doc/rfc9000/) es un protocolo estandarizado con IETF que se usa en HTTP/3, diseñado para la máxima protección de datos con TLS 1.3 y que requiere cifrado que no se puede deshabilitar. SMB se comporta con normalidad en el túnel de QUIC, lo que significa que la experiencia del usuario no cambia. Las características de SMB como el multicanal, la firma, la compresión, la disponibilidad continua y la concesión de directorios funcionan con normalidad. 

SMB a través de QUIC también se integra con los [procedimientos recomendados para máquinas con Automanage para Windows Server](automanage-windows-server.md) a fin de facilitar la administración de SMB a través de QUIC. QUIC usa certificados para proporcionar su cifrado y las organizaciones suelen tener dificultades para mantener infraestructuras de clave pública complejas. Los procedimientos recomendados para máquinas con Automanage garantizan que los certificados no expiren sin previo aviso y que SMB a través de QUIC permanezca habilitado para una continuidad máxima del servicio.

Para obtener más información, consulte [SMB a través de QUIC](https://aka.ms/smboverquic) y [Administración de SMB a través de QUIC con procedimientos recomendados para máquinas con Automanage](automanage-smb-over-quic.md).
 

### <a name="extended-network-for-azure"></a>Red extendida para Azure

La red extendida para Azure está disponible en las imágenes siguientes:

- Windows Server 2022 Datacenter: Azure Edition (Desktop Experience)
- Windows Server 2022 Datacenter: Azure Edition (Core)

La red extendida de Azure permite extender una subred local a Azure para que las máquinas virtuales locales puedan conservar sus direcciones IP privadas locales originales al migrar a Azure. Para más información, consulte el artículo sobre la [red extendida de Azure](/windows-server/manage/windows-admin-center/azure/azure-extended-network).  


## <a name="getting-started-with-windows-server-azure-edition"></a>Primeros pasos con Windows Server Azure Edition

Es importante tener en cuenta por adelantado qué funcionalidades de Automanage para Windows Server desea usar y, después, elegir una imagen de máquina virtual correspondiente que admita todas esas funcionalidades.  Algunas de las imágenes de _Windows Server Azure Edition_ solo admiten un subconjunto de funcionalidades; para más detalles, consulte la siguiente tabla.

### <a name="deciding-which-image-to-use"></a>Decidir qué imagen usar 

|Imagen|Funcionalidades|
|--|--|
|Windows Server 2022 Datacenter: Azure Edition (Desktop Experience) | SMB a través de QUIC, red extendida para Azure | 
| Windows Server 2022 Datacenter: Azure Edition (Core) | Revisión en caliente, SMB a través de QUIC, red extendida para Azure | 

### <a name="creating-a-vm"></a>Creación de una máquina virtual

Para empezar a usar las funcionalidades de Automanage para Windows Server en una nueva máquina virtual, use el método que prefiera para crear una máquina virtual de Azure y seleccione la imagen de _Windows Server Azure Edition_ que corresponde al conjunto de [funcionalidades](#getting-started-with-windows-server-azure-edition) que desea usar.  

> [!IMPORTANT]
> Algunas funcionalidades contienen pasos de configuración específicos que se deben llevar a cabo durante la creación de máquinas virtuales y algunas funcionalidades que se encuentran en versión preliminar incluyen requisitos específicos para la participación y la visualización en el portal.  Consulte los temas individuales de cada funcionalidad indicados anteriormente para obtener más información sobre el uso de la funcionalidad en cuestión con la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre Azure Automanage](automanage-virtual-machines.md)