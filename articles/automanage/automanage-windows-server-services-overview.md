---
title: Automanage para servicios de Windows Server (versión preliminar)
description: Introducción a Automanage para servicios de Windows Server y funcionalidades con Windows Server Azure Edition
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: 475ea083a6412f38093f601ce34da8775357fa3e
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286450"
---
# <a name="automanage-for-windows-server-services-preview"></a>Automanage para servicios de Windows Server (versión preliminar)

Automanage para servicios de Windows Server aporta nuevas funcionalidades específicamente a _Windows Server Azure Edition_.  Estas son algunas de ellas:
- Revisiones en caliente
- SMB a través de QUIC
- Red extendida

> [!IMPORTANT]
> Automanage para los servicios de Windows Server está actualmente en versión preliminar pública. No es necesario ningún procedimiento de participación para usar la funcionalidad de revisión en caliente que se describe a continuación.
> Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Las funcionalidades de Automanage para Windows Server se pueden encontrar en una o varias de estas imágenes de _Windows Server Azure Edition_: 

- Windows Server 2019 Datacenter: Azure Edition (Core)
- Windows Server 2022 Datacenter: Azure Edition (Desktop Experience)
- Windows Server 2022 Datacenter: Azure Edition (Core)

Las funcionalidades varían según la imagen; para más detalles, consulte la sección en la que se indican los [primeros pasos con Windows Server Azure Edition](#getting-started-with-windows-server-azure-edition).

## <a name="automanage-for-windows-server-capabilities"></a>Funcionalidades de Automanage para Windows Server

### <a name="hotpatch"></a>Revisiones en caliente

La revisión en caliente está disponible en versión preliminar pública en las siguientes imágenes:

- Windows Server 2019 Datacenter: Azure Edition (Core)
- Windows Server 2022 Datacenter: Azure Edition (Core)

La revisión en caliente le ofrece la posibilidad de aplicar actualizaciones de seguridad a una máquina virtual sin necesidad de reiniciarla.  Además, Automanage para Windows Server automatiza la incorporación, configuración y orquestación de las revisiones en caliente.  Para más información, consulte el artículo sobre [revisión en caliente](automanage-hotpatch.md).  

### <a name="smb-over-quic"></a>SMB a través de QUIC

SMB a través de QUIC está disponible en versión preliminar pública en las siguientes imágenes:

- Windows Server 2022 Datacenter: Azure Edition (Desktop Experience)
- Windows Server 2022 Datacenter: Azure Edition (Core)

SMB a través de QUIC permite a los usuarios acceder a los archivos cuando trabajan de forma remota sin una VPN, mediante la tunelización del tráfico SMB a través del protocolo QUIC.  Para más información, consulte el artículo sobre [SMB a través de QUIC](/windows-server/storage/file-server/smb-over-quic).  

### <a name="azure-extended-network"></a>Red extendida de Azure

La red extendida de Azure está disponible en versión preliminar pública en las siguientes imágenes:

- Windows Server 2022 Datacenter: Azure Edition (Desktop Experience)
- Windows Server 2022 Datacenter: Azure Edition (Core)

La red extendida de Azure permite extender una subred local a Azure para que las máquinas virtuales locales puedan conservar sus direcciones IP privadas locales originales al migrar a Azure. Para más información, consulte el artículo sobre la [red extendida de Azure](/windows-server/manage/windows-admin-center/azure/azure-extended-network).  


## <a name="getting-started-with-windows-server-azure-edition"></a>Primeros pasos con Windows Server Azure Edition

Es importante tener en cuenta por adelantado qué funcionalidades de Automanage para Windows Server desea usar y, después, elegir una imagen de máquina virtual correspondiente que admita todas esas funcionalidades.  Algunas de las imágenes de _Windows Server Azure Edition_ solo admiten un subconjunto de funcionalidades; para más detalles, consulte la siguiente tabla.

### <a name="deciding-which-image-to-use"></a>Decidir qué imagen usar 

|Imagen|Funcionalidades|
|--|--|
| Windows Server 2019 Datacenter: Azure Edition (Core) | Revisiones en caliente | 
|Windows Server 2022 Datacenter: Azure Edition (Desktop Experience) | SMB a través de QUIC, red extendida | 
| Windows Server 2022 Datacenter: Azure Edition (Core) | Revisión en caliente, SMB a través de QUIC, red extendida | 

### <a name="creating-a-vm"></a>Creación de una máquina virtual

Para empezar a usar las funcionalidades de Automanage para Windows Server en una nueva máquina virtual, use el método que prefiera para crear una máquina virtual de Azure y seleccione la imagen de _Windows Server Azure Edition_ que corresponde al conjunto de [funcionalidades](#getting-started-with-windows-server-azure-edition) que desea usar.  Es posible que sea necesario configurar esas funcionalidades durante la creación de la máquina virtual. Puede obtener más información sobre la configuración de máquinas virtuales en los temas de funcionalidad individuales (como [Revisión en caliente](automanage-hotpatch.md)).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre Azure Automanage](automanage-virtual-machines.md)