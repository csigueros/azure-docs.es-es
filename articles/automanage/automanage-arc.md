---
title: Azure Automanage para los servidores habilitados para Arc
description: Más información sobre Azure Automanage para los servidores habilitados para Arc
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 06/24/2021
ms.author: alsin
ms.openlocfilehash: 990cca45c41eef57f1971c866cdbe1e9efd62f6c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772614"
---
# <a name="azure-automanage-for-machines-best-practices---arc-enabled-servers"></a>Procedimientos recomendados de Azure Automanage para máquinas: servidores habilitados para Arc

Estos servicios de Azure se incorporan automáticamente cuando usa los procedimientos recomendados de Automanage para máquinas en una VM de servidor habilitado para Arc. Estos servicios son fundamentales para nuestra nota del producto de procedimientos recomendados, que puede encontrar en nuestro marco [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Todos estos servicios se incorporarán y configurarán automáticamente, se supervisarán en busca de desfases y, en caso de que se detecten, se corregirán. Para obtener más información sobre este proceso, consulte [Azure Automanage para máquinas virtuales](automanage-virtual-machines.md).

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos

Automanage admite los siguientes sistemas operativos para los servidores habilitados para Arc:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- CentOS 7.3+, 8
- RHEL 7.4+, 8
- Ubuntu 16.04 y 18.04
- SLES 12 (solo SP3-SP5)

## <a name="participating-services"></a>Servicios participantes

|Servicio    |Descripción    |Entornos compatibles<sup>1</sup>    |Preferencias admitidas<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[Supervisión de la información de las máquinas](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor para máquinas supervisa el rendimiento y el mantenimiento de las máquinas virtuales, incluidos los procesos en ejecución y las dependencias de otros recursos. [Más información](../azure-monitor/vm/vminsights-overview.md).    |Producción    |No    |
|[Azure Security Center](../security-center/security-center-introduction.md)    |Azure Security Center es un sistema unificado de administración de la seguridad de infraestructura que fortalece el nivel de seguridad de sus centros de datos, y proporciona protección contra amenazas avanzada en las cargas de trabajo híbridas de la nube. [Más información](../security-center/security-center-introduction.md).  Automanage configurará la suscripción en la que reside la máquina virtual a la oferta de nivel Gratis de Azure Security Center. Si la suscripción ya se ha incorporado a Azure Security Center, Automanage no la volverá a configurar.    |Producción, desarrollo/pruebas    |No    |
|[Administración de actualizaciones](../automation/update-management/overview.md)    |Puede usar Update Management en Azure Automation para administrar las actualizaciones del sistema operativo de las máquinas. Puede evaluar rápidamente el estado de las actualizaciones disponibles en todas las máquinas agente y administrar el proceso de instalación de las actualizaciones necesarias para los servidores. [Más información](../automation/update-management/overview.md).    |Producción, desarrollo/pruebas    |No    |
|[Change Tracking e inventario](../automation/change-tracking/overview.md) |Change Tracking e Inventario combina funciones de inventario y seguimiento de cambios que le permiten realizar un seguimiento de los cambios en la infraestructura de servidores y máquinas virtuales. El servicio admite el seguimiento de cambios en el registro, servicios, demonios, software y archivos del entorno para ayudarle a diagnosticar cambios no deseados y generar alertas. La compatibilidad con inventario le permite consultar recursos de los invitados para obtener una visualización de las aplicaciones instaladas y otros elementos de configuración.  [Más información](../automation/change-tracking/overview.md).    |Producción, desarrollo/pruebas    |No    |
|[Configuración de invitado de Azure](../governance/policy/concepts/guest-configuration.md)  | La directiva de configuración de invitado se usa para supervisar la configuración y notificar sobre el cumplimiento de la máquina. El servicio de Automanage instalará las bases de referencia Linux de Azure con la extensión de configuración de invitado. En el caso de máquinas Linux, el servicio de configuración de invitado instalará la base de referencia en modo de solo auditoría. Podrá ver dónde no cumple la máquina virtual con la base de referencia, pero el incumplimiento no se corregirá automáticamente. [Más información](../governance/policy/concepts/guest-configuration.md).    |Producción, desarrollo/pruebas    |No    |
|[Cuenta de Azure Automation](../automation/automation-create-standalone-account.md)    |Azure Automation permite la administración de la infraestructura y las aplicaciones a lo largo de su ciclo de vida. [Más información](../automation/automation-intro.md).    |Producción, desarrollo/pruebas    |No    |
|[Área de trabajo de Log Analytics](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor almacena los datos de registro en un área de trabajo de Log Analytics, que es un recurso de Azure y un contenedor en el que los datos se recopilan y se agregan, y que sirve como límite administrativo. [Más información](../azure-monitor/logs/design-logs-deployment.md).    |Producción, desarrollo/pruebas    |No    |


<sup>1</sup> La selección de entorno está disponible cuando se habilita Automanage. [Más información](automanage-virtual-machines.md#environment-configuration). También puede ajustar la configuración predeterminada del entorno y establecer sus propias preferencias dentro de las restricciones de los procedimientos recomendados.


## <a name="next-steps"></a>Pasos siguientes

Pruebe a habilitar Automanage para máquinas en Azure Portal.

> [!div class="nextstepaction"]
> [Habilitar Automanage para máquinas en Azure Portal](quick-create-virtual-machines-portal.md)