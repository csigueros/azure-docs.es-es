---
title: Azure Automanage para servidores habilitados para Azure Arc
description: Más información sobre Azure Automanage para servidores habilitados para Azure Arc
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 06/24/2021
ms.openlocfilehash: 42800401334da2f348cee6796e773c1ed0c0f8ff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451167"
---
# <a name="azure-automanage-for-machines-best-practices---azure-arc-enabled-servers"></a>Procedimientos recomendados de Azure Automanage para máquinas: servidores habilitados para Azure Arc

Estos servicios de Azure se incorporan automáticamente cuando usa los procedimientos recomendados de Automanage para máquinas en una VM de servidor habilitado para Azure Arc. Estos servicios son fundamentales para nuestra nota del producto de procedimientos recomendados, que puede encontrar en nuestro marco [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Todos estos servicios se incorporarán y configurarán automáticamente, se supervisarán en busca de desfases y, en caso de que se detecten, se corregirán. Para más información, vaya a [Azure Automanage para máquinas virtuales](automanage-virtual-machines.md).

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos

Automanage admite los siguientes sistemas operativos para servidores habilitados para Azure Arc:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- CentOS 7.3+, 8
- RHEL 7.4+, 8
- Ubuntu 16.04 y 18.04
- SLES 12 (solo SP3-SP5)

## <a name="participating-services"></a>Servicios participantes

|Servicio    |Descripción    |Perfil de configuración <sup>1</sup>    |
|-----------|---------------|----------------------|
|[Supervisión de la información de las máquinas](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor para máquinas supervisa el rendimiento y el mantenimiento de las máquinas virtuales, incluidos los procesos en ejecución y las dependencias de otros recursos.    |Producción    |
|[Administración de actualizaciones](../automation/update-management/overview.md)    |Puede usar Update Management en Azure Automation para administrar las actualizaciones del sistema operativo de las máquinas. Puede evaluar rápidamente el estado de las actualizaciones disponibles en todas las máquinas agente y administrar el proceso de instalación de las actualizaciones necesarias para los servidores.    |Producción, desarrollo/pruebas    |
|[Change Tracking e inventario](../automation/change-tracking/overview.md) |Change Tracking e Inventario combina funciones de inventario y seguimiento de cambios que le permiten realizar un seguimiento de los cambios en la infraestructura de servidores y máquinas virtuales. El servicio admite el seguimiento de cambios en el registro, servicios, demonios, software y archivos del entorno para ayudarle a diagnosticar cambios no deseados y generar alertas. La compatibilidad con inventario le permite consultar recursos de los invitados para obtener una visualización de las aplicaciones instaladas y otros elementos de configuración.    |Producción, desarrollo/pruebas    |
|[Configuración de invitado de Azure](../governance/policy/concepts/guest-configuration.md)  | La directiva de configuración de invitado se usa para supervisar la configuración y notificar sobre el cumplimiento de la máquina. El servicio de Automanage instalará las bases de referencia Linux de Azure con la extensión de configuración de invitado. En el caso de máquinas Linux, el servicio de configuración de invitado instalará la base de referencia en modo de solo auditoría. Podrá ver dónde no cumple la máquina virtual con la base de referencia, pero el incumplimiento no se corregirá automáticamente.    |Producción, desarrollo/pruebas    |
|[Cuenta de Azure Automation](../automation/automation-create-standalone-account.md)    |Azure Automation permite la administración de la infraestructura y las aplicaciones a lo largo de su ciclo de vida.    |Producción, desarrollo/pruebas    |
|[Área de trabajo de Log Analytics](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor almacena los datos de registro en un área de trabajo de Log Analytics, que es un recurso de Azure y un contenedor en el que los datos se recopilan y se agregan, y que sirve como límite administrativo.    |Producción, desarrollo/pruebas    |


<sup>1</sup> La selección de [perfil de configuración](automanage-virtual-machines.md#configuration-profile) está disponible al habilitar Automanage. También puede crear un perfil personalizado propio con el conjunto de servicios y valores de Azure que necesite.


## <a name="next-steps"></a>Pasos siguientes

Pruebe a habilitar Automanage para máquinas en Azure Portal.

> [!div class="nextstepaction"]
> [Habilitar Automanage para máquinas en Azure Portal](quick-create-virtual-machines-portal.md)
