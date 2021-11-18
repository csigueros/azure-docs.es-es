---
title: Azure Automanage para Windows Server
description: Obtenga información sobre los procedimientos recomendados de Azure Automanage para máquinas virtuales para los servicios que se incorporan y configuran automáticamente para las máquinas de Windows Server.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 72ef8193215d3dc35f576a96a005bde8bf40e49f
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371949"
---
# <a name="azure-automanage-for-machines-best-practices---windows-server"></a>Procedimientos recomendados de Azure Automanage para máquinas: Windows Server

Estos servicios de Azure se incorporan automáticamente cuando usa los procedimientos recomendados de Automanage para máquinas en una VM Windows Server. Estos servicios son fundamentales para nuestra nota del producto de procedimientos recomendados, que puede encontrar en nuestro marco [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Todos estos servicios se incorporarán y configurarán automáticamente, se supervisarán en busca de desfases y, en caso de que se detecten, se corregirán. Para más información, vaya a [Azure Automanage para máquinas virtuales](automanage-virtual-machines.md).

## <a name="supported-windows-server-versions"></a>Versiones de Windows Server admitidas

Automanage es compatible con las siguientes versiones de Windows Server:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2022
- Windows Server 2022 Azure Edition

## <a name="participating-services"></a>Servicios participantes

|Servicio    |Descripción    |Perfil de configuración <sup>1</sup>    |
|-----------|---------------|----------------------|
|[Supervisión de la información de las máquinas](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor para máquinas supervisa el rendimiento y el mantenimiento de las máquinas virtuales, incluidos los procesos en ejecución y las dependencias de otros recursos.    |Producción    |
|[Backup](../backup/backup-overview.md)    |Azure Backup proporciona copias de seguridad independientes y aisladas para impedir la destrucción accidental de los datos en las máquinas. Los cargos se basan en el número y el tamaño de las VM que se están protegiendo.    |Producción    |
|[Microsoft Defender for Cloud](../defender-for-cloud/defender-for-cloud-introduction.md)    |Microsoft Defender for Cloud es un sistema unificado de administración de la seguridad de la infraestructura que fortalece la posición de seguridad de los centros de datos y proporciona protección avanzada frente amenazas en las cargas de trabajo híbridas en la nube.  Automanage establece la suscripción en la que reside la máquina virtual en la oferta de nivel Gratis de Microsoft Defender for Cloud. Si la suscripción ya se ha incorporado a Microsoft Defender for Cloud, Automanage no la vuelve a configurar.    |Producción, desarrollo/pruebas    |
|[Microsoft Antimalware](../security/fundamentals/antimalware.md)    |Microsoft Antimalware para Azure es una protección gratuita en tiempo real que ayuda a identificar y eliminar virus, spyware y otro software malintencionado. Genera alertas cuando software no deseado o malintencionado intenta instalarse o ejecutarse en los sistemas de Azure. **Nota:** Microsoft Antimalware requiere que no haya ningún otro software antimalware instalado o puede que no funcione. |Producción, desarrollo/pruebas    |
|[Administración de actualizaciones](../automation/update-management/overview.md)    |Puede usar Update Management en Azure Automation para administrar las actualizaciones del sistema operativo de las máquinas. Puede evaluar rápidamente el estado de las actualizaciones disponibles en todas las máquinas agente y administrar el proceso de instalación de las actualizaciones necesarias para los servidores.    |Producción, desarrollo/pruebas    |
|[Change Tracking e inventario](../automation/change-tracking/overview.md) |Change Tracking e Inventario combina funciones de inventario y seguimiento de cambios que le permiten realizar un seguimiento de los cambios en la infraestructura de servidores y máquinas virtuales. El servicio admite el seguimiento de cambios en el registro, servicios, demonios, software y archivos del entorno para ayudarle a diagnosticar cambios no deseados y generar alertas. La compatibilidad con inventario le permite consultar recursos de los invitados para obtener una visualización de las aplicaciones instaladas y otros elementos de configuración.    |Producción, desarrollo/pruebas    |
|[Configuración de invitado](../governance/policy/concepts/guest-configuration.md) | La directiva de configuración de invitado se usa para supervisar la configuración y notificar sobre el cumplimiento de la máquina. El servicio Automanage instala las [Líneas de base de seguridad de Windows](/windows/security/threat-protection/windows-security-baselines) con la extensión de configuración de invitado. En el caso de las máquinas Windows, el servicio de configuración de invitado volverá a aplicar automáticamente la configuración de la base de referencia si no son compatibles.    |Producción, desarrollo/pruebas    |
|[Diagnósticos de arranque](../virtual-machines/boot-diagnostics.md)    | Diagnósticos de arranque es una característica de depuración para máquinas virtuales (VM) de Azure que permite el diagnóstico de errores de arranque de la máquina virtual. Los diagnósticos de arranque permiten a un usuario observar el estado de la máquina virtual cuando está arrancando mediante la recopilación de información de registro serie y capturas de pantallas. Esta característica solo se habilitará en las máquinas que usan discos administrados. |Producción, desarrollo/pruebas    |
|[Windows Admin Center](/windows-server/manage/windows-admin-center/azure/manage-vm)    | Use Windows Admin Center (versión preliminar) en Azure Portal para administrar el sistema operativo Windows Server dentro de una máquina virtual de Azure. Esto solo se admite para las máquinas que usan Windows Server 2016 o superior. Automanage configura Windows Admin Center a través de una dirección IP privada. Si desea conectarse con Windows Admin Center a través de una dirección IP pública, abra una regla de puerto de entrada para el puerto 6516. Automanage incorpora a Windows Admin Center para el perfil de desarrollo/pruebas de forma predeterminada. Use las preferencias para habilitar o deshabilitar Windows Admin Center para los entornos de producción y desarrollo/pruebas. |Producción, desarrollo/pruebas    |
|[Cuenta de Azure Automation](../automation/automation-create-standalone-account.md)    |Azure Automation permite la administración de la infraestructura y las aplicaciones a lo largo de su ciclo de vida.    |Producción, desarrollo/pruebas    |
|[Área de trabajo de Log Analytics](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor almacena los datos de registro en un área de trabajo de Log Analytics, que es un recurso de Azure y un contenedor en el que los datos se recopilan y se agregan, y que sirve como límite administrativo.    |Producción, desarrollo/pruebas    |


<sup>1</sup> La selección de [perfil de configuración](automanage-virtual-machines.md#configuration-profile) está disponible al habilitar Automanage. También puede crear un perfil personalizado propio con el conjunto de servicios y valores de Azure que necesite.


## <a name="next-steps"></a>Pasos siguientes

Pruebe a habilitar Automanage para máquinas en Azure Portal.

> [!div class="nextstepaction"]
> [Habilitar Automanage para máquinas en Azure Portal](quick-create-virtual-machines-portal.md)
