---
title: Solución de problemas comunes de Azure Chaos Studio
description: Obtenga información sobre cómo solucionar problemas comunes al usar Azure Chaos Studio.
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/10/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: 9d56666f42d837f59ec22031646d46e22f0b298d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372499"
---
# <a name="troubleshoot-issues-with-azure-chaos-studio"></a>Solución de problemas con Azure Chaos Studio

A medida que usa Chaos Studio, es posible que encuentre algunos problemas ocasionalmente. En este artículo se detallan los problemas comunes y los pasos para solucionarlos.

## <a name="general-troubleshooting-tips"></a>Sugerencias para solución de problemas generales

Las siguientes fuentes son útiles para solucionar problemas con Chaos Studio:
1. **Registro de actividad**: el [registro de actividad de Azure](../azure-monitor/essentials/activity-log.md) contiene todas las operaciones de creación, actualización y eliminación de una suscripción, incluidas las operaciones de Chaos Studio, como la habilitación de un destino o funcionalidades, la instalación del agente y la creación o ejecución de un experimento. Los errores en el registro de actividad indican que es posible que una acción del usuario esencial para usar Chaos Studio no se haya completado. La mayoría de los errores directos del servicio también insertan errores al ejecutar una operación de Azure Resource Manager, por lo que el registro de actividad también tendrá el registro de los errores insertados durante un experimento para algunos errores directos del servicio.
2. **Detalles del experimento**: los detalles de ejecución del experimento muestran el estado y los errores de la ejecución de un experimento individual. Al abrir un error específico en los detalles del experimento, se mostrarán los recursos con errores y los mensajes de cada error. [Obtenga más información sobre cómo acceder a los detalles del experimento](chaos-studio-run-experiment.md#view-experiment-history-and-details).
3. **Registros de agente**: si usa un error basado en agente, es posible que tenga que usar RDP o SSH en la máquina virtual para comprender por qué el agente no pudo ejecutar un error. Las instrucciones para acceder a los registros del agente dependen del sistema operativo:
    * **Agente de Windows de Chaos**: los registros del agente se encuentran en el registro de eventos de Windows en la categoría Aplicación con el origen AzureChaosAgent. El agente agrega eventos de actividad de error y comprobación de mantenimiento periódica (capacidad para autenticarse y comunicarse con el servicio del agente de Chaos Studio) a este registro.
    * **Agente de Linux de Chaos**: el agente de Linux usa systemd para administrar el proceso del agente como servicio de Linux. Para ver el diario de systemd del agente (los eventos registrados por el servicio del agente), ejecute el comando `journalctl -u azure-chaos-agent`.
4. **Estado de la extensión de VM**: si usa un error basado en agente, es posible que también tenga que comprobar que la extensión de VM está instalada y en buen estado. En Azure Portal, vaya a la máquina virtual y, a continuación, a **Extensiones** o **Extensiones y aplicaciones**. Haga clic en la extensión ChaosAgent y busque los campos siguientes:
    * El **Estado** debería mostrar el mensaje "El aprovisionamiento se realizó correctamente". Cualquier otro estado indica que el agente no se pudo instalar. Compruebe que se cumplen los [requisitos del sistema](chaos-studio-limitations.md#limitations) y vuelva a intentar instalar el agente.
    * El **Estado del controlador** debería mostrar "Listo". Cualquier otro estado indica que el agente está instalado, pero no se puede conectar al servicio Chaos Studio. Compruebe que se cumplen todos los [requisitos de red](chaos-studio-limitations.md#limitations) y que la identidad administrada asignada por el usuario se ha agregado a la máquina virtual e intente reiniciar.

## <a name="issues-onboarding-a-resource"></a>Problemas al incorporar un recurso

### <a name="resources-do-not-show-up-in-the-targets-list-in-the-azure-portal"></a>Los recursos no se muestran en la lista de destinos de Azure Portal
Si no ve los recursos que quiere habilitar en la lista de destinos de Chaos Studio, puede deberse a cualquiera de los siguientes problemas:
* Los recursos no se encuentran en [una región admitida para Chaos Studio](https://azure.microsoft.com/global-infrastructure/services/?products=chaos-studio).
* Los recursos no son de [un tipo de recurso compatible en Chaos Studio](chaos-studio-fault-providers.md).
* Los recursos están en una suscripción o grupo de recursos que se filtran en los filtros de la lista de destino. Cambie los filtros de suscripción y grupo de recursos para ver los recursos.

### <a name="target-andor-capability-enablement-fails-or-doesnt-show-correctly-in-the-target-list"></a>Se produce un error en la habilitación de destinos o funcionalidades o no se muestran correctamente en la lista de destino.
Si ve un error al habilitar destinos o funcionalidades, pruebe los pasos siguientes:
1. Compruebe que tiene los permisos adecuados para los recursos que va a incorporar. La habilitación de un destino o funcionalidades requiere el permiso Microsoft.Chaos/\* en el ámbito del recurso. Los roles integrados, como Colaborador, tienen permisos comodín de lectura y escritura, lo que incluye permisos para todas las operaciones de Microsoft.Chaos.
2. Espere unos minutos a que se actualice la lista de destinos y funcionalidades. Azure Portal usa Azure Resource Graph para recopilar información sobre la incorporación de destinos y funcionalidades, y la actualización puede tardar hasta cinco minutos en propagarse.
3. Si el recurso sigue apareciendo como "No habilitado", pruebe los pasos siguientes:
    1. Intente habilitar el recurso de nuevo.
    2. Si la habilitación de recursos sigue devolviendo errores, visite el registro de actividad y busque la operación de creación de destino con errores para ver información detallada sobre el error.
4. Si el recurso muestra "Habilitado", pero la incorporación de funcionalidades ha devuelto errores, pruebe los pasos siguientes:
    1. Haga clic en el botón **Administrar acciones** en el recurso en la lista de destinos. Compruebe las funcionalidades que no se comprobaron y haga clic en **Guardar**.
    2. Si la habilitación de funcionalidades sigue devolviendo errores, visite el registro de actividad y busque la operación de creación de destino con errores para ver información detallada sobre el error.

## <a name="prerequisite-issues"></a>Problemas de requisitos previos

Algunos problemas se deben a que faltan algunos requisitos previos. 

### <a name="agent-based-faults-fail-on-a-virtual-machine"></a>Errores basados en agente en una máquina virtual
Los errores basados en agente pueden deberse a diversos motivos relacionados con la falta de cumplimiento de los requisitos previos:
* En VM Linux, los errores de [presión de CPU](chaos-studio-fault-library.md#cpu-pressure), [presión de memoria física](chaos-studio-fault-library.md#physical-memory-pressure), [presión de E/S de disco](chaos-studio-fault-library.md#disk-io-pressure-linux) y [esfuerzo stress-ng arbitrario](chaos-studio-fault-library.md#arbitrary-stress-ng-stress) requieren la instalación de la [utilidad stress-ng](https://wiki.ubuntu.com/Kernel/Reference/stress-ng) en la máquina virtual. Para obtener más información sobre cómo instalar stress-ng, consulte las secciones de requisitos previos de los errores.
* En VM Linux o Windows, la identidad administrada asignada por el usuario proporcionada durante la habilitación de destino basada en agente también debe agregarse a la máquina virtual.
* En VM Linux o Windows, a la identidad administrada asignada por el sistema para el experimento se le debe conceder el rol Lector en la VM (los roles aparentemente elevados de Colaborador de máquina virtual no incluyen la operación \*/Read necesaria para que el servicio del agente de Chaos Studio lea el recurso del proxy de destino del agente de Microsoft en la máquina virtual).

### <a name="aks-chaos-mesh-faults-fail"></a>Errores de Chaos Mesh de AKS
Los errores de Chaos Mesh de AKS pueden deberse a diversos motivos relacionados con la falta de cumplimiento de los de requisitos previos:
* Chaos Mesh debe instalarse primero en el clúster de AKS antes de usar los errores de Chaos Mesh de AKS. Puede encontrar las instrucciones en el [tutorial sobre errores de Chaos Mesh en AKS](chaos-studio-tutorial-aks-portal.md#set-up-chaos-mesh-on-your-aks-cluster).
* Se debe usar la versión de Chaos Mesh 2.0.4 o posterior. Para obtener la versión de Chaos Mesh, puede conectarse al clúster de AKS y ejecutar `helm version chaos-mesh`.
* Chaos Mesh debe instalarse con el espacio de nombres `chaos-testing`. No se admiten otros nombres de espacios de nombres para Chaos Mesh.
* El rol de administrador del clúster de Azure Kubernetes Service debe asignarse a la identidad administrada asignada por el sistema para el experimento de Chaos.

## <a name="issues-creating-or-designing-an-experiment"></a>Problemas al crear o diseñar un experimento

### <a name="when-adding-a-fault-my-resource-does-not-show-in-the-target-resources-list"></a>Al agregar un error, mi recurso no se muestra en la lista Recursos de destino
Al agregar un error, si no ve el recurso de destino con un error en la lista Recursos de destino, puede deberse a cualquiera de los siguientes problemas:
* El filtro **Suscripción** se establece para excluir la suscripción en la que se implementa el destino. Haga clic en el filtro de suscripción y modifique las suscripciones seleccionadas.
* El recurso aún no se ha incorporado. Visite la vista **Destinos** y habilite el destino. Al acabar, tendrá que cerrar el panel Agregar error y volver a abrirlo para ver una lista de destino actualizada.
* El recurso aún no se ha habilitado para el tipo de destino de ese error. Consulte la [biblioteca de errores](chaos-studio-fault-library.md) para ver qué tipo de destino se usa para el error. A continuación, vaya a la vista **Destino** y habilite el tipo de destino (basado en agente para los errores de agente de Microsoft o directo de servicio para el resto de tipos de destino). Al acabar, tendrá que cerrar el panel Agregar error y volver a abrirlo para ver una lista de destinos actualizada.
* El recurso aún no tiene habilitada la funcionalidad para ese error. Consulte la [biblioteca de errores](chaos-studio-fault-library.md) para ver el nombre de funcionalidad del error y, a continuación, vaya a la vista **Destinos** y haga clic en **Administrar acciones** en el recurso de destino. Active la casilla de la funcionalidad correspondiente al error que está intentando ejecutar y haga clic en **Guardar**. Al acabar, tendrá que cerrar el panel Agregar error y volver a abrirlo para ver una lista de destinos actualizada.
* El recurso se acaba de incorporar recientemente y aún no ha aparecido en Azure Resource Graph. La lista Recursos de destino se consulta desde Azure Resource Graph y, después de habilitar un nuevo destino, la actualización puede tardar hasta cinco minutos en propagarse a Azure Resource Graph. Espere unos minutos y vuelva a abrir el panel Agregar error.

### <a name="when-creating-an-experiment-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity"></a>Al crear un experimento, aparece el error `The microsoft:agent provider requires a managed identity`.

Este error se produce cuando el agente no se ha implementado en la máquina virtual. Para obtener instrucciones de instalación, consulte el artículo sobre la [creación y ejecución de un experimento que usa errores basados en agentes](chaos-studio-tutorial-agent-based-portal.md).

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported"></a>Al crear un experimento, aparece el error `The content media type '<null>' is not supported. Only 'application/json' is supported.`

Puede que se encuentre con este error si está creando el experimento mediante una plantilla de ARM o la API de REST de Chaos Studio. El error indica que hay un código JSON con formato incorrecto en la definición del experimento. Compruebe si tiene errores de sintaxis, como llaves o corchetes que no coinciden ({} y \[\]) con un linter de JSON como Visual Studio Code.

## <a name="issues-running-an-experiment"></a>Problemas al ejecutar un experimento

### <a name="the-execution-status-of-my-experiment-after-starting-is-failed"></a>El estado de ejecución de mi experimento después del inicio es "Error".

En la lista **Experimentos** de Azure Portal, haga clic en el nombre del experimento para ver la **información general del experimento**. En la sección **Historial**, haga clic en **Detalles** junto a la ejecución del experimento con errores para ver información detallada del error.

![Historial del experimento](images/run-experiment-history.png)

### <a name="my-agent-based-fault-failed-with-error-verify-that-the-target-is-correctly-onboarded-and-proper-read-permissions-are-provided-to-the-experiment-msi"></a>Se produjo el siguiente error de mi agente: compruebe que el destino se ha incorporado correctamente y que se han concedido los permisos de lectura adecuados al msi del experimento.

Esto puede ocurrir si se ha usado Azure Portal para incorporar el agente, que tiene un problema conocido: la habilitación de un destino basado en agente no asigna la identidad administrada asignada por el usuario a la máquina virtual o al conjunto de escalado de máquinas virtuales.

Para resolver este problema, vaya a la máquina virtual o al conjunto de escalado de máquinas virtuales en Azure Portal y luego a **Identidad**, abra la pestaña **Asignado por el usuario** y **agregue** la identidad asignada por el usuario a la máquina virtual. Cuando termine, es posible que tenga que reiniciar la máquina virtual para que el agente se conecte.
