---
title: Solución de problemas con la implementación de características de Azure Automation
description: En este artículo se describe cómo solucionar y resolver problemas surgidos al implementar las características de Azure Automation.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 8112b7637fc2737a80ab9235b962b2adef6d1b19
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607800"
---
# <a name="troubleshoot-feature-deployment-issues"></a>Solución de incidencias en la implementación de características

Cuando implemente la característica Update Management de Azure Automation o la característica Change Tracking e Inventario en las VM, pueden aparecer mensajes de error. En este artículo se describen los errores que pueden producirse y cómo resolverlos.

## <a name="known-issues"></a>Problemas conocidos

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Escenario: el cambio de nombre de un nodo registrado existe la anulación o la repetición del registro

#### <a name="issue"></a>Problema

Un nodo se registra en Azure Automation y después se modifica el nombre del equipo del sistema operativo. Los informes del nodo continúan apareciendo con el nombre original.

#### <a name="cause"></a>Causa

Cuando se modifica el nombre de los nodos registrados, no se actualiza el nombre del nodo de Azure Automation.

#### <a name="resolution"></a>Resolución

Anule el registro del nodo en State Configuration de Azure Automation y después vuelva a registrarlo. Los informes publicados en el servicio antes de ese momento ya no estarán disponibles.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Escenario: No se permite volver a firmar certificados con el proxy HTTPS

#### <a name="issue"></a>Problema

Al conectarse con un proxy que finaliza el tráfico HTTPS y, después, vuelve a cifrar el tráfico utilizando un nuevo certificado, el servicio no permite la conexión.

#### <a name="cause"></a>Causa

Azure Automation no permite volver a firmar los certificados usados para cifrar el tráfico.

#### <a name="resolution"></a>Resolución

Actualmente, no hay ninguna solución alternativa para este problema.

## <a name="general-errors"></a>Errores generales

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Escenario: Se produce un error en la implementación de características con el mensaje "No se puede habilitar la solución".

#### <a name="issue"></a>Problema

Al intentar habilitar una característica en una VM, recibe uno de los siguientes mensajes:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Este error se produce porque faltan permisos para la VM, el área de trabajo o el usuario, o porque los permisos son incorrectos.

#### <a name="resolution"></a>Resolución

Asegúrese de que tiene los [permisos de implementación de características](../automation-role-based-access-control.md#feature-setup-permissions) correctos y, a continuación, intente implementar la característica de nuevo. Si recibe el mensaje de error `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, consulte la siguiente [información de solución de problemas](update-management.md#failed-to-enable-error).

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Escenario: Se produce un error en la implementación de características con el mensaje "No se pudo configurar la cuenta de Automation para el registro de diagnóstico".

#### <a name="issue"></a>Problema

Al intentar habilitar una característica en una máquina virtual, recibe el mensaje siguiente:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Este error puede producirse si el plan de tarifa no coincide con el modelo de facturación de la suscripción. Para más información, consulte [Supervisión del uso y costos estimados en Azure Monitor](../../azure-monitor//usage-estimated-costs.md).

#### <a name="resolution"></a>Resolución

Cree manualmente el área de trabajo de Log Analytics y repita el proceso de implementación de características para seleccionar el área de trabajo creada.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Escenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Este código de error significa que la consulta de búsqueda guardada del grupo de equipos que se utilizó para definir el destino de la característica no tenía un formato correcto. 

#### <a name="cause"></a>Causa

Es posible que haya modificado la consulta o que lo haya hecho el sistema.

#### <a name="resolution"></a>Resolución

Puede eliminar la consulta de la característica y volver a habilitar la característica, lo que creará de nuevo la consulta. La consulta se encuentra en la opción **Búsquedas guardadas** del área de trabajo. El nombre de la consulta es **MicrosoftDefaultComputerGroup** y la categoría de la consulta es el nombre de la característica asociada. Si hay varias características habilitadas, la consulta **MicrosoftDefaultComputerGroup** aparecerá varias veces en **Búsquedas guardadas**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Escenario: PolicyViolation

#### <a name="issue"></a>Problema

Este código de error indica que no se pudo realizar la implementación debido a la infracción de una o varias asignaciones de Azure Policy.

#### <a name="cause"></a>Causa 

Una asignación de Azure Policy está bloqueando la finalización de la operación.

#### <a name="resolution"></a>Resolución

Para implementar correctamente la característica, debe considerar la posibilidad de modificar la definición de la directiva indicada. Como se pueden establecer muchos tipos diferentes de definiciones de directivas, los cambios necesarios dependerán de la definición de la directiva que se haya infringido. Por ejemplo, si se ha asignado una definición de directiva a un grupo de recursos que deniega el permiso para cambiar el contenido de algunos recursos incluidos, puede elegir una de estas correcciones:

* Quite la asignación de la directiva por completo.
* Intente habilitar la característica para otro grupo de recursos.
* Cambie el destino de la asignación de la directiva a un recurso específico; por ejemplo, una cuenta de Automation.
* Revise el conjunto de recursos que la definición de la directiva debe denegar según la configuración.

Consulte las notificaciones en la esquina superior derecha de Azure Portal o desplácese hasta el grupo de recursos que contiene la cuenta de Automation y seleccione **Implementaciones** en **Configuración** para ver la implementación en la que se han producido errores. Para más información acerca de Azure Policy, consulte [Introducción a Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Escenario: Error al intentar desvincular un área de trabajo

#### <a name="issue"></a>Problema

Recibirá el siguiente mensaje de error cuando intente desvincular un área de trabajo:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Este error se produce cuando todavía dispone de características activas en el área de trabajo de Log Analytics que dependen de que su cuenta de Automation y el área de trabajo de Log Analytics estén vinculadas.

### <a name="resolution"></a>Resolución

Quite los recursos de las siguientes características del área de trabajo si las usa:

* Administración de actualizaciones
* Change Tracking e Inventario
* Inicio y detención de máquinas virtuales durante las horas de trabajo

Una vez que quite los recursos de las características, podrá desvincular el área de trabajo. Es importante limpiar los artefactos existentes de esas características en el área de trabajo y la cuenta de Automation:

* En Update Management, quite las **implementaciones de actualización (programaciones)** de la cuenta de Automation.
* Para la solución Start/Stop VMs during off-hours, quite los bloqueos de los componentes de la característica de la cuenta de Automation en **Configuración** > **Bloqueos**. Para obtener más información, consulte [Eliminación de la característica](../automation-solution-vm-management-remove.md).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Errores de Log Analytics para la extensión de Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Se puede producir un error en la instalación de la extensión del agente de Log Analytics para Windows por diversos motivos. En la siguiente sección se describen los problemas de implementación de características que pueden causar errores durante la implementación de la extensión del agente de Log Analytics para Windows.

>[!NOTE]
>El agente de Log Analytics para Windows es el nombre que se usa actualmente en Azure Automation para Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Escenario: Excepción durante una solicitud WebClient

La extensión de Log Analytics para Windows en la VM no puede comunicarse con los recursos externos y la implementación produce un error.

#### <a name="issue"></a>Problema

Los siguientes son ejemplos de mensajes de error que se devuelven:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Causa

Algunas de las posibles causas de este error son:

* Un servidor proxy configurado en la VM solo permite puertos específicos.
* Una configuración de firewall ha bloqueado el acceso a los puertos y las direcciones necesarios.

#### <a name="resolution"></a>Resolución

Asegúrese de que los puertos y las direcciones adecuados están abiertos para la comunicación. Para obtener una lista de direcciones y puertos, consulte este artículo sobre el [planeamiento de red](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Escenario: No se pudo realizar la instalación por problemas transitorios en el entorno

La instalación de la extensión de Log Analytics para Windows no se pudo realizar durante la implementación porque otra instalación o acción la bloquea.

#### <a name="issue"></a>Problema

A continuación, se muestran ejemplos de mensajes de error que podrían devolverse:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Causa

Algunas de las posibles causas de este error son:

* Hay otra instalación en curso.
* El sistema se desencadenó para reiniciarse durante la implementación de plantillas.

#### <a name="resolution"></a>Resolución

Este error es transitorio por naturaleza. Vuelva a intentar la implementación para instalar la extensión.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Escenario: Tiempo de expiración de la instalación

No se completó la instalación de la extensión de Log Analytics para Windows porque se agotó el tiempo de espera.

#### <a name="issue"></a>Problema

El siguiente es un ejemplo de mensaje de error que se puede devolver:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Este tipos de error se produce porque la VM se somete a una carga pesada durante la instalación.

### <a name="resolution"></a>Resolución

Intente instalar la extensión del agente de Log Analytics para Windows cuando la VM esté sometida a una carga menor.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece aquí o no puede resolverlo, intente obtener ayuda adicional mediante uno de los siguientes canales:

* Obtenga respuestas de expertos de Azure en los [foros de Azure](https://azure.microsoft.com/support/forums/).
* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente. El Soporte técnico de Azure pone en contacto a la comunidad de Azure con respuestas, soporte técnico y expertos.
* Registrar un incidente de soporte técnico de Azure. Vaya al [sitio de Soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte técnico**.