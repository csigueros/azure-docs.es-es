---
title: Instancia local de Azure Resource Manager en un dispositivo Azure Stack Edge Pro con GPU
description: Se describe información general sobre la instancia local de Azure Resource Manager local en el dispositivo de Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 8d200195540f92c88ae3dc93737ea09a461e6c26
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113114564"
---
# <a name="what-is-local-azure-resource-manager-on-azure-stack-edge"></a>¿Qué es la instancia local de Azure Resource Manager en un dispositivo de Azure Stack Edge?

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager proporciona una capa de administración que le permite crear, actualizar y eliminar recursos de su suscripción de Azure. Los dispositivos de Azure Stack Edge admiten las mismas API de Azure Resource Manager para crear, actualizar y eliminar máquinas virtuales en una suscripción local. Esta compatibilidad le permite administrar el dispositivo de manera coherente con la nube. 

En este artículo se proporciona información general sobre la instancia local de Azure Resource Manager que se puede usar para conectarse a las API locales en los dispositivos de Azure Stack Edge.

## <a name="about-local-azure-resource-manager"></a>Acerca de la instancia local de Azure Resource Manager

La instancia local de Azure Resource Manager proporciona una capa de administración coherente para todas las llamadas al dispositivo de Azure Stack Edge mediante el uso de plantillas de Resource Manager. Las ventajas de la instancia local de Azure Resource Manager se describen en las secciones siguientes.

#### <a name="consistent-management-layer"></a>Capa de administración coherente

La instancia local de Azure Resource Manager proporciona una capa de administración coherente para llamar a la API de dispositivo de Azure Stack Edge y realizar operaciones como crear, actualizar y eliminar máquinas virtuales. 

1. Cuando se envía una solicitud desde API REST o SDK, la recibe la instancia local de Azure Resource Manager en el dispositivo. 
1. La instancia local de Azure Resource Manager usa el servicio de token de seguridad (STS) para autenticar y autorizar la solicitud. STS es el responsable de la creación, validación, renovación y cancelación de tokens de seguridad. STS crea los dos tipos de tokens de seguridad: de acceso y de actualización. Estos tokens se usan para la comunicación continua entre el dispositivo y los clientes que acceden al dispositivo por medio de la instancia local de Azure Resource Manager.
1. Después, Resource Manager envía la solicitud a los proveedores de recursos que emprenden la acción solicitada.   

    Los proveedores de recursos que están registrados previamente con Azure Stack Edge son los siguientes:

    - **Proceso**: `Microsoft.Compute` o el proveedor de recursos de proceso le permite implementar máquinas virtuales en Azure Stack Edge. El proveedor de recursos de Compute incluye la capacidad de crear máquinas virtuales y extensiones de máquina virtual. 

    - **Proveedor de recursos de red**: `Microsoft.Network` o el proveedor de recursos de red le permite crear recursos como interfaces de red y redes virtuales.

    - **Proveedor de recursos de almacenamiento**: `Microsoft.Storage` o el proveedor de recursos de almacenamiento servicio de almacenamiento de blobs compatible con Azure y administración de cuentas de Key Vault para la administración y auditoría de secretos, como contraseñas y certificados.  
    
    - **Proveedor de recursos de disco**: `Microsoft.Disks` o el proveedor de recursos de disco le permitirá crear discos administrados que se pueden usar para crear máquinas virtuales.

    Los recursos son elementos administrables disponibles por medio de Azure Stack Edge y los proveedores de recursos son responsables de proporcionar recursos. Las máquinas virtuales, cuentas de almacenamiento y redes virtuales son ejemplos de recursos. Y el proveedor de recursos de proceso proporciona el recurso de máquina virtual.    

Dado que todas las solicitudes se controlan mediante la misma API, verá resultados y funcionalidades coherentes en todas las distintas herramientas.

En la imagen siguiente se muestra el mecanismo de control de todas las solicitudes de API y el rol que desempeña la instancia local de Azure Resource Manager al proporcionar una capa de administración coherente para controlar esas solicitudes.

![Diagrama de Azure Resource Manager.](media/azure-stack-edge-gpu-connect-resource-manager/edge-device-flow.svg)


#### <a name="use-of-resource-manager-templates"></a>Uso de plantillas de Resource Manager

Otra ventaja clave de Azure Resource Manager es que le permite usar plantillas de Resource Manager. Se trata de archivos de notación de objetos JavaScript (JSON) en una sintaxis declarativa que se pueden usar para implementar los recursos de forma coherente y repetida. La sintaxis declarativa permite establecer lo que se pretende crear sin tener que escribir la secuencia de comandos de programación para crearlo. Por ejemplo, puede usar estas plantillas de sintaxis declarativa para implementar máquinas virtuales en los dispositivos de Azure Stack Edge. Para obtener información detallada, vea [Implementación de máquinas virtuales en el dispositivo de Azure Stack Edge mediante plantillas](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

## <a name="connect-to-the-local-azure-resource-manager"></a>Conexión a la instancia local de Azure Resource Manager

Para crear máquinas virtuales, recursos compartidos o cuentas de almacenamiento en el dispositivo de Azure Stack Edge, tendrá que crear los recursos correspondientes. Por ejemplo, para las máquinas virtuales, necesitará recursos como la interfaz de red, el sistema operativo y los discos de datos de la máquina virtual, de los proveedores de recursos de red, disco y almacenamiento. 

Para solicitar la creación de recursos de los proveedores de recursos, primero tendrá que conectarse a la instancia local de Azure Resource Manager. Para obtener los pasos detallados, vea [Conexión con Azure Resource Manager desde el dispositivo de Azure Stack Edge](azure-stack-edge-gpu-connect-resource-manager.md).

La primera vez que se conecte a Azure Resource Manager, también tendrá que restablecer la contraseña. Para obtener los pasos detallados, vea [Restablecimiento de la contraseña de Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md).


## <a name="azure-resource-manager-endpoints"></a>Puntos de conexión de Azure Resource Manager

La instancia local de Azure Resource Manager y Los servicios STS se ejecutan en el dispositivo y son accesibles en puntos de conexión específicos. En la tabla siguiente se resumen los distintos puntos de conexión que expone este servicio en el dispositivo, los protocolos admitidos y los puertos para acceder a esos puntos de conexión. 

| # | Punto de conexión | Protocolos admitidos | Puerto usado | Se usa para |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Para conectarse a Azure Resource Manager para la automatización |
| 2. | Servicio de token de seguridad | https | 443 | Para autenticarse mediante tokens de acceso y actualización |


## <a name="next-steps"></a>Pasos siguientes

[Conexión a la instancia local de Azure Resource Manager en un dispositivo Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).
