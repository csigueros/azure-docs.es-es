---
title: Administración de Azure Cloud Services (clásico) mediante Azure Automation | Microsoft Docs
description: Obtenga información acerca de cómo puede usarse el servicio Azure Automation para administrar servicios en la nube de Azure a escala.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cc352a35308cbc77dc544c780ea7045cff561b02
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824520"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>Administración de Azure Cloud Services (clásico) mediante Azure Automation

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Esta guía le ofrece el servicio Azure Automation y cómo se puede usar para simplificar la administración de Azure Cloud Services.

## <a name="what-is-azure-automation"></a>¿Qué es Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos. Mediante Azure Automation, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten con frecuencia para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Azure Automation proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades a medida que crece la organización. En Azure Automation, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>¿Cómo puede ayudar el servicio Azure Automation a administrar Azure Cloud Services?
Los servicios en la nube de Azure se pueden administrar en Azure Automation mediante el uso de cmdlets de PowerShell que están disponibles en las [herramientas de Azure PowerShell](/powershell/). Azure Automation tiene estos cmdlets de PowerShell de servicios en la nube disponibles directamente para que pueda realizar todas las tareas de administración de servicios en la nube dentro del servicio. También puede emparejar estos cmdlets en Azure Automation con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

Algunos usos de ejemplo de Azure Automation para administrar Azure Cloud Services son los siguientes:

* [Implementación continua de un servicio en la nube siempre que cscfg o cspkg estén actualizados en el almacenamiento de blobs de Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Reinicio de instancias de servicio en la nube en paralelo (un dominio de actualización a la vez)](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los aspectos básicos de Azure Automation y cómo se puede usar para administrar servicios en la nube de Azure, siga estos vínculos para obtener más información acerca de Azure Automation.

* [Información general sobre Azure Automation](../automation/automation-intro.md)
* [Mi primer runbook](../automation/learn/automation-tutorial-runbook-graphical.md)