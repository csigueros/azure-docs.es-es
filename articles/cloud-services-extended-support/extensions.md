---
title: Extensiones de Cloud Services (soporte extendido)
description: Extensiones de Cloud Services (soporte extendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 84b27bc4ae30e28a1d97fb8e9931602a1eb14504
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444792"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Extensiones de Cloud Services (soporte extendido)

Las extensiones son aplicaciones pequeñas que proporcionan tareas de automatización y configuración en roles posterior a la implementación. Por ejemplo, puede habilitar una conexión de Escritorio remoto en su rol durante la implementación del servicio en la nube (soporte extendido) mediante la extensión de Escritorio remoto. 

## <a name="key-vault-extension"></a>Extensión de Key Vault

La extensión de máquina virtual de Key Vault proporciona la actualización automática de certificados almacenados en una instancia de Azure Key Vault. En concreto, la extensión supervisa una lista de certificados observados almacenados en almacenes de claves y, cuando detecta un cambio, recupera e instala los certificados correspondientes. También permite la referencia de certificados entre regiones o entre suscripciones para Cloud Service (soporte extendido).

Para más información, vea [Configuración de la extensión de almacén de claves para el servicio en la nube (soporte extendido)](./enable-key-vault-virtual-machine.md).

## <a name="remote-desktop-extension"></a>Extensión de Escritorio remoto

Escritorio remoto le permite tener acceso al escritorio de un rol que se ejecuta en Azure. Puede usar una conexión de Escritorio remoto para solucionar y diagnosticar problemas con su aplicación mientras se ejecuta.

Puede habilitar una conexión a Escritorio remoto en el rol durante el desarrollo mediante la inclusión de los módulos de Escritorio remoto en la definición de servicio o mediante la extensión de Escritorio remoto. 

Para más información, consulte [Configuración de Escritorio remoto desde Azure Portal](enable-rdp.md).

## <a name="windows-azure-diagnostics-extension"></a>Extensión de Microsoft Azure Diagnostics

Puede supervisar las métricas de rendimiento claves de cualquier servicio en la nube. Cada rol de servicio en la nube recopila datos mínimos: uso de CPU, uso de red y uso de disco. Si el servicio en la nube tiene la extensión Microsoft.Azure.Diagnostics aplicada a un rol, este último puede recopilar puntos de datos adicionales. 

Con la supervisión básica, los datos del contador de rendimiento de las instancias de rol se muestrean y recopilan en intervalos de tres minutos. Los datos de la supervisión básica no se almacenan en la cuenta de almacenamiento y, por tanto, no tienen ningún costo adicional asociado. 

Con la supervisión avanzada, las métricas adicionales se muestrean y recopilan en intervalos de cinco minutos, una hora y doce horas. Los datos agregados se almacenan en una cuenta de almacenamiento, en tablas, y se purgan después de diez días. La cuenta de almacenamiento utilizada se configura en función del rol; puede usar diferentes cuentas de almacenamiento para distintos roles. 

Para más información, consulte [Aplicación de la extensión de Windows Azure Diagnostics en Cloud Services (soporte extendido)](enable-wad.md).

## <a name="anti-malware-extension"></a>Extensión Antimalware
Una aplicación o un servicio de Azure puede habilitar y configurar Microsoft Antimalware para Azure Cloud Services mediante cmdlets de PowerShell. Tenga en cuenta que Microsoft Antimalware se instala en estado deshabilitado en la plataforma de Cloud Services que ejecuta Windows Server 2012 R2 y versiones anteriores, lo que requiere la acción de una aplicación de Azure para habilitarlo. En Windows Server 2016 y versiones posteriores, Windows Defender está habilitado de forma predeterminada, por lo que estos cmdlets se pueden usar para configurar Antimalware.

Para más información, consulte [Habilitación y configuración de Microsoft Antimalware para máquinas virtuales de Azure Resource Manager](../security/fundamentals/antimalware-code-samples.md#add-microsoft-antimalware-to-azure-cloud-service-using-extended-support).

Para más información acerca de Azure Antimalware, consulte [aquí](../security/fundamentals/antimalware.md).



## <a name="next-steps"></a>Pasos siguientes 
- Revise los [requisitos previos de implementación](deploy-prerequisite.md) de Cloud Services (soporte extendido).
- Vea las [preguntas más frecuentes](faq.yml) sobre Cloud Services (soporte extendido).
- Implemente una instancia de Cloud Services (soporte extendido) mediante [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), una [plantilla](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
