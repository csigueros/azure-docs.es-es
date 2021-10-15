---
title: Solución de problemas de Hybrid Runbook Worker basado en extensiones en Azure Automation
description: En este artículo se explica cómo solucionar los problemas que surgen con instancias de Hybrid Runbook Worker basadas en extensiones de Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 09/28/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 278f49ab45bd5ed65b39209f2ca81c0e3ff9ad30
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293523"
---
# <a name="troubleshoot-vm-extension-based-hybrid-runbook-worker-issues-in-automation"></a>Solución de problemas de Hybrid Runbook Worker basado en extensiones de máquina virtual en Automation

En este artículo se proporciona información sobre cómo solucionar problemas con las instancias de Hybrid Runbook Worker basadas en extensiones de Azure Automation. Para solucionar problemas de los trabajos basados en agente, consulte [Solución de problemas de Hybrid Runbook Worker basado en agentes en Automation](./hybrid-runbook-worker.md). Para obtener información general, consulte [Información general sobre Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md).

## <a name="general-checklist"></a>Lista de comprobación general

Para ayudar a solucionar problemas con instancias de Hybrid Runbook Worker basadas en extensiones, siga estos pasos:

- Compruebe que se admite el sistema operativo y que se han cumplido los requisitos previos.Consulte [Requisitos previos](../extension-based-hybrid-runbook-worker-install.md#prerequisites).

- Compruebe que la identidad administrada asignada por el sistema esté habilitada en la máquina virtual. Las máquinas virtuales de Azure y las máquinas de Azure habilitadas para Arc deben tener habilitada una identidad administrada asignada por el sistema.

- Compruebe que la extensión esté habilitada con la configuración correcta.El archivo de configuración debe tener el derecho  `AutomationAccountURL`.Realice una comprobación cruzada con la propiedad  `AutomationHybridServiceUrl` de la cuenta de Automation.  
  - En Windows: puede encontrar el archivo de configuración en  `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin`.
  - En Linux: puede encontrar el archivo de configuración en  `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/`.

- Compruebe el mensaje de error que se muestra en el estado de extensión/estado detallado de Hybrid Worker.Contiene mensajes de error y las recomendaciones correspondientes para corregir el problema.

- Ejecute la herramienta de solución de problemas en la máquina virtual y se generará un archivo de salida. Abra el archivo de salida y compruebe los errores identificados por la herramienta.
  - En Windows: puede encontrar el solucionador de problemas en `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\TroubleShootWindowsExtension.ps1`.
  - En Linux: puede encontrar el solucionador de problemas en `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/troubleshootLinuxExtension.py`.

- En máquinas Linux, compruebe si el usuario `hweautomation` está configurado con los permisos correctos.  

- Compruebe si el proceso de Hybrid Worker se está ejecutando.
   - En Windows: compruebe el servicio  `Hybrid Worker Service`.
   - En Linux: compruebe el servicio  `hwd.`.

- Ejecute la herramienta de recopilación de registros y revise los registros recopilados.
   - En Windows: los registros se encuentran en `C:\HybridWorkerExtensionLogs`. La herramienta está en `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\PullLogs.ps1`. El registro de la extensión es `HybridWorkerExtensionHandler.log`. El registro de trabajo es `SME.log`.
   - En Linux: los registros se encuentran en `/home/nxautomation/run`. La herramienta está en `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/logcollector.py`. El registro de trabajo es `worker.log`. La entrada del registro de la extensión es `register_log`. El registro de inicio de la extensión es `startup_log`. El registro de la extensión es `extension_out`.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece aquí o no puede resolverlo, intente obtener ayuda adicional mediante uno de los siguientes canales:

* Obtenga respuestas de expertos de Azure en los [foros de Azure](https://azure.microsoft.com/support/forums/).
* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente. El Soporte técnico de Azure pone en contacto a la comunidad de Azure con respuestas, soporte técnico y expertos.
* Registrar un incidente de soporte técnico de Azure. Vaya al [sitio de Soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte técnico**.