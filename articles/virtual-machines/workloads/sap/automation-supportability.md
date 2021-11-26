---
title: Matriz de compatibilidad para el marco de automatización de la implementación de SAP
description: Plataformas, topologías y funcionalidades compatibles con el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: b9295fdffc215d842bf53f1a7dcfbf984b764a07
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725337"
---
# <a name="supportability-matrix-for-the-sap-automation-framework"></a>Matriz de compatibilidad del marco de automatización de SAP

El [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) admite la implementación de todas las topologías compatibles de SAP en Azure.

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos

### <a name="control-plane"></a>Plano de control

La máquina virtual del implementador del plano de control se debe implementar en Linux, ya que el controlador de Ansible solo funciona en Linux.

### <a name="sap-application"></a>Aplicación SAP

El marco de automatización admite la implementación de la infraestructura de SAP en Azure en máquinas virtuales Linux o Windows. 

## <a name="supported-topologies"></a>Topologías admitidas

De forma predeterminada, el marco de automatización se implementa con capas de base de datos y aplicación. La capa de aplicación se divide en tres capas más: aplicación, servicios centrales e instancias de Web Dispatcher. 

También puede implementar el marco de automatización en un servidor independiente especificando una configuración sin capa de aplicación.

## <a name="supported-deployment-topologies"></a>Topologías de implementación admitidas

El marco de automatización admite implementaciones green field y brown field. 

### <a name="greenfield-deployments"></a>Implementaciones green field
En la implementación green field, el marco de automatización creará todos los recursos necesarios.

En este escenario, se proporcionan los datos pertinentes (espacios de direcciones para redes y subredes) al configurar el entorno. Consulte la [configuración de la zona de cargas de trabajo](automation-configure-workload-zone.md) para ver más ejemplos.

### <a name="brownfield-deployments"></a>Implementaciones brown field
En la implementación brown field, los recursos de Azure existentes se pueden usar como parte de la implementación.

En este escenario, se proporcionan los identificadores de recursos de Azure para los recursos existentes al configurar el entorno. Consulte la [configuración de la zona de cargas de trabajo](automation-configure-workload-zone.md) para ver más ejemplos.

## <a name="supported-azure-features"></a>Características de Azure admitidas

El marco de automatización usa o puede usar los siguientes servicios, características y funcionalidades de Azure.

- Máquinas virtuales de Azure (VM)
    - Redes aceleradas
    - Máquinas virtuales de delimitador (opcional)
    - Autenticación SSH
    - Autenticación mediante nombre de usuario y contraseña
    - Configuración de SKU
    - Imágenes personalizadas
    - Grupos con ubicación por proximidad nuevos o existentes
- Azure Virtual Network (VNET)
    - Implementación en redes emparejadas a la red SAP
    - Direccionamiento IP especificado por el cliente
    - Direccionamiento IP proporcionado por Azure
    - Grupos de seguridad de red nuevos o existentes
    - Red virtual nueva o existente
    - Subredes nuevas o existentes
- Azure Availability Zones
    - Alta disponibilidad (HA)
- Azure Firewall
- Azure Load Balancer
    - Equilibradores de carga estándar
- Azure Storage
    - Almacenamiento de diagnósticos de arranque
    - Cuenta de almacenamiento del soporte de instalación de SAP
    - Almacenamiento de archivos de estado de Terraform
    - Almacenamiento de testigos en la nube para escenarios de alta disponibilidad
- Azure Key Vault
    - Almacenes de claves nuevos o existentes
    - Claves administradas por el cliente para el cifrado de disco
- Grupos de seguridad de aplicaciones de Azure
- Azure NetApp files (ANF)
    - Para archivos compartidos

## <a name="unsupported-azure-features"></a>Características de Azure no admitidas

En este momento el marco de automatización **no admite** los siguientes servicios, características o funcionalidades de Azure:

- Azure Files para NFS
- Azure NetApp files (ANF)
    - Para archivos de base de datos

## <a name="next-steps"></a>Pasos siguientes


> [!div class="nextstepaction"]
> [Introducción al marco de automatización](automation-get-started.md)
