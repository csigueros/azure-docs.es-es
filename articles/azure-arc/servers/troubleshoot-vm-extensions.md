---
title: Solución de problemas de la extensión de máquina virtual de servidores habilitados para Azure Arc
description: En este artículo se explica cómo solucionar y resolver problemas con las extensiones de máquina virtual de Azure que surgen con los servidores habilitados para Azure Arc.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 690b92389d86ca497801af79c6930677c19178fe
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114390208"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Solución de problemas de la extensión de máquina virtual de servidores habilitados para Arc

En este artículo se proporciona información sobre cómo solucionar y resolver problemas que pueden producirse al intentar implementar o quitar extensiones de máquina virtual de Azure en servidores habilitados para Arc. Para información general, consulte [Administración y uso de extensiones de máquina virtual de Azure](./manage-vm-extensions.md).

## <a name="general-troubleshooting"></a>Solución general de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal.

Los pasos de solución de problemas siguientes se aplican a todas las extensiones de máquina virtual.

1. Para comprobar el registro de agente invitado, examine la actividad cuando la extensión se aprovisionó en `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` para Windows y en `/var/lib/GuestConfig/ext_mgr_logs` para Linux.

2. Compruebe los registros de la extensión específica para obtener más detalles en `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` para Windows. La salida de la extensión se registra en un archivo para cada extensión instalada en Linux en la ruta `/var/lib/GuestConfig/extension_logs`.

3. Revise las secciones de solución de problemas de la documentación específica de la extensión para ver los códigos de errores, los problemas conocidos, etc. Puede encontrar información adicional para solucionar los problemas de cada extensión en la sección **Solución de problemas y soporte técnico** de la información general de la extensión. Esto incluye la descripción de los códigos de error que se escriben en el registro. Los artículos de extensión están vinculados en la [tabla de extensiones](manage-vm-extensions.md#extensions).

4. Examine los registros del sistema. Compruebe si hay otras operaciones que puedan haber interferido con la extensión, como una instalación de ejecución prolongada de otra aplicación que requería acceso exclusivo al administrador de paquetes.

## <a name="troubleshooting-specific-extension-scenarios"></a>Solución de problemas de escenarios de extensiones específicos

### <a name="vm-insights"></a>VM Insights

- Al habilitar VM Insights para un servidor habilitado para Azure Arc, se instala la dependencia y el agente de Log Analytics. En una máquina lenta o en una con una conexión de red lenta, puede que aparezcan tiempos de espera durante el proceso de instalación. Microsoft está llevando a cabo los pasos necesarios para solucionar este problema en el agente de Connected Machine como ayuda para mejorar esta condición. Mientras tanto, puede que sea suficiente con reintentar la instalación.

### <a name="log-analytics-agent-for-linux"></a>Agente de Log Analytics para Linux

- La versión del agente de Log Analytics 1.13.9 (la versión de extensión correspondiente es 1.13.15) no marca correctamente los datos cargados con el identificador de recurso del servidor habilitado para Azure Arc. Aunque los registros se envían al servicio, cuando intenta ver los datos del servidor habilitado seleccionado después de seleccionar **Registros** o **Insights**, no se devuelve ningún dato. Para ver sus datos, ejecute consultas desde los registros de Azure Monitor o desde Azure Monitor para VM, cuyo ámbito es el área de trabajo.

- Actualmente, el agente de Log Analytics para Linux no admite algunas distribuciones. Para instalarlo, se requiere la instalación de dependencias adicionales, incluido Python 2. Revise la matriz de compatibilidad y los requisitos previos [aquí](../../azure-monitor/agents/agents-overview.md#supported-operating-systems).

- El código de error 52 en el mensaje de estado indica que falta una dependencia. Para más información sobre la dependencia que falta, compruebe la salida y los registros.

- Si se produce un error en la instalación, revise la sección **Solución de problemas y soporte técnico** en la información general de la extensión. En la mayoría de los casos, se incluye un código de error en el mensaje de estado. Los mensajes de estado del agente de Log Analytics para Linux se explican [aquí](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support), junto con información general de solución de problemas de esta extensión de máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece aquí o no puede resolverlo, intente obtener ayuda adicional mediante uno de los siguientes canales:

- Obtenga respuestas de expertos de Azure a través de [Preguntas y respuestas de Microsoft](/answers/topics/azure-arc.html).

- Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente. El Soporte técnico de Azure pone en contacto a la comunidad de Azure con respuestas, soporte técnico y expertos.

- Registrar un incidente de soporte técnico de Azure. Vaya al [sitio de Soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte técnico**.
