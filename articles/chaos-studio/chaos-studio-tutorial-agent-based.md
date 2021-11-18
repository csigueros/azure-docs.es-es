---
title: Creación de un experimento que use un error basado en agente con Azure Chaos Studio
description: Creación de un experimento que use un error basado en agente y configuración del agente del caos
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 90abee8e5d776b1426e306fe915f5e19a9b1716c
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158424"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-to-add-cpu-pressure-to-a-linux-vm"></a>Creación de un experimento del caos que use un error basado en agente para agregar presión de CPU a una VM Linux

Puede usar un experimento de caos para comprobar que la aplicación es resistente a los errores provocando esos errores en un entorno controlado. En esta guía, se generará un evento de CPU elevada en una máquina virtual Linux usando un experimento del caos y Azure Chaos Studio. Ejecutar este experimento le puede permitir defenderse ante el hecho de que una aplicación se quede sin recurso.

Estos mismos pasos se pueden usar para configurar y ejecutar un experimento para cualquier error basado en agente. Un error **basado en agente** requiere configurar e instalar el agente del caos, a diferencia de un error directo de servicio, que se produce directamente en un recurso de Azure sin necesidad de instrumentación.


## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Una máquina virtual Linux. Si no tiene ninguna máquina virtual, puede [seguir estos pasos para crear una](../virtual-machines/linux/quick-create-portal.md).
- Una configuración de red que le permita [acceder mediante SSH a su máquina virtual](../virtual-machines/ssh-keys-portal.md).
- Una identidad administrada asignada por el usuario **que se haya asignado a la máquina virtual de destino o al conjunto de escalado de máquinas virtuales**. Si no tiene ninguna identidad administrada asignada por el usuario, puede [seguir estos pasos para crear una](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md).


## <a name="enable-chaos-studio-on-your-virtual-machine"></a>Habilitación de Chaos Studio en una máquina virtual

Chaos Studio no puede insertar errores en una máquina virtual a menos que esta se haya incorporado primero a Chaos Studio. Se incorpora una máquina virtual a Chaos Studio creando un [destino y capacidades](chaos-studio-targets-capabilities.md) en el recurso y, después, instalando el agente del caos. Las máquinas virtuales tienen dos tipos de destino: uno que habilita los errores directos de servicio (no se requiere ningún agente) y otro que habilita los errores basados en agente (se requiera instalar un agente). El agente del caos es una aplicación instalada en su máquina virtual como una [extensión de máquina virtual](../virtual-machines/extensions/overview.md) que le permite insertar errores en el sistema operativo invitado.

### <a name="install-stress-ng"></a>Instalación de stress-ng

El agente de Chaos Studio para Linux requiere stress-ng, una aplicación de código abierto que puede generar varios eventos de estrés en una máquina virtual. Puede instalar stress-ng [conectándose a su máquina virtual Linux](../virtual-machines/ssh-keys-portal.md) y ejecutando el comando de instalación adecuado para su administrador de paquetes; por ejemplo:

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

o

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-capabilities-and-agent"></a>Habilitación del destino, las capacidades y el agente del caos

> [!IMPORTANT]
> Antes de realizar los pasos siguientes, debe [crear una identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md) y asignarla a la máquina virtual de destino o al conjunto de escalado de máquinas virtuales.

1. Abra [Azure Portal](https://portal.azure.com).
2. Busque **Chaos Studio (versión preliminar)** en la barra de búsqueda.
3. Haga clic en **Destinos** y vaya a su máquina virtual.
![Visualización de los destinos en Azure Portal](images/tutorial-agent-based-targets.png)
4. Marque la casilla situada junto a su máquina virtual y haga clic en **Habilitar destinos** y, después, en **Habilitar destinos basados en agente** en el menú desplegable.
![Habilitación de destinos en Azure Portal](images/tutorial-agent-based-targets-enable.png)
5. Seleccione la **identidad administrada** que usará para autenticar el agente del caos y, si quiere, habilite Application Insights para ver eventos de experimentos y registros de agente.
![Selección de una identidad administrada](images/tutorial-agent-based-targets-enable-options.png)
6. Haga clic en **Revisar y habilitar** y, después, en **Habilitar**.
![Revisión de la habilitación de destinos basados en agente](images/tutorial-agent-based-targets-enable-review.png)
7. Al cabo de unos minutos, aparecerá una notificación que indica que los recursos seleccionados se habilitaron correctamente. Azure Portal agregará la identidad asignada por el usuario a la máquina virtual, habilitará el destino y las capacidades del agente, e instalará el agente del caos como extensión de máquina virtual.
![Notificación que muestra el destino habilitado correctamente](images/tutorial-agent-based-targets-enable-confirm.png)

Ha incorporado correctamente su máquina virtual Linux a Chaos Studio. En la vista **Destinos** también puede administrar las funcionalidades habilitadas en este recurso. Al hacer clic en el vínculo **Administrar acciones** junto a un recurso, se mostrarán las funcionalidades habilitadas para ese recurso.

## <a name="create-an-experiment"></a>Creación de un experimento
Ahora que tiene la máquina virtual incorporada, puede crear un experimento. Un experimento de caos define las acciones que desea realizar en los recursos de destino, organizados en pasos, que se ejecutan secuencialmente, y ramas, que se ejecutan en paralelo.

1. Haga clic en la pestaña **Experimentos** en el panel de navegación de Chaos Studio. En esta vista, puede ver y administrar todos los experimentos de caos. Haga clic en la vista **Agregar un experimento**
![ Experimentos en Azure Portal](images/tutorial-agent-based-add.png)
2. Rellene la **Suscripción**,el **Grupo de recursos** y la **Ubicación** donde desea implementar el experimento de caos. Ponga un **Nombre** al experimento. Haga clic en **Siguiente: Diseñador de experimentos >** 
![Agregar los detalles básicos del experimento](images/tutorial-agent-based-add-basics.png)
3. Ahora está en el diseñador de experimentos de Chaos Studio. El diseñador de experimentos permite compilar el experimento mediante la adición de pasos, ramas y errores. Asigne un nombre descriptivo a su **Paso** y **Rama** y,a continuación, haga clic en **Agregar error**.
![Diseñador de experimentos](images/tutorial-agent-based-add-designer.png)
4. Seleccione **Presión de CPU** en el menú desplegable y, después, rellene el campo **Duración** con el número de minutos durante el que se debe aplicar presión y **pressureLevel** con la cantidad de presión de CPU que hay que aplicar. Deje en blanco el campo **virtualMachineScaleSetInstances**. Haga clic en **Siguiente: Recursos de destino >** 
![Propiedades del error](images/tutorial-agent-based-add-fault.png)
5. Seleccione su máquina virtual y haga clic en **Siguiente**
![Agregar un destino](images/tutorial-agent-based-add-targets.png).
6. Compruebe que el experimento tiene el aspecto correcto y, a continuación, haga clic en **Revisar y crear** y, después, en **Crear.** 
![Revisar y crear el experimento](images/tutorial-agent-based-add-review.png)

## <a name="give-experiment-permission-to-your-virtual-machine"></a>Concesión de permiso de experimento a la máquina virtual
Al crear un experimento de caos, Chaos Studio crea una identidad administrada asignada por el sistema que ejecuta errores en los recursos de destino. Esta identidad debe tener los [permisos adecuados](chaos-studio-fault-providers.md) en el recurso de destino para que el experimento se ejecute correctamente.

1. Vaya a su máquina virtual y haga clic en **Control de acceso (IAM)** .
![Página de información general de la máquina virtual](images/tutorial-agent-based-access-resource.png)
2. Haga clic en **Agregar** y, a continuación, en **Agregar asignación de roles**.
![Introducción al control de acceso](images/tutorial-agent-based-access-iam.png)
3. Busque **Lector** y seleccione el rol. Haga clic en **Siguiente**
![Asignación del rol Colaborador de máquina virtual](images/tutorial-agent-based-access-role.png).
4. Haga clic en **Seleccionar miembros** y busque el nombre del experimento. Seleccione el experimento y haga clic en **Seleccionar**. Si hay varios experimentos en el mismo inquilino con el mismo nombre, el nombre del experimento se truncará con caracteres aleatorios agregados.
![Adición de un experimento al rol](images/tutorial-agent-based-access-experiment.png)
5. Haga clic en **Revisar y asignar** y, a continuación, en **Revisar y asignar**.

## <a name="run-your-experiment"></a>Ejecutar el experimento
Ahora ya puede ejecutar el experimento. Para ver el impacto, recomendamos abrir un [gráfico de métricas de Azure Monitor](../azure-monitor/essentials/tutorial-metrics-explorer.md) con la presión de CPU de su máquina virtual en otra pestaña del navegador.

1. En la vista **Experimentos,** haga clic en el experimento y, a continuación, haga clic en **Iniciar** y, a continuación, haga clic en **Aceptar**.
![Inicio del experimento](images/tutorial-agent-based-start.png)
2. Cuando el **estado** cambie a **En ejecución**,haga clic en **Detalles** de la última ejecución en **Historial** para ver los detalles del experimento en ejecución.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha ejecutado un experimento basado en agente, podrá:
- [Crear un experimento que use errores directos de servicio](chaos-studio-tutorial-service-direct.md)
- [Administrar el experimento](chaos-studio-run-experiment.md)
