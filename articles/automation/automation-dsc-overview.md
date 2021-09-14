---
title: Introducción a Azure Automation State Configuration
description: En este artículo se proporciona una introducción sobre Azure Automation State Configuration.
keywords: powershell dsc, configuración de estado deseado, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 08/17/2021
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c40a8b002605b8776ac04d2af0d920b824aaeab0
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396657"
---
# <a name="azure-automation-state-configuration-overview"></a>Introducción a Azure Automation State Configuration

Azure Automation State Configuration es un servicio de administración de configuración de Azure que le permite escribir, administrar y compilar [configuraciones](/powershell/scripting/dsc/configurations/configurations) de PowerShell Desired State Configuration (DSC) para nodo de cualquier centro de datos en la nube local. El servicio también importa [recursos de DSC](/powershell/scripting/dsc/resources/resources) y asigna configuraciones a los nodos de destino, todos en la nube. Para acceder a Azure Automation State Configuration desde Azure Portal, seleccione **State Configuration (DSC)** en **Administración de configuración**.

> [!NOTE]
> Antes de habilitar State Configuration de Automation, nos gustaría que conociera la [configuración de invitados de Azure Policy](../governance/policy/concepts/guest-configuration.md), que puede auditar o configurar los ajustes dentro de las máquinas que se ejecutan en Azure o en las máquinas fuera de Azure conectadas con [servidores habilitados para Arc](../azure-arc/servers/overview.md). La configuración de invitados de Azure Policy ofrece una funcionalidad similar y está diseñada para aprovechar las ventajas de la tecnología más reciente en Azure.

Puede utilizar Azure Automation State Configuration para administrar diversas máquinas:

- Azure Virtual Machines
- Azure Virtual Machines (clásico)
- Máquinas físicas y virtuales con Windows locales o en una nube que no sea Azure (incluidas las instancias de AWS EC2)
- Máquinas físicas y virtuales con Linux locales, en Azure o en una nube que no sea Azure

Si no está preparado para administrar la configuración de las máquinas desde la nube, Azure Automation State Configuration también se puede usar como punto de conexión meramente informativo. Esta característica permite establecer (insertar) configuraciones a través de DSC y ver detalles de los informes en Azure Automation.

> [!NOTE]
> La administración de máquinas virtuales de Azure con State Configuration de Azure Automation se incluye sin cargo extra alguno si la versión de la extensión Desired State Configuration de máquina virtual de Azure instalada es superior a 2.70. Consulte la [**página de precios de Automation**](https://azure.microsoft.com/pricing/details/automation/) para más información.

## <a name="why-use-azure-automation-state-configuration"></a>Razones para usar Azure Automation State Configuration

Azure Automation State Configuration proporciona varias ventajas con respecto al uso de DSC fuera de Azure. Este servicio permite la escalabilidad en miles de máquinas de forma rápida y sencilla desde una ubicación central y segura. Puede habilitar fácilmente máquinas, asignarles configuraciones declarativas y ver informes que muestran el cumplimiento por parte de cada máquina del estado deseado que se especifique.

Azure Automation State Configuration es a DSC lo que los runbooks de Azure Automation son al scripting de PowerShell. En otras palabras, Azure Automation le ayuda a administrar las configuraciones de DSC, de la misma manera que lo hace con los scripts de PowerShell.

### <a name="built-in-pull-server"></a>Servidor de extracción integrado

Azure Automation State Configuration proporciona un servidor de extracción de DSC similar a la [característica de Windows DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). Los nodos de destino pueden recibir automáticamente configuraciones, ajustarse al estado deseado y enviar informes de cumplimiento. El servidor de extracción integrado en Azure Automation elimina la necesidad de configurar y mantener su propio servidor de extracción. Azure Automation puede tener como destino máquinas físicas o virtuales Windows o Linux, en la nube o en un entorno local.

### <a name="management-of-all-your-dsc-artifacts"></a>Administración de todos los artefactos de DSC

Azure Automation State Configuration proporciona la misma capa de administración a [Desired State Configuration de PowerShell](/powershell/scripting/dsc/overview/overview) que la que ofrece para scripting de PowerShell. Desde Azure Portal o desde PowerShell, puede administrar todas las configuraciones, recursos y nodos de destino de DSC.

![Captura de pantalla de la página de Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importación de datos de informes a registros de Azure Monitor

Los nodos que se administran con Azure Automation State Configuration envían datos de informe de estado detallados al servidor de extracción integrado. Puede configurar Azure Automation State Configuration para que envíe estos datos a su área de trabajo de Log Analytics. Consulte [Reenvío de datos de informes de Azure Automation State Configuration a registros de Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Requisitos previos

Tenga en cuenta los requisitos de esta sección al usar Azure Automation State Configuration.

### <a name="operating-system-requirements"></a>Requisitos de sistema operativo

Para nodos que ejecutan Windows, se admiten las siguientes versiones:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>La SKU del producto independiente [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) no contiene ninguna implementación de DSC. Por lo tanto, no se puede administrar con DSC de PowerShell o con Azure Automation State Configuration.

Para los nodos que ejecutan Linux, la extensión DSC de Linux admite todas las distribuciones de Linux que se enumeran en la [documentación de DSC de PowerShell](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

### <a name="dsc-requirements"></a>Requisitos de DSC

En todos los nodos de Windows que se ejecutan en Azure, [WMF 5.1](/powershell/scripting/wmf/setup/install-configure) se instala cuando se habilitan las máquinas. Para los nodos que ejecutan Windows Server 2012 y Windows 7, se habilita [WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

Para todos los nodos de Linux que se ejecutan en Azure, [DSC de PowerShell para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) se instala cuando se habilitan las máquinas.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuración de redes privadas

Vea [Configuración de red de Azure Automation](automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) para obtener información detallada sobre los puertos, las direcciones URL y otros detalles de red necesarios para los nodos de una red privada.

#### <a name="proxy-support"></a>Compatibilidad con proxy

La compatibilidad con proxy para el agente DSC está disponible en Windows, versión 1809 y posteriores. Para habilitar esta opción, establezca los valores de las propiedades `ProxyURL` y `ProxyCredential` en el [script de metaconfiguraciones](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) que se utiliza para registrar los nodos.

>[!NOTE]
>Azure Automation State Configuration no proporciona compatibilidad con el proxy de DSC para versiones anteriores de Windows.

Para los nodos de Linux, el agente de DSC admite el proxy y utiliza la variable `http_proxy` para determinar la dirección URL. Para obtener más información sobre el soporte con proxy, vea [Generación de metaconfiguraciones de DSC](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="dns-records-per-region"></a>Registros DNS por región

Se recomienda usar las direcciones que aparecen en la tabla [Registros DNS por región](how-to/automation-region-dns-records.md) al definir excepciones.

## <a name="next-steps"></a>Pasos siguientes

- Para dar los primeros pasos, consulte [Introducción a Azure Automation State Configuration](automation-dsc-getting-started.md).
- Para aprender a habilitar nodos, consulte el artículo sobre [Habilitar Azure Automation State Configuration](automation-dsc-onboarding.md).
- Para aprender a compilar configuraciones de DSC para poder asignarlas a los nodos de destino, consulte [Compilación de configuraciones de DSC en Azure Automation State Configuration](automation-dsc-compile.md).
- Para ver un ejemplo del uso de State Configuration de Azure Automation en una canalización de implementación continua, consulte [Configuración de la implementación continua con Chocolatey](automation-dsc-cd-chocolatey.md).
- Para obtener información de precios, consulte [Precios de State Configuration de Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Para ver una referencia de los cmdlets de PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
